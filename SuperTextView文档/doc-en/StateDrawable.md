# StateDrawable

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/屏幕快照%202017-04-18%2008.16.22.png)

 **SuperTextView** comes with a StateDrawable function. 
 
 Two state diagrams can be displayed by **Drawable, Drawable2** and two **Drawable** pits.

  Unlike the system's native **TextView** **Drawable**, **SuperTextView**'s **Drawable** provides precise control over its position and size.  
  
  Developers can control the properties of StateDrawable directly in xml: 
  
  ```
  # boolean. Whether to open the function of Drawable 1.
  app:stv_isShowState="true"

  # Set the picture of Drawable 1
  app:stv_state_drawable="@drawable/emoji"

  # Setting the display mode of Drawable 1 determines the base position of Drawable 1.
  # The optional values ​​are as follows:
  # left、top、right、bottom、center(Default)、
  # leftTop、rightTop、leftBottom、rightBottom、
  # fill(Filled the entire Super Text View, this will invalidate the size of the set drawable)
  app:stv_state_drawable_mode="center"

  # Set the height of Drawable 1
  app:stv_state_drawable_height="30dp"

  # Set the width of Drawable 1
  app:stv_state_drawable_width="30dp"

  # Set the distance of the Drawable 1 relative to the left of the base position
  app:stv_state_drawable_padding_left="10dp"

  # Set the distance of Drawable 1 relative to the top of the base position
  app:stv_state_drawable_padding_top="10dp"


  # boolean. Whether to open the function of Drawable 2.
  app:stv_isShow2State="true"

  # Set the picture of Drawable 2
  app:stv_state_drawable2="@drawable/emoji"

   # Setting the display mode of Drawable 2 determines the base position of Drawable 2.
    # The optional values ​​are as follows:
    # left、top、right、bottom、center(Default)、
    # leftTop、rightTop、leftBottom、rightBottom、
    # fill(Filled the entire Super Text View, this will invalidate the size of the set drawable)
  app:stv_state_drawable2_mode="center"

  # Set the height of Drawable 2
  app:stv_state_drawable2_height="30dp"

  #  Set the width of Drawable 2
  app:stv_state_drawable2_width="30dp"

  # Set the distance of the Drawable 2 relative to the left of the base position
  app:stv_state_drawable2_padding_left="10dp"

  # Set the distance of Drawable 2 relative to the top of the base position
  app:stv_state_drawable2_padding_top="10dp"

  ```  
  
  The above properties also have a corresponding `setXXX()` function in Java.

  Now let's see what the two state diagrams can do.  
  
  ![](https://raw.githubusercontent.com/chenBingX/img/master/stv/屏幕快照%202017-11-16%2001.46.23.png)

  - **e.g.1**

  ```
<com.coorchice.library.SuperTextView
android:layout_width="100dp"
android:layout_height="100dp"
  ...
  app:stv_corner="50dp"
  app:stv_state_drawable="@drawable/avatar1"
  # Use Drawable 1 as the control background to display the image
  app:stv_drawableAsBackground="true"
  # configuration of state drawable 2 starts here
  app:stv_isShowState2="true"
  app:stv_state_drawable2="@drawable/recousers"
  app:stv_state_drawable2_mode="rightTop"
  app:stv_state_drawable2_height="20dp"
  app:stv_state_drawable2_width="20dp"
  ...
  />
  ```

  - **e.g.2**

  ```
<com.coorchice.library.SuperTextView
android:layout_width="100dp"
android:layout_height="100dp"
  ...
  # background
  android:background="@drawable/avatar7"
  # configuration of state drawable 1 starts here
  app:stv_isShowState="true"
  app:stv_state_drawable="@drawable/triangle"
  app:stv_state_drawable_mode="leftTop"
  app:stv_state_drawable_width="20dp"
  app:stv_state_drawable_height="20dp"
  # configuration of state drawable 2 starts here
  app:stv_isShowState2="true"
  app:stv_state_drawable2="@drawable/recousers"
  app:stv_state_drawable2_mode="rightTop"
  app:stv_state_drawable2_height="20dp"
  app:stv_state_drawable2_width="20dp"
  ...
  />
  ```
  
  As you can see, using **SuperTextView** to handle such **ui** requirements is really enjoyable, and developers can no longer have to worry about how to do this by overlaying controls. The set is implemented.


