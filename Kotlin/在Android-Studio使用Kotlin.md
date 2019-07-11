# [Kotlin官方参考](http://www.kotlincn.net/docs/reference/android-overview.html)

# [Get Started with Kotlin on Android](https://developer.android.com/kotlin/get-started.html)  

# 几个技能
首先需要下载**Android Studio 3.0 Canary 1**版本，**Android Studio**的一大特性就是多个不同的版本可以同时运行，所以你不用担心你电脑上的稳定版本会收到影响。  

> [下载地址：https://developer.android.com/studio/preview/index.html](https://developer.android.com/studio/preview/index.html)  

## 将Java代码转化为Kotiln
**Android Studio 3.0** 十分人性化的提供了将现有Java文件转化为Kotlin文件的方法，这就是说你可以快速的将你用Java编写的Android项目快速的转变成使用Kotlin编写的项目。当然，这种转化可能会有一些小错误，但你任然可以信任它，即使你后期还需对转化中出现的错误进行一些修复。  

来看看这个过程到底有多简单吧！  

1. 点击**File > New > Kotlin File/Class**来创建一个` .kt `文件；
2. 把需要转化的Java代码复制到该文件中，出现以下弹窗：  

![image](http://upload-images.jianshu.io/upload_images/1869462-c067f57821ba3a91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

点击**Yes**即可。

**Java**文件：

```
public class TestJavaClass {

  private String field;

  public String getField() {
    return field;
  }

  public void setField(String field) {
    this.field = field;
  }
}
```
转化为**Kotlin**后：

```
class TestJavaClass {

    var field: String? = null
}
```
是的，尽管惊讶去吧！居然精简成了一行代码！

## 将Kotlin代码和Java代码区分开！
默认情况下**Kotlin**的代码会和**Java**一起放在`src/main/java/`下，如果你想要区分它们，你可以新建一个目录去存放**Kotlin**的代码。比如：`src/main/kotlin/`。当然，相应的，你需要在`build.gradle`中添加：

```
android {
   ...
   sourceSets {
       main.java.srcDirs += 'src/main/kotlin'
   }
   ...
}

```
