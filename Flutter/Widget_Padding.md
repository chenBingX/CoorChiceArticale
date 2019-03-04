在 Flutter 中，间隙也是一个 Widget，即 Padding。  

用 Padding 包裹一个 Widget，能够给它加上边距。  

# 1.一个例子

```
Widget build(BuildContext context) {
  // TODO: implement build
  return Scaffold(
    appBar: AppBar(),
      body:
      Align(
      alignment: Alignment.center,
        child: SizedBox(
            width: 300,
            height: 300,
            child:
            Container(color: Colors.amber, child: Padding(
              // 设置 Padding
              padding: EdgeInsets.all(50),
              // 子 Widget
              child: Container(
                alignment: Alignment.center,
                color: Colors.lightBlue,
              ),),)
      ),
      )
  );
}
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Padding1.png) 


Padding 主要就是两个属性：  

- padding → EdgeInsetsGeometry：通常会使用的它的 EdgeInsetsGeometry 实现类 EdgeInsets。  

- child → Widget：需要加边距的 Widget。  


在 Container 中，也集成了 Padding。  

# 2. EdgeInsets

在添加间距时 **EdgeInsets** 非常的常用。  

它提供了几个便捷的方法来添加间距：  

- EdgeInsets.all(double value)  
    
    四边都设置 value 间距。  
    
- EdgeInsets.only({left, top, right ,bottom })  
    
    可单独指定各个边的间距。
    
- EdgeInsets.symmetric({ vertical, horizontal })  
    
    在水平或垂直方向上对称均分所给参数，作为边距。

