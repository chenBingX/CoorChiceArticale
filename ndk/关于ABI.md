
# 64位or32位

64位Android系统能够以兼容的方式运行32位应用程序，在加载so库时，会根据cpu支持的abi选择对应的.so库，所以通常需要添加多种类型的.so库。  

armeabi-v7a，arm64-v8a  

x86，x86_64  

64位系统在运行32位程序时，会从32位的zygote进程fork出32位的应用程序进程。

也就是说，在64位Android系统中同时存在zygote和zygote64两个进程。

# 如何判断64位or32位
通过反射`android.os.SystemProperties`获取`ro.product.cpu.abi`来判断。  

通过获取父进程可以判断应用程序是32位还是64位。  

# .so的安装
通常一个设备可能会支持多种类型的abi，在apk安装期间系统会扫描apk，根据cpu架构按照abi列表从上到下匹配最优的.so文件安装。

# System.Load()和System.LoadLibrary()

## System.Load()
可以加载未安装的.so文件。

## System.LoadLibrary()
只需要提供.so的名称，会直接加载apk中安装的.so文件。