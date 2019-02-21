# Gradient effect

 **SuperTextView** supports gradient effects by configuring simple properties.


  ```
  # You must be set to true to enable gradients. This means you have the flexibility to control this feature.
  app:stv_shaderEnable="true"

  # Set the starting color
  app:stv_shaderStartColor="@color/main_blue"

  # Set the ending color.
  app:stv_shaderEndColor="@color/pink"

  # Set the gradient mode. As can be seen from the above figure, a total of 4 modes are supported:
  # topTopBottom, bottomToTop, leftToRight, rightToLeft
  app:stv_shaderMode="rightToLeft"

  ```

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/渐变色.png)

  These properties are also supported in Java.

  ```
  // Turn on the gradient function
  stv.setShaderEnable(true);
  // Set the starting color
  stv.setShaderStartColor(Color.BLUE);
  // Set the ending color.
  stv.setShaderEndColor(Color.RED);
  // Set the gradient mode.
  stv.setShaderMode(SuperTextView.ShaderMode.LEFT_TO_RIGHT);
  ```

  There are 4 types of gradient modes:  

  ```
  public static enum ShaderMode {
  /**
  * From top to bottom
  */
  TOP_TO_BOTTOM(0),
  /**
  * From bottom to top
  */
  BOTTOM_TO_TOP(1),
  /**
  * From left to right
  */
  LEFT_TO_RIGHT(2),
  /**
  * From right to left
  */
  RIGHT_TO_LEFT(3);
  }
  ```

