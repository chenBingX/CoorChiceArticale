# 简要说明
`RollingViewPager`继承自`RelativeLayout`，支持对其大小进行设置，在每次实例化的时候必须要先调用`initItem()`对其进行初始化，这个操作会导致轮播控制线程自动开启。当然，你仍然能够调用`stopRolling()`来停止轮播，这个操作已经处理好了释放`Handler`的消息队列。通过调用`isShowIndicator()`你可以控制底部的指示器是否显示，当然他们的**背景选择器**是可以更改的，这个操作需要在`xml`中通过定义`custom:indicatorDrawable="R.drawable.xxx"`来改变，注意需要的是**背景选择器**，并且是`enable`类型。
# 使用说明
复制`RollingViewPager.java`和`ViewPagerFragment.java`两个java文件到放`java`类的包下，`background_indicator.xml`到`drawable`下。  
**注意:** 由于使用了`Fresco`来优化图片，所以使用时，请确保导入了`Fresco`库。
# 方法、属性
## 方法
```
/**
 * 这个方法在实例化View之后必需调用，它用于初始化ViewPager的页面。
 * @param urls：图片url集合
 * @param fragmentManager：Fragment管理器
 */
public void initItem(@NonNull List<String> urls, @NonNull FragmentManager fragmentManager)
```  

```
/**
 * 是否显示指示器
 *
 * @param isShow boolean true显示，false不显示
 */
public void isShowIndicator(boolean isShow)
```  

```
/**
 * 启动轮播
 *
 * @param interval 轮播时间，需要>1000ms设置才能生效，否则使用默认时长5s
 */
public void startRolling(int interval)
```  
```
/**
 * 停止轮播，清空消息队列
 */
public void stopRolling()
```


```
/**
 * 设置轮播时间间隔，这个方法可以让你随时改变轮播速度
 *
 * @param interval 需要>=1000ms设置才能生效，否则使用默认时长5s
 */
public void setInterval(int interval)
```  

```
/**
 * 获得轮播时间间隔
 *
 * @return 返回当前轮播间隔
 */
public int getInterval()
```  
## 属性

属性 | 作用
---|---
custom:indicatorDrawable | 指定指示器的背景选择器，需要是enable类型，否则使用默认背景选择器。
custom:indicatorMarginBottom | 指示器距离ViewPager底边的距离。
custom:interval | 设置轮播间隔时长，以ms为单位。

# 使用实例
```
xml文件
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:tools="http://schemas.android.com/tools"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                tools:context="com.icechen.rxandroiddemo.MainActivity">

   <com.icechen.rxandroiddemo.View.RollingViewPager
       android:id="@+id/rollingView"
       android:layout_width="match_parent"
       android:layout_height="180dp">
   </com.icechen.rxandroiddemo.View.RollingViewPager>
</RelativeLayout>




Activity或Fragment中

for (int i = 1; i <= 5; i++) {
    urls.add("http://182.254.136.123/image/2016/ImageHD_1/" + i + ".jpg");
}
rollView = (RollingViewPager)findViewById(R.id.rollingView);
rollView.initItem(urls,getSupportFragmentManager()); //关键步骤，初始化

```
# 补充
背景选择器定义示例：
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <!--可用状态-->
    <item android:state_enabled="true">
        <shape xmlns:android="http://schemas.android.com/apk/res/android"
               android:shape="rectangle">
            <size
                android:width="10dp"
                android:height="10dp"/>
            <corners android:radius="10dp"/>
            <solid android:color="#ff7473"/>
        </shape>
    </item>

    <!--默认状态-->
    <item>
        <shape xmlns:android="http://schemas.android.com/apk/res/android"
               android:shape="rectangle">
            <size
                android:width="10dp"
                android:height="10dp"/>
            <corners android:radius="10dp"/>
            <solid android:color="#d0d0d0"/>
        </shape>
    </item>
</selector>
```




