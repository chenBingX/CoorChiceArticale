在 Row, Column, 或者 Flex 容器中，有时需要让之中一个 Widget 将容器主轴上的剩余空间充满，这时通过在 Widget 上包裹一层 Expended，就能轻松实现。  

```
Row(
  children:[
    MyWidget(),
    Expanded(
      child: MyWidget()
    ),
    MyWidget()
  ]
)
```  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/Expended.gif)  
