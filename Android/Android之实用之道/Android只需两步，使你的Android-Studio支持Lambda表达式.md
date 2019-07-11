> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

Java8的一大新特性就是较为健全的Lambda表达式，但是目前Android studio并不支持这一特性。但是可以通过在gradle中配置几行代码，引用Github上的资源，就可以让你的Android Studio支持Lambda表达式了。
### 注意：使用Lambda之前，必需确保你的Java8是能够运行的。
**下面直接上代码：**
```
apply plugin: 'com.android.application'

android {
    compileSdkVersion 23
buildToolsVersion "23.0.2"

//需要添加的第一部分：

//下面这两句在创建项目时不会自动生成，必需加上，是为了引用Java8的。
compileOptions {
sourceCompatibility JavaVersion.VERSION_1_8
targetCompatibility JavaVersion.VERSION_1_8
}

    defaultConfig {
applicationId "*************"
minSdkVersion 21
targetSdkVersion 23
versionCode 1
versionName "1.0"
}
    buildTypes {
        release {
minifyEnabled false
proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
}
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
compile 'com.android.support:appcompat-v7:23.1.1'
}

//需要添加的第二部分：

//以下全部都是要添加到项目的build.gradle中的。
//注意，经测试，只有3.2.4能够生效，如果有更新还请能够留言通知。
buildscript {
    repositories {
        mavenCentral()
    }

    dependencies {
        classpath 'me.tatarka:gradle-retrolambda:3.2.4'
}
}

repositories {
    mavenCentral()
}

apply plugin: 'me.tatarka.retrolambda'
```
**只用添加标注的两部分即可，其余部分不用管。**

来张效果图：
![Lambda表达式效果图](http://upload-images.jianshu.io/upload_images/1869462-72aeb38eaa2be600?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在代码中使用Lambda表达式可以使你的代码更加简洁清晰，提高效率和可读性。

# Android Studio 2.1 之后开启Lambda
AndroidStudio2.1之后，Google引入了新的编译器**Jack**，将来可能是要替换掉现在的编译器的。它编译生成的文件类型与现有的Java编译器是不同的，但据说它能最大限度地缩小APK的体积，提高编译效率，并且64K这种蛋碎的问题也将不再存在了。  

说了这么多其实只是想说，现在你只需要在 gradle中把它开启就行了：
```
defualtConfig{
	useJack(true)
}
```
下面这一段仍然是需要添加的，只有这样才能启用Java8
```
compileOptions {
	sourceCompatibility JavaVersion.VERSION_1_8
	targetCompatibility JavaVersion.VERSION_1_8
}
```
温馨提示：目前Jack还不完善，实际开发过程中可能会遇到一些诡异的坑，这将导致你不得不把他关闭。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-2ea4edeeaeef7329.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
