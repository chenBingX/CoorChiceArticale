

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