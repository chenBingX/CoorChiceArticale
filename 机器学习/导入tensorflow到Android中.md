# 导入tensorflow到Android中

# 1. 最简单的方式

直接在 AndroidStudio 中配置依赖：  

```
implementation 'org.tensorflow:tensorflow-android:+'
```

需要配置 `jcenter()` 仓库。

# 2. 手动安装
## 2.1 准备

### 2.1.1 安装 Homebrew

```
/usr/bin/ruby -e "$(curl -fsSL \
https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

### 2.1.2 安装 Bazel
Bazel 是用来编译 tensorflow 的。  

安装：  

```
brew install bazel
```

检查：  

```
bazel version
```

更新：  

```
brew upgrade bazel
```

### 2.1.3 配置ndk环境

```
pico .bash_profile
```

打开开 `.bash_profile` 文件，新增：  

```
export NDK_ROOT={ndk路径}
export PATH=$PATH:$NDK_ROOT 
```

保存（ control＋X） 选 Y 。然后更新刚配置的环境变量输入  
 
```
source .bash_profile 
```



## 2.2 编译
### 2.2.1 Clone tensorflow

到指定文件夹下，执行命令：  

```
git clone --recurse-submodules  https://github.com/tensorflow/tensorflow.git
```

### 2.2.2 配置 sdk 和 ndk 路径

在 tensorflow 文件夹中，找到 WORKSPACE 文件，修改或添加：  

```
android_sdk_repository(
    name = "androidsdk",
    api_level = 23,
    build_tools_version = "25.0.1",
    # Replace with path to Android SDK on your system
    path = "/Users/xxx/Library/Android/sdk/",
)

android_ndk_repository(
    name="androidndk",
    path="/Users/xxx/xxx/android-ndk-r13/",
    api_level=14)
```

至少需要使用 NDK 12b 以上版本和 SDK 23 以上版本。 


### 2.2.3 使用bazel编译demo

```
bazel build -c opt //tensorflow/exmples/android:tensorflow_demo
```

编译好后使用adb命令安装：  

```
adb install -r bazel-bin/tensorflow/examples/android/tensorflow_demo.apk
```



