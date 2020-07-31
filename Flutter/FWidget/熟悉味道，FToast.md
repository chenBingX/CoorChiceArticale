
[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** 用心提供精致的组件，助您构建精美的应用。


![](https://gw.alicdn.com/tfs/TB1CdJ5ffzO3e4jSZFxXXaP_FXa-1466-562.png)

**Toast** 提示，是一个被作为现代化应用程序必不可少的视觉元素。

今天，**FWidget** 就为开发者们送上各位开发者**熟悉而陌生的 Toast** 组件。


# ✨ 特性

开发者，来看看这是你熟悉的 **Toast** 吗？

- 提供便捷的方式创建  **Toast** 

- 支持主信息、副信息，以及自定义它们的样式

- 支持灵活多变的图标视图

- 支持配置边角大小、背景色

- 支持队列展示

- 支持自定义 **Toast** 样式


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

# 🛸 传送区

#### 🛸 [【传送门：FToast Github 主页】](https://github.com/Fliggy-Mobile/ftoast)

#### 📖 [【传送门：FToast 文档】](https://pub.dev/documentation/ftoast/latest/ftoast/ftoast-library.html)

# 🔩 基础使用

> 熟悉的用法，但更胜以往

![](https://gw.alicdn.com/tfs/TB1YG.Mc8FR4u4jSZFPXXanzFXa-365-720.gif)

```dart
FToast.toast(
  context,

  /// 配置显示时长
  ///
  /// Configure display duration
  duration: 800,

  /// 配置 Msg
  ///
  /// set Msg
  msg: "I'm FToast: ${count++}",

  /// 配置 Msg 样式
  ///
  /// set Msg style
  msgStyle: TextStyle(color: Colors.white),
)
```

在最简单的情况下，要创建一个 **Toast** 元素，就像以往一样简单。

```dart
FToast.toast(context, msg: "Hi, FWidget");
```

当然，在 **FToast** 中，开发者可以很轻松的通过 `msgStyle` 属性配置文案样式。

甚至，**FToast** 在 **Flutter** 中，实现了队列展示的能力，这和 **Android** 中的 **Toast** 具有一致的表现。 


# 🔆 副信息

> 更多的提示

![](https://gw.alicdn.com/tfs/TB1b0.5NQT2gK0jSZFkXXcIQFXa-365-720.gif)

```dart
FToast.toast(
  context,
  msg: "This is Msg",

  /// 配置 subMsg
  ///
  /// set subMsg
  subMsg: "Welcome to use FToast. This is subMsg!",

  /// 配置 SubMsg 样式
  ///
  /// set SubMsg style
  subMsgStyle: TextStyle(color: Colors.white, fontSize: 13),
)
```

**FToast** 不同于以往的 **Toast**，**FToast** 支持了副文案的配置。鉴于此，开发者可以展示更多的提示信息。

# 🔳 图标

> 更精美

![](https://gw.alicdn.com/tfs/TB13JPNbZVl614jSZKPXXaGjpXa-365-720.gif)

```dart
FToast.toast(
  context,
  msg: "This is Msg",
  subMsg: "Welcome to use FToast. This is subMsg!",

  /// 配置图标
  ///
  /// set image
  image: Icon(
    Icons.star,
    color: Colors.yellow,
  ),

  /// 配置图标相对文本的位置
  ///
  /// set position of icon relative to text
  imageDirection: up,
)
```

**FToast** 允许开发者配置一个装饰图标，而且支持控制其相对位置。追求**精美**，**FWidget**不是随便说说。


#  🔩 自定义样式

> 无限制，绽放创意

![](https://gw.alicdn.com/tfs/TB1wlI_NUT1gK0jSZFrXXcNCXXa-365-720.gif)

```dart
FToast.toast(
  context,
  /// 自定义 Toast 样式
  /// 
  /// Custom Toast style
  toast: FSuper(
    text: "Custom Toast",
    style: TextStyle(color: Colors.grey),
    padding: EdgeInsets.all(12),
    shadowColor: Colors.yellow,
    shadowBlur: 80,
  ),
)
```

如果预设样式的 **Toast** 仍然不能满足开发者的天马行空，没关系，**FToast** 支持开发者自定义任何样式的 **Toast**。

开发者只需关心样式，其余的处理，就放心交给 **FToast** 处理。


### [想要了解更多详细内容？请访问 **FToast** 官方主页 (PS：别忘了投出一个你认可的 **Star** 哦 😘)。](https://github.com/Fliggy-Mobile/ftoast)


# 😃 如何使用？

在项目 `pubspec.yaml` 文件中添加依赖：

## 🌐 pub 依赖方式

```
dependencies:
  ftoast: ^<版本号>
```

> ⚠️ 注意，请到 [**pub**](https://pub.dev/packages/ftoast) 获取 **FToast** 最新版本号

## 🖥 git 依赖方式

```
dependencies:
  ftoast:
    git:
      url: 'git@github.com:Fliggy-Mobile/ftoast.git'
      ref: '<分支号 或 tag>'
```

> ⚠️ 注意，分支号 或 tag 请以 [**FToast**](https://github.com/Fliggy-Mobile/ftoast) 官方项目为准。


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/ftoast)

#### [感觉还不错？请到 《FToast》的 Github 主页投出您认可的一个 Star 🌟 吧！](https://github.com/Fliggy-Mobile/ftoast)

# 更多精彩组件

- [《FSuper》- 帮助开发者快速构建精美的复杂视图](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- 为开发者准备了诸多美妙的配置项](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- 具有优良交互和视效的精美开关元素](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- 一个适用于几乎任意单选场景的单选组件](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- 满足你对浮动元素的一切想象](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- 轻松构建下拉刷新效果](https://github.com/Fliggy-Mobile/frefresh)

- [《FDottedLine》- 辉煌的虚线效果](https://github.com/Fliggy-Mobile/fdottedline)

- [《FSearch》- 一应俱全的搜索框组件](https://github.com/Fliggy-Mobile/fsearch)
