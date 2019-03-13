ListView 被用于展示列表数据。  

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

- itemExtent → double：限制子 Widget 的高度。

- children → List<Widget>：子 Widget 数组。

- shrinkWrap → bool：默认为 false。是否根据子 Widget 的总长度来设置 ListView 的长度。  


# 3.其它构建 ListView 的方式 

## 3.1 ListView.builder

通过 **ListView.builder** 可以快速的构建一个列表。  

```
ListView.builder({
  // ListView公共参数已省略  
  ...
  @required IndexedWidgetBuilder itemBuilder,
  int itemCount,
  ...
})
```

以上是 ListView.builder 的核心参数。  

- itemBuilder：子 Widget 构建器，类型为 IndexedWidgetBuilder。你需要实现一个 IndexedWidgetBuilder 来创建子 Widget。 

- itemCount：列表项的数量，如果为null，则为无限列表。  

```
_buildBody() {
  return ListView.builder(
      itemCount: productList.length,
      itemBuilder: (BuildContext context, int index) {
        return _buildItem(context, index);
      });
}
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/listdemo.png)  


## 3.2 ListView.separated

**ListView.separated** 和 **ListView.builder** 是一样的，只不过多了一个 `separatorBuilder` 参数。  

```
ListView.separated(
    // 分割线构造器
    separatorBuilder: (BuildContext context, int index) 
      return Divider(color: Colors.red,height: 1,);
    },
    itemCount: pages.length,
    // 子 Widget 构造器
    itemBuilder: (BuildContext context, int index) {
      return _buildItem(context, pages[index], index);
    }),
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/listdivider.png)