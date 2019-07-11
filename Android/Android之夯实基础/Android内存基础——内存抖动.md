> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# 参考资料
[Google官方讲解视屏](https://www.youtube.com/watch?v=McAvq5SkeTk)
# 背景介绍
在[**Java内存管理机制**](http://note.youdao.com/noteshare?id=8f3a866f2691e420be0017f9ef685e50)中我提到过**内存抖动**会引起频繁的GC，从而使UI线程被频繁阻塞，导致画面卡顿。这次我们就聊聊内存抖动。
# 需要避免内存抖动
**内存抖动**是由于短时间内有大量对象进出*Young Generiation*区导致的，它伴随着频繁的GC。通常存在内存抖动时，我们可以在*Android Studio*的*Monitors*中看到如下场景：
![内存抖动图](http://upload-images.jianshu.io/upload_images/1869462-46d3d34c4c013112.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下面是避免发生**内存抖动**的几点建议：
- 尽量避免在**循环体**内创建对象，应该把对象创建移到**循环体**外。
- 注意自定义View的`onDraw()`方法会被频繁调用，所以在这里面不应该频繁的创建对象。
- 当需要大量使用Bitmap的时候，试着把它们缓存在数组中实现复用。
- 对于能够复用的对象，同理可以使用**对象池**将它们缓存起来。

# 总结
总之，因为**内存抖动**是由于大量对象在短时间内被配置而引起的，所以我们要做的就是谨慎对待那些可能会大量创建对象的情况。

![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-79bf5c1f534de902.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
