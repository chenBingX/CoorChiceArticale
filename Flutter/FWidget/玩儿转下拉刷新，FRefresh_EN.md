# 【FRefresh】How to easily build pull-down refresh in Flutter

[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** seriously provide exquisite widget to help you build exquisite application.


Now, developer, please pay attention 🤭.

Think 🤔，how can we create the visual effects in the picture below?

![](https://gw.alicdn.com/tfs/TB1xiGLXupyVu4jSZFhXXbBpVXa-700-525.gif)  

After the brain 🧠 is thinking fast, maybe we will eventually find that there is fraud 😧!En～This is not an easy task。 

In the process of pulling-down, as the scroll distance changes, we need to continuously handle with several different stages of the situation:


```
1. Pull-down process before trigger position

2. Reach trigger position

3. Pull-down process over trigger position

4. Free rebound to refresh position

5. Keeping refresh

6. After refresh, freely rebound to 0 position

7. Rebound to 0 o'clock without reaching the trigger position

    .
    .
    .
```

Each process requires us to make a lot of calculations and logical judgments. If you want to adjust the head **Widget** according to the scroll distance, then things are really complicated.

Are you anxious?

Developer, it is not necessary!

Because, **FRefresh** appears! 🎉🎉🎉

**FRefresh** is one of the **FWidget** series widgets developed and maintained by **[【Alibaba-FliggyMobile Team】](https://github.com/Fliggy-Mobile)**.

In the past period of time, we have opened to the community successively **5** practical and beautiful **Widget** （[ **FSuper** ](https://github.com/Fliggy-Mobile/fsuper)、[ **FButton** ](https://github.com/Fliggy-Mobile/fbutton)、[ **FSwitch** ](https://github.com/Fliggy-Mobile/fswitch)、[ **FRadio** ](https://github.com/Fliggy-Mobile/fradio)、[ **FFloat** ](https://github.com/Fliggy-Mobile/ffloat)），to help developers more easily build pleasing applications。

We are very happy to get the recognition and support from the developers. At present, we have received a total of [** 440 ** ** Star **](https://github.com/Fliggy-Mobile) voted by the developers。

The **FRefresh** offered to developers today is the **6** member of the **FWidget** series of widgets.

||||
|:--:|:--:|:--:|
|![](https://gw.alicdn.com/tfs/TB17ld1Gxz1gK0jSZSgXXavwpXa-550-391.gif)|![](https://gw.alicdn.com/tfs/TB1CTN0Gvb2gK0jSZK9XXaEgFXa-550-391.gif)|![](https://gw.alicdn.com/tfs/TB186p6Grj1gK0jSZFOXXc7GpXa-550-391.gif)|
|![](https://gw.alicdn.com/tfs/TB1fHJ3Grr1gK0jSZFDXXb9yVXa-550-391.gif)|![](https://gw.alicdn.com/tfs/TB11ex1Gvb2gK0jSZK9XXaEgFXa-550-391.gif)|![](https://gw.alicdn.com/tfs/TB19oLDGKT2gK0jSZFvXXXnFXXa-360-212.gif)|



# ✨ Features

Take a look at what **FRefresh** has prepared for developers:

- Provide super simple pull-down refresh and pull-up loading construction methods

- Support partial update refresh area and load area view

- Sufficient and comprehensive state machine support

- Practical controller

- Support active trigger refresh


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)


# 🛸 Portal

#### 🛸 [【Portal: FRefresh Github homepage-Thanks for your Star 🌟】](https://github.com/Fliggy-Mobile/frefresh)

#### 📖 [【Portal: FFloat documentation】](https://pub.dev/documentation/frefresh/latest/frefresh/frefresh-library.html)

#  💫 Basic version pull-down refresh

> Just simpler

![](https://gw.alicdn.com/tfs/TB17ld1Gxz1gK0jSZSgXXavwpXa-550-391.gif)

This is our most common pull-down refresh example 🌰 in daily development. Believe me, if you want to build such an effect, it will be very difficult!

But if you use **FRefresh**, the situation is completely different.

Now, we only need a few lines of code to complete the create of this effect.

```dart

FRefresh(

  /// Build refresh header
  header: buildRefreshView(),

  /// Need to pass the size of the header area
  headerHeight: 75.0,

  /// create content area widget
  child: ListView.builder(
      physics: NeverScrollableScrollPhysics(),
      shrinkWrap: true,
      ...
  ),

  /// This function will be called after entering Refreshing
  onRefresh: () {},
);
```

Done 🔨！

This is all you need to do to create a pull-down refresh.

**FRefresh** takes care of everything, developers only need to focus on the construction of the **Header area** and **content area**.


# 🚀 Advanced version pull-down refresh

> Simple enough, efficient enough

![](https://gw.alicdn.com/tfs/TB1CTN0Gvb2gK0jSZK9XXaEgFXa-550-391.gif)

```dart

FRefresh(

  /// Build the header area with headerBuilder
  headerBuilder: (setter, constraints) {
    return FSuper(

       /// Get the available space in the current header area
       width: constraints.maxWidth,
       height: constraints.maxHeight,
       ...
       onClick:{
          setter((){
             /// just refresh the header area
          })
       },
    );
  },
  headerHeight: 100.0,

  /// Build content area
  child: GridView.builder(),

  /// This function will be called back after entering the refreshing state
  onRefresh: () {}
)
```

**FRefresh** provides a very flexible **Header area** construction method, which is to complete the construction through the **HeaderBuilder** function.

In the **HeaderBuilder** function, developer can get the refresh function **StateSetter** for partial refresh **Header area** and the real-time size of **Header area** through parameter.

This way, the **Header area** is given more open creativity.


#  💫 Basic version pull-up loading

> Two modes, one way

![](https://gw.alicdn.com/tfs/TB186p6Grj1gK0jSZFOXXc7GpXa-550-391.gif)

Corresponding to the pull-down refresh, the construction of the pull-up loading effect is also very simple.

```dart
FRefresh(

  /// create Footer area
  footer: LinearProgressIndicator(),

  /// Need to configure the height of the footer area
  footerHeight: 20.0,

  /// create content area
  child: builderContent(),

  /// This function will be called after entering the Loading state
  onLoad: () { },
)
```

Building pull-ups is equally simple enough. Developers only need to pay attention to the construction of **Footer area** and **content area**, and the state changes and visual effects control during the pull-up loading process can be safely handed over to **FRefresh**.

# 🚀 Advanced version pull-up loading

> Never stop pursuing beauty

![](https://gw.alicdn.com/tfs/TB1fHJ3Grr1gK0jSZFDXXb9yVXa-550-391.gif)

```dart

FRefresh(

  /// Build Footer Area Widget by FooterBuilder
  footerBuilder: (setter) {

    /// Get refresh status, partially update the content of Footer area
    controller.setOnStateChangedCallback((state) {
      setter(() {
        ...
      });
    });
    return buildFooter();
  },
  footerHeight: 38.0,
  child: buildContent(),
  onLoad: () {
    controller.finishLoad();
  },
)
```

**FRefresh** also provides a builder function **FooterBuilder** for building the **Footer area**. Through this function, you can get the refresh function **StateSetter** which refreshes only the **Footer area**.

In this way, the developer can easily change the view of the **Footer area** according to the status or some other conditions.

Very intimate 🥰.

# ⚙️ FRefreshController

> You can control everything

![](https://gw.alicdn.com/tfs/TB11ex1Gvb2gK0jSZK9XXaEgFXa-550-391.gif)

**FRefresh** provides developers with intimate controllers **FRefreshController**, which supports many convenient capabilities.
 
## 1. Add controller to FRefresh
 
```dart

/// Create Controller
FRefreshController controller = FRefreshController()

/// Configure controller for FRefresh
FRefresh(
  controller: controller,
)
```

When the developer creates a controller and then sets it into a **FRefresh**, the controller can start to monitor the status of this **FRefresh** and control it.
 
## 2. Stop refreshing or loading

When the refresh state or loading state is triggered, data processing tasks such as network requests are usually performed. After these tasks are completed, we need to stop the refresh state or loading state. How to do it?

- `controller.finishRefresh()`：Can stop refreshing
 
- `controller.finishLoad()`：Can stop loading
 
## 3. State Change Listen

```dart
controller.setOnStateChangedCallback((state) {
  /// Refresh status
  if (state is RefreshState) {
  }
  /// Loading state
  if (state is LoadState) {
  }
});
```

Through the above simple code, you can monitor the status change of FRefresh, whether it is pull-down refresh or pull-up loading.


## 4. Scroll Listen

```dart
controller.setOnScrollListener((metrics) {
  /// Get scroll information
});
```

**FRefreshController** It is really convenient to add sliding monitor. The parameters received is [[ScrollMetrics]](https://api.flutter.dev/flutter/widgets/ScrollMetrics-class.html)，it can get very comprehensive information such as **current scroll distance**, **maximum scroll distance**, **whether it exceeds the scroll range**, etc..


## 5. Actively trigger refresh

Through  **FRefreshController** , developers can also actively trigger a refresh, and can specify the length of time to slide to the refresh position. 

```dart

controller.refresh(duration: Duration(milliseconds: 2000));

```

This feature is very useful in many scenarios.

#### 6. Scroll control

**FRefreshController** provides a variety of intimate and delicate sliding controls for developers to choose.

```dart

/// Scroll to the specified position
controller.scrollTo(100.0, duration:Duration(milliseconds: 2000));

/// Scroll the specified distance
controller.scrollBy(20.0, duration:Duration(milliseconds: 800));

/// Jump to the specified position
controller.jumpTo(100.0);
```

This makes many beautiful interactions easier to build.

### [Want to know more details? Please visit the **FRefresh** official homepage (PS: Do n’t forget to vote for a **Star** you approved 😘)](https://github.com/Fliggy-Mobile/frefresh)


# 😃 How to use？

Add dependency in project pubspec.yaml file:

## 🌐 pub dependency

```
dependencies:
  frefresh: ^<version number>
```

> ⚠️ Attention，please go to [**pub**] (https://pub.dev/packages/frefresh) to get the latest version number of **FRefresh**

## 🖥 git dependency

```
dependencies:
  ffloat:
    git:
      url: 'git@github.com:Fliggy-Mobile/frefresh.git'
      ref: '<Branch number or tag number>'
```

> ⚠️ Attention，please refer to [**FRefresh**](https://github.com/Fliggy-Mobile/frefresh) official project for branch number or tag.


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/frefresh)

#### [Like it? Please go to the Github homepage of 《FRefresh》 to vote for it 🌟!](https://github.com/Fliggy-Mobile/frefresh)

# Past Widget

- [《FSuper》- Help developers quickly build beautifully complex views](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- Many wonderful configuration items are prepared for developers](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- Exquisite switching elements with excellent interaction and visual effects](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- A radio component suitable for almost any radio scenario](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- Satisfy all your imagination of floating elements](https://github.com/Fliggy-Mobile/ffloat)


