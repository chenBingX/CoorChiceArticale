PathProvider 提供了统一的方式来获取 Android、iOS 平台的文件路径。  

# 1. 引入 PathProvider

在 `pubspec.yaml` 中，加入 PathProvider 插件：  

```
dependencies:
  path_provider: ^0.5.0+1
```

你可以通过访问以下链接来获取 PathProvider 的最新版本：  

[https://pub.dartlang.org/packages/path_provider#-readme-tab-](https://pub.dartlang.org/packages/path_provider#-readme-tab-)  

# 2. 支持访问的目录

## 2.1 临时目录

临时目录指的就是缓存，对应于 iOS 中的 `NSTemporaryDirectory()`，Android 中的 `getCacheDir()` 。  

通过 `getTemporaryDirectory()` 可以获取该路径：

```
Future<File> _getCacheFile() async {
  String dir = (await getTemporaryDirectory()).path;
  return new File('$dir/test.txt');
}
```

## 2.2 文档目录

文档目录指的是应用的文档目录，对应于 iOS 中的 `NSDocumentDirectory` 目录，Android 中的 `AppData` 目录。  

通过 `getApplicationDocumentsDirectory()` 可以获取该路径：

```
Future<File> _getLocalFile() async {
  String dir = (await getApplicationDocumentsDirectory()).path;
  return new File('$dir/test.txt');
}
```


## 2.3 外部存储目录

文档目录指的是应用的文档目录，对应于Android 中的 `getExternalStorageDirectory()`，在 iOS 中调用会崩溃，因为 iOS 不支持外部目录。  

通过 `getExternalStorageDirectory()` 可以获取该路径：

```
Future<File> _getExternalFile() async {
  String dir = (await getExternalStorageDirectory()).path;
  return new File('$dir/test.txt');
}
```

