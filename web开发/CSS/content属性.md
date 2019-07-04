**content** 属性与 **::before** 和 **::after** 伪元素一起使用，用于在指定元素前、后插入内容。

```
<a href="http://www.mozilla.org/en-US/">Home Page</a>

a::before{
    content: url(http://www.mozilla.org/favicon.ico) " MOZILLA: ";
    font:    x-small Arial,freeSans,sans-serif;
    color:   gray;
}
```