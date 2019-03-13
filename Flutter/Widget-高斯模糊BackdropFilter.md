使用 Flutter 实现高斯模糊的效果十分的便捷，这对广大 Android 开发者来说，是一个好消息。  

如果你想让一个视图高斯模糊化，只需要使用 Flutter 提供的 **BackdropFilter** 组件盖在一个视图就可以了。  

```
BackdropFilter(
    // 设置高斯模糊参数
    filter: ImageFilter.blur(sigmaX: 5, sigmaY: 5),
    // 设置蒙层
    child: Container(
      color: Colors.black.withOpacity(0),
))
```

如上代码，BackdropFilter 的参数只有两个。  

需要注意的是，它相当于一个蒙层，因此你需要使用类似于 **Stack** 的 Widget，来使 **BackdropFilter** 盖在你的视图上。  

e.g.:  

```
Stack(
  children: <Widget>[
    Center(
      child: Image.asset('images/pic1.jpg'),
    ),
    BackdropFilter(
        filter: ImageFilter.blur(sigmaX: 5, sigmaY: 5),
        child: Container(
          color: Colors.black.withOpacity(0),
        ))
  ],
)
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/BackdropFilter.png)