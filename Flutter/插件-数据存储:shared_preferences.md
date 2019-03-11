SharedPreferences 插件提供了类似于 Android 中的 SharedPreferences 的轻量级数据储存方式。  

# 1. 引入 SharedPreferences

在 `pubspec.yaml` 中，加入 SharedPreferences 插件：  

```
dependencies:
  shared_preferences: ^0.5.1+1
```

你可以通过访问以下链接来获取 SharedPreferences 的最新版本：  

[https://pub.dartlang.org/packages/shared_preferences](https://pub.dartlang.org/packages/shared_preferences) 

# 2.使用

## 2.1 储存数据

首先，需要引入 SharedPreferences：

```
import 'package:shared_preferences/shared_preferences.dart';

``` 

然后，获取 SharedPreferences 实例，进行数据储存：  

```
Future _write(String content) async {
  SharedPreferences prefs = await SharedPreferences.getInstance();
  await prefs.setString('content', content);
}
```

SharedPreferences 支持储存的数据：  

```
prefs.setString(key, value)
prefs.setBool(key, value)
prefs.setDouble(key, value)
prefs.setInt(key, value)
prefs.setStringList(key, value)
```

通常，你可以把一个对象转换成 String，然后储存。  

## 2.2 读取数据

同样需要先获取 SharedPreferences 实例，然后读取数据：

```
Future<String> _read() async {
  try {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    String r = await prefs.get('content');
    return r;
  } on Exception {
    return "";
  }
}
```


SharedPreferences 支持读取的数据：

```
dynamic get(String key)
bool getBool(String key)
int getInt(String key)
double getDouble(String key)
String getString(String key)
List<String> getStringList(String key)
```

## 2.3 移除数据

```
Future<bool> remove(String key) 

```

## 2.4 清空数据

```
Future<bool> clear()
```

