Wrap 能够同时支持横向或者纵向的列表布局，就像 Row 或者 Column 那样。  

不同的是，当空间不足时，Wrap 能够自动换行！ 

# 1. 能够自动换行的 Wrap 

考虑以下代码：  

```
Row(
  children: <Widget>[
    MyWidget(),
    MyWidget(),
    MyWidget(),
    MyWidget(),
  ],
)

```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Row没有足够的空间.png)


将 Row 换成 Wrap：

```
Wrap(
  children: <Widget>[
    MyWidget(),
    MyWidget(),
    MyWidget(),
    MyWidget(),
  ],
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Wrap1.gif)  

# 2. Wrap 的常用属性 

|属性|类型|说明|
|---|---|---|
|children|List<Widget>|子 Widget|
|direction|Axis|方向。Axis.horizontal-水平方向；Axis.vertical-垂直方向|
|alignment|WrapAlignment|整体在可用区域内主轴方向对齐方式|
|runAlignment|WrapAlignment|整体在可用区域内交叉轴方向对齐方式|
|crossAxisAlignment|WrapCrossAlignment|交叉轴对齐方式|
|spacing|double|主轴方向的间距|
|runSpacing|double|交叉轴方向的间距|
|verticalDirection|VerticalDirection|扩展方向。VerticalDirection.down-向下（左）增长；VerticalDirection.up-向上增长|

