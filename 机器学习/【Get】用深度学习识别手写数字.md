

![image](http://upload-images.jianshu.io/upload_images/1869462-dd32a688a635051c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前置参考读物：  

[《机器学习，看完就明白了》传送门](https://www.jianshu.com/p/2ffeb08d0c2a) 

# 获取数据源

训练数据直接使用开源的手写数据集MNIST。

MNIST数据集是一个开源的手写数据库。它提供了大量的数据样本作为训练集和验证集。这个数据集拥有 60000 个训练样本，和 10000 个测试样本。    

[MNIST 官网（一个很 low 的网站）传送门：http://yann.lecun.com/exdb/mnist/](http://yann.lecun.com/exdb/mnist/)   

 

![image_mnist_web](http://upload-images.jianshu.io/upload_images/1869462-008d3e2586d76820.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



就是上面那几个红色的、带下划线的！！！

如果你下载的 tensorflow 包括了 mnist 的例子的话，很幸运，你可以直接引用到数据加载的工具：  

```
from tensorflow.contrib.learn.python.learn.datasets.mnist import read_data_sets
```  

否则的话，你需要自己写一个工具，用于训练数据的下载和读取。但你可以在这个地址中找到这段代码。  

[【mnist.py 传送门】](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/learn/python/learn/datasets/mnist.py)  

我们实际在外面，需要调用的就是 `read_data_sets` 这个函数。  

关于数据下载，你可以直接到 CoorChice 给出的 MNIST 官网上直接下好数据，然后使用 `read_data_sets` 函数从储存路径读取就行。

# 开始构建网络
## 定义几个辅助函数
首先，先抽象出几个函数，用来创建 权重、偏置量、卷积核和池化层。它们是这样的。  

```
# 定义一个用于创建 权重 变量的函数
def weight_variable(shape):
    initial = tf.truncated_normal(shape, stddev=0.1)
    var = tf.Variable(initial)
    # 记录每一个权重，因为后面要使用正则化
    # 至于原因，后面具体再说
    tf.add_to_collection(tf.GraphKeys.WEIGHTS, var)
    return var


# 定义一个用于创建 偏置量 变量的函数
def bias_variable(shape):
    # 初始化值为0.1
    initial = tf.constant(0.1, shape=shape)
    return tf.Variable(initial)


# 构建卷积函数
# 该卷积核每次在长、宽上移动一个步长，padding采用"SAME"策略
def conv2d(x, W):
    return tf.nn.conv2d(x, W, strides=[1, 1, 1, 1], padding='SAME')


# 该池化核大小为2x2，长、宽上步长为2，padding采用"SAME"策略
def max_pool_2x2(x):
    return tf.nn.max_pool(x, ksize=[1, 2, 2, 1], strides=[1, 2, 2, 1], padding='SAME')
```

在上面这段代码中，CoorChice 有必要解释解释权重 weight 的生成。  

```
tf.truncated_normal(shape, mean, stddev)
```

这个函数会从一个正太分布（该正太分布的均值为 mean，默认为0）中产生随机数，什么意思呢？  

![image_truncated](http://upload-images.jianshu.io/upload_images/1869462-e76bf874af50af0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


看上图，stddev 表示标准差，就是图中横坐标上的取值，也就是说，这个函数会从 `[mean ± 2stddev]` 的范围内产生随机数，如果产生的随机数不在这个范围内，就会再次随机产生，直到随机数落在这个范围内。  

至于为什么要用这种方法来初始化 weight 呢？这是根据各位大牛们的经验所得，使用这种方式产生的 weight 不容易出现梯度消失或者爆炸的问题。  

反正这也是一门玄学。




## 构建网络结构

先看一下完整代码，我们在逐一解释。

```
import tensorflow as tf

class CnnModel_MNIST:
    def __init__(self):
        # 创建占位tensor，用于装载数据
        self.x_data = tf.placeholder(tf.float32, [None, 784])
        self.y_data = tf.placeholder(tf.float32, [None, 10])

        # -----------------------构建第一层卷积-----------------------
        with tf.name_scope('hidden1'):
            W_conv1 = weight_variable([5, 5, 1, 32])
            b_conv1 = bias_variable([32])
            x_image = tf.reshape(self.x_data, [-1, 28, 28, 1])
            h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)
            h_pool1 = max_pool_2x2(h_conv1)

        # -----------------------构建第二层卷积------------------------
        with tf.name_scope('hidden2'):
            W_conv2 = weight_variable([5, 5, 32, 64])
            b_conv2 = bias_variable([64])
            h_conv2 = tf.nn.relu(conv2d(h_pool1, W_conv2) + b_conv2)
            h_pool2 = max_pool_2x2(h_conv2)

        # -----------------------构建密集(全)链接层------------------------
        with tf.name_scope('FC1'):
            W_fc1 = weight_variable([7 * 7 * 64, 1024])
            b_fc1 = bias_variable([1024])
            h_pool2_flat = tf.reshape(h_pool2, [-1, 7 * 7 * 64])
            h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1) + b_fc1)

        # -----------------------加入Dropout------------------------
        with tf.name_scope('dropout'):
            self.keep_prob = tf.placeholder(tf.float32)
            h_fc1_drop = tf.nn.dropout(h_fc1, self.keep_prob)

        # -----------------------构建输出层------------------------
        with tf.name_scope('output'):
            W_fc2 = weight_variable([1024, 10])
            b_fc2 = bias_variable([10])

            self.y_conv = tf.nn.softmax(tf.matmul(h_fc1_drop, W_fc2) + b_fc2)

```

### 创建数据输入的占位符

```
x_data = tf.placeholder(tf.float32, [None, 784])
y_data = tf.placeholder(tf.float32, [None, 10])
```

我们需要先创建两个占位变量来容纳待会儿输入的数据，然后把它们带着到网络中进行运算。  

- **x_data** 是用来容纳训练数据的，它的 shape 形状在这里被莫名其妙的定义为 [None, 784]。其实这里是有学问的，且听 CoorChice 慢慢道来。  

    第一维定义为 None 表示不确定，后面会被实际的数值替代。这样做是因为我们一开始并不知道会有多少张图片数据会被输入。或者当我们采取 mini-batch 的梯度下降策略时，可以自由的设置 batch 的大小。  

    第二个维度定义为 784，这完全是因为我们数据集中的图片大小被统一为了 28*28 。 
    
- **y_data** 是用来容纳训练数据的标签的，它的 shape 之所以被定义为 [None, 10] ，是因为它的第一维为 None 与 **x_data** 具有相同理由，而第二维为 10 是因为我们总共有 0~9 共 10 种类别的数字。  



### 构建第一层网络

```
W_conv1 = weight_variable([5, 5, 1, 32])
b_conv1 = bias_variable([32])
x_image = tf.reshape(self.x_data, [-1, 28, 28, 1])
h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)
h_pool1 = max_pool_2x2(h_conv1)
```

#### 权值 w 的构建，  

```
W_conv1 = weight_variable([5, 5, 1, 32])
```

传入的数组表示 w 的形状，其实就定义了该层卷积核的大小和数量： 

- 每个卷积核的大小为 5x5

- 输入通道数为 1，因为我们用的图片是灰度图。如果是用不带透明通道的 rgb 彩色图该值就设为 3，如果在带了透明通道的 rgba 彩色图该值就设为 4

- 该层输出通道数为 32，即该层有 32 个卷积核。对于隐藏层中每个卷积层中的卷积核大小如何的确定，再次强调，这是一个玄学，凭感觉设置。最靠谱的方法是用一些公开的网络模型，照着巨人们的设置，毕竟是经过反复尝试论证出来的。  

#### 偏置量 b 的构建

```
b_conv1 = bias_variable([32])
```

b 的大小和卷积核个数相对应就行了。什么意思呢？就是每个卷积核和输入卷积后，再加上一个偏置量就好。回顾一下卷积核的结构。  

```
wx + b
```
  
#### 改变输入数据的形状

```
x_image = tf.reshape(self.x_data, [-1, 28, 28, 1])
```

这行代码作用是改变我们输入数据的形状，让它可以和我们的卷积核进行卷积。因为输入的数据 x_data 是一个 Nx784 的张量，所以需要先变为 M x 28 x 28 x 1 的形状才能进行运算。  

第一个维度的 -1 表示大小待定，优先满足后面 3 个维度，最后再计算第一个维度的大小。也就是这样的：`N x 784 / (28 x 28 x 1)` 。

第二、第三维度实际上表示每张输入图片的大小需改为 28 x 28。  

第四维度表示通道数，灰度为 1，rgb 为 3，rgba 为 4 。  


#### 构建卷积并加上激活函数

```
h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)
```

这行代码包含了 3 个操作：

- `conv2d(x_image, W_conv1)`，将输入与该层的所有卷积核进行卷积运算


![image](http://upload-images.jianshu.io/upload_images/1869462-3ebd478cb486207b.gif?imageMogr2/auto-orient/strip) 

这是一个卷积运算的动态示意图。  

每次从输入中取出一个和卷积核大小相同的张量进行卷积运算。  

![image_conv_compute](http://upload-images.jianshu.io/upload_images/1869462-0c1f77a6775d1267.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其意义就是，将卷积核旋转 180 度，然后再输入进行计算。  

![image_conv_compute2](http://upload-images.jianshu.io/upload_images/1869462-732d7dc8b13ed32a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于涉及到旋转操作，所以卷积核的大小通常会取奇数，这样能让卷积核有一个明显的旋转中心。  


- `conv2d(x_image, W_conv1) + b_conv1`，每次卷积后加上一个偏置量  



- `tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)`，最后加上一个激活函数，增加非线性的变换


此处使用的激活函数是比较流行的 ReLu 函数，它的的函数式很简单： 

```
y = max(x, 0)
```

图像也比较直观：  

![image](http://upload-images.jianshu.io/upload_images/1869462-6929e212c9855c38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

ReLu激活函数的好处在于，由于它在第一象限就是 x，所以能够大量的减少计算，从而加速收敛。同时它天生就能减小梯度消失发生的可能性，不过梯度爆炸还是可能会发生。  


#### 池化

```
h_pool1 = max_pool_2x2(h_conv1)
```

最后，再加上一个池化层，能够压缩权值数量，减小最后模型的大小，提高模型的泛化性。  

这里使用了一个 2x2 的 max_pooling，且步长取 1。可以回到上面定义的 `max_pool_2x2(x)` 函数回顾一下。  

max_pooling 实际就是取一个 2x2 张量中的最大值，这样能够过滤掉一些并不是很重要的噪声。  

![image](http://upload-images.jianshu.io/upload_images/1869462-2f22b8bbd639e96b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

### 构建第二层网络

```
W_conv2 = weight_variable([5, 5, 32, 64])
b_conv2 = bias_variable([64])
h_conv2 = tf.nn.relu(conv2d(h_pool1, W_conv2) + b_conv2)
h_pool2 = max_pool_2x2(h_conv2)
```

第二层和第一层构建的套路基本是一样的，其实我们再多加几层也都是按照这个模式走的。  

需要注意的就是第二层中，w 的输入通道数为上一层最后的输出通道数，也就是 hpool1 的输出，这里直接算出了是 32，因为上面已经定义好了啊。  

如果不确定的时候，可以通过这种方式来确定输入通道数：  

```
in = h_pool1.get_shape()[-1].value
```

[-1]  表示不管 h_pool1 的形状如何，都取它最后一维的大小。  

### 构建全链接层

```
W_fc1 = weight_variable([7 * 7 * 64, 1024])
b_fc1 = bias_variable([1024])
h_pool2_flat = tf.reshape(h_pool2, [-1, 7 * 7 * 64])
h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1) + b_fc1)
```

> 全连接层则起到将学到的“分布式特征表示”映射到样本标记空间的作用

这句话看起来很具体，然而并不知道它在说些什么。  

那么，我们试着理解一下，先看看全链接层的结构。  

#### 定义 w 和 b
```
W_fc1 = weight_variable([7 * 7 * 64, 1024])
```

上面这行代码，将fc1 的 w 形状设定为 `[7 * 7 * 64, 1024]`。第一个维度大小为 `7 * 7 * 64`，因为经过前面一层的卷积层的池化层后，输出的就是一个  `7 * 7 * 64` 的张量，所以这里的输入就是上一层的输出。嗯，这一点在整个神经网络里都是这样。  

第二个维度大小为 1024。这个就有点诡异了！为什么是这个数值？  

实际上，这是我们可以自己随便设定的，它表示该全链接层的神经元个数，数量越多计算耗时越长，但是数量过少，对前面提取出来的特征的分类效果又不够好。  

因此，我们的全链接层拥有的特征数量就是 **7 x 7 x 64 x 1024**。数量还是比较惊人的。  

```
b_fc1 = bias_variable([1024])
```

同样，b 的数量需要对应于 w 的最后一个维度，也就是一个神经元对应一个偏置量b。  

#### 变形输入

```
h_pool2_flat = tf.reshape(h_pool2, [-1, 7 * 7 * 64])
```

为了能和上面定义的全链接层特征张量相乘，需要把输入的张量变形。其实对于每一个 `7 x 7 x64` 的输入张量而言，就是将它们展平成一个一维的向量。  

第一个维度取 -1 同上面提到过的意思一样，最后确定这个维度。实际上就是最后一个池化层输出的数量。  

#### 构建线性函数，加上 ReLu 函数

```
h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat, W_fc1) + b_fc1)
```

上面的代码还是按照 `wx + b` 的线性公式构建函数，然后加上 ReLu 函数增加非线性变化。  

![image](http://upload-images.jianshu.io/upload_images/1869462-d8306dea4bf0e952.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

这一波计算，清晰的表达出了，当一个来自最后一层池化层的 `[7 x 7 x 64]` 的输出经过全链接层后，就被平铺成了一个 `[1 x 1024]` 的向量。相当于把前面分散的特征全部链接在了一起，这也就是为什么说前面的卷积核是从局部观察，而全链接层是从全局的视野去观察的，因为它这一层整合所有前面的特征。整合了所有的特征，我们就可以进行后续的分类操作了。  


至此，相信你对全链接层有了一个大概的了解。从中可以看出一些猫腻来。  

- 全链接层增加模型的复杂度，因为增加了很多神经元来扩充特征集。也因此，它有助于提升模型的准确率。

- 但随着特征数量的爆炸式增加，训练速度必然会变慢。而且如果全链接层设置的神经元数量过多，会出现过拟合的现象。所以，需要适当的设置，不能一味的贪多。

#### 加入 Dropout 

```
self.keep_prob = tf.placeholder(tf.float32)
h_fc1_drop = tf.nn.dropout(h_fc1, self.keep_prob)
```

在全链接层之后，往往会跟着 Dropout 操作。由于在神经网络中，神经元的个数非常爆炸，往往会产生过拟合的问题，特别是引入了全链接层这种操作之后。所以我们需要做些什么来让过拟合发生的概率减小一些。  

**Dropout** 就是一种很流行的方案。  

```
h_fc1_drop = tf.nn.dropout(h_fc1, self.keep_prob)
```

这行代码的第二个参数，我们可以动态的传入一个数值，表示每个神经元有多大的概率失效，其实就是不参与计算。  

形象点描述就是这样一个过程。每个神经元进行运算前，都按照设置的 keep_prob 概率决定它要不要参与计算。比如 `keep_prob=0.5` 的话，表示每个神经元有 50% 的概率失效。   

![image](http://upload-images.jianshu.io/upload_images/1869462-fd6c9b76a9d2c64f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

不难看出，Dropout 操作能够一定程度上加快训练速度，同时降低过拟合的可能性。

### 构建输出层

```
W_fc2 = weight_variable([1024, 10])
b_fc2 = bias_variable([10])

self.y_conv = tf.nn.softmax(tf.matmul(h_fc1_drop, W_fc2) + b_fc2)
```

最后一层输出层，我们就可以得到一个结果了。  

输出层的函数结构仍然是 `wx+b` 的线性结构。  

这里有必要解释一下输出的 w 的形状。第一个维度不用说，就是上一层输出的结果，这里上一层是从全链接层输出出来 1024 个神经元。第二个维度是我们分类的总类目数，由于识别的是 0-9 的手写数字，所以总共有 10 个类别。  

构建好线性函数后，在加入一个非线性的激活函数。在分类场景中， **Softmax** 是一个在输出层被广泛使用的激活函数。  

![image](http://upload-images.jianshu.io/upload_images/1869462-1b1294e373f5097c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

这是 Softmax 的公式，很容易看出，它的值域为 [0, 1]。这就比较厉害，直接就转成一个个的概率了。就是说，每个输出对应类别的概率是多少。  

再这个看看形象的示意图理解一下。  

![image](http://upload-images.jianshu.io/upload_images/1869462-e943bc1022381d6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 这，就是网络！
![image_graph](http://upload-images.jianshu.io/upload_images/1869462-74d5aa167a64d1c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

最后，这个网络就成型了。这其实是一个很简单的网络，总共 4 层，包含两个卷积层，一个全链接层和一个输出层。  

从图中可以清晰的看到数据的流向。

# 就要开始训练了

```
# coding=utf-8

import time
from input_data import *
from cnn_utils import *
from cnn_model import CnnMnistNetwork

train_times = 35000
base_path = "../mnist/"
save_path = base_path + str(train_times) + "/"

# 读取数据
mnist = read_data_sets("MNIST_data/", one_hot=True)

# 创建网络
network = CnnMnistNetwork()
x_data = network.x_data
y_data = network.y_data
y_conv = network.y_conv
keep_prob = network.keep_prob
# ------------------------构建损失函数---------------------
with tf.name_scope("cross_entropy"):
    # 创建正则化对象，此处使用的是 L2 范数
    regularization = tf.contrib.layers.l2_regularizer(scale=(5.0 / 50000))
    # 应用正则化到参数集上
    reg_term = tf.contrib.layers.apply_regularization(regularization)
    # 在损失函数中加入正则化项
    cross_entropy = (-tf.reduce_sum(y_data * tf.log(y_conv)) + reg_term)
tf.scalar_summary('loss', cross_entropy)
with tf.name_scope("train_step"):
    # 使用 Adam 进行损失函数的梯度下降求解
    train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)

# ------------------------构建模型评估函数---------------------
with tf.name_scope("accuracy"):
    with tf.name_scope("correct_prediction"):
        # 对比预测结果和标签
        correct_prediction = tf.equal(tf.argmax(y_conv, 1), tf.argmax(y_data, 1))
    with tf.name_scope("accuracy"):
        # 计算准确率
        accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
tf.scalar_summary('accuracy', accuracy)

# 创建会话
sess = tf.InteractiveSession()

# 合并 summary
summary_merged = tf.merge_all_summaries()
train_writer = tf.train.SummaryWriter(save_path + "graph/train", sess.graph)
test_writer = tf.train.SummaryWriter(save_path + "graph/test")

start_time = int(round(time.time() * 1000))

# 初始化参数
sess.run(tf.initialize_all_variables())

for i in range(train_times):
    # 从训练集中取出 50 个样本进行一波训练
    batch = mnist.train.next_batch(50)
    if i % 100 == 0:
        summary, train_accuracy = sess.run([summary_merged, accuracy],
                                           feed_dict={x_data: batch[0], y_data: batch[1], keep_prob: 1.0})
        test_writer.add_summary(summary, i)
        consume_time = int(round(time.time() * 1000)) - start_time
        print("当前共训练 " + str(i) + "次, 累计耗时：" + str(consume_time) + "ms，实时准确率为：%g" % (train_accuracy))
    # 记录训练时数据，每训练1000次保存一次训练信息
    if i % 1000 == 0:
        run_options = tf.RunOptions(trace_level=tf.RunOptions.FULL_TRACE)
        run_metadata = tf.RunMetadata()
        # 训练一次，dropout 的参数设置为 0.5
        summary, _ = sess.run([summary_merged, train_step],
                              feed_dict={x_data: batch[0], y_data: batch[1], keep_prob: 0.5}, options=run_options,
                              run_metadata=run_metadata)
        train_writer.add_run_metadata(run_metadata, str(i))
        train_writer.add_summary(summary, i)
    else:
        summary, _ = sess.run([summary_merged, train_step],
                              feed_dict={x_data: batch[0], y_data: batch[1], keep_prob: 0.5})
        train_writer.add_summary(summary, i)
    # 每训练 2000 次保存一次模型
    if i != 0 and i % 2000 == 0:
        test_accuracy = int(
            accuracy.eval(feed_dict={x_data: mnist.test.images, y_data: mnist.test.labels, keep_prob: 1.0}) * 100)
        save_model(base_path + str(i) + "_" + str(test_accuracy) + "%/", sess, i)

# 在测试集计算准确率
summary, test_accuracy = sess.run([summary_merged, accuracy],
                                  feed_dict={x_data: mnist.test.images, y_data: mnist.test.labels, keep_prob: 1.0})
train_writer.add_summary(summary)
print("测试集准确率：%g" % (test_accuracy))

print("训练完成！")
train_writer.close()
test_writer.close()
# 保存模型
save_model(save_path, sess, train_times)

```

先放一波完整代码，后面再挑着重点说一说。 

其中涉及到一些深度学中的基本概念，本篇篇幅已经够长了，CoorChice 就不在这里过多解释了。如果还不清楚，可以先跳到以下这篇文章，花个几分钟了解了基本概念后再继续往下。  

[《机器学习，看完就明白了》传送门](https://www.jianshu.com/p/2ffeb08d0c2a) 

## 构建损失函数


```
# 创建正则化对象，此处使用的是 L2 范数
regularization = tf.contrib.layers.l2_regularizer(scale=(5.0 / 50000))
# 应用正则化到参数集上
reg_term = tf.contrib.layers.apply_regularization(regularization)
# 在损失函数中加入正则化项
cross_entropy = (-tf.reduce_sum(y_data * tf.log(y_conv)) + reg_term)
tf.scalar_summary('loss', cross_entropy)
with tf.name_scope("train_step"):
# 使用 Adam 进行损失函数的梯度下降求解
train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)
```

实际上，构建损失函数的关键代码就两行：  

```
cross_entropy = (-tf.reduce_sum(y_data * tf.log(y_conv)) + reg_term)
train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)
```

第一行构建出一个 **交叉熵** 损失函数，第二行对损失函数做梯度下降获得 tensor。  

这里使用了 Adam优化算法 能够为不同的参数动态的计算不同的自适应学习率，这与 SGD 恒定不变的学习率有区别。这种优化算法使得各个参数的变化比较平稳，计算消耗的内存更小，收敛也会更快一点。  

但，网上看到有说它的训练效果不如 SGD 的 ？？？  


本来，损失函数的构建到此就结束了，反手就可以开始训练了。但是 CoorChice 在训练过程中发现，每次当训练进行到 2w 多次的时候就会出现梯度爆炸的现象。突然的 loss 就都变为 NaN，accuracy 本来好好的 0.99 呢，骤降的趋近于 0 ！  

![image](http://upload-images.jianshu.io/upload_images/1869462-8e68fb64056c7ce5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

于是 CoorChice 二话不说，打开 Google 就是一通搜索，网上说的各种各样的原因都有。  

这种问题也不太好确定具体是因为哪一个原因导致的，于是就加个正则化试试。结果就好了！  

来看看正则化是怎么加。  

前面在构建 w 函数里加了一行代码：

```
tf.add_to_collection(tf.GraphKeys.WEIGHTS, var)
```  

目的就是为了把每个 w 放到集合中，以便此时进行正则化使用。  

此处，CoorChice 选择使用高端一点的 L2范式，加入正则化后的 Loss 公式如下：  

![image](http://upload-images.jianshu.io/upload_images/1869462-949d772d50c94d25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


c0就是原本的损失函数部分，这里就是 **交叉熵**，这部分又被称作是 **经验风险**。后面的一部分就是我们的 L2正则化式了，它实际就是把每个权重平方后求和，然后除以 w 的数量，在乘以个重要度系数。正则化的部分又叫作 **结构风险**，因为它是基于 w 计算出的一个数值，加在 **交叉熵** 上，从而每次增大交叉熵的值，也就是增大梯度，达到惩罚loss的效果。它一定程度上削弱了网络中特征值的作用，从而使模型的泛化性提高，也就能进一步的避免过拟合发生的可能。  

再回过头看看上面的代码，就理解正则化是如何加入到网络中的。  


## 构建评估模型

```
# 对比预测结果和标签
correct_prediction = tf.equal(tf.argmax(y_conv, 1), tf.argmax(y_data, 1))
# 计算准确率
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
```

这两行代码，构建了一个用于评估模型准确率的 accuracy tensor。第一行实际就是比较了一下预测值和真实值，结果是一个 bool 向量，在第二行中转化为浮点数，求个平均就是准确率了。  


## 开始训练啦！
```
for i in range(train_times):
    # 从训练集中取出 50 个样本进行一波训练
    batch = mnist.train.next_batch(50)
    summary, _ = sess.run([summary_merged, train_step],
                              feed_dict={x_data: batch[0], y_data: batch[1], keep_prob: 0.5})
    # 每训练 2000 次保存一次模型
    if i != 0 and i % 2000 == 0:
        test_accuracy = int(
            accuracy.eval(feed_dict={x_data: mnist.test.images, y_data: mnist.test.labels, keep_prob: 1.0}) * 100)
        save_model(base_path + str(i) + "_" + str(test_accuracy) + "%/", sess, i)
```

首先从训练集中随机的取出 50 个样本作为一次训练的输入。为什么要这么干呢？因为训练集有赤裸裸的 60000 个样本啊！训练一次太耗时了，特别是在平时开发用的笔记本上。  

这么做理论上准确率没有全集训练高，但是也能达到 99% 的准确率，却能节省巨量的时间，这点理论上的准确率还是可以适当的舍弃的。  

接着就调用 `sess.run(train_step)` 开始一次训练了，注意此处由于加了 dropout，所以每次 feed_dict 中需要设定它的值。  

```
if i != 0 and i % 2000 == 0:
        test_accuracy = int(
            accuracy.eval(feed_dict={x_data: mnist.test.images, y_data: mnist.test.labels, keep_prob: 1.0}) * 100)
        save_model(base_path + str(i) + "_" + str(test_accuracy) + "%/", sess, i)
```

CoorChice 每训练两千次，在测试集上测试一下，然后保存一下模型。这是良好的习惯。因为一旦训练起来，很多不可控的因素，多保存些模型，后面还可以挑最合的。

上面这个过程 CoorChice 循环了整整 35000 次！电脑全速运转了一晚上才训练完成。  

![image](http://upload-images.jianshu.io/upload_images/1869462-da3d191e88849f9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


上图就是一堆存档的模型文件夹，可以看到，一开始其实准确率其实也不低，97%。随着训练次数的增加，准确率就稳定在了 99% 了。  

# 使用模型进行识别

现在，模型已经训练好了，接下来就可以使用这个模型识别我们自己手写的数字了。  

```
# coding=utf-8

import numpy as np
from PIL import Image
import os
from cnn_model import CnnMnistNetwork
import tensorflow.python as tf

train_times = 20000
num = 5
image_path = "num_images_test/num"
CKPT_DIR = "../mnist/" + str(train_times) + "_99%"
# 将数字图片缩放为标准的 28*28，接着进行灰度处理
img = Image.open(image_path + str(num) +".png").resize((28, 28), Image.ANTIALIAS).convert("L")
# os.system("open " + image_path + str(num) + ".png")
flatten_img = np.reshape(img, 784)
arr = np.array([1 - flatten_img])
print(arr)

# 创建模型对应的网络
network = CnnMnistNetwork()
x_data = network.x_data
y = network.y_conv
keep_prob = network.keep_prob
# 创建会话
sess = tf.InteractiveSession()
# 初始化参数
sess.run(tf.initialize_all_variables())
saver = tf.train.Saver()
ckpt = tf.train.get_checkpoint_state(CKPT_DIR)
if ckpt and ckpt.model_checkpoint_path:
    # 读取恢复模型
    saver.restore(sess, ckpt.model_checkpoint_path)
    # 载入数据，进行识别
    y = sess.run(y, feed_dict={x_data: arr, keep_prob:1.0})
    # 取最大可能
    result = str(np.argmax(y, 1))
    print("\n期望结果" + str(num) + ", 预测结果：" + result)
    os.system("open num_images_test/num" + result[1] + ".png")
else:
    print("没有模型")
```

使用模型比较简单，就是读取一张图片，然后创建出模型所对应的网络结构来，接着读取模型，输入图片，就能得到识别结果了。  


![image](http://upload-images.jianshu.io/upload_images/1869462-32a086bdcf9825c5.gif?imageMogr2/auto-orient/strip)

# 闲扯两句

MNIST 数据的训练相当于是机器学习的 **HelloWorld** 程序，我们构建了一个 4 层的简单的网络进行训练识别，最后得到的模型准确率也是不错的。  

完整的体验了如何从 0 开始构建一个神经网络，然后保存模型，再读取模型进行识别。总的来说，这个过程思路还是比较简单的，关键就在于一些参数设置，还有出现问题如何去解决。比如，CoorChice 在训练过程中就碰到了 NaN 的问题。机器学习还是比较依靠经验的一门技术，需要在不断的实战中总结出一套自己的分析、解决问题的套路来。  



> - 抽出空余时间写文章分享需要信仰，还请各位看官动动小手点个赞，快给 CoorChice 充值信仰吧 😄  
> - CoorChice 会不定期的分享干货，想要上车只需进到 [CoorChice的【个人主页】](https://www.jianshu.com/u/cfec7d70bbec) 点个关注就好了哦。
