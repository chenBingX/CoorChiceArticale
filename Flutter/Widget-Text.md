Text Widget 是用来展示普通文字的。  

# 1.创建 Text

这样就能创建一个 Text 实例：  

```
Text(
  'Hello, $_name! How are you?',
  // 对齐方式
  textAlign: TextAlign.center,
  // 省略方式
  overflow: TextOverflow.ellipsis,
  // 文字风格
  style: TextStyle(fontWeight: FontWeight.bold),
  // 文字方向
  textDirection: TextDirection.ltr,

)
```

  

例子, `main.dart` 中：  

```
void main() => runApp(Container(
      decoration: BoxDecoration(color: Colors.white),
      child: Center(
        child: Text(
          "Hello Flutter",
          style: TextStyle(color: Colors.deepPurpleAccent, fontSize: 56),
          textDirection: TextDirection.ltr,
        ),
      ),
    ));
```  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter示例1.png)

# 2.Text 的属性
  

    - data → String: 内容，不能为 null。

    - style → TextStyle: 文字属性。可配置文字的颜色、大小、字体..
    
    - textDirection → TextDirection: 文字的方向。可选 `TextDirection.ltr` 或 `TextDirection.rtl` 。
    
    - textSpan → TextSpan: 富媒体的文字。  
    
    - maxLines → int: 最大行数。  
    
    - overflow → TextOverflow: 文字超长的处理。可选择值 clip（剪裁）、fade（渐隐）、ellipsis（省略）。  
    
    - textAlign → TextAlign: 文字水平对齐方式。常用值：left、right、center。
    

例子，在 `main.dart` 中：    

```
void main() => runApp(Container(
      decoration: BoxDecoration(color: Colors.white),
      // 设置 padding
      padding: EdgeInsets.all(16),
      child: Center(
        child: Text(
            "Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!",
            // 设置字体风格
            style: TextStyle(color: Colors.deepPurpleAccent, fontSize: 26),
            textDirection: TextDirection.ltr,
            // 设置对齐方式
            textAlign: TextAlign.right,
            // 设置最大行数
            maxLines: 2,
            // 设置超长处理方式
            overflow: TextOverflow.fade),
      ),
    ));
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter示例2.png)

## TextDecoration

尝试运行以下代码：  

```
class TestPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Container(
      alignment: Alignment.center,
      color: Colors.lightBlue,
      child: Text(
        'Hello, Flutter!',
      ),
    );
  }
}
```  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Text下面有线.png)  

你会发现，文字下面出现了两条黄线。  

它们是默认的装饰线，你可以通过设置 Text 的 `style` 把它们去掉。  

```
class TestPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Container(
      alignment: Alignment.center,
      color: Colors.lightBlue,
      child: Text(
        'Hello, Flutter!',
        // TextDecoration.none
        style: TextStyle(decoration: TextDecoration.none),
      ),
    );
  }
}
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Text下面无线.png)  

设置 **TextDecoration.none** 就可以去掉两条黄线了。  