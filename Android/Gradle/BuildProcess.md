# APK打包流程
---
## 典型Android应用模块构建流程
### 1. apk基本打包流程
1. 通过aapt打包res资源文件，生成R.java、resources.arsc和res文件（二进制 & 非二进制如res/raw和pic保持原样）
> * res中图片和raw文件下内容保持原样，res中其他xml文件内容均转化为二进制形式；assets文件内容保持原样
> * res中的文件会被映射到R.java文件中，访问的时候直接使用资源ID即R.id.filename；assets文件夹下的文件不会被映射到R.java中，访问的时候需要AssetManager类 

2. 处理.aidl文件，生成对应的Java接口文件
3. 通过Java Compiler编译R.java、Java接口文件、Java源文件，生成.class文件
4. 通过dex命令，将.class文件和第三方库中的.class文件处理生成classes.dex
5. 通过apkbuilder工具，将aapt生成的resources.arsc和res文件、assets文件和classes.dex一起打包生成apk
6. 通过Jarsigner工具，对上面的apk进行debug或release签名
7. 通过zipalign工具，将签名后的apk进行对齐处理。zipalign工具使得apk中的资源文件偏离文件起始位置4个字节，从而可以通过mmap()直接访问，从而减少RAM占用

### 详细流程
#### 打包资源文件

