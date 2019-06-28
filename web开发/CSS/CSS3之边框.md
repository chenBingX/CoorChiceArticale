CSS3 中，边框增加了圆角、阴影、图片边框的支持。

# 圆角边框

使用 **border-radius** 属性设置边框圆角。

```
div{
    border-radius:25px;
}
```

你可以分别设置每个角，从左到右依次为：左上、右上、右下、左下。

# 图片边框

通过 **border-image** 属性设置阴影效果。

```
div{
    border-image:url(border.png) 20px 20px round;
}
```

# 阴影

通过 **box-shadow** 属性设置阴影效果。

```

div{
    box-shadow:10px 10px 5px #888888;
}
```


