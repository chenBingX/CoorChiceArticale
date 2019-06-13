使用 HTML 来实现表格或者表格布局，需要先了解几个表格相关的标签。

# 表格相关的标签

在 HTML 中，通过  `<table>`（表格） 、`<tr>`（行）、`<td>`（列单元）、`<th>`（表头单元）等标签来构建一个表格，或者表格布局。

## `<table>`

`<table>` 标签的常用属性 **border** 表示表格的边框。如：`border="1"`，不要边框就写 `border=""`。

其余属性在 HTML5 中均不支持。

```
<table border="2">
    <tr>
        <td>属性</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>急速</td>
        <td>加快施法速度</td>
    </tr>
    <tr>
        <td>智力</td>
        <td>增加法术伤害</td>
    </tr>
</table>
```

<table border="2">
    <tr>
        <td>属性</td>
        <td>说明</td>
    </tr>
    <tr>
        <td>急速</td>
        <td>加快施法速度</td>
    </tr>
    <tr>
        <td>智力</td>
        <td>增加法术伤害</td>
    </tr>
</table>

## `<td>`

`<td>` 表示一个单元格。

这是它的两个常用属性：

|属性|描述|
|---|---|
|colspan|设置单元格所跨的列数。|
|rowspan|设置单元格所跨的行数。|

```
<table>
    <tr>
      <th>Name:</th>
      <td>小可爱惊雷</td>
    </tr>
    <tr>
      <!跨两行>
      <th rowspan="2">武器:</th>
      <td>法杖</td>
    </tr>
    <tr>
      <td>大宝剑</td>
    </tr>
</table>
```

<table>
    <tr>
      <th>Name:</th>
      <td>小可爱惊雷</td>
    </tr>
    <tr>
      <!跨两行>
      <th rowspan="2">武器:</th>
      <td>法杖</td>
    </tr>
    <tr>
      <td>大宝剑</td>
    </tr>
</table>