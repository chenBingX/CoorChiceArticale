App 的 minSdkVersion 小于依赖包时，会报类似以下的错误：

```
uses-sdk:minSdkVersion 9 cannot be smaller than version 11 declared in library
```

解决办法：

1. 升级 App minSdkVersion；

2. 在 `AndroidManifest.xml` 中增加 

```
<uses-sdk tools:overrideLibrary="依赖库"/>
```