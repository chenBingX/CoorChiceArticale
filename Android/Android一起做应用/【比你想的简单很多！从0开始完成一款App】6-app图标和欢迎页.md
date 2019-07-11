> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索`CoorChice`关注我的微信公众号，同期文章也将会优先推送到公众号中，以提醒您有新鲜文章出炉。亦可在文章末尾扫描二维码关注。  


![封面.jpg](http://upload-images.jianshu.io/upload_images/1869462-5258fc1989dfefed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# [本系列文章列表](http://www.jianshu.com/notebooks/8017443/latest)  

经过前面5篇的构建，我们已经基本打好了地基，现在可以开始往上叠加楼层(功能)了！本篇我们一起来为这个app更换一个图标，然后制作一个有意思的欢迎页面。
# 更换图标
使用Android Studio创建的应用默认会为我们在mipmap包下生成一个icon，它里面包含了一组不同大小的图标，目的是为了适配不同尺寸的Android设备，我们在更换时，也应该仿照它生成对应大小的图标。 
## 不同像素密度下app图标的建议
以下是官方建议的不同像素密度下app图标大小：

dpi类型 | dpi | 建议图标尺寸 |
:---:|:---:|:---:|
idpi | 120dpi | 36*36 |
mdpi | 160dpi(标准尺寸1dp = 1px) | 48*48 |
hdpi | 240dpi | 72*72 |
xhdpi | 320dpi | 96*96 |
xxhdpi | 420dpi | 144*144 |  

## 图标导入插件

我们一个使用制图软件生成不同尺寸的图标，然后再分别复制粘贴到对应尺寸的**drawable** 或**mipmap** 文件夹下。但是这里我推荐一个Android Studio的插件，叫做**Android Drawable Importer** 。它可以直接以拖拽的方式将图标拖进去，然后自动生成对应不同尺寸的图标，当然你可以自定义。   
下载该插件重启Sutiod后，在Project区按下**command + N** 或者**右键->New** 找到**Batch Drawable Import**。  

![**command + N**](http://upload-images.jianshu.io/upload_images/1869462-d40db69d3b72899d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

![**右键->New**image](http://upload-images.jianshu.io/upload_images/1869462-84934c2b8356db46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

点击后看到如下界面，点击+号选择图标导入。  

![导入界面](http://upload-images.jianshu.io/upload_images/1869462-d737df19337dc870.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

点击ok后，进入如下页面，我们根据官方建议修改基准值，我以XHDPI为基准(在Source-Resolutionchu处选择)将尺寸设为72*72，后面的它会自动计算生产。下面选择目录到项目res下，改好名字，点击确定，他就会帮我们在drawable下生产符合要求的图片。  

![配置](http://upload-images.jianshu.io/upload_images/1869462-9517f59f6bda8211.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  
![生成的图标](http://upload-images.jianshu.io/upload_images/1869462-c7a872cba13c72ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

下面在AndroidManifest.xml配置文件中，把<application>标签下的**android:icon** 的值改成刚刚导入的图标。  

```
<application
    android:name=".ChiceApplication"
    android:allowBackup="true"
    android:icon="@drawable/launch_icon2"
    android:label="@string/app_name"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
```  

![效果图](http://upload-images.jianshu.io/upload_images/1869462-f578146a1c4fce53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)    




# 欢迎页面  
现在很多app都喜欢在启动应用的时候加一个欢迎页面，这样做可以让用户有一个缓冲，不会因为一进去就是操作页而感到突兀，同时最重要的是我们能在欢迎页初始化数据和配置，这样当进入主页时，能够直接呈现数据。当然我们这个项目的首次天气数据请求也会放到欢迎页中进行。  
## 欢迎页面需求
- 生动有趣，这样不会让人感觉到枯燥；
- 有按钮点击进入主页，并且不能被返回；
- 启动数据加载，并将数据缓存共享。  

暂时这些，我们会在开发中根据情况再变化。  

## 实现  
看似这几个简单的需求，我们要做的事情也不少，所以我本篇只实现前两个需求，不然篇幅太长了。下面我们一步步来完成这个欢迎页面。  
### 效果图走一波  

![效果图1](http://upload-images.jianshu.io/upload_images/1869462-5b72192b658653f2.gif?imageMogr2/auto-orient/strip)  

![效果图2](http://upload-images.jianshu.io/upload_images/1869462-7e165bebd914de59.gif?imageMogr2/auto-orient/strip)

### SplashActivity
我将欢迎页面定义为一个Activity，本次我们主要关注的是动画和交互。  
通过**右键->New** 或**command+N** 找到Activity，然后创建一个EmptyActivity，创建时需要勾选上**LauncherActivity**把它作为启动后的第一个Activity，相应的会在AndroidManifest自动注册这个Activity(很方便啊！)，然后继承改成之前写好的**BaseActivity** 。注意，由于Studio自动生成的MainActivity是默认的**LauncherActivity**所以需要把它该成普通的Activity。  
此时的AndroidManifest.xml  

```
<application
    android:name=".ChiceApplication"
    android:allowBackup="true"
    android:icon="@drawable/launch_icon2"
    android:label="@string/app_name"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity android:name=".View.activitys.MainActivity">

    </activity>
    <activity android:name=".View.activitys.SplashActivity"
      android:theme="@style/FullScreen"> //设置自定义好的主题
      <intent-filter>
        <action android:name="android.intent.action.MAIN"/>

        <category android:name="android.intent.category.LAUNCHER"/>
      </intent-filter>
    </activity>
  </application>
```   

**这一有个坑需要注意** Android在绘制Activity页面之前，Window窗口默认是黑色的，可以看到上图效果中我已经把它调成了我需要的背景色，并且去掉了**状态栏** 。注意上面的AndroidManifest文件我标注地方，我使用了自定义主题。自定义主题写在values/styles.xml中。     

```
<!-- Base application theme. -->
  <style name="FullScreen" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="android:windowFullscreen">true</item>  //设置全屏
    <item name="android:windowBackground">@color/themeBlueDeep</item>  //关键：设置背景颜色
    <item name="android:windowNoTitle">true</item> //设置没有状态栏
    <item name="android:windowTranslucentNavigation">true</item> 
    <item name="android:windowTranslucentStatus">true</item> 
  </style>
```

其中themeBlueDeep颜色值为：  

```
<color name="themeBlueDeep">#52AEB4</color>
```  
下面看看SplashActivity的xml文件。  

```
<RelativeLayout
  xmlns:android="http://schemas.android.com/apk/res/android"
  xmlns:tools="http://schemas.android.com/tools"
  android:id="@+id/activity_splash"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  tools:context="com.chenbing.oneweather.View.activitys.SplashActivity">

  <ImageView
    android:id="@+id/sun"
    android:layout_width="100dp"
    android:layout_height="100dp"
    android:layout_centerHorizontal="true"
    android:layout_marginTop="90dp"
    android:src="@drawable/sun"
    />

  <ImageView
    android:id="@+id/cloud_1"
    android:layout_width="140dp"
    android:layout_height="100dp"
    android:layout_below="@+id/sun"
    android:layout_centerHorizontal="true"
    android:layout_marginTop="-50dp"
    android:src="@drawable/cloud_1"
    android:tint="@color/white"
    />

  <ImageView
    android:id="@+id/cloud_2"
    android:layout_width="90dp"
    android:layout_height="90dp"
    android:layout_below="@+id/sun"
    android:layout_centerHorizontal="true"
    android:layout_marginLeft="-20dp"
    android:layout_marginTop="-50dp"
    android:layout_toEndOf="@+id/cloud_1"
    android:src="@drawable/cloud_1"
    android:tint="@color/white"
    />

  <ImageView
    android:id="@+id/cloud_3"
    android:layout_width="130dp"
    android:layout_height="100dp"
    android:layout_below="@+id/sun"
    android:layout_centerHorizontal="true"
    android:layout_marginLeft="20dp"
    android:layout_marginTop="-40dp"
    android:src="@drawable/cloud_1"
    android:tint="@color/white"  //原本是黑色，使用tint来让它变成白色，这个属性在实际开发中能帮助我们节省很多内存，因为仅仅是颜色不同的图片，我们也只需要一张。
    />

  <com.chenbing.oneweather.CustomViews.TextView.NormalTextView
    android:id="@+id/join_now"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_alignParentBottom="true"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="180dp"
    android:text="@string/join_now"
    android:textColor="@color/white"
    android:textSize="@dimen/join_now_text_size"
    android:visibility="visible"
    />
</RelativeLayout>
```  
很简单，一个太阳、三个云都是ImageView，进入按钮是TextView。  
我对TextView进行了一个简单的二次封装，便于以后统一修改。  

```
public class NormalTextView extends TextView {
  public NormalTextView(Context context) {
    super(context);
  }

  public NormalTextView(Context context, AttributeSet attrs) {
    super(context, attrs);
  }

  public NormalTextView(Context context, AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
  }

  {
    this.setTypeface(Typeface.DEFAULT); //设置字体为默认字体
  }
}
```

从上图可以看到，这个欢迎页面主要就5个动画，我使用属性动画完成：
- 太阳做旋转动画  

```
private void playSunAnim() {
    ObjectAnimator anim = ObjectAnimator.ofFloat(sun, "rotation", 0f, 360f);
    anim.setRepeatMode(ObjectAnimator.RESTART); //因为是0-360，所以重复模式为从头开始
    anim.setRepeatCount(ObjectAnimator.INFINITE); //设置为无限重复
    anim.setInterpolator(new LinearInterpolator()); //设置为匀速动画，不然会出现明显的停顿效果。
    anim.setDuration(30 * 1000);
    anim.start();
  }
```

- 3朵云做x轴上的水平动画，都差不多，就先展示一个。完整代码到GitHub看，在**View->Activitys->SplashActivity** 。地址：[项目地址GitHub](https://github.com/chenBingX/OneWeather)。  


```
private void playCloud_1Anim() {
    //下面几步是在计算平移动画的起止点，结合xml来理解，最终实现的效果就是
    //当完全移动出屏幕左边时，立即从屏幕右边重新开始向左平移。有的为了实现层次感，它右边的起点我设置的在屏幕右边缘稍远的地方，具体的到GitHub看。
    int left = cloud_1.getLeft(); 
    int right = DisplayUtils.getScreenWidth() - cloud_1.getRight();
    int width = cloud_1.getMeasuredWidth();
    ObjectAnimator anim =
        ObjectAnimator.ofFloat(cloud_1, "translationX", right + width, -(left + width)); 
        
    anim.setRepeatMode(ObjectAnimator.RESTART);
    anim.setRepeatCount(ObjectAnimator.INFINITE);
    anim.setInterpolator(new LinearInterpolator());
    anim.setDuration(31 * 1000);
    anim.start();
  }
```
注意上面代码中，由于需要获取到**云**控件的尺寸，而在View没有被绘制时我们是无法获取的，所以需要等View绘制到屏幕上后才能获取到。怎么知道View什么时候被绘制到屏幕上呢，我们在Activity的前几个回调**onCreate()-onResume()** 中均无法获取到，但是我们又让它一旦显示就要立即开始动画，怎么办呢？不怕，可以使用**ViewTreeObserver.OnGlobalLayoutListener**来监听View什么时候被绘制到屏幕上。  

```
private void playAnimOnLayoutFinish() {
    // 需要在布局填充完成后才能获取到View的尺寸
    getWindow().getDecorView().getViewTreeObserver().addOnGlobalLayoutListener(
        new ViewTreeObserver.OnGlobalLayoutListener() {
          @Override
          public void onGlobalLayout() {
            playAnim();
            // 需要移除监听，否则会重复触发
            getWindow().getDecorView().getViewTreeObserver().removeGlobalOnLayoutListener(this);
          }
        });
  }
```

- 进入按钮做透明度动画。  

```
private void playJoinNowAnim() {
    ObjectAnimator anim = ObjectAnimator.ofFloat(joinNow, "alpha", 0.1f, 1f);
    anim.setDuration(2000);
    anim.setRepeatCount(ObjectAnimator.INFINITE);
    anim.setRepeatMode(ObjectAnimator.REVERSE);
    anim.start();
  }
```
 
接着，我们需要监听进入按钮的点击事件，一旦点击就跳入MainActivity，并**finish()**掉，不然这个Activity就会存在Activity返回栈中 。  


```
private void registerJoinNowListener() {
    joinNow.setOnClickListener(v -> jumpToMainActivity());
  }
  
private void jumpToMainActivity() {
    Intent intent = new Intent(this, MainActivity.class);
    startActivity(intent);
    finish();
  }
```

现在我们已经实现了前两个需求，第三个加载数据的需求我们下篇在一起来完成。  
本篇的具体代码请到GitHub查看，地址：[项目地址GitHub](https://github.com/chenBingX/OneWeather)。

# 资源
- [Launcher图标资源](http://i1.piimg.com/567571/613a88c7b8eecb30.png)
- [太阳](https://github.com/chenBingX/OneWeather/blob/master/app/src/main/res/drawable-xhdpi/sun.png)
- 云的图片是SVG格式的，请到GitHub项目中找。[快捷传送门](https://github.com/chenBingX/OneWeather/tree/master/app/src/main/res/drawable)
  
# [项目地址GitHub](https://github.com/chenBingX/OneWeather)

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-3131cf8570f0f380.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
