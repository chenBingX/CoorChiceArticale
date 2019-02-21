# Pressing discoloration

  **SuperTextView** can quickly support the press color change effect, as shown in the figure below, both text and background colors are supported.


![](https://raw.githubusercontent.com/chenBingX/img/master/stv/按压变色.gif)


  Just configure the simple properties:

  ```
  # Set the background color when pressed
  app:stv_pressBgColor="@color/red"
  # Set the text color when pressed
  app:stv_pressTextColor="@color/white"
  ```

  In Java：

  ```
  // Set the background color when pressed
  stv.setPressBgColor(Color.RED);
  // Cancel the PressingDiscolorationEffect of the text
  stv.setPressTextColor(SuperTextView.NO_COLOR);
  ```

  - If you want to cancel the PressingDiscolorationEffect of the background, just set `PressBgColor` to a transparent color, `Color.TRANSPARENT`.
  
  
  - If you want to cancel the PressingDiscolorationEffect of the text, just set `PressTextColor` to `SuperTextView.NO_COLOR`.


