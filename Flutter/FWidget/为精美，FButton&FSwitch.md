# 为精美，FButton&FSwitch

[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Android-Team)

> **FWidgets** 用心提供精致的组件，助您构建精美的应用。

嘿，开发者，快停止构建 **Old GUI**！

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fbutton/80gui.jpg)  

如果你有注意到的话，你会发现，我们需要更多的精美的 **New GUI**！

为帮助开发者能够舒适的构建出拥有精美 **GUI** 的现代应用程序，[【阿里巴巴-飞猪-Fliggy Android Team 技术团队】](https://github.com/Fliggy-Android-Team) 推出了 **FWidgets** 系列组件。

就在前几天，我们开放了 **FWidgets** 的第一个组件 [**《FSuper》**](https://github.com/Fliggy-Android-Team/fsuper)，获得了社区开发者们良好的反馈，目前该组件已经获得了来自开发者们投出的 **214** 个 **Star**。

现在，我将为你介绍 [**FButton**](https://github.com/Fliggy-Android-Team/fbutton) 和 [**FSwitch**](https://github.com/Fliggy-Android-Team/fswitch)。

[**FButton**](https://github.com/Fliggy-Android-Team/fbutton) 能够帮助你构建丰富多彩的按钮元素，而 [**FSwitch**](https://github.com/Fliggy-Android-Team/fswitch) 将为你提供拥有良好交互的开关元素。

![](https://gw.alicdn.com/tfs/TB1WlnfDlr0gK0jSZFnXXbRRXXa-1570-552.png)

# ✨ 特性

## 🧩 FButton

- 丰富的 **边角** 效果

- 精美的 **边框** 装饰

- **渐变效果** 也不在话下

- 灵活的 **图标** 支持

- 贴心的 **Loading** 模式

- 炫酷的交互 **特效**

- 更具空间感的 **阴影**



## 🧩 FSwitch

- 支持打开、关闭的提示

- 支持为 **Slider** 设置装饰

- 支持灵活的配置可用状态

- 更具空间感的 **阴影**

- 良好的开关交互体验



# 🛸 传送区

#### 🔥 [【传送门：FButton Github 主页 - 感谢您的 Star 🌟】](https://github.com/Fliggy-Android-Team/fbutton)

#### 📖 [【传送门：FButton Documentation】](https://pub.dev/documentation/fbutton/latest/fbutton/fbutton-library.html)

#### 🔥 [【传送门：FSwitch Github 主页 - 感谢您的 Star 🌟】](https://github.com/Fliggy-Android-Team/fswitch)

#### 📖 [【传送门：FSwitch Documentation】](https://pub.dev/documentation/fswitch/latest/fswitch/fswitch-library.html)


# 🧩 FButton

## 🎈 圆角 和 边框 

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fbutton/fbutton_corner_effect.gif)


```dart
// #1
FButton(
  width: 130,
  effect: true,
  text: "FButton #1",
  textColor: Colors.white,
  color: Color(0xffFF7043),
  onPressed: () {},
  clickEffect: true,
  corner: FButtonCorner.all(25),
),

// #2
FButton(
  width: 130,
  effect: true,
  text: "FButton #2",
  textColor: Colors.white,
  color: Color(0xffFFA726),
  onPressed: () {},
  clickEffect: true,
  corner: FButtonCorner(
    leftBottomCorner: 40,
    leftTopCorner: 6,
    rightTopCorner: 40,
    rightBottomCorner: 6,
  ),
),

...

```

通过 **FButton** 可以十分便捷的构建一个有 **圆角**，有 **边框** 的按钮元素。

## 🌈 渐变色

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fbutton/fbutton_gradient.jpg)

```dart

// #1
FButton(
  width: 100,
  height: 60,
  text: "#1",
  textColor: Colors.white,
  color: Color(0xffFFc900),
  gradient: LinearGradient(colors: [
    Color(0xff00B0FF),
    Color(0xffFFc900),
  ]),
  onPressed: () {},
  clickEffect: true,
  corner: FButtonCorner.all(8),
)
```

通过 `gradient` 属性，可以构建带有渐变色的 **FButton** 你可以自由构建多种类型的渐变色。

使用 **FButton**，你可以 `既要，也要，还要` 😄


## 🍭 图标

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fbutton/fbutton_image.jpg)

```dart

// #1
FButton(
  width: 88,
  height: 38,
  padding: EdgeInsets.all(0),
  text: "Back",
  textColor: Colors.white,
  color: Color(0xffffc900),
  onPressed: () {
    toast(context, "Back!");
  },
  clickEffect: true,
  corner:
  FButtonCorner(
    leftTopCorner: 25,
    leftBottomCorner: 25,),
  image: Icon(
    Icons.arrow_back_ios,
    color: Colors.white,
    size: 12,
  ),
  imageMargin: 8,
),


// #3
FButton(
  onPressed: () {},
  image: Icon(
    Icons.print,
    color: Colors.grey,
  ),
  imageMargin: 8,
  imageAlignment: ImageAlignment.top,
  text: "Print",
  textColor: Colors.grey,
  color: Colors.transparent,
),

```

**FButton** 允许配置相对于文字的图标，足够灵活，和简单。


## 🔥 特效

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fbutton/fbutton_effect.gif)

```dart

FButton(
  width: 200,
  effect: true,
  text: "Try Me!",
  textColor: Colors.white,
  color: Color(0xffffc900),
  onPressed: () {},
  clickEffect: true,
  corner: FButtonCorner.all(9),
  splashColor: Color(0xffff7043),
  highlightColor: Color(0xffE65100).withOpacity(0.20),
  hoverColor: Colors.redAccent.withOpacity(0.16),
),
```

不论是普通 Old 式按钮或是有特效的交互按钮，你只需掌握 **FButton** 就够了。

## 阴影

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fbutton/flutter_shadow.gif)

```dart

FButton(
  width: 200,
  effect: true,
  text: "Shadow",
  textColor: Colors.white,
  color: Color(0xffffc900),
  onPressed: () {},
  clickEffect: true,
  corner: FButtonCorner.all(28),
  shadowColor: Colors.black87,
  shadowBlur: _shadowBlur,
),
```

**FButton** 当然会为你提供快捷的阴影属性。


## 🔆 Loading

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fbutton/flutter_loading.gif)

```dart
// #1
FButton(
  effect: true,
  text: "Click top loading",
  textColor: Colors.white,
  color: Color(0xffffc900),
  onPressed: () {
    print("Loading...");
  },
  clickEffect: true,
  corner: FButtonCorner.all(9),
  loadingSize: 15,
  imageMargin: 6,
  loadingStrokeWidth: 2,
  clickLoading: true,
  loadingColor: Colors.white,
  loadingText: "Loading...",
  imageAlignment: ImageAlignment.top,
),

// #2
FButton(
  width: 170,
  height: 70,
  effect: true,
  text: "Click to loading",
  textColor: Colors.white,
  color: Color(0xffffc900),
  onPressed: () {
    print("Loading...");
  },
  clickEffect: true,
  corner: FButtonCorner.all(9),
  image: Icon(
    Icons.cloud_download,
    size: 18,
    color: Colors.white,
  ),
  imageMargin: 8,
  loadingSize: 15,
  loadingStrokeWidth: 2,
  clickLoading: true,
  loadingColor: Colors.white,
  loadingText: "Loading...",
  hideTextOnLoading: true,
),
```

**FButton** 集成了 **Loading** 功能，这真是..太棒了 🎉。


# 🧩 FSwitch

## 可用状态 & 阴影

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fswitch/fswitch_enable_shadow.gif)

```dart
FSwitch(
  onChanged: (bool value) {
    value_1 = value;
  },
  open: value_1,
  enable: enable_1,
  shadowColor: Colors.black.withOpacity(0.5),
  shadowBlur: 3.0,
),
```

**FSwitch** 提供了可用状态配置的基础能力，同时能够支持十分便捷的阴影配置。

## 🌝 提示

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fswitch/fswitch_tip.gif)

```dart

/// #1
FSwitch(
  width: 65.0,
  height: 35.538,
  onChanged: (v) {},
  closeChild: Text(
    "Off",
    style: TextStyle(
        color: Colors.white, fontSize: 11),
  ),
  openChild: Text(
    "On",
    style: TextStyle(
        color: Colors.white, fontSize: 11),
  ),
),

...

```

在 **FSwitch** 中，你可以分别为**打开/关闭**状态配置不同的提示样式，而且他们实现起来很简单。

## 👀 Slider 装饰

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/fswitch/fswitch_slider.gif)

```dart
FSwitch(
  width: 300,
  height: 38,
  onChanged: (bool value) {},
  sliderChild: Text(
    "😃",
    style: TextStyle(fontSize: 20),
  ),
)
```

一个有趣的 **FSwitch** 当然会为你准备有趣的配置选项。


# 😃 如何使用？

在项目 `pubspec.yaml` 文件中添加依赖：

## 🌐 pub 依赖方式

- **FButton**

```
dependencies:
  fbutton: ^<版本号>
```

> ⚠️ 注意，请到 [**pub**](https://pub.dev/packages/fbutton) 获取 **FButton** 最新版本号


- **FSwitch** 

```
dependencies:
  fswitch: ^<版本号>
```

> ⚠️ 注意，请到 [**pub**](https://pub.dev/packages/fswitch) 获取 **FSwitch** 最新版本号

## 🖥 git 依赖方式

- **FButton** 

```
dependencies:
  fbutton:
    git:
      url: 'git@github.com:Fliggy-Android-Team/fbutton.git'
      ref: '<分支号 或 tag>'
```

> ⚠️ 注意，分支号 或 tag 请以 [**FButton**](https://github.com/Fliggy-Android-Team/fbutton) 官方项目为准。


- **FSwitch**

```
dependencies:
  fswitch:
    git:
      url: 'git@github.com:Fliggy-Android-Team/fswitch.git'
      ref: '<分支号 或 tag>'
```

> ⚠️ 注意，分支号 或 tag 请以 [**FSwitch**](https://github.com/Fliggy-Android-Team/fswitch) 官方项目为准。


[![](https://raw.githubusercontent.com/chenBingX/img/master/暴漫/u=882152114,566911940&fm=26&gp=0.jpg)](https://github.com/Fliggy-Android-Team/)

#### [感觉 《FButton》 还不错？到 《FButton》的 Github 主页投出您认可的一个 Star 🌟 吧！](https://github.com/Fliggy-Android-Team/fbutton)

#### [感觉《FSwitch》还不错？到 《FSwitch》的 Github 主页投出您认可的一个 Star 🌟 吧！](https://github.com/Fliggy-Android-Team/fswitch)
