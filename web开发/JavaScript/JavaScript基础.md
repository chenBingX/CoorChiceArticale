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

## 函数对象

函数其实是一个对象。

```
var x = function (a, b) {return a * b};
var z = x(4, 3);
```

## 函数参数

JavaScript 像 Python 等一样，函数参数可以不指定类型：

```
function myFunction(x, y) {
    y = y || 0;
}
```

可以设置默认值：


```
function myFunction(x, y = 10) {
    // y is 10 if not passed or undefined
    return x + y;
}

myFunction(0, 2) // 输出 2
myFunction(5); // 输出 15, y 参数的默认值
```

# prototype 原型对象

所有的对象都会从一个原型对象 prototype 中集成属性和方法。

比如一个 Date 对象就从 Date.prototype 中继承。

当然，通过对象 prototype 可以给对象添加新的属性和函数：

```
function Person(first, last, age, eyecolor) {
  this.firstName = first;
  this.lastName = last;
  this.age = age;
  this.eyeColor = eyecolor;
}

Person.prototype.nationality = "English";
Person.prototype.name = function() {
  return this.firstName + " " + this.lastName;
};

var person = new Person("","",1,"");
var name = person.name();
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


# 获取输入框内容

```
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
</head>
<body>

<h1>JavaScript 验证输入</h1>

<p>请输入 1 到 10 之间的数字：</p>

<input id="numb">

<button type="button" onclick="myFunction()">提交</button>

<p id="demo"></p>

<script>
function myFunction() {
    var x, text;

    // 获取 id="numb" 的值
    x = document.getElementById("numb").value;

    // 如果输入的值 x 不是数字或者小于 1 或者大于 10，则提示错误 Not a Number or less than one or greater than 10
    if (isNaN(x) || x < 1 || x > 10) {
        text = "输入错误";
    } else {
        text = "输入正确";
    }
    document.getElementById("demo").innerHTML = text;
}
</script>

</body>
</html>
```

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