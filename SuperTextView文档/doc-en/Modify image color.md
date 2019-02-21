# Modify image color

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/着色.png)  

Developers can easily change the color of an icon without adding an icon that is just a different color to the project. This technology will give you a chance to slim down your Android app.    


In xml：  

```
<SuperTextView
    ...
    # Modify the color of the drawable
    app:stv_state_drawable_tint="@color/gray"

    # Modify the color of drawable2
    app:stv_state_drawable2_tint="@color/red"
    ...
/>
```

In Java：  

```
stv.setDrawableTint(tintColor);
stv.setDrawable2Tint(tintColor);
```

With such a simple line of code, you can instantly give an image a variety of capabilities.

If you want any color, of course you have the final say. 

And all this happens, no need to introduce another picture.


