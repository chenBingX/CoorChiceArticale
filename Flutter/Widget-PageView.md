PageView 是一个滑动切换页面的 Widget。  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/PageView1.gif)  

# 1.如何创建一个PageView

1. 创建一个 PageController
    
    ```
    pageController = PageController();
    ```
    
2. 创建一个 PageView，传入 PageController
    
    ```
    PageView(
      controller: pageController,
      children: <Widget>[
        _buildPage_1(context),
        _buildPage_2(context),
      ],
    ),
    ```
    
这样，一个 PageView 就创建完成了。  

# 2.PageView 的常用属性

|属性|类型|说明|
|---|---|---|
|scrollDirection|Axis|滚动方向。Axis.vertical-纵向滚动；Axis-horizontal-横向滚动（默认值）|
|reverse|bool|是否逆向排列子 Widget|
|controller|PageController|PageView 的控制器|
|physics|ScrollPhysics|控制 PageView 的滚动效果。默认使用 PageScrollPhysics|
|pageSnapping|bool|设置是否每个 Page 滑动到一定程度就自动滚动到完全展示。默认为true。如果为false，滚动效果有点 ScrollView|
|onPageChanged|ValueChanged<int>-(index){}|当 Page 改变时回调用|

# 3.PageView.builder

类似于 ListView 等，可以通过一个指定的构造器，快捷的创建出多个类似的 Page。

```
PageView.builder(
  controller: PageController(),
  itemBuilder: (BuildContext context, int index) {
    return _buildPage_2(context);
  },
  itemCount: 5,
)
```

