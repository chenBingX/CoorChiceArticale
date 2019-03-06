Flutter 的 Material 中提供了 **TabBar** 和 **TabBarView** 来实现下图的 Tab+ViewPage 联动的效果。  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/tabbar+tabbarview.gif)  

# 1.TabBar

TabBar 是 Tab 的容器，先了解以下它有哪些可配置项。  


|属性|类型|说明|
|---|---|---|
|tabs|List<Widget>|Tab，Tab的数量必须和TabController.length相等|
|controller|TabController|控制TabBar的控制器|
|isScrollable|bool|是否可以滚动。不可滚动会把所有Tab压缩到一屏|
|indicatorColor|Color|下边指示器的颜色|
|indicatorWeight|double|下边指示器的宽度|
|indicatorSize|TabBarIndicatorSize|设置指示器的长度。TabBarIndicatorSize.tab-和Tab一样长；TabBarIndicatorSize.label和Tab的文字一样长|
|indicator|Decoration|设置自己定义的指示器。|
|labelColor|Color|Tab文字选中的颜色|
|unselectedLabelColor|Color|Tab文字未选中的颜色|
|labelStyle|TextStyle|Tab文字选中的style，注意在这里设置color是无效的|
|unselectedLabelStyle|TextStyle|Tab文字未选中的style，注意在这里设置color是无效的|
|onTap|ValueChanged<int>|Tab被点击时的回调|  

```
// 需要传 TickerProvider
var tabController_1 = TabController(length: tabs_1.length, vsync: this)

TabBar(
  controller: tabController_1,
  labelColor: Colors.amber,
  unselectedLabelColor: Colors.black38,
  indicatorColor: Colors.amber,
  indicatorSize: TabBarIndicatorSize.tab,
  tabs: tabs_1
      .map((e) => Tab(
            text: e,
          ))
      .toList(),
  onTap: (index) {
    setState(() {
      // ..
    });
  },
)
```

实现起来还是比较方便的。  

# 2.Tab

TabBar 中的 Tab 由 Tab 类创建，这个类比较简单。  

|属性|类型|说明|
|---|---|---|
|text|String|Tab的文字|
|icon|Icon|Tab的图标|
|child|Widget|自定义tab的样式，会覆text和icon|

```
Tab(
   text:"Android",
   icon:Icon(Icons.android)
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/IconTab.png)  

# 3.TabBarView

TabBarView 可以和 TabBar 方便的建立联动。  

看看它有那些属性：  

|属性|类型|说明|
|---|---|---|
|children|List<Widget>|对应一个tab的widget|
|controller|TabController|需要和 TabBar 使用同一个 TabController 对象|
|physics|ScrollPhysics|设置PageView滚动效果|  


```
TabBarView(
controller: curTabController,
children: curTabs.map((e) {
  return Center(
    child: Text(
      e,
      style: TextStyle(fontSize: 26),
    ),
  );
}).toList())
```

效果就像本文开始的示例。