CSS3 中增加了一些新的背景属性。

# background-size

可以指定背景图片的大小。

```
div{
    background:url(img.png);
    background-size:100px 100px;
    background-repeat:no-repeat;
}
```

尺寸可以用以下形式：

|值|说明|
|---|---|
|数值|具体数值。px、em等|
|%|基于父区块尺寸的百分比|


# background-origin

设置背景图片的范围。

可选值：
    - content-box
    - padding-box
    - border-box

示意图：

![](https://raw.githubusercontent.com/chenBingX/img/master/其它/background-origin.gif)

# background-clip

剪裁指定范围背景图。

可选值：
    - content-box
    - padding-box
    - border-box


# 多重背景图

CSS3 支持设置多张背景图。

```
div{
    background-image:url(pic1.png),url(pic2.png);
    background-position:left top,right bottom;
}
```

使用 `,` 号分隔，属性与 `background-image` 顺序一一对应。

# 渐变色

[讲解地址](https://www.runoob.com/css3/css3-gradients.html)