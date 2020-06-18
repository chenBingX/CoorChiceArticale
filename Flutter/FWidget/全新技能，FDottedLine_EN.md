

[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** seriously provide exquisite widget to help you build exquisite application. 


For a **Flutter** developer, it is not an easy task to achieve the dotted line effect in the picture below 🤔.

![](https://gw.alicdn.com/tfs/TB1eFZCI7Y2gK0jSZFgXXc5OFXa-360-340.png)

Because the official does not prepare **Widget** about the dotted line for developers to eat.

Many times, developers have to manually draw or use some special methods to achieve, but the process is always not so easy.

Now, **FDottedLine** is about to change the current situation. Provide developers with a sufficiently elegant way to complete the construction of the dashed view.

**FDottedLine** is regarded as the main component of the **FWidget** series developed and maintained by **[【Alibaba-FliggyMobile Team】](https://github.com/Fliggy-Mobile)** The seventh component is dedicated to community developers. Hope to help developers create beautiful applications more easily.
 
In the past few weeks, the **FWidget** component that has been opened has received continuous attention from community developers, and developers have currently invested nearly [**800 Star**] (https://github.com/Fliggy-Mobile).

Today, we bring powerful components to developers that support the flexible construction of dotted line effects-**FDottedLine**.

![](https://gw.alicdn.com/tfs/TB182cqI.z1gK0jSZLeXXb9kVXa-1380-1282.png)

# ✨ Features

See what changes **FDottedLine** brings:


- Supports dotted lines in both horizontal and vertical directions

- Support to create dashed shapes

- Provide super easy way to add dotted border to **Widget**

- Support creating flexible dotted corner effects


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

 

# 🛸 Portal

#### 🛸 [【Portal: FDottedLine Github homepage】](https://github.com/Fliggy-Mobile/fdottedline)

#### 📖 [【Portal: FRefresh documentation】](https://pub.dev/documentation/fdottedline/latest/fdottedline/fdottedline-library.html)


# 🔩 Horizontal dotted line

> Consistent simplicity

![](https://gw.alicdn.com/tfs/TB1ClEbI5_1gK0jSZFqXXcpaXXa-360-340.png)


```dart
FDottedLine(
  color: color,
  width: 160.0,
)
```

It is very simple to create a horizontal dotted line through **FDottedLine** 😱.

Stop the fear of the dotted view!

# ⛓ Vertical dotted line

> It's also simple to change the effect

![](https://gw.alicdn.com/tfs/TB1_eVgXDM11u4jSZPxXXahcXXa-360-319.png)

```dart
FDottedLine(
  color: color,
  height: 160.0,
)
```

It is as simple as **horizontal dotted line**.

At this point, perhaps the careful developer will find out. The only difference between constructing **horizontal dotted line** and **vertical dotted line** is: 

> When the **horizontal dotted line** needs to be constructed, only the `width` parameter needs to be assigned; and when the **vertical dotted line** needs to be constructed, only the `height` parameter needs to be assigned.

God, this is too convenient 😱.

# 🔹 Dotted graphics

> Always, not only that

![](https://gw.alicdn.com/tfs/TB1q5Y_IVY7gK0jSZKzXXaikpXa-638-360.png)

```dart
FDottedLine(
  color: Colors.lightBlue[600],
  height: 100.0,
  width: 50,
  strokeWidth: 2.0,
  dottedLength: 10.0,
  space: 2.0,
)
```

If you assign both width and height, developers will be surprised to find that a dotted line appears.

Yes, **FDottedLine** can not only help developers build a one-dimensional dashed line view very concisely, but also construct two-dimensional dashed line graphics in the same concise way.


# 🌏 Corner

> Change a little bit more

![](https://gw.alicdn.com/tfs/TB17TwjI8r0gK0jSZFnXXbRRXXa-629-360.png)

```dart
FDottedLine(
  color: Colors.lightBlue[600],
  height: 70.0,
  width: 70.0,
  space: 2.0,
  
  /// Set corner
  corner: FDottedLineCorner.all(50),
)
```

With **FDottedLine**, developers can even create corner effects of dotted graphics. For example: dotted rounded rectangle, dotted round..

And the construction is always simple, just configure a `corner` parameter, you can complete many amazing effects.


# 🧩 Child

> Make things simple

![](https://gw.alicdn.com/tfs/TB1aSZaI1H2gK0jSZFEXXcqMpXa-360-324.png)

```dart
FDottedLine(
  color: color,
  corner: FDottedLineCorner.all(6.0),
  
  /// add widget
  child: Container(
    color: Colors.blue[100],
    width: 130,
    height: 70,
    alignment: Alignment.center,
    child: Text("0873"),
  ),
)
```

I believe that developers all feel deeply. In the past, it was very difficult to add a dotted border to a **Widget**.

But **FDottedLine** will make things easier than ever. Developers only need to use their **Widget** as a child of **FDottedLine** to complete all the work.

Behind **FDottedLine**, **FDottedLine** will help developers calculate the size of **Widget** to further determine how to create the correct dashed border.

This work has opened up a new path for developers.

# 💡 Explore more fun

See what **FDottedLine** can do!

![](https://gw.alicdn.com/tfs/TB17_wjI8r0gK0jSZFnXXbRRXXa-480-511.png)

When there is such a simple way to create a dotted line, developers can freely build more wonderful views.

![](https://gw.alicdn.com/tfs/TB1geStkIKfxu4jSZPfXXb3dXXa-720-227.gif)

Next for more applications of **FDottedLine**, look forward to the exploration of developers 🔆.


### [Want to know more details? Please visit the **FDottedLine** official homepage (PS: Do n’t forget to vote for a **Star** you approved 😘)](https://github.com/Fliggy-Mobile/fdottedline)


# 😃 How to use？

Add dependency in project pubspec.yaml file:

## 🌐 pub dependency

```
dependencies:
  fdottedline: ^<version number>
```

> ⚠️ Attention，please go to [**pub**] (https://pub.dev/packages/fdottedline) to get the latest version number of **FDottedLine**

## 🖥 git dependency

```
dependencies:
  fdottedline:
    git:
      url: 'git@github.com:Fliggy-Mobile/fdottedline.git'
      ref: '<Branch number or tag number>'
```

> ⚠️ Attention，please refer to [**FDottedLine**](https://github.com/Fliggy-Mobile/fdottedline) official project for branch number or tag.


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/fdottedline)

#### [Like it? Please go to the Github homepage of 《FDottedLine》 to vote for it 🌟!](https://github.com/Fliggy-Mobile/fdottedline)

# Past Widget

- [《FSuper》- Help developers quickly build beautifully complex views](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- Many wonderful configuration items are prepared for developers](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- Exquisite switching elements with excellent interaction and visual effects](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- A radio component suitable for almost any radio scenario](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- Satisfy all your imagination of floating elements](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- Easily build pull-down refresh effects](https://github.com/Fliggy-Mobile/frefresh)


