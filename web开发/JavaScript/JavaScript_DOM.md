# HTML DEOM 文档对象模型

![](https://gw.alicdn.com/tfs/TB15cEyXRSD3KVjSZFqXXc4bpXa-486-266.gif)

JavaScript 通过 `document` 对象可以获取到 HTML 中的元素。

## 通过 id 查找

通过标签id可以找到 HTML 中对应的元素。

```
var element = document.getElementById("elementId");
```

## 通过标签查找

```
var element = document.getElementById("elementId");

// 找出 element 下的所有标签名为 p 的元素
// y会是一个数组
var y=element.getElementByTagName("p");


```