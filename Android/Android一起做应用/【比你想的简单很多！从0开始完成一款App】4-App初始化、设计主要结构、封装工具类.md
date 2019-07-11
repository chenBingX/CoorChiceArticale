> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面.jpg](http://upload-images.jianshu.io/upload_images/1869462-b284e414f4c34ed2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# [本系列文章列表](http://www.jianshu.com/notebooks/8017443/latest)  

通过前面几篇你已经准备好，可以开始输入代码，创建你的应用了。
# App初始化
## 我们要自定义Application
默认情况下，系统会让你的应用使用默认的Application，可以，这很方便。但是实际开发中，我们往往需要在应用程序一进去就开始一些初始化(初始化配置、加载后面要使用的资源)的操作，所以我们需要使用自己的Application。  
1. 首先extends Application，目前暂时只在其中初始化异常捕捉
```
public class ChiceApplication extends Application {

  private static Context context;
  @Override
  public void onCreate() {
    super.onCreate();
    context = this;
//    初始化异常处理类
    CrashHandler.getInstance().init(context);
  }

  //获取系统级Context
  public static Context getAppContext(){
    return context;
  }
}
```
2. 在AndroidManifest.xml中把`<application>`标签的**name**属性改成我们定义的这个Application。接下来，我们每次启动应用时，系统将初始化我们的**ChiceApplication**  

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.chenbing.oneweather">

  <application
    android:name=".ChiceApplication" //就是在这里修改为我们的Application
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:supportsRtl="true"
    android:theme="@style/AppTheme">
    <activity android:name=".View.activitys.MainActivity">
      <intent-filter>
        <action android:name="android.intent.action.MAIN"/>

        <category android:name="android.intent.category.LAUNCHER"/>
      </intent-filter>
    </activity>
  </application>

</manifest>

```
## 编写基类
### Activity基类
我们的Activity继承自v7兼容包下的**AppComponentActivity**。由于v7包提供了更多的特性，并且当今时代，我们并不需要考虑android 2.1之前的兼容性，试问你听说过有谁在用2.1之前的Android机吗？有的用户可能喜欢刷机，但是网上的ROM也基本都是基于android 2.1以上，所以我就直接使用v7兼容包了。  

```
//我们后续创建的Activity都需要继承这个BaseActivity，这样便于以后植入统一的操作。
//不然以后需要统一给Activity进行相同操作时，你就要一个个去改。真蛋疼啊！

//之所以写成抽象，是为了规范约束，提醒你下面几个方法是必要的
public abstract class BaseActivity extends AppCompatActivity {
  
  //初始化数据请求(不是必须的，习惯了)
  abstract protected void initData();

  //初始化View
  abstract protected void initView();

  //给View添加监听器
  abstract protected void addListener();

}
```

### Fragment基类
Fragment我选择继承v4包的，因为app包下只支持4.4以上，对于现在的Android市场来说太高了。
```
public abstract class BaseFragment extends Fragment {

  abstract protected void initData();

  abstract protected void initView();

  abstract protected void addListener();
  @Override
  public void onViewCreated(View view, @Nullable Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);
    view.setClickable(true); //让Fragment最底层的View拦截事件，防止扯到蛋的点透
  }
}
```
### BaseRelativeLayout
因为我比较喜欢使用RelativeLayout，所以选择它做View的基类。这个基类可选，主要是为了方便创建自定义View。  
```
public abstract class BaseRelativeLayout extends RelativeLayout {
  public BaseRelativeLayout(Context context) {
    this(context, null);
  }

  public BaseRelativeLayout(Context context, AttributeSet attrs) {
    this(context, attrs, 0);
  }

  public BaseRelativeLayout(Context context, AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
    bindView();
  }

  private void bindView() {
    LayoutInflater inflater = LayoutInflater.from(getContext());
    inflater.inflate(getLayout(), this, true);
    ButterKnife.bind(this);
  };

  //重写这个方法就能加载布局了。
  abstract protected int getLayout();
}
```
### BaseItemViewHolder
这个是专门为RecyclerView设计的ViewHolder，目的是为了简化ViewHolder的使用。  

```
public class BaseItemViewHolder extends RecyclerView.ViewHolder {
  public BaseItemViewHolder(View itemView) {
    super(itemView);
  }
}

//看看RecyclerView的Adapter现在的变化
@Override
  public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
    return new BaseItemViewHolder(new NavigatorItemView(context)); //一句话创建
  }
```

# 设计主要架构
本项目我们采用时下流行的MVP框架，现在针对MainActtivity先来进行结构设计。设计结构时需要注意以下几点：
- 遵循**依赖倒置原则**，即**模块应该依赖抽象，而不要依赖具体**。这样做的的好处是，单需求发生变化，比如Model需要切换逻辑时，我们只需要换一个实现了同一个接口的实现类就可以，而不用对Presenter修改。同样，当我们现有的一套Presenter和Model需要在另一个View模块上展示时，只要View模块不要求View接口发生变化，我们可以很容易的就把这套东西用到新的View模块上。当然，理论上可行，但实际的Android开发中往往是View模块占据主导地位，至少大多数时候是这样。
- 使用MVP框架其实就已经注定了代码量会增大，所以不能为了省事把Model模块的逻辑、算法代码写到Presenter中。
- 要注意我们的依赖关系。  

![MVP依赖关系](http://upload-images.jianshu.io/upload_images/1869462-da2a6f2d31885b47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

**下面看看View、Model、Presenter的结构。**  

## View模块
```
//抽象
//我们的Presenter需要依赖的就是这个抽象
public interface MainActivityView extends MvpView {

}

//具体实现
public class MainActivity extends BaseActivity implements MainActivityView {
  private MainActivityPresenterApi presenter;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    presenter = new MainActivityPresenter(this); //初始化Presenter
    //下面调用方法我没有固定，就是为了根据需求变化。当然大多数情况下是这样的。
    initData();
    initView();
    addListener();
  }

  @Override
  protected void initData() {

  }

  @Override
  protected void initView() {

  }

  @Override
  protected void addListener() {

  }
}

```  

## Model模块  
```
//抽象
public interface MainActivityModelApi {
}

//实现
public class MainActivityModel implements MainActivityModelApi {
}
```

## Presenter模块

```
//抽象
public interface MainActivityPresenterApi {
}

//实现
public class MainActivityPresenter implements MainActivityPresenterApi {

  private MainActivityView view; //依赖的是抽象
  private MainActivityModelApi model;

  public MainActivityPresenter(MainActivityView view) {
    this.view = view;
    model = new MainActivityModel(); //创建Model真实对象
  }
}
```

# 封装工具类  

工具类基本上都是提供一些静态方法供外界调用的。  

![工具类](http://upload-images.jianshu.io/upload_images/1869462-52b6a950f9858a10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

根据自己的需要来就行，我暂时封装这么多，有需要再加，具体的到我的GitHub中看。下面我简单介绍下他们的作用。  

- AppUtils：主要用于获取系统信息，控制一些通用的、应用级的操作；
- CrashHandler：处理未捕获异常，参考我的这篇[Android——Exception异常的正确打开方式](http://blog.csdn.net/qq_31370269/article/details/53115879)；
- DisplayUtils：主要用于dp、sp、px等单位换算，获取屏幕尺寸等；
- FileUtils：封装文件IO操作，注意线程；
- GsonUtils：封装Gson，避免频繁创建Gson对象；
- ImageUtils：封装Image操作；
- LogUtils：封装Log，非常便捷高效的Log输出，参考我的这篇[Android——使用StackTraceElement实现自己的Log日志输出管理类](http://blog.csdn.net/qq_31370269/article/details/52314015);
- MobileUtils：封装部分设备信息获取；
- NetworkUtils：封装网络信息获取；
- ReflectUtils：帮助更高效的使用反射，参考我的这篇[
Android——反射Reflect机制运用](http://blog.csdn.net/qq_31370269/article/details/52314361);
- ThreadUtils：封装Thread线程调度，参考我的这篇[Android——线程管理之ExecutorService](http://blog.csdn.net/qq_31370269/article/details/51418741)；
- ToastUtils：封装Toast的使用，避免出现Toast错乱的Bug。
  
工具类根据实际开发，可继续不断的扩展添加。  


# [项目地址GitHub](https://github.com/chenBingX/OneWeather)


![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-ae982f5b3590433b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
