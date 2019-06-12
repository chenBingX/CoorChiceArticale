
CSS（Cascading Style Sheets） 被用来描述 HTML 元素的样式，包括它们的颜色、边框、字体、对齐方式..

# 使用 CSS 的方式

## 内联式

直接在标签的 `style` 属性中定义该标签的样式。

```
<p style="color:blue;margin-left:20px;">这是一个内联CSS示例</p>
```

看看上面的效果吧：

<div style="border: 1px solid #e1e1e1";width:max-content;height:min-content;>
    <p style="color:blue;margin-left:50px;height:min-content">这是一个内联CSS示例</p>
</div>

如上可以看到，在 `style` 中定义标签的样式的格式:

```
style="属性名1:值1;属性名2:值2;"
```

## 内部样式表

你可以直接在 `<head>` 标签中，通过 `<style>` 标签，来定义指定标签的样式。

```
<head>

    <style type="text/css">
        p {color:red}
    </style>
</head>
```

上面的代码中，将 `<p>` 标签的文本颜色定义为了 `red`，即 红色。

现在，你使用的所有 `<p>` 的字体颜色，都将变成红色。


## 外部样式表

通过 `<link>` 标签，你可以引入一份在外部定义好的样式表。

```
<head>
    <link rel="stylesheet" type="text/css" href="mystyle.css">
</head>
```

通过 `<link>` 标签定义引入了 `href` 所指向的 css 文档。

当然， `href` 定义的 css 文档位置，可以指向一个相对路径 `/themes/mystyle.css`。

或者一个绝对路径 `http://www.example.com//themes/mystyle.css`。


> [关于更多的 `<link>` 标签说明，点击这里](https://raw.githubusercontent.com/chenBingX/CoorChiceArticale/master/web开发/html/HTML之link标签.md)。