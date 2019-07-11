> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

本文是从个人学习过程中所记录的笔记中经过再次整理而得，所有观点仅代表个人理解，如有错误，还望指出。
# 矩阵的基本原理
## 标准矩阵
标准矩阵又称为对角矩阵，它的形式如下：  
```
  1, 0, 0  
  0, 1, 0  
  0, 0, 1  
```
## 矩阵的运算原理
矩阵的简单运算规则：左矩阵的一行的每个值，分别按顺序一一对应的乘上右矩阵的一列的值。也就是说，运算的左右两个矩阵必须符合左矩阵的列数要等于右矩阵的行数，否则两个矩阵就不能进行运算。下面给出两个例子：
![例1](http://upload-images.jianshu.io/upload_images/1869462-a62004d141188995?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![例2](http://upload-images.jianshu.io/upload_images/1869462-eb28805f20abc9c8?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看出，由于矩阵乘法的特殊性，所以它是不支持交换律的。不理解的同学可以找线性代数的书看看，这里就不再赘述了。
# 认识Matrix变换矩阵
Matrix变换矩阵包含了4种基本变换：缩放、错切（Skew）、平移、旋转。
我们先来看看Matrix这个3*3的矩阵结构：
![Matrix矩阵结构](http://upload-images.jianshu.io/upload_images/1869462-681820a171ee6543?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
其实都是顾名思义的:

- MSCALE_X和MSCALE_Y控制着缩放变换
- MSKEW_X和MSKEW_Y控制着错切变换
- MTRANS_X和MTRANS_Y控制着平移变换
- MSCALE_X、MSCALE_Y和MSKEW_X、MSKEW_Y共同控制着旋转变换
- 最后3个值MPERSP_0、MPERSP_1、MPERSP_2控制着透视变换

# Matrix的具体使用
## 改变一个Matrix
通过调用`new Matrix` 所获得矩阵是一个标准矩阵，我们可以用两种方式变换它：

- 通过`setValue（）`方法给它设置一个大小为9的float[]，这种方式每次都会覆盖掉矩阵的原有值。
```
float[] floats = new float[9]; //创建一个一维数组
Matrix matrix = new Matrix(); //创建一个变换矩阵对象
matrix.setValues(floats); //将一维数组转化为变换矩阵
canvas.drawBitmap(bitmap,matrix,null); //达到变换图片的效果
```
- 通过系统封装好的方法来进行变换：
1. set\pre\postScale（） —— 进行缩放变换；
2. set\pre\postSkew（） —— 进行错切变换；
3.  set\pre\postTranslate（） —— 进行平移变换；
4.  set\pre\postRotate（） —— 进行旋转变换。

## setXXX（）、preXXX（）、postXXX（）区别
### 区别一：矩阵的连接顺序
- setXXX（）是直接覆盖掉原矩阵，或者说先把原矩阵刷新成标准矩阵，然后在运算。所以，如果参数为null的话，意味着不发生变换，即得到一个标准矩阵。
- preXXX（）是前乘运算，即把矩阵乘在前面。
- postXXX（）是后乘运算，即把矩阵乘在后面。

### 误区一：pre先执行，并且是顺序执行；post后执行，并且是逆序执行
首先，正常情况下，程序是会按照代码的顺序执行的。其次，虽然矩阵乘法不支持交换律，但是支持** (T1 * T2) * T3 = T1 * (T2 * T3) ** 这样的运算。就是说矩阵乘法先乘和后乘是一样的。  
所以，很多地方这样说是错误的。
##使用Matrix变换图片一般做法
- 调用drawBitmap（mBitmap，mMatrix，mPaint）来直接改变Bitmap的绘制。
- 调用mImageView.setImageMatrix（mMatrix）来改变ImageView中的图片。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-d78143a2f9a9d13e.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
