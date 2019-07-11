![image](http://upload-images.jianshu.io/upload_images/1869462-5a7d103cfb21b27c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

⚠️**️ Warning！Warning！前方高能，阅读本文可能需要3分钟哦！**

# 有什么料？
1. 重新认识神秘的**PorterDuffXfermode**。
2. 学会正确的使用**PorterDuffXfermode**。
3. 收获【两张示例图】，帮助你在实际中正确的运用各种混合模式。

# 解密PorterDuffXfermode

先上两张示例图，大家快来保存啊！  

![image](http://upload-images.jianshu.io/upload_images/1869462-41b0a5777b4c9405.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

![image](http://upload-images.jianshu.io/upload_images/1869462-02d625b8655a48d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

**注：这两张图仅用了两个强大的View完成的。[【从未如此惊艳！你好，SuperTextView (v1.1) - http://www.jianshu.com/p/1b91e11e441d】](http://www.jianshu.com/p/1b91e11e441d)，你值得拥有！**

自己绘过图的筒靴一定见过或者用过`mPaint.setXfermode(Xfermode xfermode)`，它是干什么的呢？它的作用就是将画布上的当前图像（即目标图像**DST**）和后面需要绘制的图像（即源图像**SRC**）按照一定的算法进行混合。简单点说就是把**源图像SRC**与**目标图像DST**进行混合。而**ProterDuffXfermode**继承自**Xfermode**，提供了18像素混合模式的算法，它们是由**Thomas Porter**和**Tom Duff**在 于**1984年7月**的一篇名为[【《Compositing Digital Images》https://keithp.com/~keithp/porterduff/p253-porter.pdf】](https://keithp.com/~keithp/porterduff/p253-porter.pdf)的论文中提出的。这对图像处理来说具有重大的意义。  
## Xfermode的意义你知道吗？
在上面的两张图中，**CoorChice**已经向筒靴们展示了使用**Xfermode**来混合简单的图形所达到的效果。  

对于一些比较难画的图形，如果通过运算坐标和尺寸去绘制当然是可以的。但是这些运算将会非常复杂！如果合理的使用**Xfermode**去将一些简单的图形进行混合，同样可以获得你所期望的复杂图形。在**CoorChice**的这篇文章[【从未如此惊艳！你好，SuperTextView (v1.1)http://www.jianshu.com/p/1b91e11e441d】](http://www.jianshu.com/p/1b91e11e441d) 中，**CoorChice**向你展示了一个**扫光特效**，它的两端有个截断效果，就是通过**Xfermode**完成的。如果靠计算的话，实现起来会稍微费点力。  

当然，这些并不能发挥**Xfermode**的真正威力。如果你使用它对一些图片进行混合，你会看到**Xfermode**到底能做什么不可思议的事！比如，在 **《Compositing Design Images》** 中就有一个这样的例子：  

![image](http://upload-images.jianshu.io/upload_images/1869462-79ad0e4fd523fdce.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

图中最后**Composite**的结果就是前面几张图片通过一定组合合成的，这是合成算法：  
```
(FFire plus (BFire out Plant)) over Darken(Plant, .8) over Stars .
```
你看，**Xfermode**就是如此的强大，通过合理的组合，能合成图片。   

再一看张**CoorChice**用图片合成的各种效果：  

![image](http://upload-images.jianshu.io/upload_images/1869462-c2e1906417acd0b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

哇！这鱼飞起来啦！


## 18种混合模式
在 **《Compositing Degital Images》** 中，**Thomas Porter**和**Tom Duff**展示了12中基本的混合模式：  
![image](http://upload-images.jianshu.io/upload_images/1869462-d6ba359129ca8e27.PNG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
我们可以将上图中的**A**对应**目标图像DST**，**B**对应**源图像SRC**去理解。通过组合这12种混合模式，足够实现任意的2D图像合成效果了。十分的强大。对照着上面**CoorChice**画的图理解吧。  

也许筒靴们平时都只听说**PorterDuff.Mode**是16种模式，因为官方的例子中就出现了16种模式。但事实上，Android提供的混合模式共有18种，筒靴们在上图中也是能看到滴。就是最后一排的**ADD**和**OVERLAY**是不常被提到的。  

下面**CoorChice**和大家一起捋一捋这18种混合模式：  
在此之前，先说明下各个符号的意义。  
在支持透明通道的情况下，一个像素点通过**alpha**透明值和**RGB**色值来描述，即`[alpha, rgb]`。
```


Sa: Src Alpha，即源图像的透明值

Sc：Src color，即源图像的色值

Da：Dst Alpha，即目标图像的透明值

Dc：Dst color，即目标图像的色值

```

PorterDuff.Mode | 算法 | 作用
---|---|---
CLEAR | [0, 0] |图像的alpha和rgb值均为0.
SRC | [Sa, Sc]|取源图像的值。
DST | [Da, Dc]|取目标图像的值。
SRC_OVER |[Sa + (1 - Sa)*Da, Rc = Sc + (1 - Sa)*Dc]|结果是Src盖在了Dst上。注意alpha值的影响，不一定是这个结果。
DST_OVER | [Sa + (1 - Sa)*Da, Rc = Dc + (1 - Da)*Sc]|结果是Dst盖在了Src上。注意alpha值的影响，不一定是这个结果。
SRC_IN |[Sa * Da, Sc * Da] |结果是在Src色值不为0的地方，且Dst透明值不为0的地方能看到合成图像。
DST_IN | [Sa * Da, Sa * Dc]|结果是在Dst色值不为0的地方，且Src透明值不为0的地方能看到合成图像。
SRC_OUT | [Sa * (1 - Da), Sc * (1 - Da)] |结果是在Src色值不为0，且Dst透明值不为1的地方能看到合成图像。
DST_OUT | [Da * (1 - Sa), Dc * (1 - Sa)]|结果是在Dst色值不为0，且Src透明值不为1的地方能看到合成图像。
SRC_ATOP |[Da, Sc * Da + (1 - Sa) * Dc] |结果是在Src和Dst色值不同时为0，且Dst透明值不为0，且当Src色值为0但Src透明值不为1的地方能看到合成图像。
DST_ATOP | [Sa, Sa * Dc + Sc * (1 - Da)]|结果是在Src和Dst色值不同时为0，且Src透明值不为0，且当Dst色值为0但Dst透明值不为1的地方能看到合成图像。
XOR | [Sa + Da - 2 * Sa * Da, Sc * (1 - Da) + (1 - Sa) * Dc]|在不相交的地方按原样绘制源图像和目标图像，相交的地方受到对应alpha和色值影响。
DARKEN | [Sa + Da - Sa*Da, Sc*(1 - Da) + Dc*(1 - Sa) + min(Sc, Dc)] |取较暗的透明值，色值计算相对复杂。
LIGHTEN | [Sa + Da - Sa*Da, Sc*(1 - Da) + Dc*(1 - Sa) + max(Sc, Dc)]|取较亮的透明值，色值计算相对复杂。
MUTIPLY | [Sa * Da, Sc * Dc]|结果是在Src和Dst透明值均不为0，且色值均不为0的地方能看到合成图像。
SCREEN | [Sa + Da - Sa * Da, Sc + Dc - Sc * Dc]|可以看到，它可能有多种情况，需要实际计算。
ADD | Saturate(S + D) |
OVERLAY | |

筒靴们对着上面【Bitmap绘制】图来看这18种混合模式。

## 令人困惑的图
筒靴们可以看到【Bitmap绘制】图和官方的例子图是一样一样的，但是很多筒靴自己画出来的效果却和【Canvas直接绘制】图是一样一样的。嗯，百思不解！有没有可能是官方的例子错了呢？  

想多了，没有的。只是筒靴们没注意到官方标准例子中的细节：
- 首先需要关闭硬件加速。因为硬件加速模式下，渲染是通过GPU完成的，和普通CPU渲染可能有点不一样，导致了部分合成算法呈现的效果有差异。你可以看看我的这篇文章[**【用两张图告诉你，为什么你的App会卡顿?http://www.jianshu.com/p/df4d5ec779c8】**](http://www.jianshu.com/p/df4d5ec779c8)，了解下View的绘制流程。  
- 其次，像素混合是对两个区域进行的。官方的示例中，黄圆和蓝正方形都是画在大小和黑色边框相等的Bitmap上的，然后再将两个Bitmap的像素进行混合，此时两个Bitmap的区域是【完全重合】的。所以得到了标准效果。而很多同学可能没注意，往往就以为两个区域大小就是两个图形的外接矩形的大小，而它们相交的地方只有1/4。所以得到了“Canvas直接绘制”图的效果。两种方式最大的差别在于，【Bitmap绘制】图中有一部分透明像素点参与了混合，而【Canvas直接绘制】图中几乎没有。

这是**CoorChice**写的示例，就是上面几幅图的实现，[【Github：https://github.com/chenBingX/CoorChiceLibOne/blob/448cf36e0b33fb667cb4fd5a8d8db2651bf0647e/app/src/main/java/com/chenbing/coorchicelibone/Views/PorterDuffXDemoActivity.java】](https://github.com/chenBingX/CoorChiceLibOne/blob/448cf36e0b33fb667cb4fd5a8d8db2651bf0647e/app/src/main/java/com/chenbing/coorchicelibone/Views/PorterDuffXDemoActivity.java)，大家可以对照的动手感受一下。  







# 总结

> - 抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，给我点鼓励
> - 我一直在不定期的创作新的干货，想要上车只需进到我的【个人主页】点个关注就好了哦。发车喽～   

本篇文章向大家详细的展示了强大的**PorterDuffXfermode**的正确打开方式，**CoorChice**相信以后不会再有筒靴抱怨：我的天呐！这玩意儿有毒！为什么我合成出来的图像和官方示例不一样！


# 参考链接
1. [Api PorterDuff.Mode - https://developer.android.com/reference/android/graphics/PorterDuff.Mode.html](https://developer.android.com/reference/android/graphics/PorterDuff.Mode.html)  
2. [Xfermode in android - http://weishu.me/2015/09/23/Xfermode-in-android/](http://weishu.me/2015/09/23/Xfermode-in-android/)
3. [Compositing Digital Images - https://keithp.com/~keithp/porterduff/p253-porter.pdf](https://keithp.com/~keithp/porterduff/p253-porter.pdf)


**看到这里的童鞋快奖励自己一口辣条吧！**
