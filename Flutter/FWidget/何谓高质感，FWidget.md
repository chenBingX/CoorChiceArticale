
[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)


>[ **FWidget** ](https://github.com/Fliggy-Mobile)用心提供精致的组件，助您构建精美的应用。


最近，我们对[ **FWidget** ](https://github.com/Fliggy-Mobile)进行了一次 **超前** 的升级 🚀


![](https://gw.alicdn.com/tfs/TB1hY_yPrY1gK0jSZTEXXXDQVXa-1514-724.png)

[ **FWidget** ](https://github.com/Fliggy-Mobile)是由[**【阿里巴巴-飞猪-FliggyMobile技术团队】**](https://github.com/Fliggy-Mobile) 开发的系列组件库，[ **FWidget** ](https://github.com/Fliggy-Mobile) 始终奉行 **"用心提供精致的组件，助您构建精美的应用"** 的信念，力图为开发者们提供一套精致易用的组件库，帮助开发者们更易构建出精彩绝伦的应用。

迄今为止，[ **FWidget** ](https://github.com/Fliggy-Mobile)已经为开发者们打造了一批精致的组件。目前，[ **FWidget** ](https://github.com/Fliggy-Mobile)累计向社区开放发布了其中的 **10** 余个组件，获得了来自社区开发者们投出的超过 **1130 个 Star**。开发者们给予的认可和支持，也是[ **FWidget** ](https://github.com/Fliggy-Mobile)继续用心提供更多新组件的重要动力。

最近，[ **FWidget** ](https://github.com/Fliggy-Mobile)为开发者们带来了一次前所未有的 **超前** 升级，赋能予开发者们构建更惊艳的，高质感的应用的能力。

# 🧩 那么，何谓高质感？

开发者，请定目细看，来自全新升级的 [**FButton**](https://github.com/Fliggy-Mobile/fbutton) 和 [**FSuper**](https://github.com/Fliggy-Mobile/fsuper) 所构建出来的高质感视效。

>  `#FButton`

![](https://gw.alicdn.com/tfs/TB18CN4dTM11u4jSZPxXXahcXXa-832-644.gif)




通过对 **光源、阴影、明暗变化** 的细腻处理，[ **FWidget** ](https://github.com/Fliggy-Mobile)为开发者们打造出了极具艺术感的高质感组件。

正如你所见，这些细致的光影效果处理，能够使得界面元素 **"脱离"** 界面。呈现出逼真的 **突出**，**陷入** 视效，带来前所未有的视觉冲击感。


> `#FSuper`

![](https://gw.alicdn.com/tfs/TB1F3evNpT7gK0jSZFpXXaTkpXa-720-1037.gif)

于此同时，[ **FWidget** ](https://github.com/Fliggy-Mobile)所提供的不是简单的固定光源下的光影效果。开发者可以根据喜好或是需要，自由设定光源方向，以充分呈现**代码家**们的艺术设计。

是的，[ **FWidget** ](https://github.com/Fliggy-Mobile)能够根据不同的光源方向，动态的调整适合的阴影、明暗效果，以完美呈现逼真的视效。


# 👀 这很难吗？

不!

对于开发者而言，[ **FWidget** ](https://github.com/Fliggy-Mobile)所提供的这种惊艳的表现力，实现起来保持了一贯的简洁。甚至，开发者只需要添加一个属性，就可以立即构建出一个的**高质感**界面元素。


在 [**FButton**](https://github.com/Fliggy-Mobile/fbutton) 中：

```dart

FButton(

  /// 开启 Neumorphism 支持
  ///
  /// Turn on Neumorphism support
  isSupportNeumorphism: true,

  /// 配置光源方向
  ///
  /// Configure light source direction
  lightOrientation: lightOrientation,

  /// 配置亮部阴影
  ///
  /// Configure highlight shadow
  highlightShadowColor: Colors.white,

  /// 配置暗部阴影
  ///
  /// Configure dark shadows
  shadowColor: mainShadowColor,
  strokeColor: mainBackgroundColor,
  strokeWidth: 3.0,
  width: 190,
  height: 60,
  text: "FWidget",
  style: TextStyle(
      color: mainTextTitleColor, fontSize: neumorphismSize_2_2),
  alignment: Alignment.center,
  color: mainBackgroundColor,
  ...
)

```

在 [**FSuper**](https://github.com/Fliggy-Mobile/fsuper) 中：

```dart
FSuper(

  /// 开启 Neumorphism 支持
  ///
  /// Turn on Neumorphism support
  isSupportNeumorphism: true,

  /// 配置光源方向
  ///
  /// Configure light source direction
  lightOrientation: lightOrientation,

  /// 配置暗部阴影
  ///
  /// Configure dark shadows
  shadowColor: Colors.black87,

  /// 配置亮部阴影
  ///
  /// Configure highlight shadow
  highlightShadowColor: Colors.white24,

  /// 是否呈浮起视效
  ///
  /// Whether it is floating visual effect
  float: false,
  shadowOffset: Offset(0.0, 1.0),
  width: 50,
  height: 50,
  backgroundColor: Color(0xff28292f),
  corner: FCorner.all(40),
  child1: Icon(
    Icons.star,
    color: Color(0xfffff176),
    size: 23,
  ),
),
```

仅仅是为组件配置 `isSupportNeumorphism: true` 就已经可以即刻构建出**高质感**的精美元素。

通过 `lightOrientation` 开发者更是可以自由的配置光源方向，以制造出更多意想不到的惊艳效果。

当然，[ **FWidget** ](https://github.com/Fliggy-Mobile)向开发者们提供了自由配置 **阴影明暗** 的参数，开发们可以按照喜好，随心所欲的创造。


# 🛠 如何快速使用？

在项目 `pubspec.yaml` 中，配置(升级) [**FButton**](https://github.com/Fliggy-Mobile/fbutton) 和 [**FSuper**](https://github.com/Fliggy-Mobile/fsuper) 到 `2.0.0` 版本。

```dart

/// fbutton
fbutton: ^2.0.0

/// fsuper
fsuper: ^2.0.0

```

> ⚠️注意，`2.0.0` 版本组件仅对 `1.17` 以上的 **Flutter** 版本提供支持！

> ⚠️注意，`2.0.0` 版本中，[**FButton**](https://github.com/Fliggy-Mobile/fbutton) 和 [**FSuper**](https://github.com/Fliggy-Mobile/fsuper) 部分参数有变更，具体请参与组件仓库主页，**查看最新文档**。或者可以参阅 [**FButton Changelod**](https://pub.dev/packages/fbutton/changelog) 和 [**FSuper Changelod**](https://pub.dev/packages/fsuper/changelog) 获取变更信息。


# 😘 请给予我们支持 

开发者，如果你觉得[ **FWidget** ](https://github.com/Fliggy-Mobile)能够对你有所帮助，或者你喜欢[ **FWidget** ](https://github.com/Fliggy-Mobile)的话，请到 [**FWidget**](https://github.com/Fliggy-Mobile) **组件**主页，为你喜爱的**组件**投出 **Star** 吧。

![](https://gw.alicdn.com/tfs/TB1bWhvPuL2gK0jSZFmXXc7iXXa-84-32.png)

- [《FSuper》- 帮助开发者快速构建精美的复杂视图](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- 为开发者准备了诸多美妙的配置项](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- 具有优良交互和视效的精美开关元素](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- 一个适用于几乎任意单选场景的单选组件](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- 满足你对浮动元素的一切想象](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- 轻松构建下拉刷新效果](https://github.com/Fliggy-Mobile/frefresh)

- [《FDottedLine》- 辉煌的虚线效果](https://github.com/Fliggy-Mobile/fdottedline)

- [《FSearch》- 一应俱全的搜索框组件](https://github.com/Fliggy-Mobile/fsearch)

- [《FToast》- 精致灵活的 **Flutter** 原生 **Toast** 组件](https://github.com/Fliggy-Mobile/ftoast)

- [《FLoading》- 这也许就是你想要的 **Loading** 组件](https://github.com/Fliggy-Mobile/floading)

![](https://gw.alicdn.com/tfs/TB1bWhvPuL2gK0jSZFmXXc7iXXa-84-32.png)

![](https://gw.alicdn.com/tfs/TB1w9E_Pvb2gK0jSZK9XXaEgFXa-256-256.jpg)

# 🌏 欢迎访问官网

现在，我们为 [ **FWidget** ](https://github.com/Fliggy-Mobile) 着手开发了**唯一**官方网站 [【**https://fwidget.cn**】](https://fwidget.cn)，为开发者们提供：

- 统一的 [ **FWidget** ](https://github.com/Fliggy-Mobile) 组件获取平台

- 具备可交互 **Demo** 的精致文档

- 支持一键快速引用组件

![](https://gw.alicdn.com/tfs/TB1UghrPRr0gK0jSZFnXXbRRXXa-1014-456.png)

欢迎开发者们**收藏访问**，[给我们提供更多的**精美创意** 🥰](https://github.com/Fliggy-Mobile/fwidget.github.io)。

> 值得一提的是，[ **FWidget** ](https://github.com/Fliggy-Mobile) 官网是完全使用 **Flutter** 进行开发的，其中绝大部分界面元素和功能均由 [ **FWidget** ](https://github.com/Fliggy-Mobile) 组件提供支持。




