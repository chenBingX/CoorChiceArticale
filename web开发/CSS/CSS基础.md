
# 什么是 CSS？


CSS 相当于是一个定义了 HTML 中各种标签元素样式的一个清单文件（样式表）。

通过 CSS 可以将 HTML 中的元素样式分离开来，单独放到 `.css` 文件中，这样就能使得 HTML 仅仅专注于定义文档内容。

同时，定义在 `.css` 文件中的样式，是可以被复用的；对于 HTML 文档而言，它又可以随时整体的切换样式，而不需要对 HTML 文档进行修改。

# CSS 是如何协同 HTML 工作的？

首先了解一下 CSS 的格式：

```
选择器 {属性1: 值1; 属性2: 值2;}
```

例如：

```
p
{
color:red;
text-align:center;
}
```

上面的代码，定义 `<p>` 标签的样式。


## id 和 class 选择器

除了上面例子中出现的标签选择器，css 还支持 id 选择器和 class 选择器。

**id 选择器**就是将样式绑定到一个特定的 id 上，HTML 中使用这个 id 的元素会使用这个样式。

**id 选择器** 使用 `# + id值` 来开始定义，比如：

```
#id1
{
text-align: center;
}

<p id="id1">这个标签使用id1选择器对应的样式</p>
```

> 注，id不能以数字开头。

**class 选择器**就是定义了一个类型的样式，在 HTML 中，所有使用这个类型的元素，都使用这个样式。

**class 选择器**的定义格式：

1.`.+class名`，所有使用该 class 的元素都会使用该样式：

```
.center
{
text-align: center;
}

<p class="center">p元素使用了class</p>
<div class="center">
</div>
```

2.`元素名.+class名`，你可以在最前面加上标签限制，这样同样的类型，只有指定的标签才会应用这些样式：

```
p.center
{
text-align: center;
}

<p class="center">class为center的p元素会受到影响</p>
<h1 class="center">class为center的h1元素不会受到影响</p>
```
# 分组选择器

```
h1, h2, p
{
    color:blue;
}
```

几个相同的样式，可以合并在一起，就像上面那样。

# 嵌套选择器

- `p {}`：为所有 p 元素设置样式。

- `.one {}`：为所有使用了 `class="one" 的元素设置样式。

- `.one p {}`：为所有使用了 `class="one"` 中的 p 元素设置样式。

- `p.one {}`：为所有使用了 `class="one"` 的 p 元素设置样式。

# 属性选择器

```
[title]
{
    color:blue;
}
```

对所有包含了 `title` 属性的元素都有效。

# 属性和值选择器

```
[title=demo]
{
    color:blue;
}
```

对所有包含了 `title` 属性，并且值为 demo 的元素都有效。


# 为元素添加多个属性

```
<div class="css1 css2 css3">

</div>
```

元素可以添加多个配置，只需要用空格分隔。

# 属性兼容

很多 css 属性为了兼容，需要在属性名前增加标示：

```
box-sizing: border-box;

// 为了兼容
-webkit-box-sizing: border-box;
```

`-webkit-` 对应 Safari and Chrome


> [更多 CSS 例子](https://www.runoob.com/css/css-examples.html)



