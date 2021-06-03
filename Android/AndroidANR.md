# Android ANR  

## 概念
---
ANR全称是 Application Not Responding（应用程序无响应），ANR最直观的体验是用户在操作app时，感觉界面卡顿，无响应，比如按下某个按键，打开某个页面，界面在一定的时间内没有反应就会出现ANR对话框，提醒用户是否关闭应用。  
<br>

## 原因
---
只有当应用程序的UI线程响应超时才会引起ANR，超时原因一般两种：
1. 当前事件正在处理，但是由于耗时太久没有及时完成。         
2. 当前事件由于某种原因被堵塞了，导致没有机会处理，例如UI线程正在响应另外一个事件。
3. CPU、内存、IO 占用过高资源耗尽（其他进程也可以导致）

具体对应到android组件中表现为：
1. KeyDispatchTimeout()  
原因是View的按键事件或者触摸事件在特定的时间（5秒）内无法得到响应  
logcat日志关键字：Input event dispatching timed out
2. BroadcastTimeout  
前台Broadcast：onReceiver在10S内没有处理完成发生ANR。  
后台Broadcast：onReceiver在60s内没有处理完成发生ANR。   
logcat日志关键字：Timeout of broadcast BroadcastRecord
3. ServiceTimeout  
前台Service：onCreate，onStart，onBind等生命周期在20s内没有处理完成发生ANR。  
后台Service：onCreate，onStart，onBind等生命周期在200s内没有处理完成发生ANR  
logcat日志关键字：Timeout executing service  
4. ContentProviderTimeout  
ContentProvider 在10s内没有处理完成发生ANR。  
logcat日志关键字：timeout publishing content providers  
<br/>

## 常见场景
---
### SharedPreference引起的ANR  
[SharedPreference引起的ANR](https://www.jianshu.com/p/3f64caa567e5?utm_source=desktop&utm_medium=timeline)复现方式:  
在当前activity中，调用apply,写入多次，大量的数据到sp中，再进行页面跳转,触发onPause、onStop方法，则在一些低端机(如红米note 1)很容易复现该问题，出现anr.该问题在android8.0以上一般不会出现，都分布在android8.0以下。  
问题分析：  
日志关键字:  
```
at android.app.SharedPreferencesImpl$EditorImpl$1.run(SharedPreferencesImpl.java:364)
at android.app.QueuedWork.waitToFinish(QueuedWork.java:88)
at android.app.ActivityThread.handleStopActivity(ActivityThread.java:3561)
at android.app.ActivityThread.access$1100(ActivityThread.java:172)
```  
使用sp读写配置文件，一般会采用官方的推荐做法，调用apply提交，调用这个方法时，会首先写入内存中，然后将落盘的任务加入队列中，会在异步线程中做落盘的操作，这个操作一般来说是没有问题的，也是google官方推荐的做法。但是另一方面android的系统会在Activity的onStop,onPause等生命周期中，调用QueuedWork.waitToFinish，等待落盘的任务队列执行完成，如果任务队列中的任务很多，或者待写入的数据量很大时(sp文件是全量读写的)，在一些io性能差的中低端机型上就会很容易出现anr.  
apply方法的流程:  
```
final class SharedPreferencesImpl implements SharedPreferences {
 
 public void apply() {
           //调用commitToMemory将数据改动同步到内存中，也就是SharedPreferencesImpl的mMap(HashMap)
           final MemoryCommitResult mcr = commitToMemory();
           final Runnable awaitCommit = new Runnable() {
                   public void run() {
                       try {
                         // 等待写入任务完成
                           mcr.writtenToDiskLatch.await();
                       } catch (InterruptedException ignored) {
                       }
                   }
               };
           // 调用 QueuedWork.add(awaitCommit);将一个等待的任务加入到列表中，在Activity等的生命周期中，就是以这个为判断条件，等待写入任务执行完成的
           QueuedWork.add(awaitCommit);

           Runnable postWriteRunnable = new Runnable() {
                   public void run() {
                       awaitCommit.run();
                       QueuedWork.remove(awaitCommit);
                   }
               };
           // 调用enqueueDiskWrite方法的实现，将写入任务加入到队列中，写入磁盘的操作会在子线程中执行。
           SharedPreferencesImpl.this.enqueueDiskWrite(mcr, postWriteRunnable);
           // 通知回调
           notifyListeners(mcr);
 }
```  
enqueueDiskWrite方法的实现:  
android8.0之前，是将runnable任务加入到单线程的线程池中， android 8.0之后做了很大的调整，几乎是对QueuedWork类做了重写。android 8.0中是将任务加入到LinkedList链表中，而且是在HandlerThread中做异步处理，而不是使用线程池。  
Android8.0对Sp的优化主要是有两个方面：  
* 改变原来被动等待线程调度执行写入的方式，改为主动去调用，涉及主要方法是SharedPreferencesImpl.waitToFinish 
* 增加版本号控制的逻辑，原来是所有的提交都会执行写入磁盘一遍，现在是只执行最后、最新的提交写入磁盘，涉及的主要方法是：SharedPreferencesImpl.writeToFile  

解决思路：
	1. 
老版本 的QueuedWork.waitToFinish方法实现有缺陷，可以去规避这个方法来解决这个问题，就是去清除等待锁的队列，主线程在调用这个方法时，不必去等待。可以只在Android8.0以下加入此处理
	2. 
在工作线程中写入sp时，直接调用commit就可以，不必调用apply,这种情况下，commit的开销更小，在主线程中写入sp时，不要调用commit，要调用apply。(commit是同步的提交到硬件磁盘，因此，在多个并发的提交commit的时候，他们会等待正在处理的commit保存到磁盘后在操作，从而降低了效率)。  
<br/>

### ContextImpl锁引起的ANR
ContextImpl是Context的实现类,涉及到shared_prefs文件的IO操作，系统考虑到线程安全，搞了个同步锁，mSync对象被锁住。  
<br/>

> privatefinalObject mSync = newObject();  

<br/>
这个成员是不可变的，而且是私有的，不准继承，即在Context的生命周期内全局只实例化一次，这样才能在加锁的时候保证唯一性。当某个短暂的时间内，需要通过某个Context进行的IO操作太多了，各个线程都排着队要锁mSync，就可能导致超时。(比如Application初始化时，各种SDK初始化通过getApplicationContext来拿到的同一个Context引用，请求锁的也是同一个mSync对象)

解决方案:  
1. 调用Context相关的IO操作，不是启个子线程就高枕无忧了，由上面分析，mSync对象锁就这么一把，该阻塞还是阻塞，和是不是主线程无关。   
2. 尽量不要在Application的初始化时刻进行太多的方法调用，尤其是针对ApplicationContext的IO操作。 
3. 在主Activity中延后初始化，用IntentService进行异步操作（因为实例化一个Service就是另一个Context对象了）等都是比较好的优化方案。 
4. 不要滥用SharedPreference  

## ANR分析
---
### ANR日志文件导出  
```
adb pull data/anr/traces.txt  
# android10: 
adb bugreport /mypath  
```