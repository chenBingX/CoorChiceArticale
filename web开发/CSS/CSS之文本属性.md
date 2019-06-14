你当然可以通过 CSS 来设置文本的文字格式了。

# 如何设置字体样式？

- 通过 **font-family** 属性设置：

```
p
{
    font-family:"Times New Roman", Timers, serif;
}
```

这个属性可以配置一组字体，中间通过 `,` 分隔。浏览器会从左到右尝试加载一个它所支持的字体。

- 通过 **font-style** 属性设置：

```
p.normal
{
    font-style:normal;
}
p.italic
{
    font-style:italic;
}

<p class="normal">使用了 normal 字体</p>
<p class="italic">使用了 italic 字体</p>
```

# 如何设置字体的大小？

通过 **font-size** 属性可以配置字体的大小。

**font-size** 的值有两种设置方式。

- 使用像素

```
p
{
    font-size:16px;
}
```

- 使用 **em** 单位

```
h1
{
    font-size:2.5em;
}
```

**em** 表示相对一个浏览器的字体默认大小的比值。

如浏览器的默认大小为 16px，如果你想设置一个 40px 的字体，可以设置 `font-size:2.5em` (40/16=2.5)。

这样当浏览器窗口被缩放时，字体大小就能准确的跟着缩放了。

通常，通过 `百分比 + em` 的方式，可以完美解决字体适配问题。

```
body
{
    font-size:100%;
}
h1
{
    font-size:1.5em;
}
```

即将 `<body>` 元素的默认字体大小设置百分比，然后在设置其它字体的 **em**。

# 如何设置字体的粗细？

通过 **font-weight** 属性可以设置字体的粗细。


```
h1
{
    font-weight:normal;
}
h2
{
    font-weight:900;
}
```

你可以直接设置几个固定的类型：normal、bold。

或者直接设置数值。

# 如何设置文本的颜色？

通过 **color** 属性来设置文本颜色。


```
p.color1
{
   color:red;
}

p.color2
{
   color:#ffffff;
}

p.color3
{
   color:rgb(255,0,0);
}
```

如上，**color** 的值可以是内置的 **颜色名称**，也可以是 **十六进制格式的色值**，或者直接使用 `rgb(255,255,255)` 的方式设置。


# 如何设置文字的对齐方式？

通过 **text-align** 属性可以设置文字的对齐方式。

```
p.align
{
    text-align:left;
}
```

可选值有：

|值|说明|
|---|---|
|left|左排列|
|right|右排列|
|center|居中|
|justify|两端对齐|


# 给文字加点装饰？

通过 **text-decoration** 属性可以设置文字装饰（就是些线条！）。

```
<style>
p.decoration1
{
    text-decoration: overline;
}
p.decoration2
{
    text-decoration: line-through;
}
p.decoration3
{
    text-decoration: underline;
}
</style>

<p class="decoration1">这是 decoration1 的 overline 效果</p>
<p class="decoration2">这是 decoration2 的 line-through 效果</p>
<p class="decoration3">这是 decoration3 的 underline 效果</p>
```
效果如下：


<style>
p.decoration1
{
    text-decoration: overline;
}
p.decoration2
{
    text-decoration: line-through;
}
p.decoration3
{
    text-decoration: underline;
}
</style>

<p class="decoration1">这是 decoration1 的 overline 效果</p>
<p class="decoration2">这是 decoration2 的 line-through 效果</p>
<p class="decoration3">这是 decoration3 的 underline 效果</p>

如果你想把超链接上的下划线去掉，可以这么干：

```
<style>
a
{
    text-decoration:none;
}
</style>

<a href="http://www.example.com">效果显著！</a>
```

# 如何设置文字间距？

- 设置字符间距

```
p
{
    letter-spacing:3px;
}
```

- 这只词间距

```
p
{
    word-spacing:10px;
}
```

- 设置文本缩进

```
p
{
    text-indent:50px;
}
```

指定文本第一行的缩进空间。

# 如何转换文本的大小写？

通过配置 **text-transform** 属性，可以设置文本大小写。

```
p.uppercase {text-transform:uppercase;}
p.lowercase {text-transform:lowercase;}
p.capitalize {text-transform:capitalize;}
```