> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索`CoorChice`关注我的微信公众号，同期文章也将会优先推送到公众号中，以提醒您有新鲜文章出炉。亦可在文章末尾扫描二维码关注。  

![封面.jpg](http://upload-images.jianshu.io/upload_images/1869462-fc45f48173063f46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
# [本系列文章列表](http://www.jianshu.com/notebooks/8017443/latest)  

上一篇我们已经把欢迎页面的UI和交互完成了，由于篇幅问题，所以把数据请求放到这篇来继续撸。下面进入正题，来看看如何在欢迎页面出现时，加载数据并缓存，已备进入主页后能够快速展示。  
# 细化需求
“启动数据加载，并将数据缓存共享。”这看起来是一个需求，但它太笼统了，所以我们需要把它拆分细化，然后一个一个点完成。  

- 一进入欢迎页就启动数据加载；
- 能够定位当前位置并缓存，默认请求定位城市的天气数据；
- 由于数据具有时效性，所以仅缓存在内存。
  
# 功能实现
因为这个项目是使用MVP模式编写的，而上一篇由于关注点仅仅在UI层面，所以**SplashActivity** 并不具备MVP的结构。现在，我们一起来补全它。  
## View模块
- 创建View层接口，并让SplashActivity继承它；

```
public interface SplashActivityView extends MvpView {
}  

public class SplashActivity extends BaseActivity implements SplashActivityView {
    .
    .
    .
    
}
```  
- 在**onCreate()** 中初始化**Presenter**(**Presenter** 的在下面)，并在**initData()** 启动数据加载；  

```
private SplashActivityPresenterApi presenter; //注意这里需要依赖抽象，以提高程序的灵活性

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_splash);
    presenter = new SplashActivityPresenter(this); //初始化Presenter
    ButterKnife.bind(this);
    initData();
    initView();
    addListener();
  }  
  
  @Override
  protected void initData() {
    presenter.requestWeatherData(); //启动数据加载
  }
```

- 在**onDestroy()** 时释放**Presenter**。因为每个Activity几乎都会涉及到Presenter的释放，所以我把这个操作放到了我们的**BaseActivity**中，并新增**getPresenter()**抽象方，要求在这个方法中返回**Presenter**实例。来看看现在的**BaseActivity**长啥样？  

```
public abstract class BaseActivity extends AppCompatActivity {
  abstract protected void initData();

  abstract protected void initView();

  abstract protected void addListener();

  /**
   * 创建Presenter后必须重写这个方法，将其作为返回值
   */
  abstract protected BasePresenter getPresenter();

  @Override
  protected void onDestroy() {
    super.onDestroy();
    if (getPresenter() != null){
      getPresenter().destroy(); //销毁Presenter，避免Activity对象因被Presenter持有而不能被销毁
    }
  }
}
```  

## Presenter模块
上面一直在用Presenter，很疑惑？没关系，现在来看看Presenter。  
- 创建Presenter接口，并实现一个实例。  

```
public interface SplashActivityPresenterApi extends BasePresenter{

  void requestWeatherData(); //这就是我们在Activity中调用的方法。
}
```  
我来说说多出来的这个**BasePresenter**，这是BaseActivity中抽象方法要求返回的Presenter基类，以后我们的Presenter接口全部都需要继承它，这样便于对Presenter的统一特性进行控制。  

```
public interface BasePresenter {
  void destroy(); //BaseActivity中调用的Presenter销毁方法
}
```

- 实现SplashActivityPresenter接口，实现对Model模块和View模块的协调调用。  

```
public class SplashActivityPresenter
    implements
      SplashActivityPresenterApi,
      //实现数据加载的回调
      WeatherDataModelApi.RequestWeatherDataListener {

  //同样依赖抽象
  private WeatherDataModelApi model;
  private SplashActivityView view;

  public SplashActivityPresenter(SplashActivityView view) {
    this.model = new WeatherDataModel(); //实例化一个Model
    this.model.setRequestWeatherDataListener(this); //设置监听，具体的看Model模块
    this.view = view;
  }


  @Override
  public void requestWeatherData() {
    model.requestWeatherData(); //让model去请求数据
  }

  @Override
  public void onRequestWeatherDataSuccess(WeatherData data) {
    //model请求数据成功
    DataCache.getInstance().setWeatherData(data); //缓存数据到DataCache(用于一些通用数据的缓存，见下)中。
  }

  @Override
  public void onRequestWeatherDataFailure(String message) {
    LogUtils.e("请求出错：" + message);
  }

  @Override
  public void destroy() {
    model.setRequestWeatherDataListener(null);  //置null，为了释放内存
    model = null;
    view = null;
  }
}
``` 
- DataCache

```
//单例实现，缓存一些公用数据
public class DataCache {

  private WeatherData weatherData;
  private BDLocation currentLocation;

  private DataCache(){

  }

  public static DataCache getInstance(){
    return DataCacheHolder.instance;
  }

  private static class DataCacheHolder{

    private static final DataCache instance = new DataCache();
  }
  public WeatherData getWeatherData() {
    return weatherData;
  }

  public void setWeatherData(WeatherData weatherData) {
    this.weatherData = weatherData;
  }

  public BDLocation getCurrentLocation() {
    return currentLocation;
  }

  public void setCurrentLocation(BDLocation currentLocation) {
    this.currentLocation = currentLocation;
  }
}
```
## Model模块
最后一步啦，我们来编写Model。Model模块在这里需要负责两个事务：
- 定位
- 根据定位获取相应位置的天气数据  

下面我们来一个个完成。
- 编写天气的Model接口  

```
public interface WeatherDataModelApi {
  void requestWeatherData();

  void setRequestWeatherDataListener(RequestWeatherDataListener requestWeatherDataListener);

  // 因为每个Model可能需要请求多个接口，所以每个回调可能不同，就把它写到内部了
  interface RequestWeatherDataListener {
    void onRequestWeatherDataSuccess(WeatherData data);

    void onRequestWeatherDataFailure(String message);
  }
}
```  
- 实现天气Model接口  

```
public class WeatherDataModel implements WeatherDataModelApi {
  private RequestWeatherDataListener requestWeatherDataListener;

  @Override
  public void requestWeatherData() {
    locationThenGetWeatherData();
  }

  //定位使用的是百度定位SDK
  private void locationThenGetWeatherData() {
    LocationHelper.getInstance().startLocation(); //这是封装的一个定位帮助类，在Utils->Helpers包下
    LocationHelper.getInstance().setListener(location -> {
      //成功定位
      String cityname = location.getCity();
      if (cityname != null) {
        getWeatherData(cityname);
      }

      DataCache.getInstance().setCurrentLocation(location); //缓存定位信息
    });
  }

  private void getWeatherData(String cityname) {
    //请求天气数据
    ApiClient.getWeatherData(cityname, data -> {
      LogUtils.i("requestWeatherData: " + GsonUtils.getSingleInstance().toJson(data));
      if (requestWeatherDataListener != null) {
        requestWeatherDataListener.onRequestWeatherDataSuccess(data); // 请求成功
      }
    }, message -> {
      if (requestWeatherDataListener != null) {
        requestWeatherDataListener.onRequestWeatherDataFailure(message);
      }
    });
  }

  @Override
  public void setRequestWeatherDataListener(RequestWeatherDataListener requestWeatherDataListener) {
    //暴露该方法，让Presnter能够监听到数据加载完成
    this.requestWeatherDataListener = requestWeatherDataListener;
  }
}
```  
- 定位帮助类  
我只是简单的封装成了单例，具体的到[百度地图开发者平台：传送门](http://lbsyun.baidu.com/index.php?title=android-locsdk/guide/getloc)看文档。
```
public class LocationHelper {

  private LocationClient mLocationClient = null;

  private LocationHelper() {
    mLocationClient = new LocationClient(ChiceApplication.getAppContext()); // 声明LocationClient类
    initLocation();
  }

  public static LocationHelper getInstance() {
    return LocationHelperHolder.instance;
  }

  public void setListener(BDLocationListener listener) {
    if (listener != null){
      mLocationClient.registerLocationListener(listener);
    }
  }

  private static class LocationHelperHolder {
    private static final LocationHelper instance = new LocationHelper();
  }

  private void initLocation() {
    LocationClientOption option = new LocationClientOption();
    option.setLocationMode(LocationClientOption.LocationMode.Hight_Accuracy);// 可选，默认高精度，设置定位模式，高精度，低功耗，仅设备
    option.setCoorType("bd09ll");// 可选，默认gcj02，设置返回的定位结果坐标系
    int span = 1000;
    option.setScanSpan(span);// 可选，默认0，即仅定位一次，设置发起定位请求的间隔需要大于等于1000ms才是有效的
    option.setIsNeedAddress(true);// 可选，设置是否需要地址信息，默认不需要
    // option.setOpenGps(true);//可选，默认false,设置是否使用gps
    // option.setLocationNotify(true);//可选，默认false，设置是否当GPS有效时按照1S/1次频率输出GPS结果
    option.setIsNeedLocationDescribe(true);// 可选，默认false，设置是否需要位置语义化结果，可以在BDLocation.getLocationDescribe里得到，结果类似于“在北京天安门附近”
    // option.setIsNeedLocationPoiList(true);//可选，默认false，设置是否需要POI结果，可以在BDLocation.getPoiList里得到
    option.setIgnoreKillProcess(false);// 可选，默认true，定位SDK内部是一个SERVICE，并放到了独立进程，设置是否在stop的时候杀死这个进程，默认不杀死
    option.SetIgnoreCacheException(false);// 可选，默认false，设置是否收集CRASH信息，默认收集
    option.setEnableSimulateGps(false);// 可选，默认false，设置是否需要过滤GPS仿真结果，默认需要
    mLocationClient.setLocOption(option);
  }

  public void startLocation() {
    if (mLocationClient != null) {
      mLocationClient.start();
    }
  }

  public void stopLocation() {
    if (mLocationClient != null) {
      mLocationClient.stop();
    }
  }
}
``` 

基本完成了，运行程序看下输出：  
![输出](http://upload-images.jianshu.io/upload_images/1869462-f9e1dd22310946b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

  
我对网络请求底层也进行了一点调整，具体的请到GitHub查看。 

# [项目地址GitHub](https://github.com/chenBingX/OneWeather)

  
![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-87b59816843c6aa1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
