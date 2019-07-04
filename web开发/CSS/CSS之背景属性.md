

在 HTML 中的几乎每个元素，都可以为它们配置背景样式。

背景常用的配置项：

```
background-color: 背景颜色

background-image: 背景图片。如 background-image:url('图片地址')
background-position: 背景图片的位置
background-repeat: 背景图片的重复模式。可以 水平重复 repeat-x；垂直重复 repeat-y。默认为 repeat-y。如果不需要就设置 no-repeat。
background-attachment: 背景图片是否随页面滚动。scroll-跟随滚动；fixed-固定
background-size: 背景图片大小。具体数值、auto（按图片比例缩放）、cover（按比例以最大边缩放）、contain（按比例以最小边缩放）
```

下面的例子，会将页面变为红色背景：

```
<style>
body
{
background-color:red;
}
</style>
```

下面的例子，会给页面添加图片背景，并且水平重复：

```
<style>
body
{
background-image:url('https://www.runoob.com/try/demo_source/gradient2.png');
background-repeat:repeat-x;
}
</style>
```

这个例子将所有的 `<p>` 标签背景配置为图片：

```
<style>
p
{
background-image:url('https://www.runoob.com/try/demo_source/gradient2.png');
background-repeat:repeat-x;
}
</style>

<p>看看变没变？</p>
```

效果如下：

<iframe height="50px" seamless frameborder="1" srcdoc="
<style>
p
{
background-image:url('https://www.runoob.com/try/demo_source/gradient2.png');
background-repeat:repeat-x;
}
</style>
<p>看看变没变？</p>
">
</iframe>

设置背景样式，还可以通过 **background** 属性，这样就可以不用细分每个属性了，可以写到一起：

```
<style>
body
{
 background:pink url('https://www.runoob.com/try/demo_source/gradient2.png' no-repeat right top);
}
</style>
```

# 背景图片如何居中？

```
div{
    background-img: url("img.png");
    // 背景图居中
    background-position: center;

}
```