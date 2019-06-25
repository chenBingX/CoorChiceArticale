
# 如何修改列表的标志样式？

通过 **list-style-type** 或者 **list-style-image** 属性设置。

**list-style-type** 有一些预设值可选：

- none：无标志
- circle：圆形标志
- square：方形标志
- upper-roman：罗马数字标志
- lower-alpha：小写字母标志

**list-style-image** 可以设置图片标志：

```
ul
{
    list-style-image: url('tag.png');
}
```
# 如何开发一个导航栏？

通过使用列表元素，可以比较方便开发一个导航栏。

```
<style>
    ul.my{
        list-style-type:none;
        margin:0;
        padding:0;
        background-color:#333;
    }

    ul.my li{
        display:inline;
    }

    ul.my a{
        display:inline-block;
        padding: 9px 16px;
        font-size: 1.1em;
        color:#fff;
        text-decoration:none;
        text-align:center;
    }

    ul.my a:hover{
        background-color:#111;
    }

    .dropdown{
        display:inline-block;
    }

    .dropdown-content{
        display:none;
        min-width:100px;
        position:absolute;
        background-color:#f9f9f9;
        box-shadow:0px 8px 16px 0px rgba(0,0,0,0.2);
    }


    .dropdown:hover .dropdown-content {
        display:block;
    }

    div.dropdown-content a{
        display:block;
        color:#000;
        background-color:#e1e1e1;
        font-size:1em;
        text-align:left;

    }

    .dropdown-content a:hover{
        color:white;
        background-color:#000;
    }
</style>

<ul class="my">
    <li><a href="#d1">推荐</a></li>
    <li><a href="#d2">热点</a></li>
    <li><a href="#d3">科技</a></li>
    <li><a href="#d4">金融</a></li>
    <div class="dropdown">
        <a href="#0">更多</a>
        <div class="dropdown-content">
            <a href="#">项目一</a>
            <a href="#">项目二</a>
            <a href="#">项目三</a>
            <a href="#">项目四</a>
        </div>
    </div>
</ul>
```

看看效果：

<style>
    ul.my{
        list-style-type:none;
        margin:0;
        padding:0;
        background-color:#333;
    }

    ul.my li{
        display:inline;
    }

    ul.my a{
        display:inline-block;
        padding: 9px 16px;
        font-size: 1.1em;
        color:#fff;
        text-decoration:none;
        text-align:center;
    }

    ul.my a:hover{
        background-color:#111;
    }

    .dropdown{
        display:inline-block;
    }

    .dropdown-content{
        display:none;
        min-width:100px;
        position:absolute;
        background-color:#f9f9f9;
        box-shadow:0px 8px 16px 0px rgba(0,0,0,0.2);
    }


    .dropdown:hover .dropdown-content {
        display:block;
    }

    div.dropdown-content a{
        display:block;
        color:#000;
        background-color:#e1e1e1;
        font-size:1em;
        text-align:left;

    }

    .dropdown-content a:hover{
        color:white;
        background-color:#000;
    }
</style>

<ul class="my">
    <li><a href="#d1">推荐</a></li>
    <li><a href="#d2">热点</a></li>
    <li><a href="#d3">科技</a></li>
    <li><a href="#d4">金融</a></li>
    <div class="dropdown">
        <a href="#0">更多</a>
        <div class="dropdown-content">
            <a href="#">项目一</a>
            <a href="#">项目二</a>
            <a href="#">项目三</a>
            <a href="#">项目四</a>
        </div>
    </div>
</ul>


