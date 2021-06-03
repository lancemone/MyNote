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

