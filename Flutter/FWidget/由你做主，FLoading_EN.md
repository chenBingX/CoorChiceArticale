
[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** seriously provide exquisite widget to help you build exquisite application.


![](https://gw.alicdn.com/tfs/TB1XHHyf8Bh1e4jSZFhXXcC9VXa-1462-552.png)


Today, the **exquisite** component that **FWidget** brings to developers is ~~ **FLoading**.

In actual application scenarios, building **Loading** elements seems to be a skill that every developer must master. Now, **FLoading** is here to help developers further refine their skills.

**FLoading** provides a minimalistic way to build **Loading** elements, and provides developers with many highly customizable configurations, allowing developers to easily create beautiful **Loading** .


# ✨ Features

So, let's take a look at what **FLoading** brings.

- Provide the most convenient way to control **Showing/Hiding** of **Loading**

- Support to modify background color

- Support timing auto hide

- Support local and global custom styles


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

# 🛸 Portal

#### 🛸 [【Portal: FLoading Github homepage-Thanks for your Star 🌟】](https://github.com/Fliggy-Mobile/floading)

#### 📖 [【Portal: FLoading documentation】](https://pub.dev/documentation/floading/latest/floading/floading-library.html)


# 🌈 Show/hide

> Simple and effective, all in one go

![](https://gw.alicdn.com/tfs/TB1PN_dbwgP7K4jSZFqXXamhVXa-720-449.gif)

```dart

/// show 
FLoading.show(context);


/// hide 
FLoading.hide();
```

It's too simple to implement **Show/Hide** of **Loading** through **FLoading**.


# 💎 Custom style

> Flexible enough to shine

![](https://gw.alicdn.com/tfs/TB1cZHVpcKfxu4jSZPfXXb3dXXa-750-468.gif)

**FLoading** allows developers to freely define the global **Loading** style or the **Loading** style for a single impression.

## Custom global style

```dart

///Define global loading style
FLoading.init(CupertinoActivityIndicator(), backgroundColor: Colors.black38);

/// show
FLoading.show(context);
```

After customizing the global style, developers can easily use `FLoading.show(context);` to build a unified **Loading** style anywhere in the world.

##  Custom one-shot style

```dart
FLoading.show(context,
  
  /// One-shot style configuration via loading
  loading: Image.asset(
    "assets/loading_gif_2.gif",
    width: 100,
    height: 100,
  ));
```

Even after defining the global style, in a special location, **FLoading** still allows developers to specify a special **Loading** style in a single build.

# 🛰 Timed hiding and background color

> From the details, care about developers

![](https://gw.alicdn.com/tfs/TB1LAuKaOpE_u4jSZKbXXbCUVXa-750-468.gif)

```dart
FLoading.show(
  context,
  loading: Image.asset(
    "assets/loading_gif_1.gif",
    width: 200,
    height: 200,
  ),

  /// Timed hiding
  duration: 3000,

  /// Configure background style
  color: Colors.red[300].withOpacity(0.3),
)
```

**FLoading** provides configurations for **automatically hide at a fixed time** and change **background color** to satisfy the unlimited creativity of developers.

### [Want to know more details? Please visit the **FLoading** official homepage (PS: Do n’t forget to vote for a **Star** you approved 😘)](https://github.com/Fliggy-Mobile/floading)


# 😃 How to use？

Add dependencies in the project `pubspec.yaml` file:

## 🌐 pub dependency

```
dependencies:
  floading: ^<version number>
```

> ⚠️ Attention，please go to [**pub**] (https://pub.dev/packages/floading) to get the latest version number of **FLoading**

## 🖥 Git dependency

```
dependencies:
  floading:
    git:
      url: 'git@github.com:Fliggy-Mobile/floading.git'
      ref: '<Branch number or tag number>'
```

> ⚠️ Attention，please refer to [**FLoading**] (https://github.com/Fliggy-Mobile/floading) official project for branch number or tag.


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/floading)

#### [Like it? Please go to the Github homepage of 《FLoading》 to vote for it 🌟!](https://github.com/Fliggy-Mobile/floading)

# Past Widget

- [《FSuper》- Help developers quickly build beautifully complex views](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- Many wonderful configuration items are prepared for developers](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- Exquisite switching elements with excellent interaction and visual effects](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- A radio widget suitable for almost any radio scenario](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- Satisfy all your imagination of floating elements](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- Easily build pull-down refresh effects](https://github.com/Fliggy-Mobile/frefresh)

- [《FDottedLine》- Brilliant dotted line effect](https://github.com/Fliggy-Mobile/fdottedline)

- [《FSearch》- A comprehensive search box widget](https://github.com/Fliggy-Mobile/fsearch)

- [《FToast》- Exquisite and flexible **Flutter** native **Toast** widget](https://github.com/Fliggy-Mobile/ftoast)

开心的在Flutter中使用Loading
