Icon 用于展示字形图标，在 Flutter 中，已经内置了很多常用的图标。  

你可以通过 **Icons**  引用到这些图标。  

# 1.创建 Icon

```
Widget _buildIcons() => Center(
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceEvenly,
        children: <Widget>[
          Icon(Icons.account_box, color: Colors.black, size: 26),
          Icon(Icons.add_a_photo, color: Colors.black, size: 26),
          Icon(Icons.add_circle, color: Colors.black, size: 26),
          Icon(Icons.android, color: Colors.black, size: 26),
        ],
      ),
    );
```

看看效果：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter-Icons.png)  


# 2.Icon 的属性

- icon → IconData：图标数据。通过 **Icons** 可以引用到很多常用的图标。  

- color → Color：设置图标颜色。  

- size → double：设置图标大小。