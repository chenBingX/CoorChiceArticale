# 如何设置透明度？

通过 **opacity** 属性可以设置透明度。

```
img{
    opacity:0.4;
}
```

在 IE8 以前，使用 **filter** 属性设置透明度。

```
img{
    filter:alpha(opacity=40);
}
```

# 如何展示图片的特定部分？

```
img.home{
    width: 40px;
    height: 40px;
    background: url(xxx.png) 20 20;
}
```

上面的定义表示，使用 **xxx.png** 图片从 `(20, 20)` 位置起，取一个 `40px x 40px` 大小的图片子部分，作为元素的背景。

使用：

```
<img class="home" src="透明图片.png">
```

由于 `<img>` 元素需要设置 **src**，不然就会出现默认图，所以给它设置一个透明图片，来让背景图显示出来。



