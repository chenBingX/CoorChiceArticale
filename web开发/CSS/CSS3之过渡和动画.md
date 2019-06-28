# 过渡

**过渡** 是指元素从一个状态，变到另一个状态时的过渡效果。

```
div{
    width:50px;
    transition:width 2s;
}

div.hover{
    width:100px;
}
```

当 `<div>` 元素从默认状态变为 *hover** 状态时，会有一个2s的过渡效果。

多个过渡效果，使用 `,` 号分隔。

```
div{
    transition:width 2s, height 2s, color:2;
}
```

# 如何制作动画？

## 1. 先定义一个动画

```
@keyframes myAnim{
    from {
        background:red;
    }
    to {
        background:yellow;
    }
}
```

**from** 到 **to** 等价于 **0%** 到 **100%**，即：

```
@keyframes myAnim{
    0%{
        background:red;
    }
    100%{
        background:yellow;
    }
```

你也可以多指定一些中间状态：

```
@keyframes myAnim{
    0%{
        background:red;
    }
    25%{
        background:blue;
    }
    100%{
         background:yellow;
    }
}
```


## 2. 使用动画

语法：

```
animation: name duration timing-function(相当于插值器，如linear) delay(延迟开始) iteration-count(重复次数，infinite表示无数次) direction fill-mode play-state(设置暂停或运行);
```

```
div{
    animation:myAnim 5s;
}
```

只需要设置要使用的动画名，以及动画的时长就可以了。

