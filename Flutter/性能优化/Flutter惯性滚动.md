
# 浏览器中的惯性滚动

浏览器由于其异步分块光栅化的机制，能够很好的保障惯性滚动的流畅度。因为js、光栅化 和 合成都在不同的线程，因此其它线程的卡顿不会引起合成上屏。

所以，理论上，浏览器的惯性滚动性能是要优于 Android Native 或者 Flutter 这种直接光栅化的机制的。

但这样的机制也会带来诸如渲染不跟手，或是撕裂白屏等体验问题。

# Flutter 中的惯性滚动

![](https://img-blog.csdnimg.cn/img_convert/b3217730808df6d1d83aaa284f3d55b2.gif)


![](https://pic4.zhimg.com/80/v2-d602ec0f6f108abb2585433f75697e2f_720w.jpg)

Flutter 的惯性滚动每一帧都需要依赖 Relayout 来驱动。而 Flutter 采用的是直接光栅化，整个渲染流水线是串行的，任意阶段的阻塞都会造成卡顿。

Flutter 惯性滚动丢帧的一般原因：

1. 滑动中懒加载新的视图，新视图的 build & layout 耗时阻塞

2. 视图过于复杂，光栅化耗时多


Android 的惯性滑动由于 RecyclerView 的复用机制，一定程度上减少了 build & layout 的耗时，以及对象重建销毁的耗时，同时系统专门定制的光栅化引擎对 UI 框架会有一些指向性的优化，比如绘制指令的优化。

因此 Android 的惯性滑动会优于 Flutter，但任然和 Chromium 有些差距。

