# SuperTextView 的层级设计

在 **SuperTextView** 中，将绘制内容划分成了 **4** 个层级。了解 **SuperTextView** 中的层级设计，将会有助于开发者更加合理的组织 **UI** 逻辑。

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/SuperTextView层级.png)

  **1.Background层**：源于 View 自身的 `Background` 背景层。

  **2.背景层**：即通过 `app:stv_solid` 设置的纯色背景层。在 `SuperTextView` 中通常将该层视做背景层，而不是View的 `Background`。当我们设置了背景层色后，View 本身的 `Background` 将会被覆盖。

  **3.Drawable层**：`SuperTextView` 的 **Drawable** 所在的层级。如果你希望通过 `SuperTextView` 来展示图片，就是在该层展示。从 **v2.0** 版本开始，**SuperTextView** 的 **1** 号 **Drawable** 可以被支持用于作为 **SuperTextView** 的背景图片，这意味着当开发者这么做时，通过 **solid** 属性设置的背景颜色将会被覆盖。

  **4.文字层**：即绘制文字的层级。

