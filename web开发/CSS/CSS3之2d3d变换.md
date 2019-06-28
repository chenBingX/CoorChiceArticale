
# 2D变换

## translate 平移

```
div{
    transform:translate(50px,100px);
}
```

## rotate 旋转

```
div{
    transform:rotate(30deg);
}
```

## scale 缩放

```
div{
    transform:scale(2,3);
}
```

## skew 切错

```
div{
    transform:skew(30deg,20deg);
}
```

## matrix

当然，也可以直接使用一个 2x3 的矩阵来变换。


```
div {
    transform:matrix(1,1,2,2,3,3);
}
```

# 3D变换

## 3D旋转

3D变换可以沿着 x、y 轴进行旋转：

```
div.one{
    transform:rotateX(20deg);
}

div.two{
    transform:rotateY(20deg);
}

```

## 3D缩放

```
div {
    transform:scale3d(x,y,z);
}
```


