




1. 引入 Flutter 的工程需要添加到 Android 节点：

compileOptions {
        sourceCompatibility 1.8
        targetCompatibility 1.8
}



2. Flutter 的宿主工程需要添加下面的子工程配置，否则 plugin 会找不到

```
subprojects {
   project.buildDir = "${rootProject.buildDir}/app"
}
```

3. Flutter release 只支持 arm架构


4. 下载 [facade文件.zip](https://raw.githubusercontent.com/chenBingX/img/master/其它文件/facade.zip) 解压放到

`Flutter工程/android/app/src/main/java/io/flutter/` 下

5. 需要增加依赖

```
    implementation 'com.android.support:support-v13:27.1.1'
    implementation 'com.android.support:support-annotations:27.1.1'
```