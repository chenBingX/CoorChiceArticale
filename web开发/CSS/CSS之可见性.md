# 如何隐藏一个元素？

```
p.gone
{
    visibility:hidden;
}
```

<hr>

这种方式元素虽然隐藏了，但仍然会占用空间。

```
p.gone
{
    display:none;
}
```

这种方式，元素即隐藏了，也不占用空间。

# 如何让区块变为一行？

```
p.line
{
    display:inline;
}

<p class="line">第1行</p>
<p class="line"> 第2行</p>
```

效果如下。`<p>` 标签元素不再分段了，而是变成了一行。

<style>
p.line
{
    display:inline;
}
</style>
<p class="line">第1行</p>
<p class="line"> 第2行</p>

# 如何让非区块元素分块？

```
p.block
{
    display:block;
}

<span class="block">第1个元素</span>
<span class="block">第2个元素</span>
```

效果如下。`<span>` 标签元素不再是一行了，而是变成了分行。

<style>
span.block
{
    display:block;
}
</style>
<span class="block">第1个元素</span>
<span class="block">第2个元素</span>