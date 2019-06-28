```
h1:before{
    counter-increment:index;
    content: "Index " counter(index) ":"
}
```

在 CSS 中，可以通过 **counter-increment** 定义一个变量（比如：index），每有一个匹配的元素，这个变量就 +1，通过 `counter()` 函数可以取到当前的变量大小。