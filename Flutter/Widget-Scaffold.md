Scaffold 提供了快速构建 MaterialDesign 风格的页面的方案。  

Scaffold 组织的页面结构包括：AppBar，body（页面主体内容）、bottomNavigationBar（底部导航栏）、floatingActionButton 等。  

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