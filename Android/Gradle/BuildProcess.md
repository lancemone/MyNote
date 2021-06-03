# APK打包构建流程
---
## 典型Android应用模块构建流程
<br/>

### 1. apk基本打包流程
1. 通过aapt打包res资源文件，生成R.java、resources.arsc和res文件（二进制 & 非二进制如res/raw和pic保持原样）
> * res中图片和raw文件下内容保持原样，res中其他xml文件内容均转化为二进制形式；assets文件内容保持原样
> * res中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即R.id.filename；assets文件夹下的文件不会被映射到R.java中，访问的时候需要AssetManager类 

2. 处理.aidl文件，生成对应的Java接口文件 (如果项目中有使用AIDL)
3. 通过Java Compiler编译R.java、Java接口文件、Java源文件，生成.class文件
4. 通过dex命令，将.class文件和第三方库中的.class文件处理生成classes.dex
5. 通过apkbuilder工具，将aapt生成的resources.arsc和res文件、assets文件和classes.dex一起打包生成apk
6. 通过Jarsigner工具，对上面的apk进行debug或release签名
7. 通过zipalign工具，将签名后的apk进行对齐处理。zipalign工具使得apk中的资源文件偏离文件起始位置4个字节，从而可以通过mmap()直接访问，从而减少RAM占用  
--- 
<br/>

### 2. APK文件结构
>* assets 存放需要打包到APK中的静态文件  
>* lib 存放应用程序所依赖的native库  
>* res 存放应用程序的资源文件  
>* META-INF 存放应用程序的签名和证书  
>* AndroidManifest.xml 应用程序配置文件  
>* classes.dex dex可执行文件  
>* resources.arsc 资源索引表, 记录资源文件和资源ID之间的映射关系  

* assets  
存放需要打包到APK中的静态文件，assets和res的区别如下，assets目录支持任意深度的子目录，用户可以根据自己的需求任意部署文件夹架构，而且res目录下的文件会在.R文件中生成对应的资源ID，assets不会自动生成对应的ID，访问的时候需要AssetManager类。

* res  
是resource的缩写，存放资源文件，存在这个文件夹下的所有文件都会映射到Android工程的.R文件中，生成对应的ID，访问的时候直接使用资源ID，即R.id.filename。  
res文件夹下可以包含多个文件夹，常用的有：  
  * anim存放动画文件
  * drawable目录存放图像资源
  * layout目录存放布局文件  
  * values目录存放一些特征值，colors.xml存放color颜色值，dimens.xml定义尺寸值，string.xml定义字符串的值，styles.xml定义样式对象  
  * xml文件夹存放任意xml文件，在运行时可以通过Resources.getXML()读取  
  * raw是可以直接复制到设备中的任意文件，无需编译

* lib  
存放应用程序依赖的native库文件，一般是用C/C++编写，这里的lib库可能包含4中不同类型，根据CPU型号的不同，大体可以分为ARM，ARM-v7a，MIPS，X86，分别对应着ARM架构，ARM-V7架构，MIPS架构和X86架构。  

* META-INF  
保存应用的签名信息，签名信息可以验证APK文件的完整性。AndroidSDK在打包APK时会计算APK包中所有文件的完整性，并且把这些完整性保存到META-INF文件夹下，应用程序在安装的时候首先会根据META-INF文件夹校验APK的完整性，这样就可以保证APK中的每一个文件都不能被篡改。以此来确保APK应用程序不被恶意修改或者病毒感染，有利于确保Android应用的完整性和系统的安全性。  
META-INF目录下包含的文件有CERT.RSA，CERT.DSA，CERT.SF和MANIFEST.MF，其中CERT.RSA是开发者利用私钥对APK进行签名的签名文件，CERT.SF、MANIFEST.MF记录了文件中文件的SHA-1哈希值

* AndroidManifest.xml  
开发Android应用程序的时候，一般都把代码中的每一个Activity，Service，Provider和Receiver在AndroidManifest.xml中注册，只有这样系统才能启动对应的组件，另外这个文件还包含一些权限声明以及使用的SDK版本信息等等。程序打包时，会把AndroidManifest.xml进行简单的编译，便于Android系统识别，编译之后的格式是AXML格式。  

* classes.dex
而Dalvik虚拟机是在Java虚拟机进行了优化，执行的是Dalvik字节码，而这些Dalvik字节码是由Java字节码转换而来，一般情况下，Android应用在打包时通过AndroidSDK中的dx工具将Java字节码转换为Dalvik字节码。  
dx工具可以对多个class文件进行合并，重组，优化，可以达到减小体积，缩短运行时间的目的。

* resources.arsc  
用来记录资源文件和资源ID之间的映射关系，用来根据资源ID寻找资源。  
Android的开发是分模块的，res目录专门用来存放资源文件，当在代码中需要调用资源文件时，只需要调用findviewbyId()就可以得到资源文件，每当在res文件夹下放一个文件，aapt就会自动生成对应的ID保存在.R文件，我们调用这个ID就可以，但是只有这个ID还不够，.R文件只是保证编译程序不报错，实际上在程序运行时，系统要根据ID去寻找对应的资源路径，而resources.arsc文件就是用来记录这些ID和资源文件位置对应关系的文件。  

