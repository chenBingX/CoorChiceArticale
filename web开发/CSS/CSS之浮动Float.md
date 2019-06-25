

**float** 属性用于设置元素被周围元素的环绕方式。

```
span
{
    float: left;
}
```

取值：

- left：元素居左

- right：元素居右

- none：不浮动


例子：

```
<style>
span.float
{
    float: left;
    font-size:180%;
    line-height:80%;
}
</style>


<p>
<span>Hi！</span>元素的水平方向浮动，意味着元素只能左右移动而不能上下移动。

一个浮动元素会尽量向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。

浮动元素之后的元素将围绕它。

浮动元素之前的元素将不会受到影响。
</p>

```

效果：

<style>
span.float
{
    float: left;
    font-size:180%;
    line-height:80%;
}
</style>


<p>
<span class="float">Hi！</span>元素的水平方向浮动，意味着元素只能左右移动而不能上下移动。

一个浮动元素会尽量向左或向右移动，直到它的外边缘碰到包含框或另一个浮动框的边框为止。

浮动元素之后的元素将围绕它。

浮动元素之前的元素将不会受到影响。
</p>