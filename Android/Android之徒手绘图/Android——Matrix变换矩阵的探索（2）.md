> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

前面我已经大概的介绍了一下Matrix矩阵，下面我们来看一下
```
xxxScale(),
xxxSkew(),
xxxTranslate(),
xxxRotate()
```
的计算过程：
# 首先我们需要先明确矩阵是如何改变坐标点的
对于一个坐标为
```math
(X_0,Y_0)
``` 
的点，我希望把他变成
```math
(kx_0,ky_0),
``` 
那么我们可以给它乘上一个矩阵：
![(X_0,Y_0)->(kx_0,ky_0)](http://upload-images.jianshu.io/upload_images/1869462-d4eb7a49adcda7c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这样，我们就把这个点成功的进行了变换。  
**那么矩阵是如何对图片进行变换的呢？**  
其实很简单，它只不过是对图片的每个像素点进行了变换，组成图片的所有像素点都变了，那么图片自然也就变了。  
**这样说的话，那缩放变换感觉很诡异啊，一个像素点还能变大变小吗？**  
图片的缩放原理相对复杂一些，但其实它就是按照一定的算法，对图片进行下采样（缩小）、上采样（放大）来对图片进行变换的。  
所谓**下采样**，就是按照一定的规律，在原图的所有像素点中，截取符合规律的像素点（这意味着一些像素点会被丢弃掉），然后将这些像素点呈现出来，我们就可以看到一张缩小了得图片。  
所谓**上采样**，就是按照一定的算法，在原图片的额像素群中插入一些像素点，然后再把这些像素点呈现出来，就得到一张放大的图片。  
当然这个复杂的计算过程不需要我们自己去实现，我们只需要告诉系统，我要一张多大的图片，系统就会帮助我们完成这个过程，然后直接给我们一张处理好的图片。
# 缩放变换xxxScale() 
前面我们已经知道了**Scale**变换是由**MSACLE_X**,和**MSCALE_Y**控制的，那么自然的，`xxxScale()`就是通过修改这两个值到达变换矩阵目的。  
**注：下面的讲解都基于一个标准矩阵 A 来进行。**
## setScale()
调用
```math
setScale(k_1,k_2)
```
，则是直接把A矩阵设置为：
![setScale(k_1,k_2)](http://upload-images.jianshu.io/upload_images/1869462-53ba62e17c5441a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
再次说明，调用`setXXX()`是直接设置矩阵的值，不管你此前对矩阵做了什么操作，它都会被这个矩阵给覆盖掉。
## preScale()
调用
```math
preScale(k_1,k_2)
```
，则是经过如下计算：
![preScale(k_1,k_2)](http://upload-images.jianshu.io/upload_images/1869462-359bdaf2291f3a24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## postScale()
```math
postScale(k_1,k_2)
```
是后乘，它的计算过程如下：
![postScale(k_1,k_2)](http://upload-images.jianshu.io/upload_images/1869462-cddbfc2ad407f24a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 错切变换xxxSkew()
## setSkew()
调用
```math
setSkew(k_1,k_2)
```
，直接将A设置成：
![setSkew(k_1,k_2)](http://upload-images.jianshu.io/upload_images/1869462-2feffcd2bb1de235.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## preSkew()
调用
```math
preSkew(k_1,k_2)
```
的计算过程如下：
![preSkew(k_1,k_2)](http://upload-images.jianshu.io/upload_images/1869462-c11d6dc074a253b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## postSkew()
调用
```math
postSkew(k_1,k_2)
```
的计算过程如下：
![postSkew(k_1,k_2)](http://upload-images.jianshu.io/upload_images/1869462-c5474c8bf23750a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 平移变换xxxTranslate()
## setTranslate()
 调用
 ```math
 setTranslate()
 ```
 是将矩阵直接设置为：
![setTranslate()](http://upload-images.jianshu.io/upload_images/1869462-2e891fab20759421.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 **preTranslate()和postTranslate()的计算原理和上述的一样的。**
# 缩放变换xxxRotate()
## setRotate()
 调用`setRotate(a)`会直接把矩阵设置为：
![setRotate(a)](http://upload-images.jianshu.io/upload_images/1869462-7199fdfb956ecb39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  **preRotate()和postRotate()的计算过程就不在赘述了。**

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-3fcbda40baf43744.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
