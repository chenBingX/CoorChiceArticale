HTML 是一种描述网页的标记语言。

# HTML 书写格式

`<标签 属性=属性值 ..>内容</标签>`

和 XML 一样一样的。

通常头部会声明文档格式，有助于浏览器正确显示页面：

```
<!DOCTYPE html>
```

# 常用标签

## 头部标签 `<head>`

在 `<head>` 标签中，可以插入脚本，css文件以及各种 meta 信息。

## `<title>`

`<title>` 放在 `<head>` 节点下，用于定义浏览器工具栏的标题、收藏加标题或者搜索引擎的标题。

## `<base>`

定义一个基本链接地址。

看看如何使用：

```
<head>
    <base href="基础链接" target="_blank">
</head>
<body>
    <img src="/images/test.png" width="28" height="28"/>
</body>
```

`<img>` 中的 `src` 会自动在前面添加 `<base>` 中定义的基础链接。


## `<link>`

定义文档与外部资源的关系。

```
<head>
<link rel="stylesheet" type="text/css" href="theme.css">
</head>
```

`rel` 属性描述了当前文档与被链接资源/文档的关系:

|值|说明|
|---|---|
|stylesheet|要导入的样式表的URL|
|icon|导入表示该文档的图片|
|author|链接到作者信息|

## `<style>`

定义 HTML 的样式信息。

```
<head>
<style type="text/css">
    h1 {color:red;}
    p {color:blue;}
</style>
</head>
```

文档中 `<h1>` 标签文字会变成 red，`<p>` 标签会变为 blue。


## `<meta>`

meta 标签用于描述一些基本的元数据。

如：

```
<!-- 定义网页内容 -->
<meta name="description" content="Allspark Demo">

<!-- 定义作者 -->
<meta name="author" content="CoorChice">
```

## `<script>`

用于加载 js 脚本，或者插入一段 js。

## 标题标签 `<h1> - <h6>`

数字越大，字体越小。

```
<h1>这是一个标题</h1>
<h2>这是一个标题</h2>
<h3>这是一个标题</h3>
```

## 段落 `<p>`

```
<p>一个段落</p>
```

通过 `<hr>` 标签可以添加水平线。

通过 `<\br>` 可以换行。

## 文本相关标签

### 加粗

```
<b>这个文本是加粗的</b>
<strong>这个文本是加粗的</strong>
```

### 放大字体

```
<big>这个文本字体放大</big>
```

### 斜体

```
<i>这个文本是斜体的</i>
```

### 下标和上标

```
<sub>下标</sub>

<sup>上标</sup>
```

## 图像 `<img>`

```
<img src="/images/logo.png" width="258" height="39" />
```


## 链接 `<a>`

```
<a href="链接">这是一个链接</a>



<!-- 图片链接 -->
<a href=""链接>
<img border="0" src="图片" alt="HTML 教程" width="28" height="28"></a>


<!-- 跳转到文档指定元素位置 -->
<a href="#id">跳转到指定元素</a>
```

跳转链接放到属性 `href` 中。

