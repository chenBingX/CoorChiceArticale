Scaffold 提供了快速构建 MaterialDesign 风格的页面的方案。  

Scaffold 组织的页面结构包括：AppBar，body（页面主体内容）、bottomNavigationBar（底部导航栏）、floatingActionButton 等。  

# 1.一个例子

看一个例子：  

```
class MyStatefulWidget extends StatefulWidget {
  MyStatefulWidget({Key key}) : super(key: key);

  @override
  _MyStatefulWidgetState createState() => _MyStatefulWidgetState();
}

class _MyStatefulWidgetState extends State<MyStatefulWidget> {
  int _count = 0;

  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Sample Code'),
      ),
      body: Center(
        child: Text('You have pressed the button $_count times.'),
      ),
      bottomNavigationBar: BottomAppBar(
        child: Container(
          height: 50.0,
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => setState(() {
          _count++;
        }),
        tooltip: 'Increment Counter',
        child: Icon(Icons.add),
      ),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
    );
  }
}
```


运行效果：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-demo6.png)  

# 2. Scaffold 的常用属性 

|属性|类型|说明|
|---|---|---|
|appBar|PreferredSizeWidget|顶部导航栏。最常用的实现类型为 **AppBar**|
|body|Widget|页面的主体内容|
|floatingActionButton|Widget-FloatingActionButton|一种特殊的漂浮Button。|
|floatingActionButtonLocation|FloatingActionButtonLocation|设置floatingActionButton的位置|
|persistentFooterButtons|List<Widget>|设置一组底部的Button|
|drawer|Widget-Drawer|抽屉菜单|
|backgroundColor|Color|设置背景颜色|
|bottomNavigationBar|Widget|底部导航栏。在 `persistentFooterButtons` 下面|
|resizeToAvoidBottomInset|bool|是否避免键盘弹起时挡住视图。默认为true。为false键盘会覆盖在视图上|

# 3.Scaffold 中的常用 Widget

按照 Scaffold 的定义的页面结构，介绍其中几个常用的 Widget。  

## 3.1 AppBar

Scaffold 中的顶部导航栏。  

AppBar 的常用属性：  

| 属性  |类型|说明|
|---|---|---|
| leading  |Widget|左边的 Widget。通常放返回键，或者 Drawer 开关|
|title|Widget|AppBar 的 title|
|actions|List<Widget>|右边的操作按钮|
|flexibleSpace|Widget-FlexibleSpaceBar|配合ScrollView实现下拉展开效果|
|bottom|PreferredSizeWidget|在AppBar下的 Widget|
|elevation|double|AppBar 的悬浮高度。值越大下方阴影就越大|
|backgroundColor|Color|背景颜色|
|brightness|Brightness|影响状态栏上的文字颜色。Brightness.light-黑色。Brightness.dark-白色|
|titleSpacing|double|设置 title 的空间|
|toolbarOpacity|double|透明度|
|bottomOpacity|double|bottom 的透明度|  

```
AppBar(
  title: Text('Scanffold Page'),
  // 返回
  leading: IconButton(
      icon: Icon(Icons.menu),
      onPressed: () {
        Navigator.pop(context);
      },
      tooltip: MaterialLocalizations.of(context).openAppDrawerTooltip),
  actions: <Widget>[
    IconButton(
        icon: Icon(Icons.share),
        onPressed: () {},
        tooltip: 'Share Button Clicked!')
  ],
  elevation: 5.0,
  backgroundColor: Colors.yellow,
  brightness: Brightness.dark,
  titleSpacing: 10,
  toolbarOpacity: 1.0,
  bottom: TabBar(
      labelColor: Colors.black,
      unselectedLabelColor: Colors.grey,
      indicatorColor: Colors.white,
      controller: tabController,
      tabs: tabs.map((e) => Tab(text: e)).toList()),
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/AppBar.png) 


## 3.2 BottomNavigationBar

Scaffold 中的底部导航栏。  

BottomNavigationBar 的常用属性：  

|属性|类型|说明|
|---|---|---|
|items|List<BottomNavigationBarItem>|导航栏上的item|
|onTap|ValueChanged<int>|当一个item被点击的时候会回调|
|currentIndex|int|设置当前选中的item|
|fixedColor|Color|选中的item的颜色|
|iconSize|Color|选中的item的颜色|  

```
BottomNavigationBar(
  items: [
    BottomNavigationBarItem(icon: Icon(Icons.home), title: Text('首页')),
    BottomNavigationBarItem(
        icon: Icon(Icons.favorite), title: Text('收藏')),
    BottomNavigationBarItem(
        icon: Icon(Icons.add_shopping_cart), title: Text('订单')),
  ],
  currentIndex: _currentIndex,
  fixedColor: Colors.blue,
  onTap: (index) {
    setState(() {
      _currentIndex = index;
    });
  },
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/BottomNavbar.gif)  

### 3.2.1 BottomNavigationBarItem

在 BottomNavigationBar 中，item 的类型需要为 **BottomNavigationBarItem**。  

看看 BottomNavigationBarItem 都有那些常用的属性：  

|属性|类型|说明|
|---|---|---|
|icon|Widget-Icon|item的图标|
|activeIcon|Widget|设置当item被选中时的Widget，没有设置，选中时也使用icon|
|title|Widget|item的文字|


```
BottomNavigationBarItem(icon: Icon(Icons.home)
      // 选中时
    , activeIcon: Icon(Icons.pages)
    , title: Text('首页')
)
```




 
