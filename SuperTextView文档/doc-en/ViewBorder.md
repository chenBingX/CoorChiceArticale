# ViewBorder

**SuperTextView** can be used to border the control with a simple configuration. Developers don't have to create a lot of `<shape>` files for borders or write complex code.  

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/屏幕快照%202017-04-18%2008.15.42.png)    

In xml：  

```
 <SuperTextView
  ...
  app:stv_solid="#78C3ED"
  // Set the border color
  app:stv_stroke_color="#5166ED"
  // Set the border width
  app:stv_stroke_width="5dp"
  ...
  />
```

In Java  

```
stv.setStrokeColor(color);
stv.setStrokeWidth(width);
```

Just set `app:stv_stroke_width` to be greater than 0 to enable the border function.  

  ⚠️  At this point, if `app:stv_stroke_color` is not set, there will be a default black border.

The effect of the border can also be effective when displaying images.

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/圆形图片加边框.png)

