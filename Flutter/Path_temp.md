

# PathMetric

`Path.computeMetrics()` 可以获得 **PathMetrics** 数组对象。

 **PathMetrics**  中是一个个  **PathMetric**  对象。
 
 每个  **PathMetric**  存有  **Ptah**  中的每一段的信息。比如：
 
 ```dart

path.add(buildPath());
path.add(buildPath());
```

那么这段 `ptah` 的  **PathMetrics**  长度就是 2，里面有两个  **PathMetric** 。

**PathMetric** 的参数：

|参数|说明|
|:--|:--|
|length|path的长度。拉直了计算的|
|isClosed|是否是闭合的 path|
|contourIndex|顺序|

函数：

- `Path extractPath(double start, double end, {bool startWithMoveTo = true})`

截取从起始位置 `start`，到结束位置 `end` 的线段。




