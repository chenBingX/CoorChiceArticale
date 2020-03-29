# Semantics

[【官方组件文档传送门】](https://api.flutter.dev/flutter/widgets/Semantics-class.html)

**Semantics** 中文译做 "语义学"。  

它的功能和它的名字一致，就是为组件提供一个语义，以便搜索引擎或者语义分析工具能够知道一个部分的用途。  

对于需要兼顾可用性，如 Android TalkBack 或者 iOS VoiceOver 的应用来说，它是十分必要的一个组件。


它内涵了多达 50 多种语义的设置，如：

|属性|含义|
|--|--|
|label|文本描述|
|enbabled|是否可用|
|readOnly|仅限于读取使用|


官方组件在实现的过程中，往往都考虑到了每个组件的语义，如 Button 组件：


```

class _RawMaterialButtonState extends State<RawMaterialButton> {

  ...

  @override
  Widget build(BuildContext context) {

    ...

    return new Semantics(
      container: true,
      button: true,
      enabled: widget.enabled,
      child: new ConstrainedBox(
        constraints: widget.constraints,
        child: new Material(
          ...
        ),
      ),
    );
  }
}
```

在 **MaterialApp** 中加上属性 **showSemanticsDebugger=true**，即可开启对语义的调试。

```
void main(){
  runApp(new MyApp());
}

class MyApp extends StatefulWidget {
  @override
  _MyAppState createState() => new _MyAppState();
}

class _MyAppState extends State<MyApp> {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: new Text('My Semantics Test Application'),
      showSemanticsDebugger: true,
      theme: new ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: new FirstScreen(),
    );
  }
}
```