[![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Flutter快速上手指南封面2.JPG)](https://juejin.im/post/5c8f8e62e51d456a0f23d0fe)

[**目录传送门：**《Flutter快速上手指南》先导篇](https://juejin.im/post/5c8f8e62e51d456a0f23d0fe)

Dialog 在现代的视觉交互中是很常用的一种 UI。

做移动端开发的同学一定对它不陌生。

本篇将会探索一下，在 Flutter 中如何构建一个 Dialog UI。

# 1.如何展示一个Dialog？

1.导入 `material` 插件

```
'package:flutter/material.dart'
```

2.调用 showDialog() 来展示一个 Dialog

```
Future<T> showDialog<T>({
  @required BuildContext context,
  // 点击 dialog 外部是否可消失
  bool barrierDismissible = true,
  // 构建 Dialog 视图
  WidgetBuilder builder,
})
```

没错，只需要调用这个简单的函数就可以展示一个 Dialog。

重点是，在 `builder` 中构建你的 Dialog 视图。

这是一个简单的例子：

```
showDialog(
  // 传入 context
  context: context,
  // 构建 Dialog 的视图
  builder: (_) => Padding(
        padding: EdgeInsets.all(16),
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: <Widget>[
            Container(
              alignment: Alignment.center,
              color: Colors.white,
              child: Column(
                children: <Widget>[
                  Padding(
                    padding: EdgeInsets.only(top: 8),
                    child: Text('Custom Dialog',
                        style: TextStyle(
                            fontSize: 16,
                            decoration: TextDecoration.none)),
                  ),
                  Padding(
                    padding: EdgeInsets.only(top: 15, bottom: 8),
                    child: FlatButton(
                        onPressed: () {
                          // 关闭 Dialog
                          Navigator.pop(_);
                        },
                        child: Text('确定')),
                  )
                ],
              ),
            )
          ],
        ),
      ),
);
```

看看效果：

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/customdialog.png)

其实和平时构建一个页面没多大区别，只不过是在 `showDialog()` 的 `builder` 中构建。

如果你注意细节的话，你会发现上面代码中有一行：

```
Navigator.pop(_);
```

它就是用来关闭 Dialog 的操作。

还记得 **Navigator** 吗？

它是用来进行页面跳转的，也许你可以看看 [《8.页面跳转》]() 来回顾回顾。

这意味着，弹出一个 Dialog，实际上就是打开了一个 Dialog 风格的页面！！😺

跟踪源码会发现，在 `showDialog()` 的最后实现中，实际是这样的：

```
Navigator.of(context, rootNavigator: true).push<T>(_DialogRoute<T>(
    pageBuilder: pageBuilder,
    barrierDismissible: barrierDismissible,
    barrierLabel: barrierLabel,
    barrierColor: barrierColor,
    transitionDuration: transitionDuration,
    transitionBuilder: transitionBuilder,
  ))
```

🥶 可不就是打开了一个页面嘛..

# 2.常用 Dialog

Flutter 当然会提供一些 Dialog 的封装，帮助开发者快速的构建常用的 Dialog。

## 2.1 SimpleDialog

先看看 SimpleDialog 有些什么常用参数：

|属性|类型|说明|
|---|---|---|
|title|Widget|Dialog的标题|
|titlePadding|EdgeInsetsGeometry|title周围的 Padding|
|children|List<Widget>|child内容数组，会从上到下的排列 child|
|contentPadding|EdgeInsetsGeometry|内容区域周围的 Padding|
|backgroundColor|Color|Dialog的背景色|
|elevation|double| Dialog 的悬浮高度，和阴影效果有关|
|shape|ShapeBorder|Dialog的边框形状|

🌰 e.g.：

```
showDialog(
  context: context,
  builder: (ctx) {
    return SimpleDialog(
      title: Text("SimpleDialog"),
      titlePadding: EdgeInsets.all(10),
      backgroundColor: Colors.amber,
      elevation: 5,
      shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.all(Radius.circular(6))),
      children: <Widget>[
        ListTile(
          title: Center(child: Text("Item_1"),),
        ),
        ListTile(
          title: Center(child: Text("Item_2"),),
        ),
        ListTile(
          title: Center(child: Text("Item_3"),),
        ),
        ListTile(
          title: Center(child: Text("Close"),),
          onTap: (){
            Navigator.pop(context);
          },
        ),
      ],
    );
  });
```

🖼 看看是啥效果：

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/SimpleDialog.png)


## 2.2 AlertDialog

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/AlertDialog.png)

上图中的 Dialog 样式是现在比较常用的。

Flutter 帮助我们封装了 **AlertDialog** 来方便的实现这种样式。

看看 **AlertDialog** 有哪些常用的属性：

|属性|类型|说明|
|---|---|---|
|title|Widget|Dialog的标题|
|titlePadding|EdgeInsetsGeometry|title周围的 Padding|
|content|Widget|Dialog 中间的内容|
|contentPadding|EdgeInsetsGeometry|内容区域周围的 Padding|
|backgroundColor|Color|Dialog的背景色|
|elevation|double| Dialog 的悬浮高度，和阴影效果有关|
|shape|ShapeBorder|Dialog的边框形状|
|actions|List<Widget>|通常为Dialog底部的操作按钮|

🌰 e.g.：

```
showDialog(
  context: context,
  builder: (context) => AlertDialog(
        title: Text('Dialog'),
        content: Text(('Dialog content..')),
        actions: <Widget>[
          new FlatButton(
            child: new Text("取消"),
            onPressed: () {
              Navigator.of(context).pop();
            },
          ),
          new FlatButton(
            child: new Text("确定"),
            onPressed: () {
              Navigator.of(context).pop();
            },
          ),
        ],
      ));
```

这就是上图中的 Dialog 的实现。

