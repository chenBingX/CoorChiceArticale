
[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** seriously provide exquisite widget to help you build exquisite application.


Speaking of **home travel, bi~bi~bi~bi~** must-have items, I believe that developers can always pop out so few favorite items from their knowledge bases accumulated in their minds 🧠 .
 

![](https://gw.alicdn.com/tfs/TB1NoTEKa61gK0jSZFlXXXDKFXa-720-536.png)

However, just today, we tried to add a brand new **must-have item** to the developer’s knowledge base ---- **F~~Search** 🎊.

**FSearch** has a sacred mission. It tries to help developers build their own beautiful **search bar** 🔍 in the most comfortable way.
 
Today in the information 💥, we always want to add a search bar to our application, the users of the application need it, so the developers also need it.

So now, please adjust the spotlight angle and put the BGM, please **F~~Search** 👏👏👏.


||||
|:--:|:--:|:--:|
|![](https://gw.alicdn.com/tfs/TB1QSOkJYr1gK0jSZR0XXbP8XXa-320-469.gif)|![](https://gw.alicdn.com/tfs/TB1fWytJYj1gK0jSZFuXXcrHpXa-320-469.gif)|![](https://gw.alicdn.com/tfs/TB1dh1sJ7T2gK0jSZFkXXcIQFXa-320-469.gif)|
|![](https://gw.alicdn.com/tfs/TB1XrOdJ1L2gK0jSZPhXXahvXXa-320-464.gif)|![](https://gw.alicdn.com/tfs/TB1x4CmJVP7gK0jSZFjXXc5aXXa-320-464.gif)|![](https://gw.alicdn.com/tfs/TB1iFAMXI4z2K4jSZKPXXXAYpXa-360-466.gif)|
|![](https://gw.alicdn.com/tfs/TB1Pt1oJYY1gK0jSZTEXXXDQVXa-360-298.gif)|![](https://gw.alicdn.com/tfs/TB19oLDGKT2gK0jSZFvXXXnFXXa-360-212.gif)|![](https://gw.alicdn.com/tfs/TB19oLDGKT2gK0jSZFvXXXnFXXa-360-212.gif)|


# ✨ Features

Allow me to introduce the amazing features of **FSearch** to all developers.

- Support beautiful border effect

- Provide rich corner configuration

- Support stunning gradient effects

- Provides easy to use shadow capabilities

- Support any number of prefix and suffix action buttons

- Provide colorful, flexible and powerful Hint effects

- Controllers that are easier to use for developers


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

 

# 🛸 Portal

#### 🛸 [【Portal: FSearch Github homepage-Thanks for your Star 🌟】](https://github.com/Fliggy-Mobile/fsearch)

#### 📖 [【Portal: FSearch documentation】](https://pub.dev/documentation/fsearch/latest/fsearch/fsearch-library.html)

# 🔩 Base Demo

> Get rid of the shackles and start from scratch

![](https://gw.alicdn.com/tfs/TB1QSOkJYr1gK0jSZR0XXbP8XXa-320-469.gif)

```dart
FSearch(

  /// Set height
  height: 30.0,
  
  /// Set background color
  backgroundColor: color,

  /// Set input text style
  style: style,

  /// Fired when you click on the keyboard to search
  onSearch: (value) {
    /// do something
  },

  prefixes: [buildAction()],
)
```

Using **FSearch** to build a search bar is very relaxing.

Through some simple parameters, developers can easily change the size, color, and font of the search bar.

When the user clicks on the keyboard to search for **Action**, onSearch will be triggered, allowing the developer to perform some search operations here.

# 🌖 Prefixes & Suffixes

> Highly flexible and highly controllable


![](https://gw.alicdn.com/tfs/TB1fWytJYj1gK0jSZFuXXcrHpXa-320-469.gif)

```dart
FSearch(
  height: 30.0,
  backgroundColor: mainBackgroundColor,
  style: style,

  /// prefix widget
  prefixes: [ buildAction() ],

  /// suffix widget
  suffixes: [
    buildAction_1(),
    buildAction_2(),
    buildAction_3(),
  ],
  onSearch: _onSearch,
)
```

In FSearch, developers can configure any number of prefix or suffix action buttons for the search bar through the `prefixes` and `suffixes` parameters.

Such a high degree of flexibility and control may be unprecedented.

# 🌈 Gradient

> Gradient is artist

![](https://gw.alicdn.com/tfs/TB1dh1sJ7T2gK0jSZFkXXcIQFXa-320-469.gif)

```dart
FSearch(
  height: 30.0,
  backgroundColor: mainBackgroundColor,
  style: style,

  /// Set gradient
  gradient: _gradient,
  prefixes: [ buildAction() ],
)
```


**FSearch** can support developers to create a beautiful gradient search bar.

Only need to configure through the `gradient` parameter.

# 🍄 Corner & Stroke & Shadow

> Change as you want

![](https://gw.alicdn.com/tfs/TB1XrOdJ1L2gK0jSZPhXXahvXXa-320-464.gif)


```dart

/// #1
FSearch(
  height: 30.0,
  backgroundColor: color,
  style: style,

  /// Corner
  corner: FSearchCorner(
      leftTopCorner: 15.0,
      leftBottomCorner: 15.0,
      rightBottomCorner: 15.0),

  /// border width
  strokeWidth: 1.0,

  /// border color
  strokeColor: mainTextTitleColor,

  /// shadow
  shadowColor: Colors.black38,
  shadowBlur: 5.0,
  shadowOffset: Offset(2.0, 2.0),
  prefixes: [buildAction()],
)
```

The **Border** and **Shadow** effects of **FSearch** are the same as those of other **FWidget** members, and are simple and easy to use.

Through the `corner` parameter, developers can use **FSearchCorner** to freely control the table corner size of **FSearch**.

```dart
/// #2
FSearch(
  height: 30.0,
  backgroundColor: color,
  style: style,

  /// Corner
  corner: FSearchCorner.all(6.0),

  /// Corner style
  cornerStyle: FSearchCornerStyle.bevel,
  prefixes: [buildAction()],
)
```

If combined with `cornerStyle`, more complex and exquisite effects can be achieved.

# 📍 Cursor

> Controllable details

![](https://gw.alicdn.com/tfs/TB1x4CmJVP7gK0jSZFjXXc5aXXa-320-464.gif)

```dart
FSearch(
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

**FSearch** supports modifying the cursor in the input box of the search bar. You can change it to whatever you want.

# 🗂 Hint

> One step, you can dazzle!

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

  /// Turn on hint exchange animation
  hintSwitchEnable: true,

  /// Configure hint exchange animation type
  hintSwitchType: FSearchAnimationType.Fade,
)
```

**FSearch** provides developers with a very powerful **Hint** effect.

Developers can easily set multiple **Hint** for **FSearch**, and can configure multiple **Hint** exchange animations by configuring `hintSwitchEnable: true`.

When the user starts typing, Hint will be automatically hidden, and the swap animation will also be stopped.

When the content of the search input box becomes empty again, Hint will appear again, and the exchange animation will start playing.

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


**FSearch** has prepared rich exchange animations for developers. By default, **FSearch** will use the most common scroll swap animation, which is `FSearchAnimationType.Scroll`.
 
Of course, developers can configure their favorite animation types through the `hintSwitchType` parameter.

> 💡 Note that when `hints.length == 1`, the Hint swap animation will not be played. Only an ordinary Hint will be displayed at this time.


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

  /// Configure hint exchange animation type
  hintSwitchType: FSearchAnimationType.Scale,

  /// Whether to stop exchanging animation when focus is obtained
  stopHintSwitchOnFocus: false,
)
```

By default, when **FSearch** gains input focus, **FSearch** will automatically pause **Hint** to swap animations; when it gets focus again, it will resume automatically.

By configuring `stopHintSwitchOnFocus: false`, you can continue to play **Hint** swap animation until the user starts typing when **FSearch** has the focus.


# 💻 Controller

> Your program, you have the final say

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

/// Get the input box content
String input = controller.text;

/// Clear the contents of the input box
controller.text = null;

/// Get the current hint, if any
String hint = controller.hint;

/// Remove focus
controller.clearFocus();


/// Request focus
controller.requestFocus();
```

 **FSearch** provides developers with simple, easy-to-use, certain controllers, through which developers can modify or obtain the contents of the search bar at any location.

### [Want to know more details? Please visit the **FSearch** official homepage (PS: Do n’t forget to vote for a **Star** you approved 😘)](https://github.com/Fliggy-Mobile/fsearch)

# 😃 How to use？

Add dependency in project pubspec.yaml file:

## 🌐 pub dependency

```
dependencies:
  fsearch: ^<version number>
```

> ⚠️ Attention，please go to [**pub**] (https://pub.dev/packages/fsearch) to get the latest version number of **FSearch**

## 🖥 git dependency

```
dependencies:
  fsearch:
    git:
      url: 'git@github.com:Fliggy-Mobile/fsearch.git'
      ref: '<Branch number or tag number>'
```

> ⚠️ Attention，please refer to [**FSearch**](https://github.com/Fliggy-Mobile/fsearch) official project for branch number or tag.


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/fsearch)

#### [Like it? Please go to the Github homepage of 《FSearch》 to vote for it 🌟!](https://github.com/Fliggy-Mobile/fsearch)

# Past Widget

- [《FSuper》- Help developers quickly build beautifully complex views](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- Many wonderful configuration items are prepared for developers](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- Exquisite switching elements with excellent interaction and visual effects](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- A radio component suitable for almost any radio scenario](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- Satisfy all your imagination of floating elements](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- Easily build pull-down refresh effects](https://github.com/Fliggy-Mobile/frefresh)

- [《FDottedLine》- Brilliant dotted line effect](https://github.com/Fliggy-Mobile/fdottedline)
