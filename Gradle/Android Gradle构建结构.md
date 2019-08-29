# Android Gradle构建结构

## 1. setting.gradle
settings.gradle 文件位于项目根目录，用于指示 Gradle 在构建应用时应将哪些模块包括在内。对大多数项目而言，该文件很简单，只包括以下内容：

```
include ':app'
include ':mylibrary'
```

不过，多模块项目需要指定应包括在最终构建之中的每个模块。

## 2. 顶级的 `build.gradle`

顶级 `build.gradle` 文件位于项目根目录，用于定义适用于项目中所有模块的构建配置。默认情况下，这个顶级构建文件使用 `buildscript {}` 代码块来定义项目中 **所有模块** 共用的 Gradle 存储区和依赖项。以下代码示例描述的默认设置和 DSL 元素可在新建项目后的顶级 `build.gradle` 文件中找到。  

```
/**
 * 此处的 buidlscript{} 代码块是用来配置gradle
 * 自身编译使用的 repositories 和 dependencies 。
 * 不要在这个地方配置 module 所需的 repositories 和 dependencies。
 */

buildscript {

    /**
     * 你需要在 repositories{} 代码块中配置用于
     * 搜索下载依赖的仓库地址。gradle预设了JCenter、
     * Maven Central 和 Ivy 仓库地址，配置时不用输
     * 路径。如果使用除此之外的仓库，需要自行配置路径。
     */

    repositories {
        jcenter()
        # 这是一个非预置的仓库，需要配置上地址
        maven { url 'https://jitpack.io' }
    }

    /**
     * 在此处的 dependencies {} 代码块中，需要配置
     * 用于编译项目的库。
     */

    dependencies {
        # tools的版本需要和gradle版本相对应
        classpath 'com.android.tools.build:gradle:3.1.0'
    }
}

/**
 * 在此处的 allprojects {} 你可以配置供所有 module
 * 共同使用的 repositeries 和 dependencies
 */

allprojects {
   repositories {
       jcenter()
   }
}
```

Android构建插件和Gradle版本的对应表：  

![](http://ogemdlrap.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-07-20%20%E4%B8%8A%E5%8D%8811.20.34.png)

## 3. 模块级的构建文件
模块级 `build.gradle` 文件位于每个 `<project>/<module>/` 目录，用于配置适用于其所在模块的构建设置。您可以通过配置这些构建设置来提供自定义打包选项（例如附加构建类型和产品风味），以及替换 main/ 应用清单或顶级 `build.gradle` 文件中的设置。  

```
/**
 * 引入Android插件，这样可以获得其提供的构建操作 android {}
 */

apply plugin: 'com.android.application'

/**
 * 在 android {} 代码块中，你可以配置所有 Android 构建的操作
 */

android {

  /**
   * compileSdkVersion 指定使用什么版本的api进行编译，这意味着包括了更低版本的api
   *
   * buildToolsVersion 指定sdk构建工具版本、命令行工具和
   * Gradle用来编译你的app的编译器
   */

  compileSdkVersion 26
  buildToolsVersion "27.0.3"

  /**
   * 在 defaultConfig {} 代码块中，你可以配置构建的动态参数
   * 
   */

  defaultConfig {

    /**
     * applicationId 配置发布的id标识，code的包名需要参考这个id
     */

    applicationId 'com.example.myapp'

    // 配置支持的最低sdk版本
    minSdkVersion 15

    // 指定用于测试的sdk版本
    targetSdkVersion 26

    // 配置版本号
    versionCode 1

    // 配置版本号名称
    versionName "1.0"
  }

  /**
   * 在 buildTypes {} 代码块中，可以配置构建的app类型
   * release 或者 debug。debug默认不显示，使用默认的debug key进行签名。
   */

  buildTypes {

    /**
     * 配置 release 版本编译参数
     */

    release {
        // 是否混淆
        minifyEnabled true 
        // 指定混淆配置文件
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
  }

  /**
   * 在 productFlavors {} 总可以配置不同的app版本
   * 可以为他们指定不同的id。可以覆盖 defualtConfig {} 中的配置。
   * 配置不同风味的版本
   */

  productFlavors {
    free {
      applicationId 'com.example.myapp.free'
    }

    paid {
      applicationId 'com.example.myapp.paid'
    }
  }

  /**
   * 在 splits {} 代码块中，可以配置仅使用一份代码和资源打包apk
   * 根据像素密度和abi版本
   */

  splits {
    // 根据像素密度进行分割
    density {

      // 是否启用该分割
      enable false

      // 分割哪些版本
      exclude "ldpi", "tvdpi", "xxxhdpi", "400dpi", "560dpi"
    }
  }
}

/**
 * 在 dependencies {} 中配置本module构建使用的依赖
 */

dependencies {
    compile project(":lib")
    compile 'com.android.support:appcompat-v7:27.1.1'
    compile fileTree(dir: 'libs', include: ['*.jar'])
}
```

## 4. gradle.properties

配置整个项目的Gradle设置，即项目的Gradle构建环境。

## 5. local.properties
为构建系统配置本地环境属性，例如 SDK 安装路径。由于该文件的内容由 Android Studio 自动生成并且专用于本地开发者环境，因此您不应手动修改该文件，或将其纳入您的版本控制系统。

## 6. gradle-wrapper.properties
配置 Gradle 版本等。

## [7. 迁移 Android Studio 3.0.0 指南](https://developer.android.com/studio/build/gradle-plugin-3-0-0-migration)


