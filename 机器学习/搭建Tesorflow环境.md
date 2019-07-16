# 搭建Tesorflow环境

# 1. 准备

## 1.1 安装Homebrew

```
/usr/bin/ruby -e "$(curl -fsSL \
https://raw.githubusercontent.com/Homebrew/install/master/install)" 
```

## 1.2 安装Bazel

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

## 1.3 安装pip
pip是Python的包管理工具，需要使用它来下载python包。

```
brew install pip
```


# 2. 克隆Tensorflow源码

到指定文件夹下，执行命令：  

```
git clone --recurse-submodules  https://github.com/tensorflow/tensorflow.git
```

# 3. 编译

## 3.1 安装 Numpy
需要先安装python的 **Numpy** 库，使用它来处理数值。  

```
pip install Numpy
```

## 3.2 编译Tensorflow python包

```
bazel build -c opt //tensorflow/tools/pip_package:build_pip_package
```

在编译过程中，可能会出现很多 import 错误，只需要依次将这些需要导入的包使用 pip 安装即可。

```
bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
```

该命令会在根目录下创建平台向光的 `.whl` pip包。看不到可以通过命令在根目录进入 `/tmp/tensorflow_pkg`，然后使用 `ls` 查看。  

安装pip包，`.whl` 文件与你所编译出来的文件名一致：  

```
pip install /tmp/tensorflow_pkg/tensorflow-0.5.0-cp27-none-linux_x86_64.whl
```

## 3.3 验证
打开 python shell 模式，输入：  

```
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print sess.run(hello)
Hello, TensorFlow!
>>> a = tf.constant(10)
>>> b = tf.constant(32)
>>> print sess.run(a+b)
42
>>>
``` 


