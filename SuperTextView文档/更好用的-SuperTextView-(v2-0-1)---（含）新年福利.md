[![image](http://upload-images.jianshu.io/upload_images/1869462-c4b60a0307539feb.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/chenBingX/SuperTextView)   

  

#  新年福利

> 感谢一直以来支持 **SuperTextView** 的 Androider 们。 **SuperTextView** 从 **2017.04** 有了第一次提交之后，至今已经有近8个月的时间了，从 **star** 也从 0 积累到了现在近 1.3k ，十分感谢大家支持。  

> **SuperTextView** 从无到有的过程中，有很多同学提出了许多很有建设性的建议，**CoorChice** 非常感谢这些同学，也尽可能的满足了一些大家的诉求。由于现在 **SuperTextView** 已经在很多商业项目中落地使用，所以 **CoorChice** 在进行更新迭代的时候会更加谨慎些，以稳定性为主，所以并不是所有同学的期待都会得到满足，还请各位谅解。  

-------
**CoorChice** 在此先祝所有同学新年快乐！  

嗯，即然是新年，自然要来点福利喽，嗯哼...  

**SuperTextView** 至今还有没有一个标识性的 **Logo** ，这不科学！可惜 **CoorChice** 只是一个 **Developer** ，想了好久也画不出个满意的 **Logo** 。  

所以，此次趁着新年，放个福利出来：  

帮助 **CoorChice** 给 **SuperTextView** 设计一个棒棒哒 **Logo** ，以后这个 **Logo** 就会被作为 **SuperTextView** 的官方标识啦！  

> **CoorChice** 希望有才的你设计的 **Logo** 能够体现 **SuperTextView** 的特点（你可以参考 **SuperTextView** 的文档寻找灵感），同时简洁的 **Logo** 会更受欢迎哦！如果 **Logo** 能够具有很好的传播属性那就太棒了！  
 
 从 **2017.12.29** 起，至 **2018.01.25**  期间，你可以通过邮箱或微信将你设计的 **Logo** 发送给 **CoorChice** 。 **CoorChice** 会选出一个最适合的作品作为今后 **SuperTextView** 的官方标识。  
 
 【邮箱】：**icechen_@outlook.com**  
 
 【微信】：**759393474**  
 
 同时，重点来了！  
 
 > 被选中作为 **SuperTextView** 官方标识的作品， **CoorChice** 将会送给才华横溢的作者**【两盒酷爽美味的小龙虾】**作为感谢。嗯哼...吃货的肚子是不是已经饥渴难耐了呢？快快开始你的表演吧！  
 
 
![小龙虾1.jpg](http://upload-images.jianshu.io/upload_images/1869462-4626e9aa17a1a21d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  


![小龙虾2.jpg](http://upload-images.jianshu.io/upload_images/1869462-219b633ccb4e02dc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

![小龙虾3.jpg](http://upload-images.jianshu.io/upload_images/1869462-20e49dd141306245.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

![小龙虾4.jpg](http://upload-images.jianshu.io/upload_images/1869462-c762bafa7d3dcbec.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  




是不是看着很美味啊？  

快快开始你的表演吧！    

快快开始你的表演吧！  

快快开始你的表演吧！  
  
# SuperTextView v2.0.1 - 未来，在此
**一直以来，CoorChice都心存一个设想，期待着能够打造这样一个控件：它能满足你的大部分开发需求，展示文字、图片、几何、动画、状态，让你使用一个控件就能高效的完成大部分开发工作。它是如此的强大，仿佛有心智一般，接受着你的输入，按照你的心意，呈现出叹为观止的画面。随着【SuperTextView v2.0.1】的到来，我们离这个设想更近了一步。现在，来和【SuperTextView v2.0.1】见个面吧！**  

![SuperTextView v2.0](http://upload-images.jianshu.io/upload_images/1869462-648831323272ced8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

## 图片，就是现在
在【SuperTextView v2.0.1】中，增加了对图片展示的支持。但不仅仅止于展示图片，它还能智能的根据你的输入将图片剪裁为你期望的形状。  

![image](http://upload-images.jianshu.io/upload_images/1869462-f96e43403b042eef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

给图片加上圆角，加上边框，或者直接变成圆形，所有的一切只需要设置几个简单的属性，即刻呈现在你的眼前。  

### 展示一张图片
如何使用SuperTextView展示一张图片？只需要在xml中加上下面两句代码即可。  

```
<com.coorchice.library.SuperTextView
    ...
    app:state_drawable="@drawable/avatar1"
    app:drawableAsBackground="true"
    ...
 />
```

如果你是`SuperTextView`的忠实用户的话，你会发现，原本的`state_drawable`现在可以被用来展示一张图片。

### 给图片加上圆角
现在，你的图片呈现在了你的眼前，也许你还想对它做一些不一样的事情，比如，加个圆角，或者直接变成圆形？没问题，`SuperTextView`现在完全能胜任这样的工作。  

```
<com.coorchice.library.SuperTextView
    android:layout_width="100dp"
    android:layout_height="100dp"
    ...
    app:corner="15dp"
    app:state_drawable="@drawable/avatar1"
    app:drawableAsBackground="true"
    ...
 />
```

如此简单！在原来的基础上你仅仅需要设置合理的`corner`值就行。

### 也许，你还想要边框
有时候，你可能需要使用一个边框去包裹住你的图片，就像上面的示例那样。没错，这肯定在`SuperTextView`能力范围内。  

```
<com.coorchice.library.SuperTextView
    android:layout_width="100dp"
    android:layout_height="100dp"
    ...
    app:corner="50dp"
    app:stroke_color="#F4E187"
    app:stroke_width="4dp"
    app:state_drawable="@drawable/avatar1"
    app:drawableAsBackground="true"
    ...
 />
```

`app:stroke_color` 掌控着边框的颜色，`app:stroke_width` 掌控着边框的宽度。一切如此流畅，一个有心智的控件本该如此，对吗？  


## 第二个状态图
面对复杂的需求变化，【SuperTextView】为应对这种复杂性，孕育出了第二个状态图 `state_drawable2` 。  


[图片上传失败...(image-b29a97-1514481355797)]  

现在，CoorChice将向你展示，上图中的两种效果是如何实现的。  

- **示例一**  

```
<com.coorchice.library.SuperTextView
    android:layout_width="100dp"
    android:layout_height="100dp"
    ...
    app:corner="50dp"
    app:state_drawable="@drawable/avatar1"
    app:drawableAsBackground="true"
    // state_drawable2的配置由此开始
    app:isShowState2="true"
    app:state_drawable2="@drawable/recousers"
    app:state_drawable2_mode="rightTop"
    app:state_drawable2_height="20dp"
    app:state_drawable2_width="20dp"
    ...
 />
```

- **示例二**  

```
<com.coorchice.library.SuperTextView
    android:layout_width="100dp"
    android:layout_height="100dp"
    ...
    // 背景图
    android:background="@drawable/avatar7"
    // drawable1的配置由此开始
    app:isShowState="true"
    app:state_drawable="@drawable/triangle"
    app:state_drawable_mode="leftTop"
    app:state_drawable_width="20dp"
    app:state_drawable_height="20dp"
    // state_drawable2的配置由此开始
    app:isShowState2="true"
    app:state_drawable2="@drawable/recousers"
    app:state_drawable2_mode="rightTop"
    app:state_drawable2_height="20dp"
    app:state_drawable2_width="20dp"
    ...
 />
```  

就如你所熟悉的一样，`state_drawable2` 延续了第一代一切流畅的操作。在聪明的你合理的使用下，【SuperTextView】一定能够大放异彩！😉  

## 属于Adjuster的时代
此前，`Adjuster` 的设计使得【SuperTextView】具有了灵魂，成为更聪明的控件。对绘制过程的插入，触摸事件的捕捉，使得你能轻松的从外部改变一个控件的状态。创意始于心，而行于此。  

现在，【SuperTextView】能够同时承载最多3个 `Adjuster` ！也许，你的创意会更加的炫目。  

[图片上传失败...(image-d0ea3-1514481355797)]  

在上面这个示例中，CoorChice将早起的两个【扫光】和【涟漪】特效都加入到了一个【SuperTextView】中，结果就是你看到的这样。  

更多的 `Adjuster` 意味着更多的组合，更多的惊喜。在【v1.4.0】中，CoorChice同样使用了 `Adjuster` 来轻松的实现了按压变色功能。  

这是 `Adjuster` 的时代，睿智的你一定可以运用它挥洒创意的。  

需要注意的是⚠️，原本的 `setAdjuster(Adjuster)` 方法目前仍然被保留，但以后的版本将会被移除，你必须要尽快迁移。新的替代方法为 `addAdjuster(Adjuster)` 。  

## 其它
- 修正控制Shader模式的属性 `app:shaderMode="leftToRight"` 的拼写。原来为 `app:shaderMode="leftTopRight"` 。如果你使用了该属性，在升级【SuperTextView v2.0.1】后请及时修正。  
- 增加 `set/getPressBgColor()` 和 `set/getPressTextColor()` 用于在代码中控制按压背景色。
- 修复【SuperTextView v2.0】中设置点击事件无效的Bug。

## 如何开始SuperTextView v2.0.1
```
	dependencies {
	        compile 'com.github.chenBingX:SuperTextView:v2.0.1'
	}
```  
 
-------  

[**点击这里跳转SuperTextView项目地址。https://github.com/chenBingX/SuperTextView**](https://github.com/chenBingX/SuperTextView)  

# 写在最后  


> - 如果你喜欢**SuperTextView**，希望能顺手到 **Github** 点个**star**以示鼓励哦！
> - 抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，给我点鼓励😄
> - 我一直在不定期的创作新的干货，想要上车只需进到我的【个人主页】点个关注就好了哦。发车喽～  

[**点击这里跳转SuperTextView项目地址。https://github.com/chenBingX/SuperTextView**](https://github.com/chenBingX/SuperTextView)  

[**SuperTextView** 旧版文档：https://www.jianshu.com/p/1b91e11e441d](https://www.jianshu.com/p/1b91e11e441d)  

