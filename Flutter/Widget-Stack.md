Stack 用于组织一系列 Widget。以第一个 Widget 为基准，后续的 Widget 将会覆盖在其上。  

# 1.创建一个 Stack

```
Widget _buildStack() => Stack(
      // 影响非第一个 Widget 的相对位置
      alignment: Alignment(0.5, 0.5),
      children: [
        // 第一个 Widget
        CircleAvatar(
          backgroundImage: AssetImage('images/flutter_logo.png'),
          radius: 100,
        ),
        // 第二个 Widget
        Container(
          padding: EdgeInsets.all(3),
          decoration: BoxDecoration(
            color: Colors.black45,
          ),
          child: Text(
            'CoorChice',
            style: TextStyle(
              fontSize: 20,
              fontWeight: FontWeight.bold,
              color: Colors.white,
            ),
          ),
        ),
      ],
    );
```

把它放到一个 MaterialApp 中：  

```
main() => runApp(MaterialApp(
      title: "Flutter Demo",
      home: Scaffold(
          appBar: AppBar(title: Text("Flutter Demo")),
          body: Center(child: _buildStack())),
    ));
```  

看看效果：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter_demo3.png)  



# 2.Stack 常用属性

- alignment → AlignmentGeometry: 设置 Stack 中非第一个 Widget 的相对位置。  

- children → List<Widget>: 子 Widget。