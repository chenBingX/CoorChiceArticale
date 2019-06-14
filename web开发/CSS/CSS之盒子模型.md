
![](https://www.runoob.com/images/box-model.gif)

所有的 HTML 元素都可以抽象成上图的结构。

你可以为一个 HTML 元素配置这些属性。

# Margin 和 Padding 属性

![](https://www.runoob.com/wp-content/uploads/2013/08/VlwVi.png)

Margin 相关的属性有：

- margin-top：上边距。

- margin-right：右边距。

- margin-bottom：下边距。

- margin-left：左边距。

- margin：简写形式，从左到右分别为 **上、右、下、左**。如：`margin:25px 50px 75px 100px`。

Margin 的取值：

|值|说明|
|---|---|
|auto|依赖于浏览器|
|数值|px、em等|
|百分比|使用百分比|

<hr>

Padding 相关的属性有：

- padding-top：上内边距。

- padding-right：右内边距。

- padding-bottom：下内边距。

- padding-left：左内边距。

- padding：简写形式，从左到右分别为 **上、右、下、左**。如：`padding:25px 50px 75px 100px`。

Padding 的取值：

|值|说明|
|---|---|
|数值|px、em等|
|百分比|使用百分比|

# Border 边框属性

Border 属性支持统一设置：

- border-style：边框的样式。支持：
    - dotted：点边框。大多数浏览器无效。
    - dashed：虚线边框。大多数浏览器无效。
    - solid：实线。
    - none：无边框。

- border-color：边框的颜色。

- border-width：边框的宽。

当然，你可以单独指定任意一边的属性：

```
p.one
{
    border-top-style:solid;
    border-top-color:red;
    border-top-width:2px;
}
```

其它几边也是类似的。

这些属性也可以使用简写形式：

```
p.one
{
    border:solid blue 1px;
    border-left:solid red 5px;
}
```

# outline 轮廓属性

![](https://www.runoob.com/images/box_outline.gif)

在元素的最外围，即 Margin 之外，可以增加一圈轮廓线。

- outline-style：边框样式。和 **border** 中相同。

- outline-color：边框颜色。

- outline-width：边框的宽。可以是具体数值，也可以是几个固定值 thin（细）、medium（粗）、thick（粗）。

