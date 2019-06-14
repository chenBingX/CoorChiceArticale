你可以通过 **position** 属性，配合 `top、right、bottom、left` 来设置元素的位置。

单独使用 `top、right、bottom、left` 属性将不会有任何效果。

**position** 的取值：

- static：默认值。和没有设置一样。

- fixed：相对于浏览器窗口固定偏移。不会跟随滚动。

- relative：相对于元素本身的位置偏移。

- absolute：相对于父容器的边界来偏移。

- sticky：当元素未滚出区域时，相当于 relative；当页面滚出区域时，相当于 fixed。


# 如果元素重叠了，如何设置谁在上呢？

```
img.b
{
    z-index:-1;
}
```

通过 **z-index** 属性可以组织元素的 z 轴顺序。

-1 表示在最底层。