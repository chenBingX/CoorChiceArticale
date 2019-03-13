GridView 可以用来构建一个二维的表格视图。  

# 1.创建 GridView  

```
Widget _buildGridView() {

  // 用于创建子 Item 的函数
  Widget _buildItem(var index) {
    return ...
  }

  List<Widget> createDatas() {
    var items = <Widget>[];
    // 创建 20 条数据
    for (var i = 0; i < 20; i++) {
      items.add(_buildItem(i));
    }
    return items;
  }

  return GridView(
    // 设置 GridView 的子 Widget
    children: createDatas(),
  );
}
```

重点就是给 GridView 传一个 List<Widget> 数组。

运行效果：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/GridViewDemo.png)  

# 2.常用属性

|属性|类型|说明|
|---|---|---|
|scrollDirection|Axis|滚动方向。Axis.vertical-垂直方向（默认）；Axis.horizontal-水平方向|
|reverse|bool|是否反向排列 children。默认fasel|
|controller|ScrollController|滚动控制器|
|primary|bool|如果 controller 为null，该项为 true，会使用 PrimaryScrollController|
|physics|ScrollPhysics|控制滚动效果。默认 AlwaysScrollableScrollPhysics|
|gridDelegate|SliverGridDelegate|用于控制子 Widget 在 GridView 中的布局的代理。通常会使用 **SliverGridDelegateWithFixedCrossAxisCount**|
|shrinkWrap|bool|默认为 false。是否根据子 Widget 的总长度来设置 ListView 的长度|
|children|List<Widget>|子 Widget 数组|
|padding|EdgeInsetsGeometry|间距|

其中，gridDelegate 中比较重要的参数有：  

|属性|类型|说明|
|---|---|---|
|crossAxisCount|int|交叉轴方向上的item个数|
|mainAxisSpacing|double|主轴方向上item的间距|
|crossAxisSpacing|double|交叉轴方向上item的间距|
|childAspectRatio|double|item （主轴长度/交叉轴长度）的比值，控制每个item的区域大小|

# 3.其它构建 GridView 的方式 

## 3.1 GridView.builder

通过 **GridView.builder** 可以快速的构建一个列表。  

```
GridView.builder({
  // GridView 公共参数已省略  
  ...
  @required IndexedWidgetBuilder itemBuilder,
  int itemCount,
  ...
})
```

以上是 ListView.builder 的核心参数。  

|属性|类型|说明|
|---|---|---|
|itemBuilder|IndexedWidgetBuilder|子 Widget 构建器|
|itemCount|int|列表项的数量|

```
_buildBody() {
  return GridView.builder(
      itemCount: productList.length,
      itemBuilder: (BuildContext context, int index) {
        return _buildItem(context, index);
      });
}
```


## 3.2 GridView.count

|属性|类型|说明|
|---|---|---|
|crossAxisCount|int|交叉轴方向上的item个数|
|mainAxisSpacing|double|主轴方向上item的间距|
|crossAxisSpacing|double|交叉轴方向上item的间距|
|childAspectRatio|double|item （主轴长度/交叉轴长度）的比值，控制每个item的区域大小|

```
_buildBody() {
  return GridView.count(
      crossAxisCount: (orientation == Orientation.portrait) ? 2 : 3,
      mainAxisSpacing: 4.0,
      crossAxisSpacing: 4.0,
      childAspectRatio: (orientation == Orientation.portrait) ? 1.0 : 1.3,
      children: _buildItems(),
      );
}
```