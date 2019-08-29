# 配置项目依赖

```
apply plugin: 'com.android.application'

android { ... }
dependencies {
    // 使用一个module作为依赖
    // 这里使用了名为“mylibrary”的module作为依赖
    // 打包时会打入apk，作为aar
    // 名称要和 settings.gradle 中的配置对应
    implementation project(":mylibrary")

    // 依赖本地二进制文件
    // 会依赖 module/libs/ 下的所有jar文件
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    
    // 单独指定依赖哪些文件
    implementation files('libs/foo.jar', 'libs/bar.jar')

    // 依赖远程库
    implementation 'com.example.android:app-magic:12.3'
    
    // 拆开写
    implementation group: 'com.example.android', name: 'app-magic', version: '12.3'
}
```

# 依赖操作符

在 Android Gradle Plugin 3.0.0 中，依赖操作符发生了变化。  


| 新 | 旧 | 说明 |
|:---:|:---:|:---|
| implementation | complie | 表明所依赖的库在编译期间对其它module是不可见的，但在runtime期间对其它module是可见的。 |
| api | compile | 表明所依赖的库在编译和runtime期间对其它module都是可见的。 |
| compileOnly | provided | 表明依赖的库只在编译期间有用，这对减少包大小很有用。 |
| runtimeOnly | apk | 表明依赖库只在runtime期间才使用。 |

通过在操作符前面增加前缀，可以指定依赖库用于什么场景。如：  

```
dependencies {
    // 用于free版本的构建
    freeImplementation 'com.google.firebase:firebase-ads:9.8.0'
    // 用于本地测试
    testImplementation 'junit:junit:4.12'
    // 用于Android instrumented test
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
}
```



