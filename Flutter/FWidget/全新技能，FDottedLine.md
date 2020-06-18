

[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** 用心提供精致的组件，助您构建精美的应用。  


对于一个  **Flutter**  开发者而言，要实现下图中的虚线效果可不是一个容易的事情 🤔。

![](https://gw.alicdn.com/tfs/TB1eFZCI7Y2gK0jSZFgXXc5OFXa-360-340.png)

由于官方没有准备有关于虚线的  **Widget**  供开发者们食用。

很多时候，开发者们不得不自己手动绘制或是使用一些特别的方式去实现，但过程总是不那么容易的。

现在， **FDottedLine**  要来改变目前的状况了。为开发者提供足够优雅的方式，去完成虚线视图的构建。

**FDottedLine** 被作为由 **[【阿里巴巴-飞猪-FliggyMobile 技术团队】](https://github.com/Fliggy-Mobile)** 开发维护的 **FWidget** 系列组件中的第 7 个组件，献给社区的开发者们。希望帮助开发者们更易于创建精美的应用程序。
 
在过去的几周中，已经开放的 **FWidget** 组件获得了社区开发者们的持续关注，开发者们目前已为其投出了近  [**800** 个  **Star**  ](https://github.com/Fliggy-Mobile)。

今天，我们为开发者们带来了强大的，支持灵活构建虚线效果的组件 ——  **FDottedLine**  。

![](https://gw.alicdn.com/tfs/TB182cqI.z1gK0jSZLeXXb9kVXa-1380-1282.png)


# ✨ 特性

看看  **FDottedLine**  都带来了些什么改变吧：


- 同时支持水平、垂直两个方向的虚线

- 支持创建虚线形状

- 提供超简单的方式为  **Widget**  添加虚线边框

- 支持创建灵活的虚线边角效果


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

 

# 🛸 传送区

#### 🛸 [【传送门：FDottedLine Github 主页】](https://github.com/Fliggy-Mobile/fdottedline)

#### 📖 [【传送门：FDottedLine 文档】](https://pub.dev/documentation/fdottedline/latest/fdottedline/fdottedline-library.html)


# 🔩 水平虚线

> 一贯的简洁

![](https://gw.alicdn.com/tfs/TB1ClEbI5_1gK0jSZFqXXcpaXXa-360-340.png)


```dart
FDottedLine(
  color: color,
  width: 160.0,
)
```

通过  **FDottedLine**  创建一个水平虚线，异常简单 😱。

停止对虚线视图的恐惧！

# ⛓ 垂直虚线

> 换种效果，也同样简单

![](https://gw.alicdn.com/tfs/TB1_eVgXDM11u4jSZPxXXahcXXa-360-319.png)

```dart
FDottedLine(
  color: color,
  height: 160.0,
)
```

和 **水平虚线** 一样简单。

到这，也许留心的开发者会发现。构建  **水平虚线** 和 **垂直虚线** 的唯一差别就是： 

> 当需要构建 **水平虚线** 时，只需要给 `width` 参数赋值；而当需要构建 **垂直虚线** 时，只需要给 `height` 参数赋值。

天呐，这可太便捷了 😱。

# 🔹 虚线图形

> 永远要，不仅如此

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

如果给 `width` 和 `height` 同时赋值，开发者们会惊讶的发现，一个虚线图形出现了。

是的， **FDottedLine**  不仅仅能够帮助开发者十分简洁的构建一维虚线视图，还能用同样简洁的方式构建二维虚线图形。


# 🌏 边角

> 再善变一点

![](https://gw.alicdn.com/tfs/TB17TwjI8r0gK0jSZFnXXbRRXXa-629-360.png)

```dart
FDottedLine(
  color: Colors.lightBlue[600],
  height: 70.0,
  width: 70.0,
  space: 2.0,
  
  /// 设置边角
  corner: FDottedLineCorner.all(50),
)
```

通过  **FDottedLine** ，开发者甚至可以创建出虚线图形的边角效果。比如：虚线圆角矩形，虚线圆形..

而且构建起来一贯的简单，仅仅配置一个 `corner` 参数，就能完成诸多惊人的效果。


# 🧩 Child

> 让事情变得简单

![](https://gw.alicdn.com/tfs/TB1aSZaI1H2gK0jSZFEXXcqMpXa-360-324.png)

```dart
FDottedLine(
  color: color,
  corner: FDottedLineCorner.all(6.0),
  
  /// 添加 widget
  child: Container(
    color: Colors.blue[100],
    width: 130,
    height: 70,
    alignment: Alignment.center,
    child: Text("0873"),
  ),
)
```

相信开发者们都深有感受，在过去，想要为一个  **Widget** 添加虚线边框，是件十分困难的事情。

但 **FDottedLine**  会让事情变得空前简单。开发者只需要将自己的  **Widget**  作为  **FDottedLine**  的  `child` 就完成了所有的工作了。

在 **FDottedLine** 的背后，**FDottedLine** 会帮助开发者计算 **Widget** 的大小，从而进一步确定应该如何创建正确的虚线边框。

这项工作，为开发者们开辟了一条新的路径。

# 💡 探索更多乐趣

看看  **FDottedLine**  都能干些什么！

![](https://gw.alicdn.com/tfs/TB17_wjI8r0gK0jSZFnXXbRRXXa-480-511.png)

当拥有了如此简单的方式创建虚线，开发者可以自由构建出更多精彩绝伦的视图。

![](https://gw.alicdn.com/tfs/TB1geStkIKfxu4jSZPfXXb3dXXa-720-227.gif)

接下来关于  **FDottedLine**  的更多应用，就期待开发者们的探索吧 🔆。


### [想要了解更多详细内容？请访问 **FDottedLine** 官方主页 (PS：别忘了投出一个你认可的 **Star** 哦 😘)。](https://github.com/Fliggy-Mobile/fdottedline)


# 😃 如何使用？

在项目 `pubspec.yaml` 文件中添加依赖：

## 🌐 pub 依赖方式

```
dependencies:
  fdottedline: ^<版本号>
```

> ⚠️ 注意，请到 [**pub**](https://pub.dev/packages/fdottedline) 获取 **FDottedLine** 最新版本号

## 🖥 git 依赖方式

```
dependencies:
  fdottedline:
    git:
      url: 'git@github.com:Fliggy-Mobile/fdottedline.git'
      ref: '<分支号 或 tag>'
```

> ⚠️ 注意，分支号 或 tag 请以 [**FDottedLine**](https://github.com/Fliggy-Mobile/fdottedline) 官方项目为准。


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/fdottedline)

#### [感觉还不错？请到 《FDottedLine》的 Github 主页投出您认可的一个 Star 🌟 吧！](https://github.com/Fliggy-Mobile/fdottedline)

# 更多精彩组件

- [《FSuper》- 帮助开发者快速构建精美的复杂视图](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- 为开发者准备了诸多美妙的配置项](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- 具有优良交互和视效的精美开关元素](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- 一个适用于几乎任意单选场景的单选组件](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- 满足你对浮动元素的一切想象](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- 轻松构建下拉刷新效果](https://github.com/Fliggy-Mobile/frefresh)
