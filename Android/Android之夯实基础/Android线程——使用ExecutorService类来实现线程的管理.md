> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

在Java 5之后，ExecutorService类被引入，主要是为了方便线程的统一管理。
# ExecutorService的作用
ExecutorService其实是一个线程池，它可以接收任务，然后根据配置来分配线程，并控制其调度。
# 分类
## newCacheThreadPool
- 这种线程池是无界线程池，即它可以创建任意条线程。
- 每次创建新线程时，先检查自己的缓存池中有没有，没有才会创建。
- 它有默认的TimeOut(=60s)，超过这个时长没活动的线程就会被自动的移除，所以完全不必要担心线程的结束问题。
- 这是比较推荐的类型。

## newFixedThreadPool
这个线程和newCacheThreadPool很类似，不同点在于：
- 它有线程数量上限。
- 它没有默认的TimeOut。

## ScheduledThreadPool
- 它可以按照顺序延迟调度线程。

## SingleThreadPool
- 它只有一条线程。
- 感觉并没有多大用处。

## ThreadPoolExecutor
这种类型的线程池可以自己配置相关参数。

参数 | 说明
---|---
corePoolSize | 池中所保存的线程数，包括空闲线程。
maximumPoolSize | 池中允许的最大线程数。’
keepAliveTime | 当线程数大于核心时，此为终止多余的空闲等待新任务的线程的最长时间。 
workQueue | 执行前用于保持任务的队列。此队列仅保持由 execute 方法提交的 Runnable 任务。

![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-eca4821e2d6b3e08.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
