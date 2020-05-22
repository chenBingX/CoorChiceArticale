# 玩儿转下拉刷新，FRefresh

[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** 用心提供精致的组件，助您构建精美的应用。  


现在，开发者请集中注意力 🤭。

思考一下 🤔，如何才能构建出下图中的视效呢？

![](https://gw.alicdn.com/tfs/TB1xiGLXupyVu4jSZFhXXbBpVXa-700-525.gif)  

经过大脑 🧠 的一番飞速旋转，也许我们最终会发现这其中有诈 😧 ！嗯～这不是一个容易的事情啊。 

在下拉的过程中，随着滑动距离的变化，我们需要连续处理几个不同阶段的情况：


```
1. 临界距离之前的下拉过程

2. 到达临界距离

3. 超过临界距离的下拉过程

4. 自由回弹到刷新位置

5. 保持刷新状态

6. 刷新结束，自由回弹到 0 点位置

7. 未达临界距离回弹到 0 点位置

    .
    .
    .
```

每一个过程都需要我们进行大量的计算和逻辑判断。如果再想随着滑动距离对头部  **Widget**  作出相应调整，那事情就真是很复杂了。

很焦虑吗？

开发者，大可不必！

因为， **FRefresh**  出现啦！

 **FRefresh**  是由 **[【阿里巴巴-飞猪-FliggyMobile 技术团队】](https://github.com/Fliggy-Mobile)** 开发维护的 **FWidget** 系列组件之一。

在过去一段时间中，我们已经先后向社区开放了 **5** 实用精美的  **Widget** （[ **FSuper** ](https://github.com/Fliggy-Mobile/fsuper)、[ **FButton** ](https://github.com/Fliggy-Mobile/fbutton)、[ **FSwitch** ](https://github.com/Fliggy-Mobile/fswitch)、[ **FRadio** ](https://github.com/Fliggy-Mobile/fradio)、[ **FFloat** ](https://github.com/Fliggy-Mobile/ffloat)），帮助开发者们更易构建出赏心悦目的应用。

我们很高兴获得了开发者们给予的认同和支持，目前我们已经累计收到了开发者们投出的 [**440** 个  **Star**  ](https://github.com/Fliggy-Mobile)。

而今天给开发者们献上的  **FRefresh** ，正是  **FWidget**  系列组件中的第  **6**  个成员。

||||
|:--:|:--:|:--:|
|![](https://gw.alicdn.com/tfs/TB17ld1Gxz1gK0jSZSgXXavwpXa-550-391.gif)|![](https://gw.alicdn.com/tfs/TB1CTN0Gvb2gK0jSZK9XXaEgFXa-550-391.gif)|![](https://gw.alicdn.com/tfs/TB186p6Grj1gK0jSZFOXXc7GpXa-550-391.gif)|
|![](https://gw.alicdn.com/tfs/TB1fHJ3Grr1gK0jSZFDXXb9yVXa-550-391.gif)|![](https://gw.alicdn.com/tfs/TB11ex1Gvb2gK0jSZK9XXaEgFXa-550-391.gif)|![](https://gw.alicdn.com/tfs/TB19oLDGKT2gK0jSZFvXXXnFXXa-360-212.gif)|



# ✨ 特性

来看看  **FRefresh**  都为开发者们准备了些什么：

- 提供超简单的下拉刷新和上拉加载构建方式

- 支持局部更新刷新区域和加载区域视图

- 足够全面的状态机支持

- 实用的控制器

- 支持主动触发刷新


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)


# 🛸 传送区

#### 🛸 [【传送门：FRefresh Github 主页 - 感谢您的 Star 🌟】](https://github.com/Fliggy-Mobile/frefresh)

#### 📖 [【传送门：FRefresh 文档】](https://pub.dev/documentation/frefresh/latest/frefresh/frefresh-library.html)

#  💫 基础版下拉刷新

> 就要更简单。

![](https://gw.alicdn.com/tfs/TB17ld1Gxz1gK0jSZSgXXavwpXa-550-391.gif)

这是日常开发中我们最常见的下拉刷新例子 🌰。相信我，如果自己想要构建一个这样的效果，会很费劲的！

但如果使用  **FRefresh** ，情况就完全不同了。

接下来，我们只需要通过简单的几行代码，就能完成这一效果的构建。

```dart

FRefresh(
  /// 构建刷新 Header
  header: buildRefreshView(),

  /// 需要传入 Header 区域大小
  headerHeight: 75.0,

  /// 内容区域 Widget
  child: ListView.builder(
      physics: NeverScrollableScrollPhysics(),
      shrinkWrap: true,
      ...
  ),

  /// 进入 Refreshing 后会回调该函数
  onRefresh: () {},
);
```

完工 🔨！

这就是创建下拉刷新所要做的所有工作。

 **FRefresh**  处理好了一切，开发者只需要全身心关注 **Header 区域**  和  **内容区域** 的构建工作就够了。


# 🚀 进阶版下拉刷新

> 足够简单，足够高效

![](https://gw.alicdn.com/tfs/TB1CTN0Gvb2gK0jSZK9XXaEgFXa-550-391.gif)

```dart

FRefresh(

  /// 通过 headerBuilder 构建 Header 区域
  headerBuilder: (setter, constraints) {
    return FSuper(

       /// 获取当前 Header 区域可用空间大小
       width: constraints.maxWidth,
       height: constraints.maxHeight,
       ...
       onClick:{
          setter((){
             /// 刷新 Header 区域
          })
       },
    );
  },
  headerHeight: 100.0,

  /// 构建内容区域
  child: GridView.builder(),

  /// 进入 Refreshing 状态后会回调该函数
  onRefresh: () {}
)
```

 **FRefresh**  提供了一种十分灵活的  **Header**  区域构建方式，即通过  **HeaderBuilder**  函数完成构建。

在  **HeaderBuilder**  函数中，开发者能够通过参数获取到用于局部刷新  **Header**  区域的刷新函数  **StateSetter** ，以及  **Header 区域** 的实时大小。

这种方式，赋予了  **Header 区域** 更加开放的创造性。


#  💫 基础版上拉加载

> 两种模式，一种风格

![](https://gw.alicdn.com/tfs/TB186p6Grj1gK0jSZFOXXc7GpXa-550-391.gif)

与下拉刷新对应，上拉加载效果的构建也同样非比寻常的简单。

```dart
FRefresh(

  /// 构建 Footer 区域
  footer: LinearProgressIndicator(),

  /// 需要配置 Footer 区域高度
  footerHeight: 20.0,

  /// 构建内容区域
  child: builderContent(),

  /// 进入 Loading 状态后会回调该函数
  onLoad: () { },
)
```

构建上拉加载也一样足够简单。开发者只需要关注  **Footer 区域** 和 **内容区域** 的构建，上拉加载过程中的状态变更、视效控制等就放心交给  **FRefresh**  好了。

# 🚀 进阶版上拉加载

> 不停止追求美的脚步

![](https://gw.alicdn.com/tfs/TB1fHJ3Grr1gK0jSZFDXXb9yVXa-550-391.gif)

```dart

FRefresh(

  /// 通过 FooterBuilder 构建 Footer 区域 Widget
  footerBuilder: (setter) {

    /// 获取刷新状态，局部更新 Footer 区域内容
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

 **FRefresh**  也为  **Footer 区域** 的构建提供了一个构建函数  **FooterBuilder** 。通过该函数可以获取到只局部刷新  **Footer 区域** 的刷新函数  **StateSetter** 。

这样开发者就能很方便的根据状态或是其它一些条件改变  **Footer 区域** 的视图了。

很贴心的吧 🥰。

# ⚙️ FRefreshController

> 让你掌控全局

![](https://gw.alicdn.com/tfs/TB11ex1Gvb2gK0jSZK9XXaEgFXa-550-391.gif)

 **FRefresh**  向开发者提供了贴心的控制器  **FRefreshController** ，支持诸多便捷的能力。
 
## 1. 给 FRefresh 添加控制器
 
```dart

/// 创建 控制器
FRefreshController controller = FRefreshController()

/// 给 FRefresh 配置控制器
FRefresh(
  controller: controller,
)
```

当开发者创建一个控制器，然后将它设置到一个  **FRefresh**  中后，控制器就能开始监听这个  **FRefresh**  的状态，以及对它进行控制了。
 
## 2. 停止刷新或加载

当触发刷新状态或加载状态后，通常会进行网络请求等数据处理任务，在这些任务结束后，我们需要停止刷新状态或加载状态。怎么办呢？

- `controller.finishRefresh()` 可以停止刷新
 
- `controller.finishLoad()` 可以停止加载
 
## 3. 监听状态

```dart
controller5.setOnStateChangedCallback((state) {
  /// 刷新状态
  if (state is RefreshState) {
  }
  /// 加载状态
  if (state is LoadState) {
  }
});
```

通过上面这段简单的代码，就能实现对  **FRefresh**  状态变化的监听，不论是下拉刷新，还是上拉加载。


## 4. 滑动监听

```dart
controller.setOnScrollListener((metrics) {
  /// 获取滑动信息
});
```

 **FRefreshController**  添加滑动监听真的是很方便了。接收的参数是 [[ScrollMetrics]](https://api.flutter.dev/flutter/widgets/ScrollMetrics-class.html)，通过它能获取到诸如 **当前滑动距离** 、 **最大滑动距离** 、 **是否超出滑动范围** 等非常全面的信息。


## 5. 主动触发刷新

通过 **FRefreshController**，开发者还能主动触发一次刷新，而且可以指定滑动到触发刷新位置的时长。 

```dart

controller.refresh(duration: Duration(milliseconds: 2000));

```

这项功能在很多场景中都大有用处。

## 6. 滑动控制

**FRefreshController** 提供了多种贴心、细腻的滑动控制，供开发者选用。

```dart

/// 滚动到指定位置
controller.scrollTo(100.0, duration:Duration(milliseconds: 2000));

/// 滚动指定距离
controller.scrollBy(20.0, duration:Duration(milliseconds: 800));

/// 跳到指定位置
controller.jumpTo(100.0);
```

这让很多精美的交互都更易被构建。

### [想要了解更多详细内容？请访问 **FRefresh** 官方主页 (PS：别忘了投出一个你认可的 **Star** 哦 😘)。](https://github.com/Fliggy-Mobile/frefresh)


# 😃 如何使用？

在项目 `pubspec.yaml` 文件中添加依赖：

## 🌐 pub 依赖方式

```
dependencies:
  frefresh: ^<版本号>
```

> ⚠️ 注意，请到 [**pub**](https://pub.dev/packages/frefresh) 获取 **FRefresh** 最新版本号

## 🖥 git 依赖方式

```
dependencies:
  frefresh:
    git:
      url: 'git@github.com:Fliggy-Mobile/frefresh.git'
      ref: '<分支号 或 tag>'
```

> ⚠️ 注意，分支号 或 tag 请以 [**FRefresh**](https://github.com/Fliggy-Mobile/frefresh) 官方项目为准。


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/frefresh)

#### [感觉还不错？请到 《FRefresh》的 Github 主页投出您认可的一个 Star 🌟 吧！](https://github.com/Fliggy-Mobile/frefresh)

# 往期组件

- [《FSuper》- 帮助开发者快速构建精美的复杂视图](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- 为开发者准备了诸多美妙的配置项](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- 具有优良交互和视效的精美开关元素](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- 一个适用于几乎任意单选场景的单选组件](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- 满足你对浮动元素的一切想象](https://github.com/Fliggy-Mobile/ffloat)


