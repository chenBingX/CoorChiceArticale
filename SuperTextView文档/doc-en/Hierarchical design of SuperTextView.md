# Hierarchical design of SuperTextView

In **SuperTextView**, the drawing content is divided into **4** levels. Understanding the hierarchical design in **SuperTextView** will help developers organize **UI** logic more reasonably.

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/SuperTextView层级.png)  


  **1.Native Background layer**: Originated from the View's own `Background` background layer.

  **2.SuperTextView Background layer**：That is, the solid background layer set by `app:stv_solid`. This layer is usually treated as a background layer in `SuperTextView` instead of View's `Background`. When we set the background layer color, the View's own `Background` will be overwritten.

  **3.Drawable layer**：The level at which **Drawable** of `SuperTextView` is located. If you want to display the image via `SuperTextView`, it is displayed on that layer. Starting with the **v2.0** version, **SuperTextView**'s **1** **Drawable** can be supported as a background image for **SuperTextView**, which means when the developer is like this When done, the background color set by the **solid** property will be overwritten.

  **4.Text layer**: The level at which the text is drawn.


