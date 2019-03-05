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

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/AppBar.png)


## 3.2 BottomNavigationBar








 
