# Set text gradient

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/文字渐变色.png)    

**SuperTextView** is probably the simplest and elegant solution for implementing gradient text so far. With a simple configuration, you can achieve cool gradient text effects.

```
# Whether to enable gradient text
app:stv_textShaderEnable="true"

# Set the starting gradient of the text
app:stv_textShaderStartColor="@color/red"

# Set the end gradient of the text
app:stv_textShaderEndColor="@color/yellow"

# Set the gradient mode of the text
# leftToRight: left -> right
# rightToLeft: right -> left
# topToBottom:top -> bottom
# bottomToTop:bottom -> top
app:stv_textShaderMode="leftToRight"
```

In Java： 
 
```
stv.setTextShaderEnable(true);
stv.setTextShaderStartColor(color);
stv.setTextShaderEndColor(color);
stv.setTextShaderMode(shaderMode);
```

**ShaderMode** values：  

```
public static enum ShaderMode
    /**
     * top to bottom
     */
    TOP_TO_BOTTOM(0),
    /**
     * bottom to top
     */
    BOTTOM_TO_TOP(1),
    /**
     * left top right
     */
    LEFT_TO_RIGHT(2),
    /**
     * right to top
     */
    RIGHT_TO_LEFT(3);
    ...
}
```


