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