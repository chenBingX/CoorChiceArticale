![image](http://upload-images.jianshu.io/upload_images/1869462-4a20be6d7fcbab33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

# [本系列文章列表](http://www.jianshu.com/nb/8017443)  
# 先唠两句嗑
这个系列的博客是我去年开篇的，目的是为了通过一个简单的App和大家一起探讨、梳理一下Android的App设计过程中的思想，主要以时下流行的MVP架构为基础。当然，文中的思想和实现思路仅代表我个人的思考，可能会有不合理的地方，我希望能够被大家所指出，毕竟“只缘身在此山中”。  

这是款简单的入门级App，我希望能帮助刚接触Android开发不久的同学能够较为轻易的看懂，并且能够参与到关于App的设计思考中，熟悉Android应用开发的过程。  

此前这个系列的文章我已经写了9篇了，最为重要的主页展示功能已经实现，后面将会不断的完善、修改，添加新特性。这也就是平时开发过程中的迭代过程。首次看到本系列文章，又感兴趣的同学可以点击最上方的[本系列文章列表](http://www.jianshu.com/nb/8017443) ，你就可以看一下之前的内容，然后结合[Github项目源码](https://github.com/chenBingX/OneWeather)（点击蓝色字体传送至Github）看一看。相信你绝对可以很快进入状态的。  

由于没有大段的时间来写这个系列的东西，所以我不能保证定期更新。还请谅解。如果你感兴趣，请关注这个系列的专题。一般来说，我可能会先完成Github的的代码，才会抽空开始写相关博客。如果你想获得本项目的最新动态，你可以关注[Github项目源码](https://github.com/chenBingX/OneWeather)（点击蓝色字体传送至Github）。我会把该App中使用到的部分知识点简单的提一下，以帮助刚接触Android开发的同学能够更好的阅读。  

好了，关于博客这个东西，我也在探索中。所以，其中有什么不妥还请大家指教。我希望能不断尝试改进，不断把分享知识变得更让人舒适，以吸引越来越多的人参与到队伍中。  
# 简要天气列表及主页联动
老规矩，先来一张效果图供大家乐呵乐呵。 

![image](http://upload-images.jianshu.io/upload_images/1869462-7c579bbb7e7fc0f4.gif?imageMogr2/auto-orient/strip)  
  
## 需求
如上图所示，主要需求有以下几点：
- 点击右下角图标显示简要天气列表。
- 简要天气列表包含一个搜索框，总是在列表最下方，可以搜索并添加新城市的天气信息。
- 点击简要天气列表的条目，可以直接跳转到对应的详情页。
## 撸起袖子开整
### 一个遗留的问题
最近在米4上调试这款应用，发现百度SDK的定位总是失败，无法获取到正确的定位信息。说是没有以下两个权限：
```
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```
我明明在AndroidManifest.xml中注册了啊，为什么还说没有！这才留意到，米4是6.0的系统啊🤣！  
Android 6.0开始，出于安全性考虑，一些特殊权限需要动态申请！比如定位这种比较隐私的权限就属于特殊权限的范畴。那么什么是“动态申请”呢？就是在代码里申请喽。具体的大家可以参考这个链接[【聊一聊Android 6.0的运行时权限】http://droidyue.com/blog/2016/01/17/understanding-marshmallow-runtime-permission/index.html](http://droidyue.com/blog/2016/01/17/understanding-marshmallow-runtime-permission/index.html)，讲的比较详细的。  
下面我直接贴下需要新增加的代码。  

**SplashActivity**
```
//请求码
 private static final int LOCATION_PERMISSION_REQUEST_CODE = 100;


//initData()方法修改如下：
@Override
  protected void initData() {
    requestWeatherData();
  }

  private void requestWeatherData() {
   //Api大于23，即6.0以上才检查权限。
    if (AppUtils.getSdkVersion() >= 23) {
      checkPermissionAndRequest();
    } else {
      presenter.requestWeatherData();
    }
  }

  private void checkPermissionAndRequest() {
    if (checkLocationPermissions()) {
      String[] permissions = {Manifest.permission.ACCESS_COARSE_LOCATION};
      //发起请求权限
      requestPermissions(permissions, LOCATION_PERMISSION_REQUEST_CODE);
    } else {
      presenter.requestWeatherData();
    }
  }

  private boolean checkLocationPermissions() {
  //Context提供了权限检查的方法
    return checkSelfPermission(
        Manifest.permission.ACCESS_COARSE_LOCATION) != PackageManager.PERMISSION_GRANTED;
  }
  
 //Activity中多了一个回调，用于处理用户的选择结果
 @Override
  public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    switch (requestCode) {
      // requestCode即所声明的权限获取码，在checkSelfPermission时传入
      case LOCATION_PERMISSION_REQUEST_CODE:
        if (grantResults[0] == PackageManager.PERMISSION_GRANTED) {
          //获得权限时才进行定位请求
          presenter.requestWeatherData();
        } else {
          //请一定要对权限被拒绝的情况进行处理
          ToastUtil.showShortToast("定位服务需要打开定位权限才能正常使用！");
        }
        break;
      default:
        break;
    }
  }
```
**具体的可以点击链接在[【Github查看完整源码】https://github.com/chenBingX/OneWeather/blob/29484fac79c621215e5b520613f8b3efefe184ef/app/src/main/java/com/chenbing/oneweather/View/activitys/MainActivity.java](https://github.com/chenBingX/OneWeather/blob/29484fac79c621215e5b520613f8b3efefe184ef/app/src/main/java/com/chenbing/oneweather/View/activitys/MainActivity.java)**  

  



下面是对你细心成程度的考验。上面这段代码有什么问题？  

。  
。  
。  
好吧，也许你已经发现，前面明明说需要两个权限，可是我居然只申请了一个！
但是，打开App你会发现已经能成功定位了。Why？其实，Android团队还是比较体贴的。相关的权限我们只需要申请到一个，其它就默认跟着一起获取到了。比如这里我们需要的这两个定位权限。  

Ok，这个坑占时填到这。  

## 需求实现
效果图可以看出，这个简略天气列表用一个**RecyclerView**就能完美实现。稍有不同的就是这个RecyclerView中有两种类型的Item。
- 一种是简略的天气信息条目
- 一种是始终在底部的搜索框

理清思路后，可以开始动手了。  
由于只需要添加一个**RecyclerView**就可以了，所以我把它直接放到**MainActivity**中，通过显示／隐藏来达到切换到目的。这样做的好处是轻量化，所以响应比较快。并且后期添加一些动画效果将会变的更容易进行。那么有同学可能会说，这样不是加大**MainActivity**的负担了吗？其实我认为并没有多大影响。首先我们主要的数据适配工作是在**Adapter**中完成，而**Adapter**又会把更具体的数据适配工作转交给相应的**ItemView**完成。**MainActivity**只承担了发起数据请求和将数据传给**Adapter**的工作，所以并不会增加多少复杂度。当然，这谨代表我的思路。这事儿也是仁者见仁，智者见智的。  
### 在activity_main.xml中添加一个RecyclerView。
```
<!--展示天气详情的ViewPager-->

<android.support.v7.widget.RecyclerView
    android:id="@+id/weather_list"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:visibility="gone"
    tools:visibility="visible"
    tools:listitem="@layout/item_weather_list"
    />
    
<!--底部操作栏-->    
```
- 【小知识】上面你可能会注意到我给RecyclerView使用了两个tools前缀的属性：
```
tools:visibility="visible"
tools:listitem="@layout/item_weather_list"
```
它们的主要作用就是使你能够在Android Studio的Preview中能够预览相关属性的效果，但是对实际的运行效果不会产生影响。这里tools所代表的命名空间为：**"http://schemas.android.com/tools"**。当然，这个代表名称是可以随便取的，如果愿意叫**CoorChice**也是可以的。只要命名空间是http://schemas.android.com/tools就行了。  
看看Preview的效果。我设置了**android:visibility="gone"**，但是同时设置了**tools:visibility="visible"**，所以在Preview中RecyclerView默认可见。但当你运行项目后会发现，RcyclerView默认是隐藏的。  
![image](http://upload-images.jianshu.io/upload_images/1869462-92f094d7e1924e2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- 【小知识】**tools:listitem="@layout/item_weather_list"**的作用如你所见，上图中显示出来了RecyclerView的ItemView。它不仅仅适用于RecyclerView，对ListView等列表控件都适用。

上面这两个特性在平时开发过程中很用。控件的其他属性也可以使用这种方式来在Preview中预览效果。  
  
 [【activity_main.xml Github源码链接】https://github.com/chenBingX/OneWeather/blob/9d22482f34a387d727d2e6bb9d8a6dd222969b3d/app/src/main/res/layout/activity_main.xml](https://github.com/chenBingX/OneWeather/blob/9d22482f34a387d727d2e6bb9d8a6dd222969b3d/app/src/main/res/layout/activity_main.xml)  
 
---
### 初始化RecyclerView
RecyclerView的初始化主要看一下Adapter就好了，其它没什么特别的。
```
//创建Adapter
weatherListAdapter = new WeatherListAdapter(this, simpleWeathers);
//由于点击Item之后的主要操作需要MainActivity中的成员变量进行，比起
//传入参数，最好的方式就是接口回调。
weatherListAdapter.setOnItemClickListener((v, position) -> {
      AppUtils.hideInputMethod(v);  //隐藏输入法
      showWeatherList(false); //隐藏简略天气列表
      pagerContainer.setCurrentItem(position);  //跳转到对应详情页
    });
rvWeatherList.setAdapter(weatherListAdapter);
```
简要天气列表的数据是在每个创建每个天气详情页的Fragment的时候截取生成的。这一部分需要你到源码中看一下，我就不在这写。[【Github项目源码https://github.com/chenBingX/OneWeather】](https://github.com/chenBingX/OneWeather)  

**WeatherListAdapter.java**  

Adapter中比较重要的就是底部搜索框的实现。我们看看其中的几个关键方法。
```
@Override
public int getItemCount() {
    //这里+1很重要。我们传过来的datas数据只包含了天气信息，所以RecyclerView的
    //Item总数因该是datas的大小+一个底部搜索框。
    return datas.size() + 1;  
}

@Override
  public int getItemViewType(int position) {
    int type = ITEM_NORMAL;
    //记得List是从0开始计数的哦！所以小于datas.size()的是天气信息条目
    //等于的就把它定义为底部的搜索框。
    //这两个方法必须配合使用，不然RecyclerView的position的值是取不到datas.size()的
    //因为position也是从0开始计数
    if (position < datas.size()){
      type = ITEM_NORMAL;
    } else if (position == datas.size()){
      type = ITEM_FOOT;
    }
    return type;
  }
  
  
@Override
  public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
  //这里viewType的值就来自于getItemViewType方法
    if (viewType == ITEM_NORMAL){
      //天气信息条目
      return new BaseItemViewHolder(new WeatherListItem(mContext));
    } else if (viewType == ITEM_FOOT){
      //底部搜索框
      return new BaseItemViewHolder(new WeatherListItemFooter(mContext));

    }
    return null;
  }
  
  @Override
  public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
    if (getItemViewType(position) == ITEM_NORMAL){
      ((WeatherListItem)holder.itemView).setData(datas.get(position), position); //把数据设置的具体逻辑交给ItemView处理，以轻量化Adapter
      //需要注意，点击事件只需要给天气条目设置就好。如果给底部搜索框也设置就Bug了
      holder.itemView.setOnClickListener(v -> {
        if (onItemClickListener != null) {
          //就是上面在MainActivity中用到的回调
          onItemClickListener.onItemClick(v, position);  
        }
      });
    } else if (getItemViewType(position) == ITEM_FOOT){
      if (position == 0){
        ((WeatherListItemFooter)holder.itemView).setPosition(position);
      }
    }
  }
```
- 【小知识】**接口回调** 的实质简单点讲就是不同的类同时依赖同一个对象，这个对象的某个方法在一个地方被调用，自然就会执行方法里的逻辑。
而这个对象的具体逻辑是在另一个类里写的，所以就相当于执行该类里的代码了。这其实和你创建一个A类，然后在B类中实例化一个A类对象，接着调用该实例的方法，就会执行A类中的逻辑是一个道理。  
在这里，我们把OnItemClickListener对象的逻辑以内部类的形式写在MainActivity中，然后创建一个对象实例传递到Adapter中，再在Adapter中调用该实例的onItemClick()方法，就会执行写在MainActivity中的onItemClick()方法的逻辑了。    

听起来挺屌的Hook技术其实也就是这个简单的原理。感兴趣的同学可以看看我的这篇文章，简单的介绍了一下如何使用Hook技术。链接在此！[【其实用高大上的Hook技术动态注入代码很简单，一看就会！】http://www.jianshu.com/p/14d6aa8c026d](http://www.jianshu.com/p/14d6aa8c026d)  

---

这里我把OnItemClickListener接口以静态内部类的形式写在Adapter中。因为考虑到它的专用性比较强，分散出去结构就太散了。
```
  public void setOnItemClickListener(OnItemClickListener onItemClickListener) {
    this.onItemClickListener = onItemClickListener;
  }

  public static interface OnItemClickListener {
    void onItemClick(View v, int position);
  }
```
关于Adapter就这么多，详细的内容可以到[【Github源码】https://github.com/chenBingX/OneWeather/blob/469abd6b78c185d370b5f339ce924d126ca616f7/app/src/main/java/com/chenbing/oneweather/adapters/WeatherListAdapter.java](https://github.com/chenBingX/OneWeather/blob/469abd6b78c185d370b5f339ce924d126ca616f7/app/src/main/java/com/chenbing/oneweather/adapters/WeatherListAdapter.java)查看。

## ItemView
天气信息的ItemView没什么特别的东西，就不提了。主要看一下底部的搜索框。  

![image](http://upload-images.jianshu.io/upload_images/1869462-9b7db60c397fbe8b.gif?imageMogr2/auto-orient/strip)  
先看一下xml文件。  
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:layout_width="match_parent"
  android:layout_height="wrap_content"
  xmlns:app="http://schemas.android.com/apk/res-auto"
  xmlns:tools="http://schemas.android.com/tools"
  android:background="@color/opacity_3_5_black"
  android:padding="10dp"
  android:id="@+id/root"
  >

  <LinearLayout
    android:id="@+id/search_container"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_alignParentLeft="true"
    android:layout_toLeftOf="@+id/cancel"
    android:layout_below="@+id/header_info"
    android:background="@drawable/shape_weather_list_item_footer_et"
    >

    <!--这是我自定义的一个控件，能够方便的做出一些效果，比如这个
    圆角搜索图标-->
  <com.chenbing.oneweather.CustomViews.RoundCornerTextView
    android:layout_width="30dp"
    android:layout_height="match_parent"
    app:state_drawable="@drawable/search"
    app:solid="@color/opacity_3_black"
    app:isShowState="true"
    app:corner="5dp"
    />

  <EditText
    android:id="@+id/search"
    android:layout_width="match_parent"
    android:layout_height="30dp"
    android:textSize="10sp"
    android:textColor="@color/opacity_8_white"
    android:textColorHint="@color/opacity_8_white"
    android:fadingEdge="none"
    android:singleLine="true"
    android:background="@null"
    android:paddingLeft="5dp"
    android:paddingRight="5dp"
    android:textCursorDrawable="@drawable/shape_et_cursor"
    />
  </LinearLayout>

  <!--我对TextView都进行了一下包装，方便以后扩展修改-->
  <com.chenbing.oneweather.CustomViews.TextView.NormalTextView
    android:id="@+id/cancel"
    android:layout_width="wrap_content"
    android:layout_height="30dp"
    android:text="@string/cancel"
    android:textSize="14sp"
    android:gravity="center"
    android:layout_alignParentRight="true"
    android:layout_below="@+id/header_info"
    android:layout_marginLeft="8dp"
    android:textColor="@color/opacity_8_white"
    />

  <android.support.v7.widget.RecyclerView
    android:id="@+id/city_list"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_below="@+id/search_container"
    tools:listitem="@layout/item_text_view"
    android:padding="10dp"
    android:overScrollMode="never"
    android:scrollbars="none"
    android:visibility="gone"
    />

  <com.chenbing.oneweather.CustomViews.TextView.NormalTextView
    android:id="@+id/not_found"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_below="@+id/search_container"
    android:padding="5dp"
    android:text="@string/search_not_found"
    android:textColor="@color/opacity_5_white"
    android:layout_margin="10dp"
    android:visibility="gone"
    />

  <com.chenbing.oneweather.CustomViews.TextView.NormalTextView
    android:id="@+id/header_info"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="@string/weather_list_footer_title"
    android:gravity="center"
    android:textSize="14sp"
    android:textColor="@color/opacity_8_white"
    android:layout_alignParentTop="true"
    android:layout_alignParentStart="true"
    android:layout_marginBottom="5dp"
    />


</RelativeLayout>
```
[【Github源码】https://github.com/chenBingX/OneWeather/blob/master/app/src/main/res/layout/item_weather_list_footer.xml](https://github.com/chenBingX/OneWeather/blob/master/app/src/main/res/layout/item_weather_list_footer.xml)  
这个xml布局已经足够让UI界面展示出来了。需要注意的一点是，我把根布局设置成了wrap_content，因为它有一个RecyclerView，就是展示搜索结果的列表，它的高度是不确定的。  
  
- 关于RoundCornerTextView可以看这里[【震惊！这个控件绝对值得收藏。轻松实现圆角、文字描边、状态指示等效果】http://www.jianshu.com/p/cfe18cbc6924](http://www.jianshu.com/p/cfe18cbc6924)

- 【小知识】android:background="@drawable/shape_weather_list_item_footer_et"中使用了一个shape来实现圆角背景。

```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
  android:shape="rectangle"  //这里指定基础的shape
  >
  <corners android:radius="5dp"/> //这个参数控制圆角大小
  <solid android:color="@color/opacity_5_black"/>  //这个参数控制shape的颜色。

</shape>
```

Shape在开发过程中经常会用到，所以Google一下能够找到大量的资料。现在我也会使用RoundCornerTextView来代替Shape，实现各种各样的背景效果，这种方式更容易进行改变和扩展。
  
 这个WeatherListItemFooter虽然是一个自定义控件，但它需要处理稍复杂的功能，所以它可以依赖一个Presenter来解耦逻辑。就像Activity和Fragment那样，使用MVP结构。这个Item中最重要的内容就是处理搜索了。  

先看看它的Presenter模块的代码。
```
//同样先定义接口
public interface WeatherListItemFooterPresenterApi extends BasePresenter {

  //搜索匹配城市
  void matchCity(String content);
}

//实现类
public class WeatherListItemFooterPresenter
    implements
      WeatherListItemFooterPresenterApi,
      CityListModel.OnMatchedListener {
  //依赖抽象
  private WeatherListItemFooterView view;
  private CityListModelApi model;

  public WeatherListItemFooterPresenter(WeatherListItemFooter view) {
    this.view = view;
    this.model = new CityListModel(); //实例化一个Model，下面再看Model模块
    model.setOnMatchedListener(this); //设置监听器，以获取搜索匹配的结果
  }

  @Override
  public void matchCity(String content) {
    model.matchCity(content); //让Model开始搜索匹配
  }

  @Override
  public void onMatched(List<String> cities) {
    view.onMatched(cities);  //让View更新数据
  }

  @Override
  public void destroy() {
    model = null;
    view = null;
  }
}
```

Model模块。
```
//先定义接口
public interface CityListModelApi {

  void matchCity(String content); //匹配城市

  void setOnMatchedListener(CityListModel.OnMatchedListener onMatchedListener); //设置监听
}

//实现。主要就是读取城市列表文件，然后匹配。当然读取一次之后就缓存到内存中，因为它的使用频率比较高的。
public class CityListModel implements CityListModelApi {

  private OnMatchedListener onMatchedListener; 

  @Override
  public void matchCity(String content) {
    //使用RxJava来实现
    Observable.create(new Observable.OnSubscribe<List<City>>() {
      @Override
      public void call(Subscriber<? super List<City>> subscriber) {
      //获取城市列表。内存有就直接取，否则从Assets读取。
        List<City> cityList = getCityList(); 
        subscriber.onNext(cityList);
        subscriber.onCompleted();
      }
    })
        //变换线程
      .subscribeOn(Schedulers.io()) 
      .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Subscriber<List<City>>() {
          @Override
          public void onCompleted() {}

          @Override
          public void onError(Throwable e) {
            e.printStackTrace();
          }

          @Override
          public void onNext(List<City> cityList) {
            match(cityList, content);  //匹配
          }
      });
  }

  private List<City> getCityList() {
    List<City> cityList = DataCache.getInstance().get(DataCache.Key.CITY_LIST, ArrayList.class);
    if (cityList == null) {
      cityList = getCityListFromAssets();
    }
    return cityList;
  }

  private List<City> getCityListFromAssets() {
    InputStream inputStream = null;
    try {
      List<City> cityList;
      //打开读取Assets文件的通道
      inputStream = ChiceApplication.getAppContext().getAssets().open("CityArray.JSON");
      int size = inputStream.available();
      byte[] bytes = new byte[size];
      inputStream.read(bytes);
      inputStream.close();

      String string = new String(bytes);
      Type type = new TypeToken<List<City>>() {}.getType();
      cityList = GsonUtils.getSingleInstance().fromJson(string, type);

      //缓存，下次就不用再读了
      DataCache.getInstance().add(DataCache.Key.CITY_LIST, cityList);
      return cityList;

    } catch (IOException e) {
      e.printStackTrace();
      if (inputStream != null) {
        try {
          inputStream.close();
        } catch (IOException e1) {
          e1.printStackTrace();
        }
      }
      return Collections.emptyList();
    }
  }

  private void match(final List<City> cityList, final String content) {
    Observable.create(new Observable.OnSubscribe<List<String>>() {
      @Override
      public void call(Subscriber<? super List<String>> subscriber) {
        //
        List<String> result = matchAndReturnResult(cityList, content);
        subscriber.onNext(result);
        subscriber.onCompleted();
      }
    })
        .subscribeOn(Schedulers.io())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Subscriber<List<String>>() {
          @Override
          public void onCompleted() {
          }

          @Override
          public void onError(Throwable e) {
          }

          @Override
          public void onNext(List<String> result) {
            if (onMatchedListener != null) {
              //回调结果
              onMatchedListener.onMatched(result);
            }
          }
        });
  }

  @NonNull
  private List<String> matchAndReturnResult(List<City> cityList, String content) {
    List<String> result = new ArrayList<>();
    for (City city : cityList) {
      String cityName = city.getAreaname();
      if (cityName.contains(content)) {
        result.add(cityName);
      }
    }
    return result;
  }

  @Override
  public void setOnMatchedListener(OnMatchedListener onMatchedListener) {
    this.onMatchedListener = onMatchedListener;
  }

  //因为是专门的监听，所以写在该类中就好。
  public static interface OnMatchedListener {
    void onMatched(List<String> cities);
  }
}
```

现在在View模块WeatherListItemFooter中已经能获取到匹配数据了。展示数据就是RecyclerView，就不再多说了。
- 【小知识】这里主要说一下关键字变白的实现。这里使用了SpannableString在Adapter中来实现。
```
private void setItemViewData(RecyclerView.ViewHolder holder, int position) {
    if (!TextUtils.isEmpty(matchContent)){
      //创建一个SpannableString
      SpannableString ss = new SpannableString(datas.get(position));
      //获取关键字的位置
      int index = datas.get(position).indexOf(matchContent);
      //获取关键字的长度
      int length = matchContent.length();
      //将关键字部分设置为白色
      ss.setSpan(new ForegroundColorSpan(Color.WHITE), index, index + length,
        Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
      //设置文字
      ((TextView) (holder.itemView)).setText(ss);
    } else {
      ((TextView) (holder.itemView)).setText(datas.get(position));
    }
  }
```

可以看到SpannableString的使用十分简单，但实现的效果可不简单。它还能实现图片插入等诸多效果。想要详细了解的同学可以参考下这个链接：[【android中用Spannable在TextView中设置超链接、颜色、字体】http://aichixihongshi.iteye.com/blog/1207503](http://aichixihongshi.iteye.com/blog/1207503)

---
城市匹配列表的条目的点击事件我把它用接口回调的方式传递到WeatherListItemFooter进行处理，因为点击后需要WeatherListItemFooter中的UI进行相应的变化。
```
cityListAdapter.setOnItemClickListener((v, cityName) -> {
      if (!TextUtils.isEmpty(cityName)){
        CityNameEvent cityNameEvent = new CityNameEvent();
        cityNameEvent.cityName = cityName;
        //使用RxBus发送选中的名字到MainActivityPresenter进行处理
        RxBus.get().post(cityNameEvent);  
        //需要重置一下搜索框
        clearSearch(); 
      }
    });
```

MainActivityPresenter接收到CityNameEvent
```
private void addCityWeatherRxBus() {
    //注册一下事件。就像使用EventBus一样。
    RxBus.get().register(this, CityNameEvent.class).subscribe(new Subscriber<CityNameEvent>() {
      @Override
      public void onCompleted() {

      }

      @Override
      public void onError(Throwable e) {
        e.printStackTrace();
      }

      @Override
      public void onNext(CityNameEvent cityNameEvent) {
        String cityName = cityNameEvent.cityName;
        if(view != null){
          LogUtils.e("cityName = " + cityName);
          view.addCityPage(cityName); //添加一页新的天气信息
        }
      }
    });

  }
```

[【CityListAdapter源码Github】https://github.com/chenBingX/OneWeather/blob/5dc31236db48b1525d96d5b1e0f388aa9e0b3aa9/app/src/main/java/com/chenbing/oneweather/adapters/CityListAdapter.java](https://github.com/chenBingX/OneWeather/blob/5dc31236db48b1525d96d5b1e0f388aa9e0b3aa9/app/src/main/java/com/chenbing/oneweather/adapters/CityListAdapter.java)  

**RxBus基于RxJava，是我用来替代EventBus的，这样可以少依赖一个库，并且可以自己来控制实现细节。可能还有问题，但暂时没发现，所以现在RxBus能工作的很好，即使是在多线程的情况下。如果你想要了解它实现可以看一下我的源码：[【RxBus源码Github】https://github.com/chenBingX/OneWeather/blob/469abd6b78c185d370b5f339ce924d126ca616f7/app/src/main/java/com/chenbing/oneweather/Utils/RxBus.java](https://github.com/chenBingX/OneWeather/blob/469abd6b78c185d370b5f339ce924d126ca616f7/app/src/main/java/com/chenbing/oneweather/Utils/RxBus.java)**  

我们在看一个细节，就是重置搜索框。
```
private void clearSearch() {
    etSearch.setText("");
    
    //清除焦点。后面会详细说一下。
    etSearch.clearFocus(); 
    tvCancel.setFocusableInTouchMode(true);
    tvCancel.requestFocus();

    AppUtils.hideInputMethod(etSearch); //隐藏输入法，否则UI很难受。

    rvCityList.setVisibility(GONE);
    tvNotFound.setVisibility(GONE);
  }
```

- 【小知识】清除搜索框的焦点。View提供了一个clearFocus()方法来清除焦点。但是有时候你可能会发现，你的调用是无效！是不是在怀疑人生？说好的清除呢？为什么没效果！其实当我们调用clearFocus()后焦点确实被清除了，但是Android需要立即把焦点移到下一个可获得焦点的控件上。如果没有，那自然又回到原控件上。所以我后面让取消按钮变得可以获得焦点，并把焦点移动到它上面。
- 【小知识】输入法的显示／隐藏
```
public static void showInputMethod(View view) {
    try {
      //获得输入法管理器
      InputMethodManager imm = (InputMethodManager) view.getContext()
          .getSystemService(Context.INPUT_METHOD_SERVICE);
          //显示输入法，需要指定基于那个View显示。会影响输入法弹起后对UI的影响。
      imm.showSoftInput(view, InputMethodManager.SHOW_FORCED); 
    } catch (Exception e) {
      e.printStackTrace();
    }
  }

  public static void hideInputMethod(View view) {
    if (view != null && view.getContext() != null && view.getWindowToken() != null) {
      try {
        ((InputMethodManager) view.getContext().getSystemService(Context.INPUT_METHOD_SERVICE))
        //隐藏输入法，需要提供其所在窗口的Token。
            .hideSoftInputFromWindow(view.getWindowToken(), 0); 
      } catch (Exception e) {
        e.printStackTrace();
      }
    }
  }
```

# 总结
本篇主要介绍了**《One Weather》**这款入门级App的简要天气列表及其中包含的搜索框的实现。我仅抽取了一些认为需要说一下的地方写了，并且一些涉及到的知识我用【小知识】这样的标识表了出来。希望能让大家的阅读更易进行。当然，很多具体的实现细节还需要大家到Github上看。下面是项目地址：  

## [【项目地址Github】https://github.com/chenBingX/OneWeather](https://github.com/chenBingX/OneWeather)  

### 最后如果你觉得还不错，记得加个关注、点个赞哦！你的鼓励是我最大动力！
### 如果对这个项目感兴趣，请打开Github项目地址链接，关注一下点个赞。随时获取该项目的最新动态。
