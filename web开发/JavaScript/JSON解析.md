# JSON 解析

1. `JSON.parse()` 将 JSON 字符串解析为 JavaScript 对象
2. `JSON.stringify()` 将 JavaScript 对象转换为 JSON 字符串

# javascript:void(function())用法

`javascript:void(function())` 表示需要执行 function() 逻辑，但不返回值。

在链接中会使用到：

```
<a href="javascript:void(0)">相当于个占位</a>
```

# 点击跳转指定位置

使用 `# + id` 可以跳转到指定元素位置：

```
<a href="#p1">点击跳转</a>


...
<p id="p1">一个未知的位置</p>
```