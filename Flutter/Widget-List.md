List 被用于展示列表数据。  

# 1.创建 ListView  

```
Widget _buildListView() {

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

  return ListView(
    // 设置 ListView 的子 Widget
    children: createDatas(),
  );
}
```

重点就是给 ListView 传一个 List<Widget> 数组。

运行效果：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-demo7.png)  

# 2.常用属性

- childrenDelegate → SliverChildDelegate：用于提供子 Widget 的代理。  

- itemExtent → double：限制 item 的高度。  

- children → List<Widget>：item 数组。

