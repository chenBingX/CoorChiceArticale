# 大有可为，SuperTextView(v3.2.0)

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/supertextview_bg.png)

Hi，开发者，来见识一下全新的 **SuperTextView** 吧！  

|||||
|:---:|:---:|:---:|:---:|
|![](https://raw.githubusercontent.com/chenBingX/img/master/stv/stv_page1.gif)|![](https://raw.githubusercontent.com/chenBingX/img/master/stv/stv_page2.gif)|![](https://raw.githubusercontent.com/chenBingX/img/master/stv/stv_page3.gif)|![](https://raw.githubusercontent.com/chenBingX/img/master/stv/stv_page4.gif)|

> 💡以上演示效果，由 **SuperTextView** 提供强力驱动支持。

**SuperTextView** 的与众不同在于，它只是一个简单的控件元素，但却不仅仅是一个控件。它生而灵动多变，强大的内嵌逻辑，为你持续提供丰富多彩却异常简单的开发支持。

**SuperTextView** 将你从复杂的渲染逻辑中解救出来，一行简单的 **API** 接口调用，炫目渲染的效果即刻而至。  

你只需享受自己所编写出的惊人视效，剩下的一切就放心交给 **SuperTextView**。  

在全新的 `v3.2.0` 版本中，**SuperTextView** 再一次重新定义了自己。开发者，来看看精心为你准备的惊喜吧！🎉🎉🎉  

# 这是... Gif ？

这一次，**SuperTextView** 为开发者带来了强力的 **Gif** 驱动支持。

如果在过去，你曾经为如何在 Android 平台上展示一张 **Gif** 图而苦恼多日，或是困于一些三方 **Gif** 库的性能深渊中迷失方向。那么现在，**SuperTextView** 将彻底改变这一现状。

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo1.gif)

**Gif** 图与 **SuperTextView** 天生就是浑然天成，因此，你可以通过你最熟悉的、最自然的方式来让一张 **Gif** 图获得展示。就像你往常展示一张普通图片那样简单。  

得益于 **c/c++** 的超高性能，以及对内存的精确操作。**SuperTextView** 通过使用 **c/c++** 为移动平台专门定制了性能强悍的 **Gif** 驱动引擎。  

**SuperTextView** 的 **Gif** 引擎，能够精确操作图像像素内存，在 **Gif** 图像的帧刷新时，只对局部像素内存进行更新，这让 **Gif** 图像渲染效率得到了质的飞跃。  

通过异步离屏渲染技术，使得 **SuperTextView** 即使在展示超大 **Gif** 图像的同时，依旧能够确保应用界面丝滑的流畅度，以及灵敏的响应速度。

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo2.gif)

> 💡以上 Demo 中，使用 **SuperTextView** 展示了一张 **近17M** 大小，包含有 **265** 帧图像的 **Gif** 图，用户界面却依旧无比流畅。

## 展示 Gif 超简单

在 **SuperTextView** 中展示一张 **Gif** 图，超简单！

你可以直接 **XML** 布局文档中配置，或者在代码中进行添加。  

### 在 **XML** 中配置 **Gif**

```
<com.coorchice.library.SuperTextView
    android:id="@+id/stv_1"
    android:layout_width="match_parent"
    android:layout_height="150dp"
    app:stv_state_drawable="@drawable/gif_1" />
```

你可以像配置一张普通图片一样，为 **SuperTextView** 配置展示 **Gif** 图。  

### 在代码中配置 Gif

```
stv_1 = (SuperTextView) findViewById(R.id.stv_1);
stv_1.setDrawable(R.drawable.gif_1);
```

就是这么简单、自然，**SuperTextView** 可以让开发者毫无感知的，就能配置展示一张 **Gif** 图。  

在 **SuperTextView** 的内核逻辑中，**SuperTextView** 会智能的对普通图和 **Gif** 图进行分类，然后作出相应的处理和优化。

### 展示网络 **Gif** 一样简单

如果你的 **Gif** 图不在本地，而在云端，怎么办？

你无需烦恼！一切放心交给 **SuperTextView**。

```
stv_1 = (SuperTextView) findViewById(R.id.stv_1);
stv_1.setUrlImage("http://example.com/images/example.gif");
```

只需一行代码，**SuperTextView** 会在后台协助你完成 **Gif** 图的加载，然后处理渲染到屏幕上。

> 💡实际上，**SuperTextView** 的 **Drawable1** 和 **Drawable2** 图像展示位，均可用来展示 **Gif** 图。总之，一切都是你所熟悉的样子。


## 你可以掌控的更多

**SuperTextView** 所提供给开发者的不仅仅是展示 **Gif** 图这么简单，你可以掌控更多的细节。  

### 播放/暂停

你可以随时控制 **Gif** 图，播放，或者暂停。  

```
if (stv.getDrawable() instanceof GifDrawable) {
  // 先获取到 GifDrawable 对象
  GifDrawable gifDrawable = (GifDrawable) stv.getDrawable();

  // 播放
  gifDrawable.play();

  // 暂停
  gifDrawable.stop();
}
```

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo3.gif)

### 跳转/获取指定帧

在 **SuperTextView** 中，你可以随时到达你指定的帧图像，以及能够提取得到到指定帧的图像。

```
if (stv.getDrawable() instanceof GifDrawable) {
  // 先获取到 GifDrawable 对象
  GifDrawable gifDrawable = (GifDrawable) stv.getDrawable();

  // 跳转到指定帧
  gifDrawable.gotoFrame(pre);

  // 获取指定帧
  Bitmap frame = gifDrawable.getFrame(i);
}
```
> 💡由于 **SuperTextView** 能够支持局部增量渲染，所以当你的 **Gif** 支持这种渲染模式时，意味着你可能需要通过调用 `gifDrawable.setStrict(true)` 开启 **严格模式**，来确保帧跳转或者帧提取的图像是正确的。这可能会花费一些时间，所以你应该尽量将 **严格模式** 下的操作在异步线程中进行。  

### 快慢，随你心

**SuperTextView** 允许你随意的修改 **Gif** 图的播放速率。  

```
if (stv.getDrawable() instanceof GifDrawable) {
  // 先获取到 GifDrawable 对象
  GifDrawable gifDrawable = (GifDrawable) stv.getDrawable();

  // 设置帧播放间隔时间，20ms
  gifDrawable.setFrameDuration(20);
}
```

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo4.gif)  

### 你可以了若指掌

通过 **SuperTextView** 你可以对一张 **Gif** 图像的信息了若指掌。  

- 获取 **Gif** 尺寸

    ```
    // 获取宽度
    int width = gifDrawable.getWidth();

    // 获取高度
    int height = gifDrawable.getHeight();
    ```

- 获取 **Gif** 帧信息

    ```
    // 获取帧数
    int frameCount = gifDrawable.getFrameCount();

    // 获取当前帧间隔
    int frameDuration = gifDrawable.getFrameDuration();

    // 获取当前渲染到那一帧
    int framePotision = gifDrawable.getCurrentFrame();

    // 是否在播放
    boolean isPlaying = gifDrawable.isPlaying();
    ```

## 更出彩的 Gif

**SuperTextView** 凭借对 **Gif** 渲染的无缝融合，此前 **Drawable1** 和 **Drawable2** 的一切配置项，在展示 **Gif** 图时，也同样能够生效。

### Gif 作为普通 Drawable
    app:stv_state_drawable_rotate="90"

来看看在原本的 **Drawable** 位置放上一张 **Gif** 图会发生什么神奇的事情。

```
<com.coorchice.library.SuperTextView
    android:layout_width="match_parent"
    android:layout_height="50dp"
    android:paddingLeft="62dp"
    android:paddingRight="10dp"
    android:text="小火箭发射了！啦啦啦啦啦啦..."
    android:textColor="#ffffff"
    android:textSize="22dp"
    app:stv_corner="6dp"
    app:stv_isShowState="true"
    app:stv_solid="#0D1831"

    // 设置 Gif
    app:stv_state_drawable="@drawable/gif_1"

    // 设置 Gif 高
    app:stv_state_drawable_height="40dp"

    // 设置 Gif 宽
    app:stv_state_drawable_width="40dp"

    // 设置 Gif 居左展示
    app:stv_state_drawable_mode="left"

    // 设置 Gif 左间距
    app:stv_state_drawable_padding_left="10dp"/>
```

效果是...

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo5.gif)  


现在，我们将 **Gif** 旋转 90 度试试。

```
<com.coorchice.library.SuperTextView
    ...
    // 设置 Gif 旋转 90 度
    app:stv_state_drawable_rotate="90"
    ...
/>
```

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo6.gif)  


### 将 Gif 圆角化

**SuperTextView** 不可思议的实现了 **Gif** 图的圆角化，为开发者打开了一扇全新的大门。  


![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo7.gif)  


然而，这种效果的却是惊人的简单。

```
<com.coorchice.library.SuperTextView
    android:layout_width="185dp"
    android:layout_height="138.75dp"
    android:layout_gravity="center_horizontal"
    app:stv_corner="20dp"

    // 设置 Gif 作为控件背景
    app:stv_drawableAsBackground="true"

    app:stv_scaleType="fitCenter"

    // 配置 Gif
    app:stv_state_drawable="@drawable/gif_1" />
```

### 为 Gif 加上边框

开发者甚至可以轻而易举的给一张 **Gif** 图加上边框。  

```
<com.coorchice.library.SuperTextView
    android:layout_width="350dp"
    android:layout_height="148.4dp"
    android:layout_gravity="center_horizontal"
    android:gravity="center"

    // 加上文字会显的更有格调
    android:text="SuperTextView"

    android:textSize="36dp"
    android:textStyle="bold"
    android:visibility="invisible"
    app:stv_corner="6dp"
    app:stv_drawableAsBackground="true"
    app:stv_isShowState="true"
    app:stv_scaleType="center"
    // 设置边框颜色
    app:stv_stroke_color="@color/opacity_8_gray_4c

    // 设置边框宽度
    app:stv_stroke_width="5dp"

    app:stv_text_fill_color="#ccffffff"
    app:stv_text_stroke="true"
    app:stv_text_stroke_color="#cc000000"
    app:stv_text_stroke_width="2dp"

    // 配置 Gif
    app:stv_state_drawable="@drawable/gif_1"/>
```

效果即刻呈现...  


![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo8.gif)


### 轻松实现动态头像

在过去，一些炫酷的动效，往往会止步于实现的复杂和成本上。而 **SuperTextView** 为你带了更多的可能，你的灵感可以无拘无束。  

比如，动态头像的实现，会比以往更加的简单。  

```
<com.coorchice.library.SuperTextView
    android:layout_width="80dp"
    android:layout_height="80dp"
    android:layout_marginLeft="30dp"
    app:stv_corner="40dp"

    // 设置为背景图
    app:stv_drawableAsBackground="true"

    // 配置 Gif 头像
    app:stv_state_drawable="@drawable/gif_avatar"

    // 添加边框
    app:stv_stroke_color="#ffffff"
    app:stv_stroke_width="3dp"
    />
```

在代码中，你可以直接配置一张网络动态头像。

```
stv.setUrlImage("http://gif_avatar.gif");
```

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo9.gif)

# 更多的惊喜

## 两个 Drawable 都支持点击！

在新版本的 **SuperTextView** 中，**Drawable1** 和 **Drawable2** 被赋予了全新的能力 —— 支持精确点击动作。  

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/gif_demo10.gif)

**SuperTextView** 通过监控点击动作发生的位置，能够将点击动作准确的定位到所发生的区域中（**Drawable1**、**Drawable2** 或者 **其它区域**），然后触发相应的监听回调。  

你可以为 **SuperTextView** 设置 **Drawable** 上的点击动作监听器，以便在动作发生时，作出准确的响应。  

```
stv.setOnDrawableClickedListener(new SuperTextView.OnDrawableClickedListener() {
    @Override
    public void onDrawable1Clicked(SuperTextView stv) {
        // Drawable1 clicked，do something...
    }
    @Override
    public void onDrawable2Clicked(SuperTextView stv) {
        // Drawable2 clicked，do something...
    }
});

stv.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        // 非 Drawable 区域被点击，do something...
    }
});
```

## 背景图缩放模式支持

现在，当你将 **Drawable1** 作为背景图时，你可以为它配置不同的缩放模式，以达到你心仪的效果。  

```
stv.setScaleType(ScaleType.CENTER);
```

**SuperTextView** 为开发者提供了多达 3 种的缩放模式：

- `ScaleType.FIT_XY`

    将图片拉伸平铺。

- `ScaleType.FIT_CENTER`

    将图片剪裁居中。

- `ScaleType.CENTER`

    将图片自适应居中。默认值。


![](https://raw.githubusercontent.com/chenBingX/img/master/stv/stv_scaletype.png)


## 其它更新

- 在 **XML** 布局文档中，**Drawable1** 和 **Drawable2** 现在支持直接设置 **颜色** 或者 **ShapeDrawable**。  

    ```
    // circle_f9ad36 为 xml 中编写的 shape 文件
    app:stv_state_drawable="@drawable/circle_f9ad36"

    // 使用纯色作为 Drawable
    app:stv_state_drawable="#000000"
    ```

- ⚠️ 最低支持版本 **API** 提升到 **19**。

- 渲染性能比过去提升至少 **30%**。

- 升级默认图片加载引擎，支持智能缓存。也许现在，你不必再引入第三方图片加载库了。


# 如何开始 SuperTextView v3.2.0 ？

在项目 `build.gradle` 中加入：

```
dependencies {
    ...

    implementation 'com.github.chenBingX:SuperTextView:v3.2.0'

    ...
}
```

# 传送门区域

- [【传送门】：SuperTextView 官方项目地址](https://github.com/chenBingX/SuperTextView)

- [【传送门】：《SuperTextView 开发参考文档》- 你可以学习到如何使用 SuperTextView 来提高你的应用的构建效率](https://github.com/chenBingX/SuperTextView/wiki/%E9%A6%96%E9%A1%B5)

- [【传送门】：《SuperTextView API文档》— 你可以查看 SuperTextView 所有可用 API 及属性](https://chenbingx.github.io/SuperTextView/SuperTextView-doc/index.html)

-------


> - 如果你喜欢 [**SuperTextView**](https://github.com/chenBingX/SuperTextView)，就到 [**Github**](https://github.com/chenBingX/SuperTextView) 点个 **star** [🌟](https://github.com/chenBingX/SuperTextView) 支持哦！

> - **CoorChice** 会不定期的在博客平台分享干货，快进入 [CoorChice的【个人主页】](https://juejin.im/user/57fc43b67db2a200595ffd94) 关注一波吧。
  













