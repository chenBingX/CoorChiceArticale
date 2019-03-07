在 Flutter 中，有一个类似于 Android 中的 ScrollView 的 Widget，他就是 SingleChildScrollView。  

它只能接受一个子 Widget，但它能让诸如 Column 等的 Widget 变得可以滚动。  

# 1.使用 SingleChildScrollView 让 Widget 滚动起来

```
for (var i = 0; i < 50; i++) {
  items.add(Container(
    alignment: Alignment.center,
    height: 150,
    color: colors[random.nextInt(colors.length)],
  ));
}
```

当我们循环创建了 50 个 Widget，把它们放到 Column 中。  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/SingleChildScrollView1.png)  


如果，使用 SingleChildScrollView 把 Column 包裹起来：  

```
SingleChildScrollView(
  child: Column(
    children: _buildColumnItems(context),
  ),
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/SingleChildScrollView2.gif)  

它能滚动了！  

# 2. SingleChildScrollView 的常用属性

|属性|类型|说明|
|---|---|---|
|child|Widget|子 Widget|
|scrollDirection|Axis|滚动方向。Axis.vertical-垂直方向(默认值); Axis.horizontal-水平方向|
|reverse|bool|是否逆向排布子 Widget。默认为false|
|controller|ScrollController|用于控制 ScrollView 滚动的控制器|
|physics|ScrollPhysics|控制滚动效果。默认 AlwaysScrollableScrollPhysics|
|primary|bool|如果 controller 为null，该项为 true，会使用 PrimaryScrollController|
|padding|EdgeInsetsGeometry|间距。通常使用 EdgeInsets|