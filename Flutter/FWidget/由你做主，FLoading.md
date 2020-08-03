
[![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)](https://github.com/Fliggy-Mobile)

> **FWidget** 用心提供精致的组件，助您构建精美的应用。


![](https://gw.alicdn.com/tfs/TB1XHHyf8Bh1e4jSZFhXXcC9VXa-1462-552.png)


今天，**FWidget** 为开发者们带来的**精致**组件是～**FLoading**。

在实际应用场景中，构建 **Loading** 元素似乎是每个开发者必须掌握的技能。现在，**FLoading** 来帮助开发者们进一步精进这以技能。

**FLoading** 提供了一种极简的 **Loading** 元素构建方式，同时为开发者提供了诸多可高度定制化的配置，让开发者可以轻松的创建出精美的 **Loading**。


# ✨ 特性

那么，来看看 **FLoading** 都带来了什么。

- 提供最便捷的方式，控制  **Loading**  的  **展示/隐藏** 

- 支持修改背景颜色

- 支持定时自动隐藏

- 支持局部和全局自定义样式


![](https://gw.alicdn.com/tfs/TB1mDbBGND1gK0jSZFsXXbldVXa-360-324.png)

# 🛸 传送区

#### 🛸 [【传送门：FLoading Github 主页】](https://github.com/Fliggy-Mobile/floading)

#### 📖 [【传送门：FLoading 文档】](https://pub.dev/documentation/floading/latest/floading/floading-library.html)


# 🌈 显示/隐藏

![](https://gw.alicdn.com/tfs/TB1PN_dbwgP7K4jSZFqXXamhVXa-720-449.gif)

```dart

/// 显示
///
/// show 
FLoading.show(context);


/// 隐藏
///
/// hide 
FLoading.hide();
```

通过  **FLoading**  来实现  **Loading**  的 **显示/隐藏** ，真是太简单了。


# 💎 自定义样式

![](https://gw.alicdn.com/tfs/TB1cZHVpcKfxu4jSZPfXXb3dXXa-750-468.gif)

 **FLoading**  允许开发者自由的定义全局  **Loading**  样式或单次展示的  **Loading**  样式。

## 自定义全局样式

```dart

/// 定义全局 Loading 样式
///
///Define global loading style
FLoading.init(CupertinoActivityIndicator(), backgroundColor: Colors.black38);

/// 显示
///
/// show
FLoading.show(context);
```

在自定义全局样式后，开发者就可以便捷的通过 `FLoading.show(context);`，即可在全局任意位置构建统一的 **Loading** 样式。

## 自定义单次样式

```dart
FLoading.show(context,
  
  /// 通过 loading 配置单次样式
  ///
  /// One-shot style configuration via loading
  loading: Image.asset(
    "assets/loading_gif_2.gif",
    width: 100,
    height: 100,
  ));
```

即使在定义全局样式后，在特殊位置，**FLoading** 依旧允许开发者在单次构建时，指定特殊的 **Loading** 样式。

# 🛰 定时隐藏和背景颜色

![](https://gw.alicdn.com/tfs/TB1LAuKaOpE_u4jSZKbXXbCUVXa-750-468.gif)

```dart
FLoading.show(
  context,
  loading: Image.asset(
    "assets/loading_gif_1.gif",
    width: 200,
    height: 200,
  ),

  /// 定时隐藏
  ///
  /// Timed hiding
  duration: 3000,

  /// 配置背景样式
  ///
  /// Configure background style
  color: Colors.red[300].withOpacity(0.3),
)
```

**FLoading** 提供了**定时自动隐藏**和变更**背景色**的配置，以满足开发者们无限的创造力。

### [想要了解更多详细内容？请访问 **FLoading** 官方主页 (PS：别忘了投出一个你认可的 **Star** 哦 😘)。](https://github.com/Fliggy-Mobile/floading)


# 😃 如何使用？

在项目 `pubspec.yaml` 文件中添加依赖：

## 🌐 pub 依赖方式

```
dependencies:
  floading: ^<版本号>
```

> ⚠️ 注意，请到 [**pub**](https://pub.dev/packages/floading) 获取 **FLoading** 最新版本号

## 🖥 git 依赖方式

```
dependencies:
  floading:
    git:
      url: 'git@github.com:Fliggy-Mobile/floading.git'
      ref: '<分支号 或 tag>'
```

> ⚠️ 注意，分支号 或 tag 请以 [**FLoading**](https://github.com/Fliggy-Mobile/floading) 官方项目为准。


[![](https://gw.alicdn.com/tfs/TB1Zn2BGUT1gK0jSZFrXXcNCXXa-320-289.png)](https://github.com/Fliggy-Mobile/floading)

#### [感觉还不错？请到 《FLoading》的 Github 主页投出您认可的一个 Star 🌟 吧！](https://github.com/Fliggy-Mobile/floading)

# 更多精彩组件

- [《FSuper》- 帮助开发者快速构建精美的复杂视图](https://github.com/Fliggy-Mobile/fsuper)

- [《FButton》- 为开发者准备了诸多美妙的配置项](https://github.com/Fliggy-Mobile/fbutton)

- [《FSwitch》- 具有优良交互和视效的精美开关元素](https://github.com/Fliggy-Mobile/fswitch)

- [《FRadio》- 一个适用于几乎任意单选场景的单选组件](https://github.com/Fliggy-Mobile/fradio)

- [《FFloat》- 满足你对浮动元素的一切想象](https://github.com/Fliggy-Mobile/ffloat)

- [《FRefresh》- 轻松构建下拉刷新效果](https://github.com/Fliggy-Mobile/frefresh)

- [《FDottedLine》- 辉煌的虚线效果](https://github.com/Fliggy-Mobile/fdottedline)

- [《FSearch》- 一应俱全的搜索框组件](https://github.com/Fliggy-Mobile/fsearch)

- [《FToast》- 精致灵活的 **Flutter** 原生 **Toast** 组件](https://github.com/Fliggy-Mobile/ftoast)
