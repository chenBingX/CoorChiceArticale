
[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** 用心提供精致的组件，助您构建精美的应用。


说到 **居家旅行，bi~bi~bi~bi~** 的必备单品，相信开发者们总能在脑子 🧠 中从自己过去积累的知识库中蹦出那么几个心仪的单品。
 

![](https://gw.alicdn.com/tfs/TB1NoTEKa61gK0jSZFlXXXDKFXa-720-536.png)

然而，就在今天，我们试图向开发者们的知识库中添加一个全新的 **必备单品** ——  **F~~Search** 🎊。

**FSearch**  是有神圣使命的，它要力图帮助开发者以最舒适的方式，构建出属于自己的，精美的 **搜索栏**  🔍。
 
在信息 💥 的今天，我们总会想要在自己的应用中加上一个搜索栏，应用的使用者们需要它，因此开发者们也需要它。

那么现在，请调整好聚光灯角度，把 BGM 放一下，有请 **F~~Search～～** 👏👏👏。


||||
|:--:|:--:|:--:|
|![](https://gw.alicdn.com/tfs/TB1QSOkJYr1gK0jSZR0XXbP8XXa-320-469.gif)|![](https://gw.alicdn.com/tfs/TB1fWytJYj1gK0jSZFuXXcrHpXa-320-469.gif)|![](https://gw.alicdn.com/tfs/TB1dh1sJ7T2gK0jSZFkXXcIQFXa-320-469.gif)|
|![](https://gw.alicdn.com/tfs/TB1XrOdJ1L2gK0jSZPhXXahvXXa-320-464.gif)|![](https://gw.alicdn.com/tfs/TB1x4CmJVP7gK0jSZFjXXc5aXXa-320-464.gif)|![](https://gw.alicdn.com/tfs/TB1iFAMXI4z2K4jSZKPXXXAYpXa-360-466.gif)|
|![](https://gw.alicdn.com/tfs/TB1Pt1oJYY1gK0jSZTEXXXDQVXa-360-298.gif)|![](https://gw.alicdn.com/tfs/TB19oLDGKT2gK0jSZFvXXXnFXXa-360-212.gif)|![](https://gw.alicdn.com/tfs/TB19oLDGKT2gK0jSZFvXXXnFXXa-360-212.gif)|


# ✨ 特性

请允许我为各位开发者介绍  **FSearch**  的惊人特性。

- 支持精美的边框效果

- 提供丰富的边角配置

- 支持惊艳的渐变效果

- 提供简单易用的阴影能力

- 支持任意数量的前缀、后缀动作按钮

- 提供丰富多彩的、灵活强大 Hint 效果

- 给开发者更易用的控制器


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

 

# 🛸 传送区

#### 🛸 [【传送门：FSearch Github 主页】](https://github.com/Fliggy-Mobile/fsearch)

#### 📖 [【传送门：FSearch 文档】](https://pub.dev/documentation/fsearch/latest/fsearch/fsearch-library.html)

# 🔩 Base Demo

> 甩开束缚，从新出发

![](https://gw.alicdn.com/tfs/TB1QSOkJYr1gK0jSZR0XXbP8XXa-320-469.gif)

```dart
FSearch(

  /// 设置高
  ///
  /// Set height
  height: 30.0,
  
  /// 设置背景颜色
  ///
  /// Set background color
  backgroundColor: color,

  /// 设置输入内容样式
  ///
  /// Set input text style
  style: style,

  /// 点击键盘搜索时触发
  ///
  /// Fired when you click on the keyboard to search
  onSearch: (value) {
    /// do something
  },

  prefixes: [buildAction()],
)
```

使用  **FSearch**  来构建一个搜索栏是一件十分悠然自得的事。

通过一些简单的参数，开发者能够很容易去改变搜索栏的大小、颜色、字体。

当用户点击键盘的搜索  **Action**  时，会触发 `onSearch`，使得开发者可以在这里进行一些搜索操作。

# 🌖 Prefixes & Suffixes

> 高度灵活，高度可控


![](https://gw.alicdn.com/tfs/TB1fWytJYj1gK0jSZFuXXcrHpXa-320-469.gif)

```dart
FSearch(
  height: 30.0,
  backgroundColor: mainBackgroundColor,
  style: style,

  /// 前缀 Widget
  ///
  /// prefix widget
  prefixes: [ buildAction() ],

  /// 后缀 Widget
  ///
  /// suffix widget
  suffixes: [
    buildAction_1(),
    buildAction_2(),
    buildAction_3(),
  ],
  onSearch: _onSearch,
)
```

在 FSearch 中，开发者可以通过 `prefixes` 和 `suffixes` 参数，为搜索栏分别配置任意个数的前缀或后缀动作按钮。

这样的高度灵活可控，也许是前所未有的。

# 🌈 Gradient

> 彩色就是神马

![](https://gw.alicdn.com/tfs/TB1dh1sJ7T2gK0jSZFkXXcIQFXa-320-469.gif)

```dart
FSearch(
  height: 30.0,
  backgroundColor: mainBackgroundColor,
  style: style,

  /// 配置渐变色
  ///
  /// Set gradient
  gradient: _gradient,
  prefixes: [ buildAction() ],
)
```


**FSearch**  能够支持开发者创建一个漂亮的渐变色搜索栏。

只需要通过 `gradient` 参数进行配置就行。

# 🍄 Corner & Stroke & Shadow

> 想怎么变，就怎么变

![](https://gw.alicdn.com/tfs/TB1XrOdJ1L2gK0jSZPhXXahvXXa-320-464.gif)


```dart

/// #1
FSearch(
  height: 30.0,
  backgroundColor: color,
  style: style,

  /// 边角
  ///
  /// Corner
  corner: FSearchCorner(
      leftTopCorner: 15.0,
      leftBottomCorner: 15.0,
      rightBottomCorner: 15.0),

  /// 边框宽
  ///
  /// border width
  strokeWidth: 1.0,

  /// 边框颜色
  ///
  /// border color
  strokeColor: mainTextTitleColor,

  /// 阴影
  ///
  /// shadow
  shadowColor: Colors.black38,
  shadowBlur: 5.0,
  shadowOffset: Offset(2.0, 2.0),
  prefixes: [buildAction()],
)
```

 **FSearch**  的  **边框** 和 **阴影** 效果和其它的  **FWidget**  成员一样，简单易用。

通过 `corner` 参数，开发者可以使用  **FSearchCorner**  随意的控制  **FSearch**  的表边角大小。

```dart
/// #2
FSearch(
  height: 30.0,
  backgroundColor: color,
  style: style,

  /// 边角
  ///
  /// Corner
  corner: FSearchCorner.all(6.0),

  /// 边角风格
  ///
  /// Corner style
  cornerStyle: FSearchCornerStyle.bevel,
  prefixes: [buildAction()],
)
```

如果配合  `cornerStyle`，可以实现更加复杂精美的效果。

# 📍 Cursor

> 细节可控

![](https://gw.alicdn.com/tfs/TB1x4CmJVP7gK0jSZFjXXc5aXXa-320-464.gif)

```dart
FSearch(
  /// 光标配置
  ///
  /// Cursor
  cursorColor: Colors.red[200],
  cursorRadius: 5.0,
  cursorWidth: 5.0,

  height: 36.0,
  style: style,
  gradient: _gradient,
  corner: _corner,
  prefixes: [ buildAction() ],
  suffixes: [ buildAction() ],
)
```

 **FSearch**  支持通过对搜索栏输入框的光标进行修改。你想改成什么样，就改成什么样。

# 🗂 Hint

> 一步，即可炫！

![](https://gw.alicdn.com/tfs/TB1iFAMXI4z2K4jSZKPXXXAYpXa-360-466.gif)


```dart
/// #1
FSearch(
  height: 36.0,
  style: style,
  color: _color,
  corner: _corner,
  prefixes: [ buildAction() ],
  suffixes: [ buildAction() ],

  /// Hints
  hints: [
    "FSuper is awesome 👍",
    "Come to use FButton",
    "You will love FRefresh",
  ],

  /// 开启 hint 交换动画
  ///
  /// Turn on hint exchange animation
  hintSwitchEnable: true,

  /// 配置 hint 交换动画类型
  ///
  /// Configure hint exchange animation type
  hintSwitchType: FSearchAnimationType.Fade,
)
```

 **FSearch**  为开发者提供了非常强大的  **Hint**  效果。

开发者可以很容易的为  **FSearch**  设置多条  **Hint** ，而且可以通过配置 `hintSwitchEnable: true` 来开启多  **Hint**  交换动画。

当用户开始输入时，Hint 将会被自动隐藏，同时交换动画也会被停止。

当搜索输入框内容再次变为空时，Hint 将会再次出现，且开始播放交换动画。

```dart
/// #2
FSearch(
  height: 36.0,
  style: style,
  color: _color,
  corner: _corner,
  prefixes: [ buildAction() ],
  suffixes: [ buildAction1(),  buildAction2()],
  hints: [
    "Do you want to try FFloat?😃",
    "FRadio can do more 😱 !",
    "I heard that you have been waiting for FDottedLine for a long time...",
  ],
  hintSwitchEnable: true,
)
```


**FSearch**  为开发者准备了丰富的交换动画。默认情况下， **FSearch**  会使用最常见的翻滚交换动画，即  `FSearchAnimationType.Scroll`。
 
当然，开发者可以通过 `hintSwitchType` 参数来配置自己喜欢的动画类型。

> 💡 注意，当 `hints.length == 1` 时，将不会播放 Hint 交换动画。此时仅仅会展示一个普通的 Hint。


```dart
/// #3
FSearch(
  height: 36.0,
  style: style,
  color: _color,
  corner: _corner,
  prefixes: [ buildAction() ],
  suffixes: [ buildAction() ],

  /// Hints
  hints: [
    "Embrace FWidget 👬",
    "We care about your app 🥰",
    "Want to build beautiful apps 🤨 ?",
  ],
  hintSwitchEnable: true,

  /// 配置 hint 交换动画类型
  ///
  /// Configure hint exchange animation type
  hintSwitchType: FSearchAnimationType.Scale,

  /// 获得焦点时是否停止交换动画
  ///
  /// Whether to stop exchanging animation when focus is obtained
  stopHintSwitchOnFocus: false,
)
```

默认情况下，当  **FSearch**  获得输入焦点， **FSearch**  会自动暂停  **Hint**  交换动画；再次获得焦点时，又会自动恢复。

通过配置 `stopHintSwitchOnFocus: false`，可以让  **FSearch**  获得焦点的情况下，依旧继续播放  **Hint**  交换动画，直到用户开始输入.


# 💻 Controller

> 你的程序，你说了算

![](https://gw.alicdn.com/tfs/TB1Pt1oJYY1gK0jSZTEXXXDQVXa-360-298.gif)

```dart
FSearch(
  controller: _controller,
  height: 36.0,
  style: style,
  gradient: _gradient,
  corner: _corner,
  prefixes: [ buildAction() ],
  suffixes: [ buildAction() ],
  hints: [
    "Want more beautiful widgets 🤨 ?",
    "We will launch the official website of FWidget",
    "Will you expect it?",
  ],
  hintStyle: hintStyle,
  hintSwitchEnable: true,
)

/// 获取输入框内容
///
/// Get the input box content
String input = controller.text;

/// 清空输入框内容
///
/// Clear the contents of the input box
controller.text = null;

/// 获取当前 hint，如果有的话
///
/// Get the current hint, if any
String hint = controller.hint;

/// 移除焦点
/// 
/// Remove focus
controller.clearFocus();


/// 获取焦点
///
/// Request focus
controller.requestFocus();
```

 **FSearch**  为开发者们提供了简单好用的、确定的控制器，通过控制器开发者可以在任意的位置对搜索栏的内容进行修改，或者获取。


### [想要了解更多详细内容？请访问 **FSearch** 官方主页 (PS：别忘了投出一个你认可的 **Star** 哦 😘)。](https://github.com/Fliggy-Mobile/fsearch)


# 😃 如何使用？

在项目 `pubspec.yaml` 文件中添加依赖：

## 🌐 pub 依赖方式

```
dependencies:
  fsearch: ^<版本号>
```

> ⚠️ 注意，请到 [**pub**](https://pub.dev/packages/fsearch) 获取 **FSearch** 最新版本号

## 🖥 git 依赖方式

```
dependencies:
  fsearch:
    git:
      url: 'git@github.com:Fliggy-Mobile/fsearch.git'
      ref: '<分支号 或 tag>'
```

> ⚠️ 注意，分支号 或 tag 请以 [**FSearch**](https://github.com/Fliggy-Mobile/fsearch) 官方项目为准。


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/fsearch)

#### [感觉还不错？请到 《FSearch》的 Github 主页投出您认可的一个 Star 🌟 吧！](https://github.com/Fliggy-Mobile/fsearch)

# 更多精彩组件

- [《FSuper》- 帮助开发者快速构建精美的复杂视图](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- 为开发者准备了诸多美妙的配置项](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- 具有优良交互和视效的精美开关元素](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- 一个适用于几乎任意单选场景的单选组件](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- 满足你对浮动元素的一切想象](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- 轻松构建下拉刷新效果](https://github.com/Fliggy-Mobile/frefresh)

- [《FDottedLine》- 辉煌的虚线效果](https://github.com/Fliggy-Mobile/fdottedline)
