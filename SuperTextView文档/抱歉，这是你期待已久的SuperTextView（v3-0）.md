
[![SuperTextView](http://upload-images.jianshu.io/upload_images/1869462-a83dfa511349af4d.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)](https://github.com/chenBingX/SuperTextView)   

从 **SuperTextView** 诞生之初，便始终坚持一个愿景，即帮助 Android 开发者愉悦的去构建一个应用。  

经过一年多的不断的聆听、思考、探索、验证， **SuperTextView** 完成了多次迭代，改善了一些问题，新增了一些功能，不断的完善，以带给开发者更好的开发体验。  

如今， **SuperTextView** 已经具备了诸如圆角、边框、描边、按压变色、多状态图、圆角图、万能的 **Adjuster** 、加载网络图片等一系列的常用功能。得益于此，开发者能够轻松实现各种原本十分麻烦的效果，节省大量的开发时间，有效减少页面的复杂度，降低项目维护成本。  

写代码，本应如此愉悦！  

[![SuperTextView Logo.png](https://upload-images.jianshu.io/upload_images/1869462-0db1e6881769520a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
](https://github.com/chenBingX/SuperTextView)

# 1. 链接云端的SuperTextView

早在几个月前，就开始有很多开发者向 **CoorChice** 建议，是否能够让 **SuperTextView** 具备加载网络图片的功能。其实这也是 **CoorChice** 很久之前就有考虑过的，但在 **SuperTextView** 的早期，完善其核心功能仍然是首要目标，所以一直没涉猎到图片相关的功能。  

直到上一个大版本，**SuperTextView v2.0**，**CoorChie** 才尝试添加了图片展示的功能。这使得**SuperTextView** 可使用的范围得到了扩大，同时针对图片也推出了给图片增加描边、设置圆角、设置状态图等功能。相关使用文档可到以下链接查阅：  

[【你好， SuperTextView】 - https://www.jianshu.com/p/1b91e11e441d](https://www.jianshu.com/p/1b91e11e441d)  

这一次尝试，获得了开发者们不错的反响，大家对于使用 **SuperTextView** 去展示处理图片是有所期待的。上一个版本发布后，开发者们对于一个能展示网络图片的 **SuperTextView** 似乎更加的感兴趣了。  

那么，现在，你所期待已久的 **SuperTextView** 在此！  

![图片下载演示2.gif](https://upload-images.jianshu.io/upload_images/1869462-38eb981291352c9f.gif?imageMogr2/auto-orient/strip)

## 1.1 加载一张网络图片
显示一张网络图片，在 **SuperTextView** 中只需要如下代码：  

```
SuperTextView stv_1 = (SuperTextView) findViewById(R.id.stv_1);
//填入图片Url
stv_1.setUrlImage(url);
```

效果就如上图中的第二个显示头像的例子一样。  

如果你希望将网络图片作为 **SuperTextView** 的 StateDrawable 来展示的话，完全没问题。  

```
//填入图片Url
stv_1.setUrlImage(url, false);
```

第二个参数为 **false** 表示网络图片将不会被作为背景充满整个 **SuperTextView**，而是作为一个状态图。当然，有关状态图的一切配置都将运用到此。就像上图中的第一个例子一样，整个布局包括图片、文字、背景都在一个 **SuperTextView** 中被处理，从网络下载的图片被作为 **StateDrawable** 放到了图中的位置。  

## 1.2 SuperTextView中图片引擎
**SuperTextView** 为了保持依赖库的纯净和尽可能小的体积，并没有内置任何的图片加载框架。所以默认情况，将使用内置的一个简易图片引擎去下载图片，确保开发者能够正常使用展示网络图片的功能。  

但 **CoorChice** 仍然建议开发者根据项目的具体情况，选择一个目前正在使用的图片加载框架，设置到 **SuperTextView** 中，以用来加载图片。 **SuperTextView** 具备适配任意图片加载框架的能力。下面 **CoorChice** 将通过 Glide 和 Picasso 的例子展示如何将现有的图片框架安装到 **SuperTextView** 中。

### 1.2.1 实现图片引擎Engine
在 **SuperTextView** 中，核心的图片加载引擎被抽象成接口 **Engine** ，开发者需要根据所用的图片框架，实现一个 **Engine**。  

- **Glide图片加载框架**  
    
```
public class GlideEngine implements Engine {

  private Context context;

  public GlideEngine(Context context) {
        this.context = context;
  }

  @Override
  public void load(String url, final ImageEngine.Callback callback) {
        Glide.with(context).load(url).into(new SimpleTarget<GlideDrawable>() {
        @Override
        public void onResourceReady(GlideDrawable resource, GlideAnimation<? super GlideDrawable> glideAnimation) {
            // 主要是通过callback返回Drawable对象给SuperTextView
            callback.onCompleted(resource);
        }
        });
    }
}
```

- **Picasso图片加载框架**  
    
```
public class PicassoEngine implements Engine {

  private Context context;

  public PicassoEngine(Context context) {
        this.context = context;
  }

  @Override
  public void load(String url, final ImageEngine.Callback callback) {
        Picasso.with(context).load(url).into(new Target() {
        @Override
        public void onBitmapLoaded(Bitmap bitmap, Picasso.LoadedFrom from) {
            // 主要是通过callback返回Drawable对象给SuperTextView
            callback.onCompleted(new BitmapDrawable(Resources.getSystem(), bitmap));
        }

        @Override
        public void onBitmapFailed(Drawable errorDrawable) {

        }

        @Override
        public void onPrepareLoad(Drawable placeHolderDrawable) {

        }
    });
  }
}
```

### 1.2.2 安装图片引擎Engine
实现好 **Engine** 后，下一步就是要将其安装到 **SuperTextView** 中。  

**CoorChice** 建议可以在 Application的`onCreate()`中进行安装，这样当需要使用 **SuperTextView** 加载显示网络图片的时候，就能够用到三方图片框架了。  

```
public class STVApplication extends Application {

  @Override
  public void onCreate() {
    super.onCreate();
    // 安装图片引擎
    ImageEngine.install(new GlideEngine(this));
    // ImageEngine.install(new PicassoEngine(this));
  }
}
```

一行代码，轻松安装。  

需要注意的是，任何时候，后安装的 **Engine** 实例总是会替换掉先前安装的 **Engine** 实例，即 **SuperTextView** 只允许全局存在一个 **Engine** 实例。  

现在，你可以让 **SuperTextView** 使用指定的三方图片加载框架去加载图片了。 

![图片下载列表演示.gif](https://upload-images.jianshu.io/upload_images/1869462-6038572785f3df6d.gif?imageMogr2/auto-orient/strip)


# 2. 如何开始SuperTextView v3.0
```
	dependencies {
	   compile 'com.github.chenBingX:SuperTextView:v3.0.0'
	}
```  
 
-------

[**点击这里跳转SuperTextView项目地址。https://github.com/chenBingX/SuperTextView**](https://github.com/chenBingX/SuperTextView)  

# 3. 写在最后  


> - 如果你喜欢**SuperTextView**，希望能顺手到 **Github** 点个**star**以示鼓励哦！
> - 抽出空余时间写文章分享需要动力，还请各位看官动动小手点个赞，CoorChice谢谢各位老铁啊😄
> - CoorChice一直在不定期的创作新的干货，想要上车只需进到CoorChice的【简书 - 个人主页】点个关注就好了哦。发车喽～  

[**点击这里跳转SuperTextView项目地址。https://github.com/chenBingX/SuperTextView**](https://github.com/chenBingX/SuperTextView)  

[【**SuperTextView** 历史文档】：https://www.jianshu.com/p/1b91e11e441d](https://www.jianshu.com/p/1b91e11e441d)  





