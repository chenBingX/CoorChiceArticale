现在有很多异形屏，在它们上面绘制视图是有风险的，因为会被诸如 **齐刘海** 这类的遮住你的视图。  

好在 Flutter 提供了 SafeArea，用于包裹视图，让视图可以自动避开遮挡，完整的展示出来。  

如图视图被齐刘海遮挡：  

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/SafeArea1.png)  

使用 SafeArea 包裹后：  

```
SafeArea(
  child: MyWidget()
);
```

![](https://raw.githubusercontent.com/chenBingX/img/master/Flutter/SafeArea2.png)  

当然，如果你的 root 节点使用的是 **Scaffold** 的话，已经自动处理了这些不安全的因素，所以就不用添加 SafeArea 了。