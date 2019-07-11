> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# KillProcess()
调用
```
android.os.Process.killProcess(android.os.Process.myPid())
```
可以杀死当前应用活动的进程，这一操作将会把所有该进程内的资源（包括线程全部清理掉）。当然，由于ActivityManager时刻监听着进程，一旦发现进程被非正常Kill，它将会试图去重启这个进程。  
这就是为什么，有时候当我们试图这样去结束掉应用时，发现它又自动重新启动的原因.

# System.exit()
System.exit()其实是Java中结束进程的方法，调用它将关闭当前的JVM虚拟机。
## System.exit(0)和System.exit(1)的区别
- System.exit(0)表示是正常退出；
- System.exit(1)表示是非正常退出，通常这种退出方式应该放在catch块中。  

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-1cfb40202aa93246.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
