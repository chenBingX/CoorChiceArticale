Flutter 提供了很多风格的 Button，帮助开发者可以快速实现不同效果的 Button。  

# 1.继承自 MaterialButton 的 Button

继承自 **MaterialButton** 的 **Button** 有以下常用的属性：  


|属性|说明|
|---|---|
|onPressed|必要的，Button点击回调|
|child|必要的，Button的内容|
|textColor|Button 文字内容|
|disabledTextColor|Button 禁用时的文字颜色|
|disabledColor|Button 禁用时的背景颜色|
|color|Button 背景颜色|
|highlightColor|Button 按下时的背景颜色|
|splashColor|点击时，水波动画中水波的颜色|
|colorBrightness|Button 主题，默认是浅色主题 |
|padding|Button 的填充|
|shape|Button 的形状|

## 1.1 RaisedButton 

**RaisedButton** 是有上浮效果的 Button，带有阴影。

```
RaisedButton(
  child: Text("normal"),
  onPressed: () => {},
  elevation:5.0,
);
```
RaisedButton 可以设置 `elevation`，指定 Button 的上浮高度。  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/RaisedButton.png)  

## 1.2 FlatButton

FlatButton 没有上浮的效果，默认没有背景颜色。  

```
FlatButton(
  child: Text("normal"),
  onPressed: () => {},
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/FlatButton.png)

# 2.IconButton

IconButton 继承自 StatelessWidget，用于创建图标类的 Button。  

```
IconButton(
  icon: Icon(Icons.thumb_up),
  onPressed: () => {},
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/IconButton.png)  

IconButton 可以设置一个 `tooltip` 属性，用于指定长按提示文案。  

```
IconButton(
  onPressed: () {},
  icon: Icon(Icons.favorite),
  tooltip: 'You clicked me!',
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/IconButton-ToolTip.png)
