
在 Flutter 中，对齐方式是一个 Widget，即 Align。  

使用 Align 包裹一个 Widget，可以指定它在父容器中的对齐方式。

# 1.一个例子

```
Widget build(BuildContext context) {
  // TODO: implement build
  return Scaffold(
    appBar: AppBar(),
      body: Align(
        // 对齐方式
      alignment: Alignment.center,
        // 子 Widget
        child: SizedBox(
            width: 300,
            height: 300,
            child: Container(color: Colors.amber)
      ),
      )
  );
}
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Align.png) 

# 2.Alignment

| 类型  | 作用 |
|---|---|
|Alignment.topLeft|左上角对齐|
|Alignment.topCenter|上居中对齐|
|Alignment.topRight|右上角对齐|
|Alignment.centerLeft|左居中对齐|
|Alignment.center|居中对齐|
|Alignment.centerRight|右居中对齐|
|Alignment.bottomLeft|左下角对齐|
|Alignment.bottomCenter|下居中对齐|
|Alignment.bottomRight|右下角对齐|
