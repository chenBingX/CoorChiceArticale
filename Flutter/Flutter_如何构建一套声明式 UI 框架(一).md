
# Flutter：如何构建一套声明式 UI 框架(一)

![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)

在 [《Flutter：声明式 UI 会是下一个10年吗？》](https://www.atatech.org/articles/160022) 一文中，我们了解到了 Flutter 这套 UI 框架的基本设计思想就是 **声明式** 。

实际上 **声明式** 和 **命令式** 的不同，就在于框架对外提供能力的思路不同。

**声明式** 所提供的是一种描述（构造）的形式，而 **命令式** 提供的是一种控制（指令）的形式。

比如，在 **声明式** 的 UI 框架中，我们会这样去实现一个文本的展示：

```
Text('Hi, FWidget')
```

在 **命令式** 的 UI 框架中，我们会有这样的实现：

```
var textView = TextView()
textView.setText("Hi, FWidget")
```

你品，你细品...

**声明式** 需要我们为每一个状态对应一份视图声明，而在 **命令式** 中我们需要根据状态的不同，不断的去修改一个视图实例的属性。

但不管 **声明式** 还是 **命令式** 也好，其实最后都会根据一个被赋值了的对象（用于描述渲染的对象实例）指导引擎进行渲染。

只不过在 Flutter 这样的 **声明式UI框架** 中，这个对象会由框架管理，而在命令式的框架中，我们需要自己去管理。

# UI 框架三要素

通常来说，一个 **GUI（Graphical User Interface）** 框架需要包括 **事件、窗口、渲染引擎** 这三个要素。

- **事件机制**：UI 框架需要有事件机制来接收、转发或处理系统、用户产生的事件信号。

- **窗口**：建立窗口的概念是为了把不同的内容视图做隔离，同时通过窗口来整体的管理内部内容。这在 **pc** 应用中是显而易见的存在，但在移动端，窗口的存在大部分时间里是不需要我们去理会的，我们更多的是去关注窗口中的内容组织和处理。

- **渲染引擎**：渲染引擎需要提供一套用于将视图内容绘制（翻译）到内存中的工具。

基本上现在的 UI 框架都会包含这三个要素，不管是 **声明式** 还是 **命令式**，**pc** 还是**移动端**。只不过根据场景特点，各个要素的强调程度会有不同。

本篇主要从窗口中的内容组织部分探索 **Flutter** 的声明式 **UI** 框架。


# Flutter UI 框架

要构建一个**声明式 UI 框架**，我们需要解决的一个基础问题是：

## 如何让使用者能够声明式的描述他们想要的视图？

比如在 **HTML** 中，通过 **HTML** 格式标记语言，开发者能够描述出一个自己想要的视图出来。

```
<p>Hi，FWidget</p>
```

这样的做法足够清晰，但当我们需要加入逻辑改变视图时，事情就会变的复杂起来。

再看 **Android** 中，我们可以通过 **XML** 定义一个视图。

```
<TextView
    android:layout_with="wrap_content"
    android:layout_height="wrap_content"
    android:text="Hi，FWidget"/>
```

这同样是清晰的，但想要将它显示到屏幕上，我们仍然需要经过一系列的解析，让它转换成 **Android** 对应的视图实例，然后才能进行渲染。而且，我们很难根据每一个状态去定义一个对应的 **XML** 文件。

那 **Flutter** 又是如何提供给开发者更好的描述视图的方式呢？

```
Text('Hi，FWidget')
```

直接通过 **Dart** 语言，在代码中定义对象实例来描述。这可以说是很硬核了！😮

在 **Flutter** 中，通过使用代码创建一个用于描述视图组件的对象，我们称之为 **Widget**。然后将一个个的 **Widget** 组织起来，来描述一个视图界面。

可以想象，如果是在 **Android**，完全通过 **Java** 代码一行一行的创建 **View** 实例，组织视图会有多可怕。

![](https://gw.alicdn.com/tfs/TB1x_eLu.Y1gK0jSZFCXXcwqXXa-480-349.png)

因为这样创建视图的方式面临的挑战主要有：

- 创建视图的代码将会难以阅读

- 不同于 **HTML**、**XML** 语言，**Java** 等语言创建的视图代码需要经过编译才能运行，这意味着我们无法在创建阶段即时预览效果

但好处也是显而易见的：

- 省去了解析 **HTML**、**XML** 等视图文件的时间，初始化效率更高

- 更加易于根据不同逻辑修改视图以及进行封装📦


**Flutter** 在解决通过 **Widget** 使用代码创建视图的弊端上是很聪明的。

首先 **Flutter** 通过格式化工具，将视图代码能够根据层级进行格式化，这就很好的缓解了代码创建视图难以阅读的问题。在我们熟悉了这些格式化后，实际上我们能够发现，它们同样足够清晰明了。关于 **Flutter** 的格式化，[点击【传送门】了解更多](https://flutterchina.club/formatting/)。

**Flutter** 的视图代码看起来是这个样子：

```
MaterialApp(
  home: Scaffold(
    appBar: AppBar(
      title: const Text('FWidget'),
    ),
    body: Center(
      child: Text('Hi，FWidget'),
    ),
  ),
),
```

熟悉了之后还是很一目了然的😋。


此外，通过 **Dart** 语言同时具备 **AOT** 和 **JIT** 两种运行模式的特性，**Flutter** 实现了 **Hot Reload** 的调试特性，使得开发者对视图代码的修改，能够很快呈现出来，解决了代码创建视图无法即时预览的问题。


实际上，通过 **Widget** 来组织视图，就是创建不同类型的 **Widget** 对象，它们之间通过构造函数传参成为彼此的 **parent** 或者 **child** 属性来连接起来，形成一颗 **Widget** 树。

从上面的代码中可以看出，我们需要做的就是构造函数套构造函数，最终获得的对象其实就是最外层的 **MaterialApp**。这样的一种形式和过去的 **命令式 UI** 有着很大的区别。

但这在格式化之后很像是在配置一个 **DSL**，很巧妙的吧👍

## 如何将不同的组件组织起来形成一个完整的视图模块？

组织视图就是将不同的组件组合到一起，以获得我们想要的样子。

这就涉及到在定义组件的时候，我们究竟要定到一个什么样的粒度呢？

![](https://gw.alicdn.com/tfs/TB1c3OGu4z1gK0jSZSgXXavwpXa-1140-738.png)

在 **Android** 中，我们的 **View** 等控件本身带有了诸多的属性，比如`padding`、`margin` 以及 各种各样的 `listener`。我们在创建一个控件实例后，可以给它设置各种各样的属性。它是一个独立的视图控件，但事实上它所具备的能力已经远远超出了一个独立视图控件该有的。

接下来 **ViewGroup**、**TextView** 等等大批的控件元素，都继承自 **View**，不断的基于祖先扩展各自的功能。当然，在一定的复杂范围内，这种方式有着无可厚非的便捷性。

![](https://gw.alicdn.com/tfs/TB1gxChuEY1gK0jSZFCXXcwqXXa-720-346.png)

但随着我们审美水平的变化，我们对应用视觉的要求越来越高，应用视图的复杂程度也越来越高。我们很容易就察觉到的一个事实是，原本框架提供的控件远远满足不了需求，因此现在我们在开发应用的时候，最常干的事情就是继承和扩展控件。

虽然很多基础控件里的能力我们根本不需要，但我们还是不得不继承一个臃肿的庞然大物，来实现自己想要的一个小功能。这让扩展新功能变得难以进行。

![](https://gw.alicdn.com/tfs/TB1B_iiuq61gK0jSZFlXXXDKFXa-772-504.png)


大概是本着**组合优于继承**的理念，**Flutter** 在组件粒度的控制上是比较极端的。

在 **Flutter** 有一个说法，叫做 **"Everything is a widget"**。你所见到的一切都可以是组件！

除了常规的视图 **Text**、**Image** 等组件，**Padding**、**Center**、**Align**、**Semantics**、**Baseline**、**GestureDetector** 等间距、交互元素都被抽象成了组件。

在这里 [【传送门】](https://flutter.dev/docs/development/ui/widgets) 你可以看到，**Flutter** 有多达上百个组件。就像积木一样，你可以使用它们来拼出你想要的视图模块。

![](https://gw.alicdn.com/tfs/TB13WOluEY1gK0jSZFMXXaWcVXa-720-480.jpg)

对于未来的扩展而言，它们足以应付很长一段时间的复杂度发展。

![](https://gw.alicdn.com/tfs/TB1Gvynurr1gK0jSZFDXXb9yVXa-257-480.png)

比如，上图中的视图，在 **命令式 UI** 中我们可能这样来构建：

```
var textView = TextView()
textView.setText("Text")
textView.setPaddingLeft(10)
textView.setPaddingTop(10)

parent.addChild(textView)
```

在 Flutter 中我们可以这样来构建：

```
Padding(
  padding: EdgeInsets.only(left: 10, top:10),
  child: Text('Text'),
)
```

**Padding** 不在作为组件的一个属性了，它看起来更像一个容器。我们需要的时候，再用它包裹其它组件，使其整体具有 **Padding** 的功能。两种方式，扩展性的高下一目了然。这也符合**一个对象，只干一件事**的原则。

当然，这样细粒度的组件带来的问题就是，我们很难知道有些什么组件，以及它们是干什么的。因此，相关组件文档示例的建设需要很完备，方便开发者查阅。这一点 **Flutter** 做的还是很贴心的。

这样过于细致的粒度所伴随的另一个问题就是，如果不进行一些常用模块的封装，每次构建视图模块都需要使用大量的小组件进行拼装。但庆幸的是，我们当然可以使用这些精致的小部件拼装出常用的模块组件。比如 **Flutter** 就拼装出了一个 **Container** 来方便实现我们常见效果。

![](https://gw.alicdn.com/tfs/TB19kiLu1H2gK0jSZFEXXcqMpXa-666-646.png)

## 如何进行视图界面的更新呢？

在 **Android** 等 **命令式 UI** 中，我们一开始就会创建出一个视图对象，然后通过不断的修改它的属性，来达到更新视图样式的目的。

```
var parent = ViewGroup()
var child = TextView()
parent.setMarginTop(10)
parent.addChild(child)

--- New State ---

parent.removeChild(child)
var child2 = ImageView()
parent.addChild(child2)
```

也就是这些控件本身是可变的，因此在复杂的视图组织里，随意修改一个视图对象的属性是危险的。你很难确定对它的更改是否有效，或者导致其它状态下，展示了错误的内容。


### Flutter 如何解决？

通过前面的内容我们知道，**Flutter** 使用一个个 **Widget** 小部件来描述一个界面，它们向就像**单词**、**短语**一样，一旦组合成一段文字，就是不可更改的了。新的状态，对应新的描述实例。这样就避免了在状态持续变化的过程中，开发者不断修改一个视图实例而最终导致错误的发生。


```
Padding(
    padding: EdgeInsets.only(left: 10, top:10),
    child: Text()
)

--- New State ---

Padding(
    padding: EdgeInsets.only(left: 10, top:10),
    child: Image()
)
```

嗯，这样变更视图的方式看起来很美好，特别是通过一些巧妙的设计和封装后，能够快速的定义每个状态的视图，同时避免为未来留下坑。

但每次重建新的 **Widget** 树，就意味着大量新 **Widget** 实例的创建，和老 **Widget** 实例的销毁。

如果 **Widget** 中如 Android 中的 **View** 一样，每一个都包含了大量的属性信息，且直接控制着绘制，可以想像设备的内存压力将会很大，反映到视图界面上，就是**卡顿**。

为了缓解这样的情况，在 **Flutter** 中 **Widget** 被设计的很轻量级，它们只包含自己必要的配置信息。再加上 **Dart** 语言自身的内存管理机制（[详情见【传送门】](https://medium.com/flutter/flutter-dont-fear-the-garbage-collector-d69b3ff1ca30)）能够使得轻量级对象的创建和销毁过程代价很小，从而对 UI 的渲染影响尽可能的小。这使得 **Widget** 这样的 **声明式 UI** 的性能得到了保障。当然，这并不意味这我们可以毫无节制的堆叠 **Widget**。

### Widget 背后的 Element 和 RenderObject

那在我们通过 **Widget** 定义了一个视图出来之后，又该如何将它在屏幕上显示出来呢？

可以确定的是，我们最终肯定需要一个绘制对象去将这些"描述"进行绘制的。

这个对象在 **Flutter** 叫做 **RenderObject**，它就是负责将 **Widget** 的描述的内容绘制出来的。

那么问题来了，接下来应该如何建立 **Widget** 到 **RenderObject** 的映射关系呢？

![](https://gw.alicdn.com/tfs/TB1Ko9Ju4D1gK0jSZFsXXbldVXa-720-536.png)

我们肯定是免不了需要通过 **Widget** 去创建对应的 **RenderObject** 绘制对象的。但是每个 **RenderObject** 本身也是属于比较重的对象，如果每次都和 **Widget** 同步的创建和销毁，对性能的损耗将是会很大的。因此，我们还需要想办法避免它频繁的创建、销毁。说白了，其实也就是要想办法尽可能的去复用 **RenderObject** 对象。

由于我们的视图的组织结构天生就是从上到下的树形结构，如果我们能确定每个节点位置，那么在视图更新时，在该位置的 **Widget** 如果是相同类型的话，就可以实现对 **RenderObject** 的复用（只需要把新的 **Widget** 属性赋值给 **RenderObject** 就行）。

如果直接通过 **RenderObject** 来维持一个树形结构可行吗？

当然是可以的。但在 **"Everything is a widget"** 的理念下，**Widget** 并不只是用于描述视图，还包括了范围、功能等一些信息的描述，他们是不需要绘制的。如果它们的在树中的位置仍然通过 **RenderObject** 来定义的话，对 **RenderObject** 定义就变得奇怪了。

因此，我们需要一种专门维持树形结构，能够和 **Widget** 对应起来，在必要的时候让需要绘制的 **Widget** 和 **RenderObject** 建立联系，而且最好能够尽量的实现复用的对象。在 **Flutter** 中，这样一种类型的对象，被称为 **Element**。

我们通过 **Element** 可以建立一颗与 **Widget** 描述的结构对应的 **Element** 树，然后通过 **Widget** 结构的变化对这颗树中节点的增加、删除、修改，就可以实现对视图的改变。同时，对于相同位置的同类型节点，当对应的 **Widget** 更新时，我们只需要直接替换成新 **Widget** 实例，老的 **Widget** 实例就可以释放。


> **Flutter** 始终维护了一颗 **Element** 树，通过对 **Element** 节点进行增加、删除、修改等操作，实现了低成本的视图更新。


![](https://gw.alicdn.com/tfs/TB1tfqtuxz1gK0jSZSgXXavwpXa-720-570.png)


到这呢，我们对 **Flutter** 这套 UI 框架的窗口视图组织部分的设计思路也有一个整体的认知了。其实思路就是这么个思路，用 **Java** 配合 **Android** 原生的 **View** 也能封装出这么一套声明式的 **UI** 组织框架（只不过 **Java** 的特性和 **Android** 原生的历史包袱可能会带来一些性能、实现上的弯路）。


接下来，我们深入看一看，**Flutter** 在实现这套设计时的一些细节。从这些实现细节中也许我们可以收获一些有用的技巧或者"后门"。

# 如何生根发芽？

## 根节点创建

```
void main() => runApp(MyWidget());
```

在一个 **Flutter** 应用程序中，**main.dart** 文件中的上述代码，就是一个程序的起点。我们的视图树🌲，也是从这个地方开始生根发芽的。

通过 `runApp()` 函数我们自己的程序视图就能被加载和展示。前面的内容我们有了解到，实际上程序的视图是通过在一颗视图树上添加\移除从而达到显示\消失的目的的。这颗视图树的根节点一开始就是在这个地方被创建的。

```
1-1  void runApp(Widget app) {
1-2    WidgetsFlutterBinding.ensureInitialized()
1-3      ..scheduleAttachRootWidget(app)
1-4      ..scheduleWarmUpFrame();
1-5  }
```

在 `runApp()` 函数中，会首先进行手势、通讯、组件、渲染等一系列程序服务的初始化，即代码 `1-2`。

其中，**WidgetsFlutterBinding** 通过 **mixin** 混入了诸多服务能力，它们会根据混入顺序，有条不紊的进行各自的初始化。

```
class WidgetsFlutterBinding extends BindingBase
    with GestureBinding,
       ServicesBinding,
       SchedulerBinding,
       PaintingBinding,
       SemanticsBinding,
       RendererBinding,
       WidgetsBinding
```

要了解 **Flutter** 的 **UI** 框架的视图组织部分，这里我们只需要先关注 **RendererBinding** 和 **WidgetsBinding**。

从名字不难看出 **RendererBinding** 主要是负责渲染相关事务的，而 **WidgetsBinding** 主要负责组件构建相关事务的。

先看看 **RendererBinding** 的初始化。

```
2-1  void initInstances() {
2-2    ...
2-3    _pipelineOwner = PipelineOwner(
2-4      onNeedVisualUpdate: ensureVisualUpdate,
2-5      onSemanticsOwnerCreated: _handleSemanticsOwnerCreated,
2-6      onSemanticsOwnerDisposed: _handleSemanticsOwnerDisposed,
2-7    );
2-8     ...
2-9   initRenderView();
2-10   ...
2-11  }
```

在 **RendererBinding** 的初始化中，`2-3` 到 `2-7` 创建了一个重要的对象 **PipelineOwner**。它管理着整个 **Flutter** 程序生命周期中视图树的布局、合成、渲染等事务。

在 `2-9` 中创建了程序视图树的根 **RenderObject**（就是前面提到过的真正指导绘制的对象）。

```
void initRenderView() {
  assert(renderView == null);
  renderView = RenderView(configuration: createViewConfiguration(), window: window);
  renderView.prepareInitialFrame();
}
```

这里的 **RenderView** 是继承自 **RenderObject** 的。在这之后，我们自己的视图对应的 **RenderObject** 实际上都是挂到这个根 **RenderObject** 之下的。

接下来看 **WidgetsBinding** 的初始化中又创建了那些重要的对象的。

```
void initInstances() {
  ...
  _buildOwner = BuildOwner();
  buildOwner.onBuildScheduled = _handleBuildScheduled;
  ...
}
```

这里创建了另一个重要的对象 **BuildOwner**，它负责管理应用视图树的构建（主要就是维护 **Element** 树）。

在 `1-3` 中，会将我们传入的 **Widget** 绑定到视图树上。

```
3-1  void scheduleAttachRootWidget(Widget rootWidget) {
3-2    Timer.run(() {
3-3      attachRootWidget(rootWidget);
3-4    });
3-5  }
3-6
3-7  void attachRootWidget(Widget rootWidget) {
3-8    _renderViewElement = RenderObjectToWidgetAdapter<RenderBox>(
3-9      container: renderView,
3-10     debugShortDescription: '[root]',
3-11     child: rootWidget,
3-12   ).attachToRenderTree(buildOwner, renderViewElement as RenderObjectToWidgetElement<RenderBox>);
3-13 }
```

在 `3-8` 中，创建了一个 **RenderObjectToWidgetAdapter** 对象。它其实是一个 **RenderObjectWidget** 类型的 **Widget**，即对应这一个渲染对象 **RenderObject** 的 **Widget**。

它会作为程序组件树的根 **Widget**，它对应的渲染对象 **RenderObject** 就是前面在 **RendererBinding** 初始化中创建的 **RenderObject**，见 `3-9`。

```
RenderObjectWithChildMixin<T> createRenderObject(BuildContext context) => container;
```

而我们的视图 **Widget** 就会作为它的 **child** 子节点，见 `3-11`。

接下来 `3-12` 就比较关键了，它创建了程序元素树的根 **Element**。

```
4-1  RenderObjectToWidgetElement<T> attachToRenderTree(BuildOwner owner, [ RenderObjectToWidgetElement<T> element ]) {
4-2    if (element == null) {
4-3      owner.lockState(() {
4-4        element = createElement();
4-5        assert(element != null);
4-6        element.assignOwner(owner);
4-7      });
4-8      owner.buildScope(element, () {
4-9        element.mount(null, null);
4-10     });
4-11     // This is most likely the first time the framework is ready to produce
4-12     // a frame. Ensure that we are asked for one.
4-13     SchedulerBinding.instance.ensureVisualUpdate();
4-14   } else {
4-15     element._newWidget = this;
4-16     element.markNeedsBuild();
4-17   }
4-18   return element;
4-19 }
```

在上面的代码中，`4-2` 判断了如果入参 **element** 为空的话，`4-4` 会创建一个新的 **Element** 作为元素树的根节点。如果不为空就会复用根节点，将刚创建的根 **Widget** 赋值给它。这就是前面提到过的，**Flutter** 通过 **Element** 实现了节点的复用，从而避免繁重对象的频繁创建销毁。

> **WidgetsBinding#attachRootWidget()** 函数实际上我们也可以主动调用它来整体的替换掉当前的页面视图

`4-6` 中，对于首次创建的根 **Element**，会传入 **WidgetsBinding** 初始化时创建的 **BuildOwner** 对象，此后加入该元素树的 **Element** 元素的 **_owner** 其实都是这一个。

`4-8` 到 `4-10` 中，通过调用 **BuildOwner#buildScope()**，会先将我们传入的 **Widget** 创建对应的 **Element**，然后安装到元素树上。

```
5-1   void buildScope(Element context, [ VoidCallback callback ]) {
5-2       ...
5-3       try{
5-4           callback();
5-5       }
5-6       ...
5-7       _dirtyElements.sort(Element._sort);
5-8       ...
5-9       int dirtyCount = _dirtyElements.length;
5-10      int index = 0;
5-11      while (index < dirtyCount) {
5-12          ...
5-13          try {
5-14            _dirtyElements[index].rebuild();
5-15          } catch (e, stack) {
5-16          }
5-17          ...
5-18      }
5-19      ...
5-20  }
```

**BuildOwner#buildScope()** 在视图界面刷新的时候也会被调用，此处调用主要就是为了首次将传入的 **Widget** 进行相应的初始化，然后插入到视图树中。

## Element 初始化

这里 `5-4` 中的 `callback()` 实际上就是调用了 `4-9` 中传入的函数，即 **Element#mount()**。这个函数的功能是将新创建的元素插入到元素树的插槽中。


当然，不同的 **Element** 子类的 **mount** 函数实现也会稍有不同，比如此处作为根节点类型的 **RenderObjectToWidgetElement**:

```
void mount(Element parent, dynamic newSlot) {
  ...
  super.mount(parent, newSlot);
  _rebuild();
}
```

它会先去调用父类的实现，即 **RootRenderObjectElement#mount()**。

而它的父类实际上直接调用的是再往上一级的实现，即 **RenderObjectElement#mount()**：

```
6-1  void mount(Element parent, dynamic newSlot) {
6-2    super.mount(parent, newSlot);
6-3    _renderObject = widget.createRenderObject(this);
6-4    ...
6-5    attachRenderObject(newSlot);
6-6    _dirty = false;
6-7  }
```

**RenderObjectElement** 实际上对应的就是需要绘制的 **Widget**，即 **RenderObjectWidget**。前面我们已经了解到，只有需要绘制的 **Widget** 才会有与之对应的 **RenderObject**。

我们通过 `6-3` 可以看到，在这个函数中，创建了组件对应的渲染对象 **RenderObject**。

然后 `6-5` 将新创建的渲染对象插入到渲染树的插槽中。

实际上， **Element** 的子类们在实现 **mount** 函数的时候，都会首先走一遍 **Element** 的实现。目的是为了进行一些通用的参数初始化工作。

```
7-1  void mount(Element parent, dynamic newSlot) {
7-2    ...
7-3    _parent = parent;
7-4    _slot = newSlot;
7-5    _depth = _parent != null ? _parent.depth + 1 : 1;
7-6    _active = true;
7-7    if (parent != null) // Only assign ownership if the parent is non-null
7-8      _owner = parent.owner;
7-9    final Key key = widget.key;
7-10    if (key is GlobalKey) {
7-11     key._register(this);
7-12   }
7-13   _updateInheritance();
7-14   ...
7-15 }
```

值得注意的是 `7-5` 中会根据有无父节点设置元素的 **depth**。当然，根节点没有父节点，它的 **depth** 就为 **1**。

`7-7` 将父节点的 **BuildOwner** 赋值给了当前元素，这一点我们前面已经有过了解。

## 子节点的构建

走一遍上级们的实现逻辑，**RenderObjectToWidgetElement#mount()** 中会调用 `_rebuild()` 函数进行一次构建。

```
void _rebuild() {
  try {
    _child = updateChild(_child, widget.child, _rootChildSlot);
  } catch (exception, stack) {
    ...
  }
}
```

需要注意的是，直到此处，我们传入的 **Widget** 才真正被构建对应的 **Element** 插入到树中（就是作为根节点的 **child**）。

这里调用的 **Element#updateChild()** 函数是 **Flutter** 更新机制中**十分重要**的函数，**Widget** 的构建、更新、卸载都是在这里完成的。

```
8-1  Element updateChild(Element child, Widget newWidget, dynamic newSlot) {
8-2    ...
8-3    if (newWidget == null) {
8-4      if (child != null)
8-5        deactivateChild(child);
8-6      return null;
8-7    }
8-8    if (child != null) {
8-9      if (child.widget == newWidget) {
8-10       if (child.slot != newSlot)
8-11         updateSlotForChild(child, newSlot);
8-12       return child;
8-13     }
8-14     if (Widget.canUpdate(child.widget, newWidget)) {
8-15       if (child.slot != newSlot)
8-16         updateSlotForChild(child, newSlot);
8-17       child.update(newWidget);
8-18       ...
8-19       return child;
8-20     }
8-21     deactivateChild(child);
8-22   }
8-23   return inflateWidget(newWidget, newSlot);
8-24 }
```

这里存在几种情况，用个表格来说明：

|序号|child|newWidget|child.widget == newWidget|canUpdate()|说明|
|:--:|:--:|:--:|:--:|:--:|:--|
|1|有|无|--|--|删除节点|
|2|有|有|true|--|节点保持不变|
|3|有|有|false|true|更新节点Widget|
|4|有|有|false|false|删除旧节点，创建新节点|
|5|无|有|--|--|创建新的节点|

在 **3、4** 两种情况中，**Flutter** 是如何判定一个节点是应该更新，还是删除重新创建的呢？即 `8-14`：

```
static bool canUpdate(Widget oldWidget, Widget newWidget) {
  return oldWidget.runtimeType == newWidget.runtimeType
      && oldWidget.key == newWidget.key;
}
```

只有当节点位置的 **Widget** 类型相同，且 **Key** 相同，才采取更新策略。否则直接删除老节点，在该位置创建新的节点。

什么意思呢？就是当我们视图树中一个节点视图如果仅仅是重建，那么就将元素树中该位置的节点 **Element** 的 **Widget** 更新为重建的新 **Widget** 对象；如果节点视图类型发生了变化，就会将元素树中该位置的 **Element** 先删除，然后再创建一个与新 **Widget** 对应的 **Element** 节点插入。

创建新的节点都是收拢到 `8-23` 中。

```
9-1  Element inflateWidget(Widget newWidget, dynamic newSlot) {
9-2    assert(newWidget != null);
9-3    final Key key = newWidget.key;
9-4    if (key is GlobalKey) {
9-5      final Element newChild = _retakeInactiveElement(key, newWidget);
9-6      if (newChild != null) {
9-7        ...
9-8        newChild._activateWithParent(this, newSlot);
9-9        final Element updatedChild = updateChild(newChild, newWidget, newSlot);
9-10       return updatedChild;
9-11     }
9-12   }
9-13   final Element newChild = newWidget.createElement();
9-14   ...
9-15   newChild.mount(this, newSlot);
9-16   return newChild;
9-17 }
```

在创建新的节点前，会首先检查 **Widget** 的 **Key** 中是否缓存过 **Element**，如果有就直接更新该 **Element** 的 **Widget**，即 `9-3` 到 `9-12` 的逻辑。

如果没有，则通过 **Widget#createElement()** 创建一个新的节点 **Element**（`9-13`），然后进行初始化（`9-15`)。

回到 `5-7` 中，会根据每个元素 **Element** 的深度（层级），即 **Element#depth** 对需要重建的 **Element**（**BuildOwner#_dirtyElements** 数组中的 **Element**）进行排序。

`5-10` 到 `5-18` 中会对需要更新的 **Element** （**BuildOwner#_dirtyElements** 数组中的 **Element**）调用其 `rebuild()` 函数来触发 **Widget** 的重建。当然，在应用一开始的时候，**BuildOwner#_dirtyElements** 中是没有元素需要重建的，因为它们本身就会自上而下的进行一次构建。当后续有组件需要更新的时候，它就会被加入到 **BuildOwner#_dirtyElements** 中，以便在此处能够主动触发一次构建。

到此，应用的视图树已经初始化完成，且将我们的一个视图组件挂载到了树中，接下来 `1-4` 将会立即触发第一帧画面的布局和渲染。

在以上 **Flutter** 视图树的首次创建过程中，我们能够看到一些 **Widget**、**Element**、**RenderObject** 之间建立联系的实现。

**Element** 是根据 **Widget** 创建的（`9-13`），在创建的时候会传入 **Widget**，因此 **Element** 持有了 **Widget**。

**RenderObject** 也是根据 **Widget** 创建的（`6-3`），创建后会赋值给 **Element** 持有。

也就是 **Element** 同时持有了 **Widget** 和 **RenderObject**，它们之间是互相不知道彼此的存在的，而是由 **Element** 作为纽带。这样的设计使得 **Widget** 和 **RenderObject** 之间实现了解耦，当定义好组件的声明协议后，具体的绘制过程就可以随意发挥，只要最后得到的结果是声明所期望的就行。

当然，这些在我们平时开发的过程中是察觉不到的，我们只要关注 **Widget** 这一层面就够。


# 未完待续..

