


# CustomPaint 的常用属性

|属性|类型|说明|
|---|---|---|
|painter|CustomPainter|背景画笔，绘制内容会显示在child子节点后面|
|foregroundPainter|CustomPainter|前景画笔，绘制内容会显示在child子节点前面|
|size|Size|设置绘制区域的大小。如果有child，则忽略该参数，且绘制区域为child的尺寸|
|isComplex|bool|是否复杂的绘制，如果是，Flutter会应用一些缓存策略来减少重复渲染的开销。默认false|
|willChange|bool|和isComplex配合使用，当启用缓存时，该属性代表在下一帧中绘制是否会改变。默认false|
|child|Widget|没错，CustomPaint是可以包含一个子节点的|