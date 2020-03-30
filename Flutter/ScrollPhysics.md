

[参考](https://cloud.tencent.com/developer/article/1516957)

ScrollPhysics 用于控制可滚动控件的物理特性

BouncingScrollPhysics - 允许滚动超出边界，有回弹效果

ClampingScrollPhysics - 不允滚动超出边界

AlwaysScrollableScrollPhysics - 始终响应滚动

NeverScrollableScrollPhysics - 不允许滚动


滑动控件是使用 Scrollable 进行滑动控制的
```
  void _updatePosition() {
    //
    _configuration = ScrollConfiguration.of(context);
    // 默认从这里取平台相关的 ScrollPhysics
    _physics = _configuration.getScrollPhysics(context);
    if (widget.physics != null)
      _physics = widget.physics.applyTo(_physics);
    final ScrollController controller = widget.controller;
    final ScrollPosition oldPosition = position;
    if (oldPosition != null) {
      controller?.detach(oldPosition);
      scheduleMicrotask(oldPosition.dispose);
    }
    _position = controller?.createScrollPosition(_physics, this, oldPosition)
      ?? ScrollPositionWithSingleContext(physics: _physics, context: this, oldPosition: oldPosition);
    assert(position != null);
    controller?.attach(position);
  }
```




```

/// 是否启用计算出来的滚动 Offset
/// 在组件构建的时候会调用决定组件是否可滚动
bool shouldAcceptUserOffset(ScrollMetrics position)

/// [position] 当前的位置, [offset] 用户拖拽距离
/// 将用户拖拽距离 offset 转为需要移动的 pixels
double applyPhysicsToUserOffset(ScrollMetrics position, double offset)

/// 返回 overscroll ，如果返回 0 ，overscroll 就一直是0
/// 返回边界条件
/// 如 BouncingScrollPhysics 中返回 0，表示到 0 位置就是边界，就会应用边界效果
///
double applyBoundaryConditions(ScrollMetrics position, double value)

/// 创建一个滚动的模拟器
/// 控制当用户停止滑动时的滚动效果，返回 null，停止滑动就不会滚动了
/// ClampingScrollSimulation 和 ScrollSpringSimulation
Simulation createBallisticSimulation(ScrollMetrics position, double velocity)

///最小滚动数据
 double get minFlingVelocity

///传输动量，返回重复滚动时的速度
///
double carriedMomentum(double existingVelocity)

///最小的开始拖拽距离
double get dragStartDistanceMotionThreshold

///滚动模拟的公差
///指定距离、持续时间和速度差应视为平等的差异的结构。
Tolerance get tolerance
```

Simulation 实现对列表的滑动、阻尼、回弹效果的实现处理
```
  @override
  void goBallistic(double velocity) {
    assert(pixels != null);
    final Simulation simulation = physics.createBallisticSimulation(this, velocity);
    if (simulation != null) {
      beginActivity(BallisticScrollActivity(this, simulation, context.vsync));
    } else {
      goIdle();
    }
  }
```




SliverToBoxAdapter - 能够将单个 Widget 转换为 Sliver 的


[ScrollMetrics - 包含 ViewPort 当前滚动信息](http://codingdict.com/blog/article/2019/2/11/824.html)
pixels：当前滚动位置
maxScrollExtent：最大可滚动长度。
minScrollExtent：
extentBefore：滑出ViewPort顶部的长度；
extentInside：ViewPort内部长度；此示例中屏幕显示的列表部分的长度
extentAfter：列表中未滑入ViewPort部分的长度；此示例中列表底部未显示到屏幕范围部分的长度
atEdge：是否滑到了Scrollable Widget的边界


SliverConstraints

constraints.scrollOffset - 向下的滚动距离

[RenderSliver 参考](https://cloud.tencent.com/developer/article/1198726)

RenderSliver

SliverGeometry
 然后在它在performLayout方法中，需要根据SliverConstraints，计算出对应的SliverGeometry
 。SliverGeometry中也有一个很重要的参数是 SliverGeometry.paintExtent
 ，用来描述沿着主轴绘制的范围。
 最终的可见区域就是 在viewport中范围和主轴绘制范围的交集。

 scrollExtent - 会吃掉多少滚动距离，做固定的时候有用
 paintExtent - Sliver 的绘制高度
 paintOrigin - Sliver 开始绘制的位置，以 (0，0) 点最为起始点
 layoutExtent - 与后面的 Sliver 的距离，小于 0 固顶部，大于 0 即为值距离，需要大于 paintExtent 和 maxPaintExtent，否则也是固定在顶部

