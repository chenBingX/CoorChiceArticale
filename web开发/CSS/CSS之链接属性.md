对于链接标签 `<a>` 来说，它有几个不同的状态：

- link：未访问过的状态

- visited：访问过的状态

- hover：鼠标移动到连接上的状态

- active：鼠标点击时的状态


你可以单独为每一种状态设置样式：

```
a.one:link
{
    color:blue;
    font-size:20px;
}
a.one:hover
{
    color:red;
    font-size:50px;
}
a.one:visited
{
    color:#e1e1e1;
    font-size:20px;
}

<a class="one" href="null">尝试一些这个链接</a>
```

<style>
a.one:link
{
    color:blue;
    font-size:20px;
}
a.one:hover
{
    color:red;
    font-size:50px;
}
a.one:visited
{
    color:#e1e1e1;
    font-size:20px;
}
</style>

<a class="one" href="null">尝试一些这个链接</a>