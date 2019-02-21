# Appendix1: Attributes

For most of the cool effects supported by **SuperTextView**, developers can do this through simple property configuration in the **xml** layout file. 

And with the **Preview** plugin for **AndroidStudio**, you can preview these effects in real time.  

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/stv_xml编辑.png)  

The following are all the properties currently supported by **SuperTextView**.  


  ```
<SuperTextView

  // Set the RoundCorner. Will work on both the fill and the border (if the border exists). 
  // If you want to set to a circle, you only need to set the value to 1/2 of width or length.
  app:stv_corner="25dp"

  //Set the top left corner
  app:stv_left_top_corner="true"

  //Set the top right corner
  app:stv_right_top_corner="true"

  //Set the bottom left corner
  app:stv_left_bottom_corner="true"

  //Set the bottom right corner
  app:stv_right_bottom_corner="true"

  //Set the fill color
  app:stv_solid="@color/red"

  //Set the border color
  app:stv_stroke_color="@color/black"

  //Set the width of the border
  app:stv_stroke_width="2dp"

  //Place a drawable on the background layer. The default centered display.
  //The default size is half of SuperTextView.
  app:stv_state_drawable="@drawable/emoji"

  // Set the drawable display mode. The optional values ​​are as follows: 
  // left, top, right, bottom, center (default), 
  //leftTop, rightTop, leftBottom, rightBottom, 
  //fill (filling the entire SuperTextView, which will invalidate the size of the drawable)
  app:stv_state_drawable_mode="center"

  //Set the height of the drawable
  app:stv_state_drawable_height="30dp"

  //Set the width of the drawable
  app:stv_state_drawable_width="30dp"

  //Set the distance of the drawble relative to the left of the base position
  app:stv_state_drawable_padding_left="10dp"

  //Set the distance of the drawble relative to the top of the base position
  app:stv_state_drawable_padding_top="10dp"

  // boolean. Whether to open the function of Drawable 1.
  app:stv_isShowState="true"

  // Whether to use state drawable as a background image
  // Using state drawable as a background image gives Super Text View the ability to display images
  // By adjusting the properties of corner, stroke, etc., you can set the image with RoundCorner, Border, etc.
  app:stv_drawableAsBackground="true"

  //Place a drawable on the background layer. The default centered display.
  //And the default size is half of SuperTextView.
  app:stv_state_drawable2="@drawable/emoji"

  //Similar to Drawable1
  app:stv_state_drawable2_mode="center"

  //Similar to Drawable1
  app:stv_state_drawable2_height="30dp"

  //Similar to Drawable1
  app:stv_state_drawable2_width="30dp"

  //Similar to Drawable1
  app:stv_state_drawable2_padding_left="10dp"

  //Similar to Drawable1
  app:stv_state_drawable2_padding_top="10dp"

  //Similar to Drawable1
  app:stv_isShow2State="true"

  // Modify the color of the drawable
  app:stv_state_drawable_tint="@color/gray"

  // Similar to Drawable1
  app:stv_state_drawable2_tint="@color/red"

  // Modify the rotation angle of the drawable
  app:stv_state_drawable_rotate="90"

  // Similar to Drawable1
  app:stv_state_drawable2_rotate="90"

  // Whether to enable text strokes.
  // Note that the color set by setTextColor() after this mode is enabled will be overwritten.
  // You need to set the color of the text with text fill color.
  app:stv_text_stroke="true"

  // The stroke color of the text. The default is Color.BLACK.
  app:stv_text_stroke_color="@color/black"

  // The width of the text stroke.
  app:stv_text_stroke_width="1dp"

  // The color of the text fill. The default is Color.BLACK.
  app:stv_text_fill_color="@color/blue"

  // boolean。Whether to enable the Adjuster function.
  // Specifically, you need to implement an Adjuster for Super Text View in Java.
  // When you enable this feature without implementing your own Adjuster
  app:stv_autoAdjust="true"

  // Must be set to true to enable gradients. This means you have the flexibility to control this feature.
  app:stv_shaderEnable="true"

  // Set the starting color.
  app:stv_shaderStartColor="@color/main_blue"

  // Set the ending color.
  app:stv_shaderEndColor="@color/pink"

  // Set the gradient mode. As can be seen from the above figure, a total of 4 modes are supported:
  // topTopBottom, bottomToTop, leftToRight, rightToLeft
  app:stv_shaderMode="rightToLeft"

  // Set the background color when pressed
  app:stv_pressBgColor="@color/red"

  // Set the text color when pressed
  app:stv_pressTextColor="@color/white"

  // Modify the color of the drawable
  app:stv_state_drawable_tint="@color/gray"

  // Modify the color of drawable2
  app:stv_state_drawable2_tint="@color/red"

  // Modify the rotation angle of the drawable
  app:stv_state_drawable_rotate="90"

  // Modify the rotation angle of drawable2
  app:stv_state_drawable2_rotate="90"

  // Whether to enable gradient text
  app:stv_textShaderEnable="true"

  // Set the starting gradient of the text
  app:stv_textShaderStartColor="@color/red"

  // Set the end gradient of the text
  app:stv_textShaderEndColor="@color/yellow"

  // Set the gradient mode of the text
  // leftToRight：left -> right
  // rightToLeft：right -> left
  // topToBottom：top -> bottom
  // bottomToTop：bottom -> top
  app:stv_textShaderMode="leftToRight"


  />
  ```




