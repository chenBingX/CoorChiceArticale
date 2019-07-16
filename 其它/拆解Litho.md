# 拆解Litho

YogaNode - 属性实际都设置到这了，实际生效在c层
InternalNode - 代理操作YogaNode

设置属性：  

```
InternalNode minWidthPx(@Px int minWidth) {
    mPrivateFlags |= PFLAG_MIN_WIDTH_IS_SET;
    mYogaNode.setMinWidth(minWidth);
    return this;
  }
```


ComponentHost 继承自 ViewGroup， LithoView 继承自 ComponentHost。

## 请求更新
```
  @Override
  public void requestLayout() {
    ViewParent parent = this;
    // 只要往上有一级正在layout，就不应该再次请求layout
    // 因为会刷新到这的
    while (parent instanceof ComponentHost) {
      final ComponentHost host = (ComponentHost) parent;
      if (!host.shouldRequestLayout()) {
        return;
      }

      parent = parent.getParent();
    }
    // 转交给系统出发刷新
    super.requestLayout();
  }

  protected boolean shouldRequestLayout() {
    // 正在layout中就不应该再layout了
    return !mInLayout;
  }
```

## onLayout

```
  void performLayout(boolean changed, int l, int t, int r, int b) {
    // 留给子类实现
  }

  @Override
  protected final void onLayout(boolean changed, int l, int t, int r, int b) {
    // 标记layout开始和结束
    mInLayout = true;
    performLayout(changed, l, t, r, b);
    mInLayout = false;
  }
```


## 绘制

```
  @Override
  public void dispatchDraw(Canvas canvas) {
    // 开始绘制
    mDispatchDraw.start(canvas);
    // 走系统的绘制分发
    super.dispatchDraw(canvas);
    if (mDispatchDraw.isRunning()) {
      // 刷新的时候会绘制ViewTree
      mDispatchDraw.drawNext();
    }
    // 绘制结束
    mDispatchDraw.end();

    DebugDraw.draw(this, canvas);
  }

```

InterleavedDispatchDraw 是绘制分发的真正执行者。


绘制的是DisplayListDrawable，继承自Drawable。重写了draw方法。

LithoView
在onMeasure()中确定大小，然后走performLayout
setComponentAsync() 是异步测量布局->setRootAsync->setRootAndSizeSpecInternal 参数是否异步设为true

CalculateLayoutRunnable 异步runnable中调用calculateLayout()计算布局，当开始计算时，需要取消队列中的所有任务

设置View的时候会测量一次


Component是自定义的绘制元素，需要创建为ComponentTree，然后装载到LithoView容器中


```

    // 是否需要重新计算真实View的大小，进行布局
    final boolean needsAndroidLayout =
        !isCompatibleComponentAndSize(
            mMainThreadLayoutState,
            componentRootId,
            viewWidth,
            viewHeight);
    // 是否需要系统刷新整个布局，不需要就内部刷新
    if (needsAndroidLayout) {
      // 回到主线程更新，整体重新布局
      mLithoView.requestLayout();
    } else {
      mountComponentIfDirty();
    }

```

--

- 在onMeasure()中，先测量，布局ViewTree，然后再赋值
- 标记测量状态



LayoutState负责布局的测量布局，layoutState.mLayoutRoot是一个InternalNode，在绘制的时候取得是这个吗？
在创建

```
LayoutState calculate()->
    createAndMeasureTreeForComponent()->
        createTree()->
            component.createLayout()// 根据组件创建Layout树
        measureTree()

```

计算完后，需要回主线程更新UI。

CommonProps用于存放属性




