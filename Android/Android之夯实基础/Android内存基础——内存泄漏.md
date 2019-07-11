> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# 背景介绍
**内存泄漏**一种情况是一块内存没有引用指向它，却没被回收，这种情况已经由Java虚拟机的*GC*帮助我们处理好了，见[**Java内存管理机制**](http://note.youdao.com/noteshare?id=8f3a866f2691e420be0017f9ef685e50)；另一种属于**逻辑内存泄漏**，即一个对象已经不再被使用了，但它仍然被另一个被使用中的对象所持有，导致该对象所占用的内存块不能被回收。
  
少部分**内存泄漏**看不出有什么影响，但如果大量发生，将会明显减少可用内存，导致频繁GC，运行缓慢，严重时将容易引发`OutOfMemoryError`。
# Android中常见的内存泄漏
## 集合引发的泄漏
一些对象缓存到集合中，当它不在被使用时，没有从集合中移除，就造成了泄漏。

## Activity中的泄漏
一个Activity中的Context通常会被很多地方引用，如果在Activity执行了`onDestory()`后这些引用没有被置空，将会导致Activity无法被回收。而Activity中依赖了很对对象，这些对象将都不能被回收，所以一定要十分小心Activity的泄漏。以下几种常见的情况需要注意：
- 当创建一个对象，该对象需要Context作为参数时。当该对象在Activity执行了`onDestory()`后仍然可到达，那么它持有的Activity就无法被回收。
- 使用匿名内部类Handler时。Handler会间接的持有外部Activity的引用，当Activity执行了`onDestory()`后，若Handler中仍然有消息在MessageQueue中，那么该Handler就不能被释放，也就意味着Activity也不会被释放。解决方法：
  - 需要在`onDestory()`完成前确保Handler没有执行yan shi延时任务，没有被其他地方引用。同时清理Handler中所有任务，调用`removeCallbacksAndMessages(null);`,该方法参数为空时会移除所以的CallBack和Message。
  - 另一种方法是自定义一个静态内部Handler，让它持有的Activity为弱引用。
  ```
  static class MyHandler extends Handler {
        private final WeakReference<Activity> mActivity;
        public MyHandler(Activity activity) {
            mActivity = new WeakReference<Activity>(activity);
        }
        @Override
        public void handleMessage(Message msg) {
            System.out.println(msg);
            if (mActivity.get() == null) {
                return;
            }
            mActivity.get().todo();
        }
    }
  ```
- 在异步任务中引用Activity或Context，而没有在Activity销毁前停止线程，就会导致Activity的泄漏。
- 内部类、匿名内部类会持有外部类的实例，所以在Activity中使用内部类或匿名内部类，并且将该类创建的对象传递给其他对象，若该对象在Activity之外被引用，或是在仍在进行的异步任务中，那么Activity将不能释放。  

## Bitmap引发的内存泄漏
Bitmap是引用中占用内存的大户，在使用时一定要十分注意。当Bitmap不用时，应该及时调用`recycle()`释放其申请的内存。

## 异步任务引发的泄漏
异步任务如果在它应该停止的时候没有停止，那它本身及它所依赖的对象将都不能被正确的释放。比如上面提到的Activity中的异步任务。  

比如在Activity中这样来开启一个异步任务：
```
new Thread(()->{
  // doSomething with long-time 
}).start();
```
那么，在Activity已经finish()了，但异步任务中的Runnable还被持有，并且由于是属于Activity的匿名内部类，所以会导致Activity的内存不能及时回收。

# 总结
由于系统内存资源有限，所以我们需要尽量避免**内存泄漏**，以保证程序能够流畅运行。
- 由于Activity泄漏将会连带大量的对象一起泄漏，所以在Activity中写代码应该特别注意Handler、内部类及匿名内部类，还有Context不能随便传递。
- 使用异步任务时一定要明确其作用范围，在应该停止的时候记得停止。
- Bitmap占用内存通常较大，所以在使用完后及时释放十分重要。
- 此外，在传递参数到**单例**中时，也需要谨慎。


 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-8082fce4eaf68437.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
