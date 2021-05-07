
# 第一步

为元素添加动画 `.animate()` 的声明。

```dart
var player = document.getElementById('toAnimation').animate()
```

# 第二步

为 `.animate()` 设置参数

## 参数1：KeyframeEffects []

KeyframeEffects 数组描述了一组动画过程，和 css 中的 @keyframes 相同。

```dart

var player = document.getElementById('toAnimate').animate([ 
    { transform: 'scale(1)', opacity: 1, offset: 0 }, 
    { transform: 'scale(.5)', opacity: .5, offset: .3 },
    { transform: 'scale(.667)', opacity: .667 }, 
    { transform: 'scale(.6)', opacity: .6 }], {});
```

## 参数2：时间列表函数 {}

和 css 动画属性中的参数名差不多，少部分情况会有差异

```dart

var player = document.getElementById('toAnimate').animate([ ], { 
    duration: 700, //毫秒 
    easing: 'ease-in-out', //值为'linear', 或贝塞尔曲线, 等等 
    delay: 10, //毫秒 
    iterations: Infinity, //或者为数字 
    direction: 'alternate', //'normal', 'reverse', 等等. 
    fill: 'forwards' //'backwards', 'both', 'none', 'auto' });
```

参数：

- duration：表示时间。如果只有一个时间参数，可以省略对象，只写一个时间数值

- delay：动画延迟执行时间

- fill：动画前后状态保持

- easing：差值器类型

- iterations：动画执行次数。注意无限循环使用 `Infinity`，区别于 css 中的 `infinite`

完整例子

```dart

var player = document.getElementById('toAnimate').animate(
    [{ transform: 'scale(1)', opacity: 1, offset: 0 }, 
    { transform: 'scale(.5)', opacity: .5, offset: .3 },
    { transform: 'scale(.667)', opacity: .667 }, 
    { transform: 'scale(.6)', opacity: .6 }], 
    { 
      duration: 700, //毫秒 
      easing: 'ease-in-out', //值为'linear', 或贝塞尔曲线, 等等 
      delay: 10, //毫秒 
      iterations: Infinity, //或者为数字 
      direction: 'alternate', //'normal', 'reverse', 等等. 
      fill: 'forwards' //'backwards', 'both', 'none', 'auto'});
```






