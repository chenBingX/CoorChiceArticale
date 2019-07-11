> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

![封面.jpg](http://upload-images.jianshu.io/upload_images/1869462-db7168e0aa10eef1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# [本系列文章列表](http://www.jianshu.com/notebooks/8017443/latest)  

# 框架模式
选用目前比较流行的**MVP**框架模式。  
![MVP示意图](http://upload-images.jianshu.io/upload_images/1869462-7fb200f623b95c98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

- **Model**：负责处理业务逻辑，数据加载，算法；
- **View**：负责控制视图，处理交互；
- **Presenter**：负责链接Model和View，把Model加载好的数据传递到View中展示，把View中的交互数据请求发送给Model进行处理。**注意了，Presenter层由于链接了另外两个模块，在开发过程中很容易误把它们的逻辑代码放到这里，特别是Model模块的逻辑！这是应该被禁止的行为，在Presenter中编程时要好好的考虑！**  


# 配置依赖库  
- **ButterKnife注解绑定View**
- **Retrofit2配合OkHttp3构建网络请求**：[Retrofit+OkHttp3的使用](http://blog.csdn.net/qq_31370269/article/details/53323181)
- **Fresco用于展示绝大部分图片**
- **Gson用于Json的转换**
- **RxJava用于处理异步任务**：[RxJava使用](http://blog.csdn.net/qq_31370269/article/details/52314172)
- **Junit用于进行单元测试**：[有关单元测试的使用](http://blog.csdn.net/qq_31370269/article/details/52313935)
```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
        exclude group: 'com.android.support', module: 'support-annotations'
    })
    compile 'com.android.support:appcompat-v7:24.2.1'
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:multidex:1.0.1'
    compile 'io.reactivex:rxjava:1.1.9'
    compile 'com.android.support:recyclerview-v7:24.0.0'
    compile 'com.android.support:design:24.0.0'
    compile 'com.android.support:cardview-v7:24.0.0'
    compile 'com.google.code.gson:gson:2.2.4'
    compile 'com.facebook.fresco:fresco:0.12.0'
    compile 'junit:junit:4.12'
    compile 'com.jakewharton:butterknife:8.2.1'
    apt 'com.jakewharton:butterknife-compiler:8.2.1'
    compile 'io.reactivex:rxandroid:1.2.1'
    compile 'com.squareup.retrofit2:retrofit:2.1.0'
    compile 'com.squareup.retrofit2:adapter-rxjava:2.1.0'
    compile 'com.squareup.retrofit2:converter-gson:2.1.0'
    compile 'com.squareup.okhttp3:logging-interceptor:3.3.1'
    compile 'com.squareup.okhttp3:okhttp:3.3.1'
    compile 'com.squareup.okhttp3:okhttp-ws:3.3.1'
    compile 'com.umeng.analytics:analytics:latest.integration'
}
```
目前先这么多，后面有需要再更新吧。

# 配置使用Lambda  

[配置参考我的这篇教程](http://blog.csdn.net/qq_31370269/article/details/50752445)

# 规划项目结构
![规划项目结构](http://upload-images.jianshu.io/upload_images/1869462-70e8a81dd0d48edf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
  
  
  

# [项目地址GitHub](https://github.com/chenBingX/OneWeather)

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-b06c031eeab93965.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
