在 Flutter 中，有一个用于滚动视图的 Widget，他就是 CustomScrollView。  

它可以将诸如 SliverList、SliverGrid 等控件统一起来，形成一个交互整体。  

# 1.一个例子

```
CustomScrollView(
        // 子 Widget
        slivers: <Widget>[ 
          // 第一个放 SliverAppBar
          const SliverAppBar(),
          SliverGrid(),
          SliverFixedExtentList(),
        ],
      ),
    )
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/ScrollView1.gif)


# 2.CustomScrollView 的常用属性

|属性|类型|说明|
|---|---|---|
|slivers|List<Widget>|子 Widget 们|
|scrollDirection|Axis|滚动方向。Axis.vertical-垂直方向(默认值); Axis.horizontal-水平方向|
|reverse|bool|是否逆向排布子 Widget。默认为false|
|controller|ScrollController|用于控制 ScrollView 滚动的控制器|
|physics|ScrollPhysics|控制滚动效果。默认 AlwaysScrollableScrollPhysics|
|shrinkWrap|bool|是否由内容的大小决定 ScrollView 的大小|
|anchor|double|内容区域的锚点（起始位置）。比如设为 0.5，内容区域将会从 ScrollView 中间位置开始|
|primary|bool|如果 controller 为null，该项为 true，会使用 PrimaryScrollController|


CustomScrollView 中通常添加的是 Sliver 系列的 Widget，比如 SliverList、SliverGrid、SliverPadding 等，因为它们本身没有包含滚动，所以能被统一成一个整体滚动。

# 3.CustomScrollView 中的 Sliver Widget   

CustomScrollView 可以从上到下组织一种：**AppBar + 内容区域** 的页面结构。  

只需要把 AppBar 放到 `slivers` 的第一个即可。  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/ScrollView2.png)  


## 3.1 SliverAppBar

SliverAppBar 类似于 AppBar，但将它放到 CustomScrollView 中，就能实现上图的顶部效果。  

### 3.1.1 SliverAppBar 的常用属性 

|属性|类型|说明|
|---|---|---|
|leading|Widget|左边的 Widget。通常放返回键，或者 Drawer 开关|
|title|Widget|AppBar 的 title|
|actions|List<Widget>|右边的操作按钮|
|flexibleSpace|Widget-FlexibleSpaceBar|配合ScrollView实现下拉展开效果|
|bottom|PreferredSizeWidget|在AppBar下的 Widget|
|elevation|double|AppBar 的悬浮高度。值越大下方阴影就越大|
|backgroundColor|Color|背景颜色|
|brightness|Brightness|影响状态栏上的文字颜色。Brightness.light-黑色。Brightness.dark-白色|
|titleSpacing|double|设置 title 的空间|
|expandedHeight|double|指定完全展开的高度|
|floating|bool|AppBar 是否悬浮。true-AppBar 在下拉时就会出现；false-AppBar只有下拉到头了才会出现|
|pinned|bool|AppBar是否固顶。true-上滑时AppBar会始终在顶部；false-AppBar会被滑动出屏幕外|
|snap|bool|需要floating为true时才有效。true-当下拉漏出一点AppBar时，他就会自动全部展开|

### 3.1.2 一个例子

```
SliverAppBar(
  title: Text('SliverAppBar'),
  backgroundColor: Colors.amber,
  pinned: false,
  floating: true,
  snap: true,
  expandedHeight: 250.0,
  flexibleSpace: FlexibleSpaceBar(
    title: const Text('FlexibleSpaceBar'),
    background: Image(
      image: AssetImage('images/pic1.jpg'),
      fit: BoxFit.cover,
    ),
  ),
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/SliverAppBar.gif)

## 3.2 SliverGrid 和 SliverList

### 3.2.1 SliverGrid 的常用属性

|属性|类型|说明|
|---|---|---|
|gridDelegate|SliverGridDelegate|用于控制子Widget的大小和位置的代理。通常使用 **SliverGridDelegateWithMaxCrossAxisExtent**|
|delegate|SliverChildDelegate|用于创建子Widget的代理|

SliverGrid 的属性比较简单，就两个。具体看看它们的属性  

- SliverGridDelegateWithMaxCrossAxisExtent  
    
    SliverGridDelegateWithMaxCrossAxisExtent 是 SliverGridDelegate 的实现类。    
    
    |属性|类型|说明|
    |---|---|---|
    |maxCrossAxisExtent|double|item在交叉轴方向上的最大长度|
    |mainAxisSpacing|double|主轴方向上item的间距|
    |crossAxisSpacing|double|交叉轴方向上item的间距|
    |childAspectRatio|double|item （主轴长度/交叉轴长度）的比值，控制每个item的区域大小|
    
- SliverChildBuilderDelegate
    
    SliverChildBuilderDelegate 是 SliverChildDelegate 的实现类。  
    
    |属性|类型|说明|
    |---|---|---|
    |builder|IndexedWidgetBuilder|用于创建item的构造器|
    |childCount|int|item数量|
    
示例： 

```
SliverGrid(
  gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
    maxCrossAxisExtent: 200.0,
    mainAxisSpacing: 1.0,
    crossAxisSpacing: 0.0,
    childAspectRatio: 2.0,
  ),
  delegate: SliverChildBuilderDelegate(
    (BuildContext context, int index) {
      return Container(
        alignment: Alignment.center,
        color: colors[random.nextInt(colors.length)],
        child: Text(
          'grid item $index',
          style: TextStyle(
              color: Colors.white,
              fontSize: 16,
              decoration: TextDecoration.none),
        ),
      );
    },
    childCount: 20,
  ),
)
```


### 3.2.2 SliverList 的常用属性

|属性|类型|说明|
|---|---|---|
|delegate|SliverChildDelegate|用于创建子Widget的代理。同SliverGrid的一样|

示例：

```
SliverList(
    delegate:
        SliverChildBuilderDelegate((BuildContext context, int index) {
  return Container(
    alignment: Alignment.center,
    height: 80,
    color: colors[random.nextInt(colors.length)],
    child: Text('list item $index',
        style: TextStyle(
            color: Colors.white,
            fontSize: 16,
            decoration: TextDecoration.none)),
  );
}, childCount: 10))
```

### 3.2.3 SliverGrid 和 SliverList结合

通常的 GridView 和 ListView 放到一起，它们的滚动是互相分离的，如果想要它们的滚动统一，就使用 SliverGrid 和 SliverList。  

看看将上述二者组合的效果：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/SliverGridList.gif)


# 4.ScrollController

ScrollController 是用于控制滚动控件的。  

## 4.1 ScrollController的属性

先看看它的属性：  

|属性|类型|说明|
|---|---|---|
|keepScrollOffset|bool|是否保存滚动状态。如果保存了，当前重建时会恢复状态。默认为true|
|initialScrollOffset|double|初始滚动距离|

## 4.2 ScrollController 常用方法

1. `addListener()`  
    
    该方法中添加的回调，会在滚动时被调用。
    
    此时可以获取滚动过程中的距离、位置等信息。  
    
    ```
    addListener((){
      print('d = ${scrollController.offset}');
    })
    ```
    
2. `jumpTo()`  
    
    跳到指定位置。无动画。  
    
3. `animateTo()`  
    
    跳转到指定位置。有动画。




