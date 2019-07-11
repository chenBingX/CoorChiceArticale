> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# 前言
在我的另一篇文章中介绍了如何使用Python进行多渠道打包，下面再把传统的gradle多渠道打包方式介绍一下，同样是以友盟SDK为例。
# 打包前配置
## 配置gradle
```
//主要是配置android{}封包下的内容
android {
            。
            。
            。
signingConfigs { //配置签名文件，可以有多个
        icekey { 
            keyAlias 'icekey'
            keyPassword '填入key的密码'
            storeFile file('/Users/**********/icekey.jks')  //key所在文件夹
            storePassword '填入store的密码'
        }
    }
    
    buildTypes { //配置构建版本，可以有多个
        //测试版本
        debug {
            debuggable true
            zipAlignEnabled false
            shrinkResources false
            signingConfig signingConfigs.icekey
            minifyEnabled false
        }

        //发布版本
        release {
            debuggable false //是否开启测试
            zipAlignEnabled true //是否启用zipAlign压缩优化
            shrinkResources true //是否删除未被使用的资源文件
            minifyEnabled false  //是否开启混淆
            signingConfig signingConfigs.icekey //签名文件
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            //指定混淆文件
        }

        //空版本
        source {
            debuggable true //是否开启测试
            zipAlignEnabled true //是否启用zipAlign压缩优化
            shrinkResources true //是否删除未被使用的资源文件
            minifyEnabled false  //是否开启混淆
            signingConfig signingConfigs.icekey //签名文件
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            //指定混淆文件
        }
    }
    
    //创建渠道名称,main{}是一个不合法名称
    productFlavors {
        baidu {}
        huawei {}
        xiaomi {}
    }
    
    //    这里体现在AndroidManifest中的
//    <meta-data
//    android:name="UMENG_CHANNEL"
//    android:value="${UMENG_CHANNEL_VALUE}"/>
    //运行自动赋值给AndroidManifest.xml中的${UMENG_CHANNEL_VALUE}
    productFlavors.all {
        flavor -> flavor.manifestPlaceholders = [UMENG_CHANNEL_VALUE: name]
    }

    lintOptions { //配置打包线程的操作
        ignoreWarnings true //忽略警告
        checkReleaseBuilds false //是否检查release版本
        abortOnError false //出现错误是否终止
    }
    
        applicationVariants.all { variant ->
        variant.outputs.each { output ->
            def outputFile = output.outputFile
            if (outputFile != null && outputFile.name.endsWith(".apk")) {  //判断是否是apk
                //这个函数生成了apk的名称
                def apkName = "ice-${variant.productFlavors[0].name}-${defaultConfig.versionName}.apk"
                output.outputFile = new File(outputFile.parent, apkName) //输出文件
            }
        }
    }
    
            。
            。
            。
}
```
## 配置AndroidManifest.xml
在Application标签下添加：
```
 <!--友盟的key-->
    <meta-data
      android:name="UMENG_APPKEY"
      android:value="57bf375ee0f5*******018a3"/>
    <!--友盟渠道号-->
    <meta-data
      android:name="UMENG_CHANNEL"
      android:value="${UMENG_CHANNEL_VALUE}"/> //这样写就能够读取gradle中的[UMENG_CHANNEL_VALUE: name]的name的值了。
```

# 开始自动打包
在Android Studio的的终端机中输入
```
./gradlew assembleRelease  //打包所有渠道的Release版包
./gradlew assembleSource //打包所有渠道的Source版包
```

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-4dbdd4092a838e87.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
