# [jnitrace](https://github.com/chame1eon/jnitrace)

## 1. 功能
跟踪JNI API调用的工具，可以指定跟踪哪个so的JNI调用

## 2. 安装
pip install jnitrace

## 3. 使用
> jnitrace -l libnative-lib.so -b accurate -d -p com.example.myapplication

- -l libnative-lib.so   指定跟踪的so
- -m <spawn|attach>     指定启动目标app方式
- -b <fuzzy|accurate|none>      控制跟踪日志的输出模式,默认accurate模式

## jnitrace-engine
是jnitrace用于拦截和跟踪JNI API调用的项目。它已作为一个单独的项目公开，以允许Frida模块开发人员使用同一引擎来拦截和修改Android应用程序进行的JNI API调用
### 安装
> npm install jnitrace-engine