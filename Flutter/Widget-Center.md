Flutter 提供了一个专门用于居中的 Widget，即 Center。  

Center 继承自 Align，只不过其 `alignment` 为 Alignment.center。  

```
class Center extends Align {
  /// Creates a widget that centers its child.
  const Center({ Key key, double widthFactor, double heightFactor, Widget child })
    : super(key: key, widthFactor: widthFactor, heightFactor: heightFactor, child: child);
}
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Align.png) 
