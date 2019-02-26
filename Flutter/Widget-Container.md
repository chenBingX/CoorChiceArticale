
Container 通常用来包裹一个 Widget，为它增加 Padding、Border、Margin，设置背景等。

这是 Container 的结构：    


![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-container结构.png)  

# 创建 Container

例子：  

```
void main() => runApp(
    // 在中心
    Center(
        // 添加 Container
        child: Container(
            width: 200,
            height: 200,
            decoration: BoxDecoration(color: Colors.white),
            // 将子 Widget 设置为居中
            child: Center(
                // 添加 Container
                child: Container(
              width: 150.0,
              height: 150.0,
              // 设置边框装饰
              decoration: BoxDecoration(
                  color: Colors.deepPurpleAccent,
                  // 设置边框
                  border: Border.all(width: 10, color: Colors.black38),
                  // 设置圆角
                  borderRadius: BorderRadius.all(Radius.circular(8))),
              margin: EdgeInsets.all(4),
              // 添加图片
              child: Image.asset("images/dart_logo.png"),
            )))));
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-示例3.png)  


# Container 的常用属性

- child → Widget: 子 Widget。默认情况下，如果没有设置该属性，子 Widget 会被充满整个父 Widget，即使子 Widget 设置了 width 和 height。

- alignment → AlignmentGeometry: 配置子 Widget 在 Container 中的位置。  

- margin → EdgeInsetsGeometry: 设置边距。

- padding → EdgeInsetsGeometry: 设置 Padding。

- decoration → Decoration: 设置边框装饰。比如常用的 `BoxDecoration` 。

