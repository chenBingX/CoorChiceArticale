# 万能的Adjuster 

**Adjuster** 被设计用来在 **SuperTextView** 的绘制过程中插入一些操作。这具有非常重要的意义。比如，实时的改变控件的状态，制作复杂的动画效果或者交互效果。

```
public class YourAdjuster extends SuperTextView.Adjuster {

@Override
protected void adjust(SuperTextView v, Canvas canvas) {
    //do your business。
}

@Override
public boolean onTouch(SuperTextView v, MotionEvent event) {
    //you can get the touch event.
    //If want to get a series of touch event, you must return true here.
}

}
```

通过重写 **Adjuster** 的 `adjust()` 方法，可以获取每次绘制过程中控件的 `Canvas` 对象，这意味着可以在绘制过程中从外部插入一些新的元素。当然，单单通过 **SuperTextView** 的实例修改其状态也是可以的。

通过重写 **Adjuster** 的 `onTouch()` 方法，可以获取每一次控件的触摸事件，如果在该方法中返回true，表明该 **Adjuster** 需要获取后续的触摸事件，同时也会使得 **SuperTextView** 在整个控件树中回去拦截触摸事件。配合 `adjust()` 可以实现一些复杂的交互效果。值得注意的是，如果在 **SuperTextView** 之前，已经有控件拦截的触摸事件，那么其中的 **Adjuster** 将无法获取到触摸事件。

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/SuperTextView.gif)

当装载 **Adjuster** 到  **SuperTextView** 之后，需要调用以下方法来开启 **Adjuster** 的功能：

```
stv.setAutoAdjust(true);
```

当然，停止 **Adjuster** 只需要设置为false即可。

## 如何装载Adjuster到SuperTextView?

```
stv.addAdjuster(mAdjuster);
```

通过上面方法可以将一个 **Adjuster** 添加到 **SuperTextView** 中，最多支持添加3个 **Adjuster** 。超过3个的部分，将会始终覆盖最后一个 **Adjuster**。

如果你想要移除一个 **Adjuster**，通过下面方法来实现。

```
// 移除指定位置的 Adjuster
stv.removeAdjuster(index)
// 移除指定的 Adjuster
stv.removeAdjuster(adjuster)
```

## 设置Adjuster的层级

前面有描述过 **SuperTextView** 的层级划分，**Adjuster** 可以通过配置，将其插入到指定的层级。

```
mAdjuster.setOpportunity(opportunity);
```

层级定义了如下几个枚举变量：

```
public enum Opportunity {
    // 背景层和Drawable层之间
    BEFORE_DRAWABLE,
    // Drawable层和文字层之间
    BEFORE_TEXT,
    // 最上层
    AT_LAST
}
```

分别对应如下几种场景，其中Emoji图是StateDrawable状态图，蓝色圆形就是一个 **Adjuster** ：

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/Opportunity.png)

## 开启动画

在 **SuperTextView** 中，可以通过以下方法触发 **SuperTextView** 的定时绘制：

```
stv.startAnim();
```

启动动画后， **SuperTextView** 将会以默认 **60fps** 的帧率进行刷新。配合 **Adjuster** 可以十分简单的实现动画效果。

当 **SuperTextView** 离开屏幕后，将会自动停止正在播放的动画，当在次进入屏幕时，又会自动启动。所以开发者可以无需担心动画在后台消耗资源。

当然，开发者也可以随时停止动画，通过调用以下方法：

```
stv.stopAnim();
```

通过以下方法，开发者可以随时修改 **SuperTextView** 的刷新频率：

```
// 每秒30帧
stv.setFrameRate(30);
```

## 获得 Adjuster 的添加和移除事件

**Adjuster** 中包含了两个函数：

- `onAttach()`：当 **Adjuster** 被设置到一个 **SuperTextView** 中时会被调用。
- `onDetach()`：当 **Adjuster** 被从一个 **SuperTextView** 中移除时会被调用。

通过在 Adjuster 中重写这两个函数，开发者可以在正确的时机进行状态注册、初始化，或者取消注册、释放资源等操作。

```
public class MyAdjuster extends SuperTextView.Adjuster{

    @Override
    protected void adjust(SuperTextView superTextView, Canvas canvas) {

    }

    @Override
    public void onAttach(SuperTextView stv) {
      // 当 Adjuster 被加入一个 SuperTextView 时会被调用
    }

    @Override
    public void onDetach(SuperTextView stv) {
      // 当 Adjuster 被从 SuperTextView 移除时会被调用
    }
}
```

