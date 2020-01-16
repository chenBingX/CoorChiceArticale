
# Flutter：声明式 UI 会是下一个10年吗？

![](https://gw.alicdn.com/tfs/TB10J77tNv1gK0jSZFFXXb0sXXa-720-353.png)

> 从 Win32 到 Web 再到 Android 和 iOS，框架通常使用一种命令式的编程风格来完成 UI 编程。这可能是我们最熟悉的风格 — 我们手动构建一个全功能的 UI 实例，比如一个 UIView 或其他类似的，在随后 UI 发生变化时，使用方法或 Setter 修改它。

> 为了减轻开发人员的负担，无需编写如何在不同的 UI 状态之间进行切换的代码， Flutter 相反，让开发人员描述当前的 UI 状态，并将转换交给框架。

这是 [Flutter 官方](https://flutter.cn/docs/get-started/flutter-for/declarative) 对自己这套 UI 框架与过去一些 UI 框架的区别描述。

很明显，**Flutter** 给自己的定义是一套 **声明式 UI 框架**。

而近些年出现的 **声明式 UI 框架** 不只是 **Flutter**。此前出现的 **React** 就是一套典型而成功的 **声明式 UI 框架**，去年苹果也推出了自己的 **声明式 UI 框架**，[**SwiftUI**](https://developer.apple.com/videos/play/wwdc2019/216/)。

过去 **命令式 UI 框架** 用的好好的，为什么 **Google**、**Apple** 这两大移动端厂牌还要推出自己的 **声明式 UI 框架** 呢？最终实现出来的界面效果不都是一样的吗？要尝试搞清楚这个问题，需要先了解一下 **命令式** 和 **声明式** 到底是什么。

# "命令式"和"声明式"

**命令式** 和 **声明式** 其实就是两种思想不同的编程范式。虽然使用两种编程范式都可以完成相同的任务，且最终的效果也是一摸一样的，但在实现的过程中，所蕴含的思想是不一样的。

## 命令式编程

在 **命令式编程（Imperative programming）**中，开发者需要明确每一步怎么去做，需要关注更多的细节。即关注点是 —— **实现**。

比如我们有一个**命令式**的扫地机器人，当我们需要打扫屋子时：

```
> 启动扫地机器人
> 前进xxx米
> 左转xx度
> 前进xxx米
> 右转xx度
    .
    .
    .
 [清扫完成]
> 左转180度
> 前进xxx米
> 右转xx度
> 前进xxx米
    .
    .
    .
> 返回充电
```

我们需要使用一条接一条的指令让扫地机器人去执行，整个过程我们都需要关注到。

命令式编程是更 **机器化** 的编程模式。

当然好处在于，如果我们有很好的规划，扫地机器人的整个工作时间会短些。换句话说，就是我们可以控制每一个细节。当然弊端也在于，当技术足够成熟时，我们很难做到比一个专业团队更能够把清扫任务规划的更加高效。


## 声明式编程

在 **声明式编程（Declarative programming）**中，开发者只需要表达需求，剩下的由声明式的框架系统底层去做具体实施。即关注点是 —— **需求**。

比如我们有一个**声明式**的扫地机器人，当我们需要打扫屋子时：

```
> 启动扫地机器人
> 开始清扫
 [清扫完成]
 [返回充电]
```

我们只需要告诉扫地机器人需求，剩下的就全交给它去完成。具体的方向调整、前进后退这些我们都不需要关心。

声明式编程是更 **拟人化** 的编程模式。

我们只要提需求，然后框架系统会给我们想要的。具体框架系统是如何满足需求的，我们是不用关心的。

很明显，**声明式编程** 对上层的程序开发者而言，简直就是梦幻般的存在。我们只需要组织需求，然后交给框架系统，由框架系统去负责实现。这样带来的结果就是，开发效率大大提升，工程师们能够有更多的时间聚焦于真正重要的部分。

当然，这意味着对框架系统会有更高的要求，它必须提供对外足够多的能力，并且足够稳定可靠。这一点，从 **Flutter** 对外提供了多达 **140+** 个基础组件就可以看出（而且他们仍然打算继续增加）。当然大部分的工作量都统一转移到了专业的 **Flutter** 框架团队去了。


# Flutter 声明式 UI

实际上声明式的 UI 框架不是什么新奇的东西。在 Android 中，我们会通过 **XML** 来声明一个布局，接着在代码中获取到布局实例，然后根据数据、交互来对这个实例进行一系列持续的修改。

在这个过程中，我们不需要关心 **XML** 中的布局声明是如何转换成视图的，这部分是**声明式**的。但接下来我们仍然要通过一条条添加、移除、修改的代码指令来指挥视图的改变应该如何进行，这部分是**命令式**的。

来看一个 [Flutter 官方](https://flutter.cn/docs/get-started/flutter-for/declarative) 的例子。

![](https://gw.alicdn.com/tfs/TB1aM_HtHr1gK0jSZFDXXb9yVXa-350-218.png)

在这个例子中，我们需要创建一个界面，然后将界面由一个状态变化到另一个状态。

在 **命令式 UI** 框架中，首先通过 **XML** (或其它方式) 声明（创建）一个初始的布局，然后在代码中通过类似 `findViewById` 的函数获取布局实例，接着使用代码指令一步一步实现页面状态的变化。

```
b = findViewById
b.setColor(red)
b.clearChildren()
ViewC c3 = new ViewC()
c3.setColor(color)
c3.setText("ViewC c3")
b.add(c3)
```

在这个过程中，可以发现，我们始终操作的都是一个实例 b，如果实例 b 的状态依赖与外界，那么我们还需要关注外界变化对实例 b 产生的影响。

在一个需要随着时间推移不断迭代的项目中，这是让开发者很哆嗦的事情，特别是这个项目有多人参与时。在迭代的过程中，我们会对这个实例 b 变得越来越感到迷惑，因为我们很能持续关注到过去、或者他人的修改对它造成的副作用。

在 **声明式 UI 框架 Flutter** 中，由于每一帧的界面都是一个新的 **widget** 实例，这样的哆嗦终于可以停止了。我们只需要声明一个新的 **widget** 就好。

```
return ViewB(
  color: red,
  child: ViewC(...),
)
```

当界面发生变化时，**Flutter** 不是修改旧的实例，而是重新构造新实例交给引擎渲染。我们无需关注一个界面，它是应该怎么从一个状态变化到下一个状态，我们只需要告诉 **Flutter** 下一个状态的界面我们需要是什么样子的就够了。

剩下的旧界面的回收销毁，新界面的创建替换、渲染，以及中间如何保持高效的性能全部都交给 **Flutter** 框架完成。

从这个例子中可以看出，在 **命令式 UI 框架** 中，我们需要持续的关注一个视图实例的变化。在实际开发中，由于加上了时间因素，随着项目的发展，施加在这个实例上的控制指令会变的越来越多。这些指令的叠加,放到长时间来看，我们很难始终如一的清楚最终的效果会是怎样的。更何况我们要关注的不仅仅只是这一个视图实例而已。

这样带来的结果就是，在初期阶段，我们可能感觉到 **命令式 UI 框架** 只需要操纵视图实例改变就好，这很简单、有效和可控。但越是往后，我们就越需要花费越多的精力来理解过去或者他人在该视图实例上施加的指令。项目的发展速度就是这么越来越慢的。

而在 **声明式 UI 框架** 中，我们会去声明（描述）每个状态对应的视图应该是什么样子的，而不是去不断的修改一个视图实例。每一个状态都对应了一个确定的视图描述。当你在一个经过很长一段时间迭代过的项目中，企图为一个新的状态修改一个界面的时候，只需要针对这个状态声明一个新的视图实例即可。这对于需要长期迭代、多人维护的大型项目而言，是一个确确实实的进步。

## 更高的效率

由于在 **声明式 UI 框架** 中，我们只需要向框架描述一个视图的样子，而无需关心它最终是如何被构建，以及渲染到屏幕上的，因此在很多情况下，为了创建一个界面往往可以少写很多代码。毕竟框架会帮助我们完成很大一部分的工作。

看一个简单列表例子。

![](https://gw.alicdn.com/tfs/TB1PxEAtHY1gK0jSZTEXXXDQVXa-360-366.jpg)

以 Android 为例，要是实现一个上图中的列表视图，我们需要:

1. 先创建一个 **XML** 文件，在其中声明一个列表组件，比如 **RecyclerView**

2. 然后在 **java** 代码解析中获得实例

3. 创建 item 对应的 **XML** 文件，在其中声明视图样式

4. 创建一个 Adapter，在其中解析 item 的 **XML** 文件，获得 item 视图实例，然后进行数据绑定

5. 实例化上一步创建的 Adapter 类，将数据传入其中，然后把它设置到列表中，以控制列表内容的渲染

6. 通知列表进行刷新渲染



```
<android.support.v7.widget.RecyclerView
    android:id="@+id/recycler_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    />

---

setContentView()
recyclerView = findViewById(R.id.recycler_view)

---

<FrameLayout
    android:layout_width="match_parent"
    android:layout_height="50dp">

    <TextView
        android:id="@+id/text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:textColor="#333333"
        android:textSize="16sp"
        />
</FrameLayout>

---

public class Adapter extends RecyclerView.Adapter {
    public Context context;
    public List<String> datas

    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        return new RecyclerView.ViewHolder(LayoutInflater.from(context).inflate(R.layout.item_view, parent)) {
        };
    }
    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
        TextView textView = holder.itemView.findViewById(R.id.text);
        textView.setText("Item " + datas.get(position));
    }
    @Override
    public int getItemCount() {
        return datas.size();
    }
}


---

adapter = new Adapter(context, datas)
recyclerView.setAdapter(adapter)
adapter.setDatas(datas)

adapter.notifyDataSetChanged()
```

在这个例子中，使用 **命令式 UI 框架**，我们需要为这样一个简单的列表视图操碎了心，不仅要关注到它的样式以及如何让它被创建和渲染，而且不得不在多个文件中来回跳转（这里还没有涉及 `style、strings、drawable` 等 **XML** 配置文件），但这几乎已经是最简单的状况了 😾。

看看在 **声明式 UI 框架 Flutter** 中，这样一个列表视图是如何实现的。

```
ListView.builder(
    itemCount: datas.length,
    itemBuilder: (context, index) {
      return Center(
          child: Text(
        "Item ${datas[index]}",
        style: TextStyle(
          fontSize: 16,
          color: Color(0xff333333)
        ),
      ));
    }),
```

这几乎是全部！

通过 **Flutter** 提供的描述接口，我们描述了需要一个什么样的视图：

    它是一个列表，列表中的每一项是文字，文字水平居中..

接下来这个视图如何构建？如何渲染？如何保证性能？等等一系列的事情，都将由 **Flutter** 框架确保。


## 更易迭代升级

在 **命令式 UI 框架** 中，例如 Android，我们总是依赖与系统框架提供的一些 **Api** 去操控视图对象。

```
setLayoutParams
setColor
setTextColor
addView
removeAllViews
clearFocus
   .
   .
   .
```

这些命令接口使得框架系统的迭代和升级变得异常困难。

一方面，对于框架系统开发商而言，每次对框架的修改都必须十分小心。视图操作接口、行为的变化可能会让使用者们现有的代码变得不可用。在 Android 的 UI 框架中，所有的视图都是基于 **View** 这个类来构建的，**ViewGroup、TextView** 等等都继承于 **View**。在修改和扩展对外提供的接口或行为时，实际上已经随着框架的不断庞大，而显得比较吃力了。新的修改必须不断的去 Cover 过去的情况，如果一个接口决定要推到重来，那对于框架的使用者而言，很可能会造成巨大的升级成本。单单是 **View.java** 这个文件就有接近于 **30000** 行的代码。

![](https://gw.alicdn.com/tfs/TB1cO.WtUY1gK0jSZFCXXcwqXXa-360-177.png)

另一方面，对于框架系统的使用者而言，在通过框架系统提供的 **Api** 一步一步的构建界面的过程中，必须时刻保持对接口行为的关注。当框架系统升级时，需要谨慎的考虑对于过去构建的视图的影响。一旦发现接口、行为的变化，我们可能将不得不去做大量的适配工作。

在 **声明式 UI 框架** 中，如 **Flutter**，大部分情况下会采用框架提供的基础组件组合的方式来进行扩展，而非继承的方式。而且 **Flutter** 所提供的是用于描述界面样式的接口**"语言"**，而非真正要渲染的视图元素。因此，当框架系统内部的实现发生修改，对使用者而言很多时候是无感知的，因为我们不需要知道我们想要的界面是如何被实现出来的。我们只需要把想要的界面描述清楚，由框架来保证最终呈现的界面始终是我们所描述的样子。

这对于框架系统的开发商和使用者无疑都是很棒的结果。使用者只需要专注于自己的需求，而把具体的实现、优化交给更专业的开发团队来完成。

## 这是进步

当然，现实的需求是复杂多样的，很难存在一个**声明式 UI 框架**能够兼顾到所有的情况。例如，在 **Flutter** 中，目前要实现一个奇奇怪怪的视图时，往往很难通过现有的 **Widget** 来组合。这时可能需要通过 **CustomPainter** 等来自己绘制需要的视图。

但相比**命令式 UI 框架**，在大多数情况下，**声明式 UI 框架**表意更清晰，开发效率往往会更高，也更适合于需要长期迭代升级的项目。这些已经是很大的进步了。

在 **Google I/O 2019** 大会上，**Google** 亮相了一套 Android 全新的 UI 框架 —— [**Jetpack Compose**](https://developer.android.com/jetpack/compose)。这是一套使用 **Kotlin** 实现的，用于 Android UI 构建的**声明式 UI 框架**。

在介绍中，**Google** 总结了过去 10 年 Android 发展过程中所积累的诸多历史问题，以及 Android 现在这套 10 年未变的 UI 框架所面临的困境。

![](https://gw.alicdn.com/tfs/TB1BcIhtF67gK0jSZPfXXahhFXa-360-164.png)

最终， Google 希望通过 **Kotlin** 的语言特性，构建出一套更先进的 **声明式 UI 框架** 来解决这些问题。

> Android’s modern toolkit for building native UI

这是 [**Jetpack Compose**](https://developer.android.com/jetpack/compose) 官方给予这套框架的定义。

通过 **Apple** 推出的 [**SwiftUI**](https://developer.apple.com/videos/play/wwdc2019/216/)，**Google** 推出的 [**Jetpack Compose**](https://developer.android.com/jetpack/compose) 和 [**Flutter**](https://flutter.cn/docs/get-started/flutter-for/declarative) 可以看出，在经过多年的实践后，两大系统框架厂牌最终都不约而同的在尝试通过 **声明式的 UI 框架** 来解决一些多年来实践中的问题。而目前在这种 UI 编程范式的推进实践中，也确确实实能够看到过去一些难以平衡的问题，似乎正在被解决。

虽然这种 **声明式的 UI 框架** 也不是什么新技术，更不是什么黑科技，但其对编程思想的转变，使得程序开发者与框架系统（机器）的交流方式和角色发生了变化。原来的命令式框架系统相当于只是生产工具，我们需要使用这些工具来亲力亲为的完成界面的构建。而声明式的框架系统更接近于生产力，我们更多的时候只需要向它描述清楚我们需要什么就够了。

从多年习惯的 **命令式编程** 转变到 **声明式编程** 不可避免的需要经历一些痛苦的适应的，但在熟悉之后，便能够发现它的魅力和价值。

我们很难站在现在去准确的说，未来是哪一种技术能够登上王座。但透过这些技术对问题的解决和平衡程度，以及为业界提供基础设施的厂牌的动作，也可以窥探到一些动向。

> 毕竟，纵观技术的发展史，每一次技术更替，能成功登上王座的往往都是过去已经出现，但其价值在用于解决现在的问题时被发挥到了最大的技术。