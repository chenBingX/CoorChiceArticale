List 被用于展示列表数据。  

# 1.创建 ListView  

```
Widget _buildListView() {
  Widget _buildItem(var index) {
    return Container(
      color: Colors.white,
      margin: EdgeInsets.only(top: 6),
      height: 80,
      child: Row(
        mainAxisAlignment: MainAxisAlignment.start,
        crossAxisAlignment: CrossAxisAlignment.center,
        children: <Widget>[
          Container(
            width: 48,
            height: 48,
            margin: EdgeInsets.only(left: 16),
            decoration: BoxDecoration(
                shape: BoxShape.circle,
                border: Border.all(color: Colors.lightBlue, width: 1),
                image: DecorationImage(
                    image: NetworkImage(
                  'http://www.gx8899.com.img.800cdn.com/uploads/allimg/160804/3-160P4111639.jpg',
                ))),
          ),
          Container(
            margin: EdgeInsets.only(left: 16),
            child: Text(
              'Player ${index + 1}',
              textDirection: TextDirection.ltr,
              style: TextStyle(
                  color: Colors.black,
                  fontSize: 16,
                  fontWeight: FontWeight.bold),
            ),
          ),
        ],
      ),
    );
  }

  List<Widget> createDatas() {
    var items = <Widget>[];
    for (var i = 0; i < 20; i++) {
      items.add(_buildItem(i));
    }
    return items;
  }

  return ListView(
    children: createDatas(),
  );
}
```

运行效果：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-demo7.png)  

# 2.常用属性

- childrenDelegate → SliverChildDelegate：用于提供子 Widget 的代理。  

- itemExtent → double：限制 item 的高度。  

- children → List<Widget>：item 数组。