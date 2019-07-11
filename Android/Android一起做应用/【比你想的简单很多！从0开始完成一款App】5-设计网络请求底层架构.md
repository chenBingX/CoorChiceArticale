> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面.jpg](http://upload-images.jianshu.io/upload_images/1869462-e0902f4ca6a2c85e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# [本系列文章列表](http://www.jianshu.com/notebooks/8017443/latest)  

经过前面4篇构建，我们的App已经初具框架了，现在可以开始按照框架逐步的添加具体内容了。本篇我们将一起使用Retrofit+OkHttpClient([Retrofit+OkHttp3的使用](http://blog.csdn.net/qq_31370269/article/details/53323181))完成底层网络请求架构。
# 数据来源
天气数据来源是从**聚合数据**申请的一个免费天气接口，这个平台上免费数据挺多的，可以看看[聚合数据链接](https://www.juhe.cn/)。
# 分析接口及数据结构
## 数据请求
1. 聚合数据的接口开始都是以**http://op.juhe.cn/** 开始的，考虑到后期还会用到聚合数据的其他接口，所以我们的第一个网络Client的BaseUrl就设置为**http://op.juhe.cn/** 。  

2. 必要的接口参数为有两个,请求时上传它两就够了。详细使用方法在聚合数据上看(这点还是很贴心的)：  

```
String cityname;
String key;
```
根据以上的接口分析，我们已经可以编写出符合要求的网络请求底层了。  
再次说明，以下是使用**Retrofit+OkHttpClient** 完成的，具体使用方法参考：[Retrofit+OkHttp3的使用](http://blog.csdn.net/qq_31370269/article/details/53323181)。
  
- Retrofit生成网络访问客户端需要的接口Api。  

``` 
public interface Api {
//第二个参数为我们在聚合数据申请的key，每次访问时携带
  @GET("onebox/weather/query")
  Call<BaseWeatherResponse<Result>> getWeatherData(@Query("cityname") String cityName, @Query("key") String key);
}
```

- 生成网络访问客户端。  

```

public class ApiClient {
  public static final Api api;
  private static String BASE_WEATHER_URL = "http://op.juhe.cn/";

//该类创建时就实例化Api
  static {
    api = getRetrofit().create(Api.class);
  }

//创建Retrofit对象
  private static Retrofit getRetrofit() {
    OkHttpClient client = getClient(); //见下
    //Json数据解析使用Gson
    GsonConverterFactory gsonConverterFactory =
        GsonConverterFactory.create(GsonUtils.newInstance());
    return new Retrofit.Builder()
        .baseUrl(BASE_WEATHER_URL)
        .addConverterFactory(gsonConverterFactory)
        .addCallAdapterFactory(RxJavaCallAdapterFactory.create()) //异步任务处理使用RxJava
        .client(client) //设置OkHttpClient
        .build();
  }

  @NonNull
  private static OkHttpClient getClient() {
  //添加拦截器，这个拦截器可以在添加一些header信息，实际开发中我们可能需要携带很多信息进行请求
    Interceptor interceptor = chain -> {
      Request request = chain.request();
      return chain.proceed(addRequestHeader(request));
    };
    // 是一个拦截器，用于输出网络请求和结果的 Log，
    // 可以配置 level 为 BASIC / HEADERS / BODY，都很好理解，对应的是原来 retrofit 的 set log level 方法，现在
    HttpLoggingInterceptor loggingInterceptor = new HttpLoggingInterceptor();
    loggingInterceptor.setLevel(
        BuildConfig.DEBUG ? HttpLoggingInterceptor.Level.BODY : HttpLoggingInterceptor.Level.NONE);
    return new OkHttpClient.Builder()
        .addInterceptor(loggingInterceptor)
        .addNetworkInterceptor(interceptor)
        .retryOnConnectionFailure(true) //允许自动重新链接
        .build();
  }

//添加Header信息
  private synchronized static Request addRequestHeader(Request request) {
    Request.Builder rq = request.newBuilder();
    if (ChiceApplication.getAppContext() != null) {
      String imei = AppUtils.getImei(ChiceApplication.getAppContext());
      if (imei != null) {
        rq.addHeader("imei", imei);
        LogUtils.v("imei: " + imei);
      }

      String imsi = AppUtils.getImsi(ChiceApplication.getAppContext());
      if (imsi != null) {
        rq.addHeader("imsi", imsi);
        LogUtils.v("imsi: " + imsi);
      }

      String networkType = NetworkUtils.getCurrentNetworkType(ChiceApplication.getAppContext());
      if (networkType != null) {
        rq.addHeader("net", networkType);
        LogUtils.e("net: " + networkType);
      }

    }
    String sdk = AppUtils.getSdkVersion();
    rq.addHeader("cv", sdk);
    LogUtils.e("cv: " + sdk);

    String os = "Android";
    rq.addHeader("os", os);
    LogUtils.e("os: " + os);

    String tc = AppUtils.getChannelName();
    rq.addHeader("tc", tc);
    LogUtils.e("tc: " + tc);

    String dt = AppUtils.getModelVersion();
    rq.addHeader("dt", dt);
    LogUtils.e("dt: " + dt);
    return rq.build();
  }

//使用这个静态方法进行请求，通过我们自己编写的回调获取请求结果。
//请求数据时，调用这个方法，在回调接口中获取数据
  public static void getWeatherData(OnSuccessCallback<Result> onSuccessCallback,
      OnFailureCallback<Result> onFailureCallback) {
    requestData(api.getWeatherData("cityname", "key"), onSuccessCallback,
        onFailureCallback);
  }

  private static <T> void requestData(Call<BaseWeatherResponse<T>> call,
      OnSuccessCallback<T> onSuccessCallback,
      OnFailureCallback<T> onFailureCallback) {
      //异步执行请求
    call.enqueue(new Callback<BaseWeatherResponse<T>>() {
      @Override
      public void onResponse(Call<BaseWeatherResponse<T>> call, Response<BaseWeatherResponse<T>> response) {
        if (response.isSuccessful() && response.body() != null) {
          if (onSuccessCallback != null) {
          //请求成功时，通过该回调将数据暴露给调用处
            onSuccessCallback.onSuccess(response.body().getResult());
          }
        }
      }

      @Override
      public void onFailure(Call<BaseWeatherResponse<T>> call, Throwable t) {
      }
    });
  }
}
```
- 用于暴露请求结果的回调  

```
public interface OnSuccessCallback<T> {
  void onSuccess(@Nullable  T t);
}

public interface OnFailureCallback<T> {
  void onFailure(@Nullable T t);
}
```

## 数据解析  
请求的数据结构可以到聚合数据看一下，这里我要说明的是，由于它这个接口返回的数据相对复杂，在使用GsonFormat生成实体类后，会有重名报错，并且看起来十分杂乱，所以我把该数据结构进行了拆分。详细的请移步[**GitHub**](https://github.com/chenBingX/OneWeather)查看。  

![数据结构](http://upload-images.jianshu.io/upload_images/1869462-1631fdc67dcd089b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

拆分的好处是结构清晰，方便我们查看和修改。  

# 看看怎么使用  

```
ApiClient.getWeatherData(data -> {
      //在请求成功的回调中获取并操作数据
      LogUtils.e("结果 = " + GsonUtils.getSingleInstance().toJson(data));
    }, thr -> {});
```   
以北京为例，下面是请求结果：  

![请求结果](http://upload-images.jianshu.io/upload_images/1869462-d3df28925c703891.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
怎么样？使用起来十分简洁吧。  

# [项目地址GitHub](https://github.com/chenBingX/OneWeather)

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-277efc2108e56c6c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
