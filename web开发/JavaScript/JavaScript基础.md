# JavaScript 基础

JavaScript 是 Web 开发中的一种编程语言。

它负责处理网页中的执行逻辑。

# JavaScript 用法

## 在 HTML 中使用

在 HTML 文件中，JavaScript 函数需要放在 `<script>` 标签中：

```
<script>
    function test(){
       document.getElementById(elementId);
    }
</script>
```

 `<script>` 标签可以放到 `<head>` 、`<body>` 或者最外层的任意位置。

## 外部引入使用

通常，JavaScript 的代码会被放在一个单独的 `.js` 文件中，这样方便逻辑分离和复用。

当然，在 `.js` 文件中，就不能在使用 `<script>` 标签了。

在 HTML 文件中要使用的时候，需要先引入 `.js` 文件：

```
<!DOCTYPE html>
<html>
<body>
    <script src="testScript.js"></script>
    <script>
        <!--定义在 testScript.js 中的函数-->
        test();
    </script>
</body>
</html>
```

需要注意，`.js` 文件必须在使用前引入。

## 不通过函数调用

JavaScript 放到 `<script>` 标签中，在 HTML 加载的时候就可以直接执行了。

```
<!DOCTYPE html>
<html>
<body>

<script>
a = 1;
b = 2;
<!-- 输出到控制台 -->
console.log(a+b);
</script>

</body>
</html>
```

# Alert弹窗

使用 `alert()` 函数（这是一个内置函数，可以直接使用）可以弹出提示框。

```
<button type="button" onclick="alert('Hi!')">点我！</button>
```

# 寻找 HTML 中元素

```
element=document.getElementById("elementId");
```

# 改变 HTML 元素属性

```
element=document.getElementById("");

// 改变内容
element.innerHTML="new content";

// 改变<img>标签图片
element.src="/images/new_img.png";

// 改变元素样式
element.style.color="#ff0000";
```



