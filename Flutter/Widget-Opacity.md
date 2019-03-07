在 Flutter 中，Widget 的透明也有一个对应的 Widget，他就是 **Opacity**。  

# 1.一个例子

这有一个页面：  

```
final widgets = [
  MyWidget(Colors.green);
  MyWidget(Colors.blue);
  MyWidget(Colors.yellow);
]
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Opacity1.png)  

现在，看看 Opacity 是如何让中间的蓝色方块消失的吧。  

```
final widgets = [
  MyWidget(Colors.green),
  Opacity(opacity: 0.0
    , child: MyWidget(Colors.blue
  ),
  MyWidget(Colors.yellow);
]
```

只需要为它裹上一层 **Opacity**。  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Opacity2.png)

# 2.Opacity 的属性

|属性|类型|说明|
|---|---|---|
|opacity|double|透明度。如果想要一个 Widget 消失，那就设置 0.0|
|child|Widget|子 Widget|