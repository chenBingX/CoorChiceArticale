# Round Corner

**SuperTextView** supports the rounding effect of the control through property settings. From then on, developers don't have to create a lot of `<shape>` files for round corner.

## How to achieve round corner？

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/圆角矩形.png)

  In xml：

  ```
  <SuperTextView
    ...
    app:stv_corner="25dp"
    ...
  />
  ```
  
  In Java：   

  ```
  stv.setCorner(25);
  ```


## How to achieve a circular effect?

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/圆角图.png)

If you need to implement a circular effect, just set the `corner` value to half the length of the control's maximum side, ie: `corner = layout_width / 2`. such as：  

  ```
<SuperTextView
  ...
  android:layout_width="80dp"
  android:layout_height="80dp"
  app:stv_corner="40dp"
  app:stv_solid="#008673"
  ...
/>
  ```

## How to control the fillet separately?

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/屏幕快照%202017-04-18%2008.15.59.png)

  By default, setting `corner` to **SuperTextView** will be valid for all four corners of the control. 
  
  Of course, you can also specify which corner to really need to be rounded.  

In xml：

```
<SuperTextView
  ...
  //Set the top left corner
  app:stv_left_top_corner="true"

  //Set the top right corner
  app:stv_right_top_corner="true"

  //Set the bottom left corner
  app:stv_left_bottom_corner="true"

  //Set the bottom right corner
  app:stv_right_bottom_corner="true"
  ...
/>
```

In Java:  

```
  //Set the top left corner
  stv.setLeftTopCornerEnable( boolean);
  // Set the bottom left corner
  stv.setLeftBottomCornerEnable( boolean);
  //Set the top right corner
  stv.setRightTopCornerEnable(boolean);
  //Set the bottom right corner
  stv.setRightBottomCornerEnable(boolean);
```

