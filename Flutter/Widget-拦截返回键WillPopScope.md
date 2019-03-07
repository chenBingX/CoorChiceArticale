有时，你可能需要拦截返回按键（包括 Android 系统的系统返回按键），以触发交互。  

比如，在重要业务环节，询问用户是否确定离开，以防止用户错误的操作。  

在 Flutter 中，提供了这样的控件，来帮助你处理这样的场景，他就是 WillPopScope。  

# 1. WillPopScope 的属性

|属性|类型|说明|
|---|---|---|
|child|Widget|需要包裹的 Widget|
|onWillPop|WillPopCallback|当点击返回时，会触发回调|

# 2.例子

```
WillPopScope(child: scaffoldWidget(),
    onWillPop: () async {
  print('onWillPop');
  // 返回 true 表示允许返回
  // 返回 false 表示不允许返回
  return true;
})
```

这样，就能在点击返回按键的时候，拦截事件了。

