# Modify image rotation angle

**SuperTextView** is given the ability to change the **StateDrawable** form. 

The same picture, developers can combine a myriad of possibilities.  

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/旋转.png)  

With a few lines of code, you can transform any picture as you wish. 

In xml：  
 

```
# Modify the rotation angle of the drawable
app:stv_state_drawable_rotate="90"

# Modify the rotation angle of drawable2
app:stv_state_drawable2_rotate="90"
```

In Java：  

```
stv.setDrawableRotate(rotate);
stv.setDrawable2Rotate(rotate);
```

No need for complicated code, **SuperTextView** is as simple and elegant as ever.  

This ability can effectively help developers compress the volume of Android applications to the extreme.  

By using the **Drawable** shading feature, developers can remove the **.png** that was originally necessary in the app, for example, arrows, etc.

Developers only need an arrow image, you can combine any of the required arrows **icon** by modifying their color, rotation angle, size and other attributes.

