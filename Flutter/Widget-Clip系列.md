Flutter 提供了 Clip 系列的 Widget，可用于剪裁一个 Widget 的形状。  

它使用起来十分方便。  

# 1. ClipRRect

ClipRRect 用于将一个 Widget 剪裁为圆角矩形。

常用属性：  

|属性|类型|说明|
|---|---|---|
|borderRadius|BorderRadius|边框的角度|
|clipper|CustomClipper<RRect>|自定义的剪裁器|
|clipBehavior|Clip|剪裁模式。Clip.antiAlias-消除锯齿；Clip.hardEdge-有锯齿|
|child|Widget|需要剪裁的 Widget|

e.g.:  

```
ClipRRect(
  borderRadius: BorderRadius.all(Radius.circular(12)),
  child: Image.asset('images/pic2.jpg'),
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/ClipRRect.png)

# 2. ClipOval

ClipOval 用于将一个 Widget 裁剪为椭圆形。  

常用属性：  

|属性|类型|说明|
|---|---|---|
|clipper|CustomClipper<Rect>|自定义的剪裁器|
|clipBehavior|Clip|剪裁模式。Clip.antiAlias-消除锯齿；Clip.hardEdge-有锯齿|
|child|Widget|需要剪裁的 Widget|


e.g.:  

```
ClipOval(
  clipBehavior: Clip.hardEdge,
  child: Image.asset('images/pic2.jpg'),
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/ClipOval.png)


# 3. ClipPath

ClipPath 用于将一个 Widget 裁剪为自定义的形状。  

常用属性：  

|属性|类型|说明|
|---|---|---|
|clipper|CustomClipper<Path>|自定义的剪裁器。如果不设置，为矩形|
|clipBehavior|Clip|剪裁模式。Clip.antiAlias-消除锯齿；Clip.hardEdge-有锯齿|
|child|Widget|需要剪裁的 Widget|


e.g.:  

```
ClipPath(
  clipper: ShapeBorderClipper(shape: CircleBorder()),
  child: Image.asset('images/pic2.jpg'),
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/ClipPath.png)

