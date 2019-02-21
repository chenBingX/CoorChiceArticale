# Text stroke

  Using **SuperTextView** makes it easy to implement text strokes.  
  
  ![](https://raw.githubusercontent.com/chenBingX/img/master/stv/文字描边.png)

In xml：  


  ```
  # Turn on text strokes
  app:stv_text_stroke="true"
  # Set text fill color
  app:stv_text_fill_color="@color/white"
  # Set the text stroke color
  app:stv_text_stroke_color="#461B50"
  # Set the text stroke width
  app:stv_text_stroke_width="1dp"
  ```

In Java：  

```
stv.setTextFillColor(textFillColor);
stv.setTextStroke(textStrokeColor);
stv.setTextStrokeWidth(textStrokeWidth);
```



  ⚠️ Note that after opening the text stroke function, the **text color can only be set by `app:stv_text_fill_color`, don't use `android:textColor`!**


## How to achieve a hollow word effect?

If you want to achieve the effect of hollow text, just set `app:stv_text_fill_color` to ***transparent color***, or the same as ***background color***.

  ![](https://raw.githubusercontent.com/chenBingX/img/master/stv/空心文字.png)

