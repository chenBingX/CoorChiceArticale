## 1. 通过 Android Studio 的 Debug 窗口中的 `Open DevTools` 可以在浏览器中打开 Flutter 的调试工具，功能强大。  
    - 关闭左上角的 Debug 标签
    - 查看性能
    - 选择组件
    - 查看组件边界

## 2. 如何引用一个特定的 **git** 分支/Tag 依赖？

    ```
    dependencies:
      project:
        git:
          url: git://github.com/munificent/kittens.git
          ref: some-branch （tag使用tag对应的标识码）
    ```


## 3. 混编时，flutter 工程的 android 项目不能为 application，需要在app的`build.gradle` 中修改：
    a. `apply plugin: 'com.android.application'` 改为 `apply plugin: 'com.android.library'`
    b. 将 **applicationId** 去掉
    c. 如果不想混编译了，把他们改回去

## 4. flutter 在 `pubspec.yaml` 中引入资源文件时，不支持多级目录，子目录也需要单独定义。 

``` 
    - lib
      - icon
        pic1.png
      pic2.png
```  

那么在 `pubspec.yaml` 中就需要定义: 

  ```
  flutter:
    asserts:
      - lib/
      - lib/icon/
  ```

## 5. 如果一个叫做 **library1** 的包有以下结构：
  
    ```
      - pubspec.yaml
      - lib
        - icon
          pic1.png
        pic2.png
    ```  

    当他被其它应用程序应用依赖时，想要 **library1** 包中使用了资源的地方能够展示，需要做以下几件事：  

    - **library1** 的 `pubspec.yaml` 中需要这样去注册资源：   

        ```  
        flutter:
          asserts:
            - packages/library1/pic2.png
            - packages/library1/icon/pic1.png
        ```  

    - **library1** 在引用自己的资源的时候，需要加包名：   

        ```
        new AssetImage('icon/pic1.png', package: 'library1')
        new AssetImage('pic2.png', package: 'library1')
        ```  


## 6. Text组件测量，文本长度测量

    ```
    var renderObject = (textPart.build(context) as RenderObjectWidget).createRenderObject(context);
    renderObject.layout(BoxConstraints());
    var size = renderObject.paintBounds.size;
    ```


## 7. 文字测量

     ```
     final counterInfoSpan = TextSpan(
             text: counterInfo,
             style: TextStyle(
               color: getTextColor(counterInfo),
               fontSize: 15,
               height: 1,
             ));
     final tp = TextPainter(
         text: counterInfoSpan, maxLines: 1, textDirection: TextDirection.ltr);
     tp.layout(maxWidth: 105.5);


     /// tp 包含了文字的必须要信息
     ```

## 8. 找不到 'XXXPlugin.kt' 的错误
    
   ```
   'XXXPlugin.kt' even though it exists. Please verify that this file has read permission and try again.
   ```
   
   在 `flutter sdk/packages/flutter_tool/lib/src/platform_plugins.dart` 中，找到 `mainClassContent = mainPluginClass.readAsStringSync`
   
   然后删除 `flutter sdk/bin/cache/flutter_tools.snapshot`，然后重新运行，看是什么错误。
   

## 9. TextField 在获取焦点的时候，如果键盘没弹出，进入 pause 过程中，内容会被清空，需要在 pause 的时候清除焦点。
    ```dart
     FocusScope.of(context).requestFocus(FocusNode()); 
    ```

## 10. 如何判断是否处于 **Web** 环境？

```dart
if (kIsWeb) {
    /// web 环境
}
```

## 11. Flutter 支持 macOS

```
// 配置支持 macOS
flutter channel master  # or dev
flutter upgrade
flutter config --enable-macos-desktop

// build
flutter build macos

// 在现有项目中添加 macOS 支持
flutter create .
```

## 12. 增加 image 缓存

```dart
class CustomImageCache extends WidgetsFlutterBinding {
  @override
  ImageCache createImageCache() {
    ImageCache imageCache = super.createImageCache();
    // Set your image cache size
    imageCache.maximumSizeBytes = 1024 * 1024 * 100; // 100 MB
    return imageCache;
  }
}

void main() async {
  CustomImageCache();
  WidgetsFlutterBinding.ensureInitialized();
  runApp(MyApp());
}
```

## 13. 代码中设置代理抓包

```dart
_getHttpData() async {
  var httpClient = new HttpClient();
  httpClient.findProxy = (url) {
    return HttpClient.findProxyFromEnvironment(url, environment: {"http_proxy": 'http://192.168.124.7:8888',});
  };
  var uri =
      new Uri.http('t.weather.sojson.com', '/api/weather/city/101210101');
  var request = await httpClient.getUrl(uri);
  var response = await request.close();
  if (response.statusCode == 200) {
    print('请求成功');
    var responseBody = await response.transform(Utf8Decoder()).join();
    print('responseBody = $responseBody');
  } else {
    print('请求失败');
  }
}

```

## 14. 区分平台导入或导出

[条件导入/导出](https://dart.cn/guides/libraries/create-library-packages)

## 15. macOS 常用权限配置

在 `macos/Runner/DebugProfile.entitlements` 中添加：

```dart

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>com.apple.security.app-sandbox</key>
	<true/> 
    /// 如果需要读写其它文件，需要设置为 
    <false/>

	<key>com.apple.security.cs.allow-jit</key>
	<true/>

    /// 网络访问支持    
	<key>com.apple.security.network.server</key>
	<true/>
    
    /// 读写文件权限
    <key>com.apple.security.files.all</key>
    <true/>
</dict>
</plist>
```

## 16. 插件开发限制 Flutter 版本

```
flutter: ">=1.19.0"
```
