# 基本类型

## Number 数字

可以是小数、整数、科学计数法 e。

```
a = 1;
b = 3.1415;
c = 21e2;
```

## String

```
a = "双引号";

b = '当引号';

```

## Array数组

```
a = [1, 2, 3];

var cars = new Array();
cars[0]="haha";
cars[1]="hahaha";

var cars2 = new Array("haha", "hahaha");
```

## Object对象

```
car = {color:"#f1f1f1", type:"Tesla"};

// 使用
color = car["color"];
```

对象中可以添加函数，它将被作为对象的属性：

```
var car={
    color: "#000000",
    price: 1000000,

    run: function()
    {
        // do something
    }
}

// 使用
car.run();
```


# 创建变量

通过 `var` 关键字定义变量：


```
var a, b;
a = 1;
b = 2;
```

其实 `var` 也是可以省略的。

## 局部变量

在函数中声明的是局部变量。

在函数运行完后会被删除。

## 全局变量

在函数外声明的是全局变量。

在网页被关闭时会被释放。

## 未声明变量

如果给没有声明的变量赋值，该变量将会被作为一个 `window` 的属性。

```
a = "windowField";

console.log(window.a)
```


# 注释

可以通过 `//` 或者 `/* */`


# 其它

## typeof

检测变量的类型。

```
typeof "John"                // 返回 string
typeof 3.14                  // 返回 number
typeof false                 // 返回 boolean
typeof [1,2,3,4]             // 返回 object
typeof {name:'John', age:34} // 返回 object
```

## 类型转换

### 转换为字符串

```
String(x)         // 将变量 x 转换为字符串并返回
String(123)       // 将数字 123 转换为字符串并返回
String(100 + 23)  // 将数字表达式转换为字符串并返回


// 或者
x.toString()
(123).toString()
(100 + 23).toString()
```

## 寻找字符串

```
var str = "Visit Runoob!";
var n = str.search("Runoob");

---
6
```

## 替换字符串

替换第一个：

```
var str = document.getElementById("demo").innerHTML;
var txt = str.replace("Microsoft","Runoob");
```

全局替换：

```
str.replace(/Microsoft/g,"Runoob")
```

格式 `/被替换字符串/g'/

## test() 检测是否符合正则

```
var patt = /e/;
patt.test("The best things in life are free!");
```

## 严格模式

在严格模式下，不能使用未声明的变量。

```
x = 3.14 // 错误的书写方式


---正确---

var x = 3.14
```

如何开启严格模式？

在 JavaScript 1.8.5 及以上版本中，在文件头部增加：

```
"use strict"
```

# this
1. 单独的this指全局的 window 对象。

```
var x = this;
// this就是window对象
``


2. 事件中的 this 指接收事件的元素：

```
<button onclick="this.style.display='none'">
点我后我就消失了
</button>
```

3. 对象中的 this 指对象本身。

**循环、运算符号、条件句、异常** 等和 Java 句法一样。

