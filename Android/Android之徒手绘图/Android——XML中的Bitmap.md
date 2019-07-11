> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

在xml中的drawable/目录下可以创建Bitmap资源，这样可以将一张图片直接转换成bitmap，并给它设置一些属性，它指向一个BitmapDrawable对象。#调用方法## 在xml中：
```
格式: @[package:]drawable/filename
<ImageView
  android:layout_height="wrap_content"
  android:layout_width="wrap_content"
  android:src="@drawable/bitmap"/>
```
## 在Java中：
``` 
格式：
R.drawable.filenameResources res =getResources();
Drawable drawable = res.getDrawable(R.drawable.bitmap);
```

# 语法
```
<?xml version="1.0" encoding="utf-8"?>
<bitmap xmlns:android="http://schemas.android.com/apk/res/android" 
  android:src="@[package:]drawable/drawable_resource" 
  android:antialias=["true" | "false"] 
  android:dither=["true" | "false"] 
  android:filter=["true" | "false"] 
  android:gravity=["top" | "bottom" | "left" | "right" | "center_vertical" | "fill_vertical" | "center_horizontal" | "fill_horizontal" | "center" | "fill" | "clip_vertical" | "clip_horizontal"] 
  android:mipMap=["true" | "false"] 
  android:tileMode=["disabled" | "clamp" | "repeat" | "mirror"] />
```
** 常用的9个属性：**

| 属性 | 作用 |
| --- |:---|
|src|在这里引用图片资源|
|antialias|开启或关闭抗锯齿|
|dither|开启或关闭图像抖动。当使用图片的dpi和屏幕不匹配时，开启图像抖动可以获得更好的显示效果|
|filter|开启或关闭滤镜。当需要对图片进行缩放操作时，开启滤镜可以使图片更加平滑|
|tiltMode|贴图模式。详细见下文。该属性与gravity是互斥的。|
|gravity|重心。详细见下文。该属性与tileMode是互斥的。|
|tint|设置着色。有种盖一个色层在图片上的感觉。|
|tintMode|设置着色模式。详细见下文。|
|mipMap|是否将图片标记为mipmap，使用mipmap能够提高显示性能，默认为false|
## android:tileMode属性贴图模式有以下几种：
| 值 | 效果 |
| ------------- |:-------------|
| clamp | 当图片>容器时，图片多余的部分会被截去；当图片<容器时，会复制图片的边缘部分填充空白 |
| disable | 图片会根据容器大小进行缩放。这是默认值 |
| repeat| 图片会重复填充满容器。但是当图片>容器时，多余部分会被截去|
|mirror|图片会以镜像重复的形式填满容器。同样，当图片>容器时，多余部分会被截去|
## android:gravity属性当图片<容器时，重心决定了它在容器中的位置。重心有以下几种：
| 值 | 效果 |
| ------------- |:-------------|
|top|顶部|
|bottom|底部|
|left|左边|
|right|右边|
|center|中心|
|center_vertical|垂直中心|
|center_horizontal|水平中心|
|fill|充满容器，和tileMode="disable"是一个效果|
|fill_vertical|充满垂直方向|
|fill_horizontal|充满水平方向|
|clip_vertical|搞不懂是什么鬼？求大神解答|
|clip_horizontal|搞不懂是什么鬼？求大神解答|
## android:tintMode属性着色模式有以下几种：
|值|效果|
|---|:---|
|add||
|src_in||
|src_over||
|src_atop|以上几种全把图片给遮住了，看不出有什么差别。求指导！|
|mutiply|着色与透明度相结合，相当于在图片上盖了一层不是很深的颜色|
|screen|同上，但是颜色会更浅|
# 一个例子
```
<?xml version="1.0" encoding="utf-8"?>
<bitmap xmlns:
android="http://schemas.android.com/apk/res/android" 
android:alpha="1" 
android:antialias="true" 
android:dither="true" 
android:filter="true" 
android:gravity="clip_vertical" 
android:src="@drawable/share" 
android:tint="#fc747e" 
android:tintMode="multiply" />
```
效果图：![效果图](http://upload-images.jianshu.io/upload_images/1869462-ba804ce576cc6d44?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-ed1cf3854cb98675.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
