
[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** seriously provide exquisite widget to help you build exquisite application.


![](https://gw.alicdn.com/tfs/TB1CdJ5ffzO3e4jSZFxXXaP_FXa-1466-562.png)

**Toast** tip is an essential visual element that is regarded as a modern application.

Today, **FWidget** will send developers the familiar and unfamiliar Toast** components for developers.

# ✨ Features

Developers, come and see if this is the **Toast** you are familiar with?

- Provide a convenient way to create **Toast**

- Support primary information, secondary information, and customize their style

- Support flexible icon view

- Support to configure corner size and background color

- Support queue display

- Support custom **Toast** style



![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

# 🛸 Portal

#### 🛸 [【Portal: FToast Github homepage-Thanks for your Star 🌟】](https://github.com/Fliggy-Mobile/ftoast)

#### 📖 [【Portal: FToast documentation】](https://pub.dev/documentation/ftoast/latest/ftoast/ftoast-library.html)

# 🔩 Base

> Familiar usage, but better than ever

![](https://gw.alicdn.com/tfs/TB1YG.Mc8FR4u4jSZFPXXanzFXa-365-720.gif)

```dart
FToast.toast(
  context,

  /// Configure display duration
  duration: 800,

  /// set Msg
  msg: "I'm FToast: ${count++}",

  /// set Msg style
  msgStyle: TextStyle(color: Colors.white),
)
```

In the simplest case, creating a **Toast** element is as simple as ever.

```dart
FToast.toast(context, msg: "Hi, FWidget");
```

Of course, in **FToast**, developers can easily configure the copywriting style through the `msgStyle` property.

Even, **FToast** implements the ability of queue display in **Flutter**, which has the same performance as **Toast** in **Android**.


# 🔆 SubMsg

> More tips

![](https://gw.alicdn.com/tfs/TB1b0.5NQT2gK0jSZFkXXcIQFXa-365-720.gif)

```dart
FToast.toast(
  context,
  msg: "This is Msg",

  /// set subMsg
  subMsg: "Welcome to use FToast. This is subMsg!",

  /// set SubMsg style
  subMsgStyle: TextStyle(color: Colors.white, fontSize: 13),
)
```

**FToast** is different from the previous **Toast**, **FToast** supports the configuration of sub-text. In view of this, developers can display more prompt information.

# 🔳 Icon

> More beauty

![](https://gw.alicdn.com/tfs/TB13JPNbZVl614jSZKPXXaGjpXa-365-720.gif)

```dart
FToast.toast(
  context,
  msg: "This is Msg",
  subMsg: "Welcome to use FToast. This is subMsg!",

  /// set image
  image: Icon(
    Icons.star,
    color: Colors.yellow,
  ),

  /// set position of icon relative to text
  imageDirection: up,
)
```

**FToast** allows developers to configure a decorative icon, and supports controlling its relative position. Pursuing **exquisiteness**, **FWidget** is not just talking about it.


#  🔩 Custom style

> Unlimited, creative

![](https://gw.alicdn.com/tfs/TB1wlI_NUT1gK0jSZFrXXcNCXXa-365-720.gif)

```dart
FToast.toast(
  context,
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

If the default style of **Toast** still does not satisfy the developer's wishful thinking, it does not matter, **FToast** supports developers to customize any style of **Toast**.

Developers only need to care about the style and leave the rest to be handled by **FToast**.


### [Want to know more details? Please visit the **FToast** official homepage (PS: Do n’t forget to vote for a **Star** you approved 😘)](https://github.com/Fliggy-Mobile/ftoast)


# 😃 How to use？

Add dependencies in the project `pubspec.yaml` file:

## 🌐 pub dependency

```
dependencies:
  ftoast: ^<version number>
```

> ⚠️ Attention，please go to [**pub**] (https://pub.dev/packages/ftoast) to get the latest version number of **FToast**

## 🖥 Git dependency

```
dependencies:
  ftoast:
    git:
      url: 'git@github.com:Fliggy-Mobile/ftoast.git'
      ref: '<Branch number or tag number>'
```

> ⚠️ Attention，please refer to [**FToast**] (https://github.com/Fliggy-Mobile/ftoast) official project for branch number or tag.


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/ftoast)

#### [Like it? Please go to the Github homepage of 《FToast》 to vote for it 🌟!](https://github.com/Fliggy-Mobile/ftoast)

# Past Widget

- [《FSuper》- Help developers quickly build beautifully complex views](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- Many wonderful configuration items are prepared for developers](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- Exquisite switching elements with excellent interaction and visual effects](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- A radio widget suitable for almost any radio scenario](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- Satisfy all your imagination of floating elements](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- Easily build pull-down refresh effects](https://github.com/Fliggy-Mobile/frefresh)

- [《FDottedLine》- Brilliant dotted line effect](https://github.com/Fliggy-Mobile/fdottedline)

- [《FSearch》- A comprehensive search box widget](https://github.com/Fliggy-Mobile/fsearch)

- [《FLoading》- This may be the **Loading** widget you want](https://github.com/Fliggy-Mobile/floading)
 
