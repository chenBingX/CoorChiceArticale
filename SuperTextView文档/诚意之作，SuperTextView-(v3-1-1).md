![image](http://upload-images.jianshu.io/upload_images/1869462-964022d3acef55ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##### [【SuperTextView english document】](https://github.com/chenBingX/SuperTextView/blob/master/README_EN.md)

##### [点击此处，查看《SuperTextView 开发参考文档》](https://chenbingx.github.io/SuperTextView/develop_guide.html)

##### [点击此处，查看详细的《SuperTextView API文档》](https://chenbingx.github.io/SuperTextView/SuperTextView-doc/index.html)

SuperTextView 被打造来帮助 Android 开发者更高效、更便捷、更优雅的开发 Android 应用。

现在，这一诚意之作再次升级。全新的 SuperTextView 将向 Android 开发者开放更多可能性，当然一如既往，SuperTextView 也带来了更多高效的功能。  

## 神奇的着色

![image](http://upload-images.jianshu.io/upload_images/1869462-273beb3a240649df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

SuperTextView 此次的升级，为 StateDrawable 增加了神奇而强大的着色能力。开发者可以轻松的改变一个图标的颜色，而不用再增加一个仅仅是颜色不同的图标到项目中。这项技术将为你的 Android 应用程序带来一次瘦身的机遇。  

```
# 修改 drawable 的颜色
app:stv_state_drawable_tint="@color/gray"

# 修改 drawable2 的颜色
app:stv_state_drawable2_tint="@color/red"
```

只需要如此一行简单的代码，就能瞬间赋予一张图片千变万化的能力。想要任何色彩，当然是你说了算。而这一切的发生，无需再引进另外一张图片。

在 Java 代码中，有与之对应 set/get 函数，让开发者可以在任何时候都能施展魔法，改变一张图片的色彩。

## 七十二般变化

对 StateDrawable 的增强，不仅仅限于颜色的变换。SuperTextView 更被赋予了改变 StateDrawable 形态的能力。同样的一张图，开发者可以组合出无数种可能。  

![image](http://upload-images.jianshu.io/upload_images/1869462-de5f35aef86341b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

只需简单的几行代码，你便可以随心所欲的变换任何一张图片。  

```
# 修改 drawable 的旋转角度
app:stv_state_drawable_rotate="90"

# 修改 drawable2 的旋转角度
app:stv_state_drawable2_rotate="90"
```

无需复杂的代码，SuperTextView 一如既往的简洁、优雅。  

同样，在 Java 代码中，也提供了对应的 set/get 函数。

这项能力，可以有效的帮助开发者将 Android 应用的体积向着极致的方向压缩。


## 精彩远不止于此

![image](http://upload-images.jianshu.io/upload_images/1869462-7c029c837c172855.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

这就是渐变文字！  

SuperTextView 所提供的可能是目前为止实现渐变文字最简洁、优雅的解决方案。只需要简单的配置，就能实现酷炫的渐变文字效果。

```
# 是否启用渐变色文字
app:stv_textShaderEnable="true"

# 设置文字的起始渐变色
app:stv_textShaderStartColor="@color/red"

# 设置文字的结束渐变色
app:stv_textShaderEndColor="@color/yellow"

# 设置文字的渐变的模式
# leftToRight：左 -> 右
# rightToLeft：右 -> 左
# topToBottom：上 -> 下
# bottomToTop：下 -> 上
app:stv_textShaderMode="leftToRight"
```

这些属性也在 Java 中开放了 set/get 接口，便于开发者随时动态的修改它们。


## 开放了新的 API

### 1. Adjuster 增加 onAttach、onDetach

Adjuster 增加了两个新的函数：

- `onAttach()`：当 Adjuster 被设置到一个 SuperTextView 中时会被调用。
- `onDetach()`：当 Adjuster 被从一个 SuperTextView 中移除时会被调用。

通过在 Adjuster 中重写这两个函数，开发者可以在正确的时机进行状态注册、初始化，或者取消注册、释放资源等操作。  

```
public class MyAdjuster extends SuperTextView.Adjuster{

    @Override
    protected void adjust(SuperTextView superTextView, Canvas canvas) {
      
    }

    @Override
    public void onAttach(SuperTextView stv) {
      // 当 Adjuster 被加入一个 SuperTextView 时会被调用
    }

    @Override
    public void onDetach(SuperTextView stv) {
      // 当 Adjuster 被从 SuperTextView 移除时会被调用
    }
}
```

### 2. 提供 getAdjusterList() 函数

这个函数可以让开发者获得一个 SuperTextView 中的所有 Adjuster。如果 SuperTextView 中没有 Adjuster 的话，将会返回 null。  

## ⚠️ 你必须重视这些变化

### 1. 属性增加了 stv_ 前缀

现在，SuperTextView 的所有属性都加上了前缀 `stv_`。  

这样做可以避免当开发者引入的其它第三方库时，与 SuperTextView 可能产生的属性名冲突。  

如果开发者目前正在使用一个此前版本的 SuperTextView，那么当升级到新的版本后，需要在所有 xml 中的属性前加上 `stv_` 前缀。  

得益于现代 IDE 的强大，开发者可以轻松的完成这些工作。就像下面这样。  

```
app:corner="10dp"
```

corner 是旧版本中的属性名称，升级到新版本后，需要在前面增加 `stv_` 前缀，变为 `stv_corner`。  

![image](http://upload-images.jianshu.io/upload_images/1869462-86e53cb22b27aa2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果开发者使用的是 AndroidStudio，从 `Edit > Find > Replace` 打开批量替换对话框，然后按照下图操作即可。 

![image](http://upload-images.jianshu.io/upload_images/1869462-d44a6e97d14e80ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

如果开发者的项目中只有 SuperTextView 使用了相同的命名空间（如 `app`），那么很幸运，你可以直接将 `app:` 替换为 `app:stv_` 即可。

### 2. setAdjuster(Adjuster) 已被移除
 
从 SuperTextView v2.0 版本开始，`setAdjuster(Adjuster)` 函数就被标记为了将被移除的状态，同时加入了新的函数 `addAdjuster(Adjuster)` 作为替代。  

在新的版本中，`setAdjuster(Adjuster)` 函数将被正式移除，如果开发者此前使用了该方法，请将其修改为 `addAdjuster(Adjuster)`。




# 如何开始 SuperTextView v3.1.1
```
dependencies {
	 compile 'com.github.chenBingX:SuperTextView:v3.1.1'
}
```  
 
-------



> - 如果你喜欢 [**SuperTextView**](https://github.com/chenBingX/SuperTextView)，希望能到 [**Github**](https://github.com/chenBingX/SuperTextView) 点个 **star** [🌟](https://github.com/chenBingX/SuperTextView) 哦！

> - **CoorChice** 会不定期的在博客平台分享干货，快进入 [CoorChice的【个人主页】](https://juejin.im/user/57fc43b67db2a200595ffd94) 关注一波吧。
  
  
  
[**SuperTextView Github 【传送门】**](https://github.com/chenBingX/SuperTextView) 

