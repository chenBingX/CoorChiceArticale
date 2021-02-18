# widget build 优化

- setState 状态刷新位置尽量放置于视图树的低层级

- 对于长列表，避免使用 ListView() 构造函数，推荐使用 ListView.builder 构造函数


# 主 isolate 优化

- 减少或延迟 widget build 中非视图逻辑，如曝光埋点延迟到滑动停止聚合触发

- 列表 Item 高度可知的情况下，推荐设置 itemExtent，减少滑动中频繁计算列表高度

- 使用 const 修饰无需变更的 widget 或普通对象

- 使用 AnimatedBuilder 时，避免在不依赖于动画的 widget 的构造方法中构建 widget 树。动画的每次变动都会重建这个 widget 树。而应该构建子树的那一部分，并将其作为 child 传递给 AnimatedBuilder

- 避免在动画中剪裁。如果可能，请在动画开始之前预先剪切图像

# Render 线程优化

- 对于频繁更新的控件（如动画），使用 RepaintBoundary 隔离它，创建单独 layer 减少重绘区域

- 使用图片替换半透明效果

- 减少 saveLayer（ShaderMask、ColorFilter、Text Overflow）、clipPath的使用，提升 render 线程性能

- 避免使用 Opacity widget，尤其是在动画中避免使用。请用 AnimatedOpacity 或 FadeInImage 进行代替

- 避免使用带换行符的长文本

# 工具推荐

- 官方 DevTools 工具

- 利用 Debug flags 排查问题（推荐 Flutter Performance 分析工具简介）


# 滑动优化

开启滑动手势采样(1.22 版本才支持)

```dart
 GestureBinding.instance.resamplingEnabled = true;
```
