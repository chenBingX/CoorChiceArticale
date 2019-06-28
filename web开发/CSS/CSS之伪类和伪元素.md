# 什么是伪类？

**伪类** 是用来定义元素特殊状态下的样式的。

格式：

```
选择器:伪类 {...}
```

比如：

```
// 定义 <a> 标签未访问过状态的样式
a:link{color:#ff0000;}

// 定义 <a> 标签鼠标移动到元素上时的样式
a:hover{color:#ff00fff;}
```

# 一些特殊的伪类

## first-child 第一个元素

```
p:first-child{
    color:blue;
}
```

仅仅对 `<p>` 元素中的第一个子元素有效。

> [查看更多的伪类，到这个页面中](https://www.runoob.com/css/css-pseudo-classes.html)


# 什么是伪元素？

**伪元素** 是用于定义一个元素中的特殊部分内容（元素）的样式的。

格式：

```
选择器:伪元素 {...}
```

比如：

```
p:first-letter{
    color:blue;
}
```




