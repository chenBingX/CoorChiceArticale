
Container 是一个集大成的 widget，在开发过程中使用相当频繁。 

这是 Container 的结构：

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-container结构.png)  

# 1.创建 Container

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


# 2.Container 的常用属性

- child → Widget: 子 Widget。

- alignment → AlignmentGeometry: 配置子 Widget 在 Container 中的位置。  

- margin → EdgeInsetsGeometry: 设置边距。

- padding → EdgeInsetsGeometry: 设置 Padding。

- decoration → Decoration: 设置装饰，在 child 下面。比如常用的 `BoxDecoration` 。

- foregroundDecoration → Decoration: 设置装饰，在 child 上面。

- color → Color: 设置区域颜色。  
  
  ⚠️ 注意，当你在 `decoration` 中，通过 **BoxDecoration**
  设置了颜色之后，就不能再设置该属性了。  
  


