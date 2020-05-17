

# SliverConstraints

**SliverConstraints** 是用于滚动布局中的约束，在非滚动布局中通常使用 **BoxConstraints**。

```dart

const SliverConstraints({
    /// scrollOffset、remainingPaintExtent增长的方向
    @required this.axisDirection,
    /// sliver 排列的方向
    @required this.growthDirection,
    /// 用户手势的方向
    /// viewport的scrollOffset为正直是为forward, 负值为reverse，没有滚动则为idle
    @required this.userScrollDirection,
    /// 在sliver坐标系中的滚动偏移量
    /// 滚动的偏移量，注意这里是针对这个Sliver的，并且非整个Slivers的总滚动偏移量
    @required this.scrollOffset,
    /// 前面sliver已经消耗的滚动距离，等于前面sliver的scrollExtent的累加结果
    @required this.precedingScrollExtent,
    /// 该 Sliver 的上一个时刻 Sliver 组件的 layoutExtent（布局区域）和 paintExtent（绘制区域）重叠了的区域大小（paintExtent - layoutExtent）
    /// 为pinned和floating设计的，若是前一个Sliver绘制大小为100，可是布局大小只有50，那么这个Sliver的overlap为50.
    @required this.overlap,
    /// viewport仍剩余的绘制范围
    /// 还有多少内容能够绘制，参考viewport以及cache。好比多Slivers的时候，前一个占了100，那么后面能绘制的区域就要减掉前面绘制的区域大小，获得剩余的绘制区域大小
    @required this.remainingPaintExtent,
    /// viewport滚动轴纵向的范围
    @required this.crossAxisExtent,
    /// viewport滚动轴纵向的方向
    @required this.crossAxisDirection,
    /// viewport滚动轴的范围
    @required this.viewportMainAxisExtent,
    /// viewport仍剩余的缓存范围
    @required this.remainingCacheExtent,
    /// 缓存起始
    @required this.cacheOrigin,
  })

```
Viewport组件的主要作用就是提供滚动机制，可以根据传入的offset参数来显示特定的内容

```dart
class Viewport extends MultiChildRenderObjectWidget {
    Viewport({
        Key key,
        this.axisDirection: AxisDirection.down, //主轴方向，默认往下
        this.crossAxisDirection, //纵轴方向
        this.anchor: 0.0, //决定scrollOffset = 0分割线在viewport的位置（0 <= anchor <= 1.0）
        @required this.offset, //viewport偏移位置
        this.center, //标记哪个作为center组件
        List<Widget> slivers: const <Widget>[], //sliver组件双向列表
      })
  }
```

layoutOffset表示组件在Viewport中偏移多少距离才开始布局，而remainingPaintExtent表示在Viewport中剩余绘制区域大小，一旦remainingPaintExtent为0的时候，控件是不需要绘制的，因为就算绘制了用户也看不到。

Center前面的组件会一个接一个布局，但是对于Center前面的组件，刚才描述layoutOffset和remainingPaintExtent的图得要倒着来看，也就是说会变成这样：

所以Center组件其实就是一个分割线把内容分成上下两部分，一部分顺着Viewport主轴方向，另外一部分是反主轴的方向发展的

# SliverGeometry

SliverGeometry 对应了每个 sliver 的位置和绘制信息。它将一个 sliver 的信息反馈给 Viewport

```dart

SliverGeometry({
  /// 预估的Sliver可以滚动大小
  this.scrollExtent = 0.0,
  /// 对后一个的overlap属性有影响，它小于[SliverConstraints.remainingPaintExtent],为Sliver在viewport范围(包含cache)内第一个元素到最后一个元素的大小
  this.paintExtent = 0.0,
  /// Sliver 的绘制起点，是相对于自身的起点来看的
  this.paintOrigin = 0.0,
  /// 该 sliver 的起点到下一个 sliver 起点的距离。不能大于 paintExtent
  double layoutExtent,
  /// 最大能绘制的总大小，这个参数是用于[SliverConstraints.remainingPaintExtent] 是无穷大的，就是使用在shrink-wrapping viewport中
  this.maxPaintExtent = 0.0,
  /// 若是sliver被pinned在边界的时候，这个大小为Sliver的自身的高度。其余状况为0
  this.maxScrollObstructionExtent = 0.0,
  /// 点击有效区域的大小，默认为paintExtent
  double hitTestExtent,
  /// 可见，paintExtent为0不可见。
  bool visible,
  /// 是否须要作clip，省得chidren溢出
  this.hasVisualOverflow = false,
  /// viewport layout sliver的时候，若是sliver出现了一些问题，那么这个值将不等于0，经过这个值来修正整个滚动的ScrollOffset
  this.scrollOffsetCorrection,
  /// 该Sliver使用了多少[SliverConstraints.remainingCacheExtent]，针对多Slivers的状况
  double cacheExtent,
})

```


