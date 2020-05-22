# 【FFloat】Super powerful Flutter floating widget

[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** seriously provide exquisite widget to help you build exquisite application.

Hello developers, come and see, this is a new component that we carefully cook for developers 🍩, **F~~~Float**.

![](https://gw.alicdn.com/tfs/TB1DTbvGy_1gK0jSZFqXXcpaXXa-360-360.jpg)

**FFloat** is the fifth member of the **FWidget** component library developed by **[Alibaba-FliggyMobile Team](https://github.com/Fliggy-Mobile)**.

Just like our **Slogan**: **"We seriously provide exquisite widget to help you build exquisite application."**

We are trying to craft a set of beautiful and useful components to help developers build **Beautiful App** more easily.

In the past few weeks, we have opened **4** exquisite basic functional components to the community，received [**333** **Star**](https://github.com/Fliggy-Mobile) cast from developers. Thank you very much for your recognition and support 🥰。

Today, we welcome the **5** member of **FWidget**, **FFloat**.

The birth of **FFloat** comes from the **floating element** problem that has been tricky in our daily development.
 
It is almost difficult for us to calculate that before **FFloat** appeared，when we need to develop the following interactive elements，our anxiety can make us drink a few cans [Coca-Cola](https://www.coca-cola.com.cn/)🥤

![](https://gw.alicdn.com/tfs/TB1YErvGvb2gK0jSZK9XXaEgFXa-331-114.gif)

Of course, for this example 🌰, it is a very simple **floating element** example.

The emergence of **FFloat** will allow all problems to be solved.
 
Now, the time will be divided into two parts, **before FFloat appears** and **after FFloat appears**.

![](https://gw.alicdn.com/tfs/TB1za6OGET1gK0jSZFrXXcNCXXa-720-403.png)
 
 
**FFloat** is a natural player in reducing complexity to complexity.
 
This is due to the large amount of cumbersome logic processing and calculations in **FFloat**, which allows developers to use the most natural and simple way to **restore creation** when using it.


![](https://gw.alicdn.com/tfs/TB1j5H_GuH2gK0jSZFEXXcqMpXa-720-922.png)


# ✨ Features

These are some of the features **FFloat** brings to developers:

- Support both **anchor mode** and **absolute mode** dual positioning mode

- Flexible and simple enough **Position control**

- Support configuration **Background mask**

- Comes with **Exquisite dynamic effect**, which can be adjusted intelligently according to the position

- Rich **decorative triangle** configuration

- The outer container supports **rounded corners, borders, gradients, shadows** and many other configurations

- Convenient **show / hide control**


![](https://gw.alicdn.com/tfs/TB1BBv0FUY1gK0jSZFCXXcwqXXa-225-225.jpg)


# 🛸 Portal

#### 🛸 [【Portal: FFloat Github homepage-Thanks for your Star 🌟】](https://github.com/Fliggy-Mobile/ffloat)

#### 📖 [【Portal: FFloat documentation】](https://pub.dev/documentation/ffloat/latest/ffloat/ffloat-library.html)

# 📍 Position control based on anchor elements

> Simple enough and effective enough

![](https://gw.alicdn.com/tfs/TB1GwD9FhD1gK0jSZFyXXciOVXa-464-140.gif)

```dart
FFloat(
  /// Floating element content
  (setter) => createContent(),
  alignment: _alignment,
  margin: _margin

  /// Anchor element
  anchor: buildAnchor(),
)
```

**FFloat** the most incredible thing is that developers only need to provide an anchor through the `anchor` parameter, **FFloat** will know how to display **floating elements** in the right place. 

And **FFloat** will not have any adverse effect on the original anchor element and the visible view tree structure, which is amazing!

For the content to be displayed, **FFloat** is built by the **FloatBuilder** function. Developers only need to return the floating content that needs to be displayed in this function, and the rest, **FFloat** will handle it.

In addition, in the **FloatBuilder** function, developers can get the refresh function **StateSetter** through the function parameters, which can limit the refresh range to the floating content area instead of refreshing the entire page area. This is essential for building a beautiful silky application.

```dart
FFloat(
  (setter){
    return FButton(text: _text, 
      onClick: (){

        /// Update floating content
        setter((){
          updateText();
        })            
      });
  },
  ...
)
``` 

If you are not satisfied with the position of the floating element, **FFloat** provides `alignment` and` margin` parameters, enabling developers to adjust the position of the floating element in an incredibly simple way until they are satisfied.


`alignment` provides a very comprehensive relative position option: 

|Type|Explanation|
|:--|:--|
|topLeft|In the anchor element [above], and [left edge] is aligned with the anchor element|
|topCenter|In the anchor element [above], and horizontally centered|
|topRight|In the anchor element [above], and the [right edge] is aligned with the anchor element|
|bottomLeft|In the anchor element [below], and the [left edge] is aligned with the anchor element|
|bottomCenter|In the anchor element [below], and horizontally centered|
|bottomRight|In the anchor element [below], and the [right edge] is aligned with the anchor element|
|leftTop|In the anchor element [left], and the [upper edge] is aligned with the anchor element|
|leftCenter|In the anchor element [left], and vertically centered|
|leftBottom|In the anchor element [left], and the [bottom edge] is aligned with the anchor element|
|rightTop|In the anchor element [right side], and [upper edge] is aligned with the anchor element|
|rightCenter|In the anchor element [right side], and vertically centered|
|rightBottom|In the anchor element [right side], and the [bottom edge] is aligned with the anchor element|

On this basis, the `margin` parameter allows developers to further fine-tune the offset of floating elements based on the current relative positional relationship.

In the past, to achieve these effects, it took developers a lot of energy to deal with complex positional relationship logic and write different algorithms to determine the final position of floating elements.

And now, everything is so simple 😎.

# 📌 Position control based on absolute coordinates

> One more mode, one more possibility

![](https://gw.alicdn.com/tfs/TB1gnz8GrH1gK0jSZFwXXc7aXXa-858-508.gif)

```dart
GestureDetector(
  onPanDown: (details) {
    FFloat(
      (setter) => createContent(),
      autoDismissDuration: Duration(milliseconds: 2000),
      alignment: _alignment,
      canTouchOutside: false,

      /// Configure floating element position by absolute coordinates
      location: Offset(details.globalPosition.dx, details.globalPosition.dy),
    ).show(context); /// Show
  },
  child: FSuper(...),
)
```

In some cases, our floating element does not need to appear based on an anchor point, but hopes that it appears in a certain position.

If the developer knows such a position, he can use the `location` parameter to set the position of the floating element.

At this time, the developer does not need to put **FFloat** in the view tree to wrap any component elements. This means that developers can create a floating element in any callback or function anytime, anywhere.

Through `FFloat.show (context)` and `FFloat.dismiss ()`, developers can easily control floating elements at any time **show / hide**

All other configurations of **FFloat** are still valid.

#  💫 Background mask and animation

> Will be rich in effect, will be simplified

![](https://gw.alicdn.com/tfs/TB179P9GuH2gK0jSZFEXXcqMpXa-720-135.gif)

```dart

# Background mask demo
FFloat(
  (_) => buildSearchWidget(),
  color: Colors.black.withOpacity(0.95),

  /// Configure background mask color
  backgroundColor: Colors.black26,
  corner: FFloatCorner.all(20),
  margin: EdgeInsets.only(bottom: 10, left: 10),
  anchor: buildAnchor(),
  alignment: FFloatAlignment.topRight, 
 
  /// Configure whether click non-floating element area is hidden
  canTouchOutside: false,
)
```

In **FFloat**, you can achieve the background mask effect when the floating element appears only by using the `backgroundColor` parameter.

By default, when a floating element appears, it will intercept all events, and the developer can turn off this mode through the `canTouchOutside` parameter.

```
# Dynamic configuration
FFloat(
  (_) => FSuper(text: "Surprise😃 !"),
  anchor: buildAnchor(),

  /// When the parameter is not null, FFloat will automatically disappear after the specified duration
  autoDismissDuration: Duration(milliseconds: 2000),

  /// Configure animation duration
  animDuration: Duration(milliseconds: 800),
),
```

**FFloat** comes with beautiful interactive motion effects. According to the different positions of floating elements, the motion effects can be automatically adjusted to present the most elegant visual effects.

Through the `animDuration` parameter, developers can define the duration of animation effects. Of course, if you don't need any animation, just pass in `null`.

In many scenes, we often want a floating element to disappear automatically after a period of time.

Generally, to achieve such an effect, developers need to create independent timers and state machines. Under special circumstances, they also need to perform additional state logic verification to ensure that this effect is seamless. There is too much logic to consider.

**FFloat** allows developers to achieve this function by simply passing an argument of `autoDismissDuration`.

# 🔺 Decorative triangle

> Save effort, worry, save memory

![](https://gw.alicdn.com/tfs/TB1L3JoFEH1gK0jSZSyXXXtlpXa-753-220.gif)

```dart
FFloat(
  (setter) => buildContent(),
  anchor: buildAnchor(),

  /// Configure the relative position of the decorative triangle
  triangleAlignment: TriangleAlignment.start,

  /// Configure the relative offset of the decorative triangle
  triangleOffset: Offset(10, 10),

  /// Configure the width of the decorative triangle
  triangleWidth: 20,

  /// Configure the height of the decorative triangle
  triangleHeight: 15,
)
```

Before the advent of **FFloat**, the implementation of **Tooltips** style floating elements was often too tedious.

But what really disturbs developers is a **small triangle** on floating elements 😲. I believe that experienced developers must know the sadness behind this.

Since the platform system does not provide support for triangles or components with triangles, developers have to choose between using **Canvas** to draw or using pictures instead.

The former is flexible and efficient, but the implementation is very cumbersome. This is not only as simple as **draw** triangles on **Canvas**, but also needs to consider the problem of fusion with real floating elements.

The latter is convenient to implement, but for triangles of different colors, different positions, and different sizes, developers need to configure multiple sets of pictures for them, and the memory will gradually accumulate over time. But considering the development cost and efficiency, developers often choose this implementation.

**FFloat** completely solves these problems, with high efficiency and flexibility, even further than the developers expect.

In the above **Code** demo, it shows the common configuration of **FFloat** in the small triangle, including simple relative positional relationship, size, and fine-tunable position offset.

Now, developers can clean up all similar image resources in the application 👏.

In addition to solving past problems, **FFloat** can further adjust its own triangle to a reasonable position based on the relative positional relationship between floating elements and anchor elements. This process is unaware for developers.

```dart
FFloat(
  (setter) => buildContent(),

  /// Configure whether to hide the decorative triangle
  hideTriangle: true,
  anchor: buildAnchor(),
),
```

By default, FFloat will display decorative triangles. If you do n’t need it, the developer only needs to configure `hideTriangle: true` to hide it.

# 🔆 Corner & Border

> Although simple, but not simple

![](https://gw.alicdn.com/tfs/TB1xbhyFqL7gK0jSZFBXXXZZpXa-670-241.gif)

```dart

FFloat(
  (setter) => buildContent(),
  anchor: buildAnchor(),
  alignment: FFloatAlignment.bottomLeft,
  hideTriangle: true,

  /// Plus Corners
  corner: FFloatCorner.all(6),

  /// Add border
  strokeColor: mainShadowColor,
  strokeWidth: 1.0,
)
```

In the above example 🌰, we can clearly see that a beautiful rounded floating layer with a border is so simple to build.

**FFloat** provides the **FWidget** series of components in the same vein, a simple way to set the rounded corners, and only through a simple `corner` parameter can flexibly configure the rounded corner effect.

The `cornerStyle` property that comes with` corner` allows developers to switch the style of rounded corners (rounded corners or beveled corners) at any time.

For the old fans of **FWidget**, I believe that we already know that we only need to use two simple properties such as `strokeWidth` and` strokeColor` to configure the border effect for the component.

Our original intention has always been to help developers build beautiful applications more simply and efficiently.

# 🌔 Gradient & Shadow

> To be concise, to be brilliant

![](https://gw.alicdn.com/tfs/TB11GHPFrj1gK0jSZFuXXcrHpXa-414-179.gif)

```dart

FFloat(
  (setter) => buildContent(),
  anchor: buildAnchor(),
  hideTriangle: true,
  alignment: FFloatAlignment.bottomCenter,

  /// Configure gradients
  gradient: SweepGradient(
    colors: [
      Color(0xffE271C0),
      Color(0xffC671EB),
      Color(0xff7673F3),
      Color(0xff8BEBEF),
      Color(0xff93FCA8),
      Color(0xff94FC9D),
      Color(0xffEDF980),
      Color(0xffF0C479),
      Color(0xffE07E77),
    ],
  ),
  
  /// Add shadow
  shadowColor: Colors.black38,
  shadowBlur: 3,
  shadowOffset: Offset(3, 2),
)
```

Yes, **FFloat** still has support for gradients after combining many capabilities.

Only through a simple `gradient` attribute, developers can get amazing gradient effects.

In addition, as a modern component, **FFloat** will of course support shadows.

Developers only need to configure the `shadowColor` parameter to get a basic shadow effect.

If you want to further adjust the shadow, you can use the `shadowBlur` and` shadowOffset` parameters to complete.

# 👏 More surprise


![](https://gw.alicdn.com/tfs/TB1NGfIFAL0gK0jSZFtXXXQCXXa-460-500.gif)


The problem of floating elements, let **FFloat** solve it, developers only need to care about how to make the application more beautiful.


### [Want to know more details? Please visit the **FFloat** official homepage (PS: Do n’t forget to vote for a **Star** you approved 😘)](https://github.com/Fliggy-Mobile/ffloat)


# 😃 How to use？

Add dependency in project pubspec.yaml file:

## 🌐 pub dependency

```
dependencies:
  ffloat: ^<version number>
```

> ⚠️ Attention，please go to [**pub**] (https://pub.dev/packages/ffloat) to get the latest version number of **FFloat**

## 🖥 git dependency

```
dependencies:
  ffloat:
    git:
      url: 'git@github.com:Fliggy-Mobile/ffloat.git'
      ref: '<Branch number or tag number>'
```

> ⚠️ Attention，please refer to [**FFloat**](https://github.com/Fliggy-Mobile/ffloat) official project for branch number or tag.


[![](https://gw.alicdn.com/tfs/TB1NPD0FHH1gK0jSZFwXXc7aXXa-294-220.jpg)](https://github.com/Fliggy-Mobile/ffloat)

#### [Like it? Please go to the Github homepage of 《FFloat》 to vote for it 🌟!](https://github.com/Fliggy-Mobile/ffloat)

# Past Widget

- [《FSuper》- Help developers quickly build beautifully complex views](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- Many wonderful configuration items are prepared for developers](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- Exquisite switching elements with excellent interaction and visual effects](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- A radio component suitable for almost any radio scenario](https://github.com/Fliggy-Mobile/fradio)


