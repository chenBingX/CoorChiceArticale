# 从MNIST数据集训练开始

在深度学习中，数据会被分为： 

- 训练集，包括训练数据和标签数据
- 验证集，用于训练时调整参数
- 测试集，用于训练后测试，模型


# MNIST数据集

MNIST数据集是一个开源的手写数据库。它提供了大量的数据样本作为训练集和验证集。  

[地址：http://yann.lecun.com/exdb/mnist/](http://yann.lecun.com/exdb/mnist/)


# 训练保存模型


```
# coding=utf-8

from inupt_data import *
import time

mnist = read_data_sets("MNIST_data/", one_hot=True)

# 定一个 placeholder op，用来输入训练数据
# 张量[None, 784]标示训练数据集的图，None表示任意值，由于图片是 28 * 28 ，所以一个图片包括 784 个像素
# 通过一个二维张量可以定位出数据集中任意图片的任意一个像素点
x_data = tf.placeholder("float", [None, 784])
# 定义一个新的 placeholder op 用来输入标签数据集，因为是0-9的数字
# 所以，使用"独热编码"法定义了标签
y_data = tf.placeholder("float", [None, 10])
# 比如可能有60000个数据

# 定义权重值，因为需要根据训练情况不断的调整
# 所以使用变量 Variable 表示
# W 的维度是 [784, 10]，因为我们的图片像素点为 784，我们需要将它转化为一个 10 为的证据向量
# tf.zeros()初始化出的是单位向量
W = tf.Variable(tf.zeros([784, 10]))

# 定义偏置量，因为要转化为 10 维证据向量
# 相应的偏置量定义为 10
b = tf.Variable(tf.zeros([10]))

# 构建模型
# 使用 softmax 函数来构建模型
# Tensorflow 所做的事情就是自动使用这个函数来构建模型，而不需要用户关心复杂的计算过程
y = tf.nn.softmax(tf.matmul(x_data, W) + b)

# 按照交叉熵公式计算交叉熵
cross_entropy = -tf.reduce_sum(y_data * tf.log(y))

# 使用梯度下降法不断的调整变量，寻求最小的交叉熵
# 此处使用梯度下降法以0.01的学习速率最小化交叉熵
train_step = tf.train.GradientDescentOptimizer(0.01).minimize(cross_entropy)

# 创建初始化变量op
init = tf.initialize_all_variables()

# 在session中启动初始化op，以初始化变量
with tf.Session() as sess:
    train_times = 10000
    # core = "/gpu:0"
    core = "/cpu:0"
    with tf.device(core):
        sess.run(init)
        start_time = time.time()
        for i in range(train_times):
            # 每次取100个数据进行训练，这个函数可以随机的从样本总体中取出100个样本和100对应的标签
            # 实际采用的 mini-batch 梯度下降法进行训练的
            batch_x_data, batch_y_data = mnist.train.next_batch(100)
            # 用一个字典将取出的随机小批样本输入，进行训练
            sess.run(train_step, feed_dict={x_data: batch_x_data, y_data: batch_y_data})
        consume_time = (time.time() - start_time) * 1000
        print "本次使用 (" + core + ") 共训练 " + str(train_times) + "次，耗时：" + str(consume_time) + "ms"
    # -----------模型评估-----------------
    # tf.argmax(vector, 1)函数，可以返回一个向量中的最大值的索引（下标）
    # tf.argmax(y,1)返回的是模型对于任一输入x预测到的标签值
    # tf.argmax(y_data,1) 返回标签数据集中向量的最大值位置
    # 联系 "独热编码" 法来理解
    # tf.equal 来检测我们的预测是否真实标签匹配(索引位置一样表示匹配)，返回一个和第一个参数维度相同的布尔矩阵
    correct_prediction = tf.equal(tf.argmax(y, 1), tf.argmax(y_data, 1))
    # 先将布尔向量correct_prediction转换成浮点数向量，形式：[1,1,0,1]
    # 然后用tf.reduce_mean()函数来求该向量元素的平均值
    accuracy = tf.reduce_mean(tf.cast(correct_prediction, "float"))
    # 启动检测函数，输入测试数据集
    accuracy_value = sess.run(accuracy, feed_dict={x_data: mnist.test.images, y_data: mnist.test.labels})
    print "准确率：" + str(accuracy_value * 100) + "%"

    # 获得一个储存对象
    saver = tf.train.Saver()
    saver.save(sess, "/Users/coorchice/Desktop/ML/model/" + str(train_times) + "/", train_times)
    print "保存模型成功！模型路径：/Users/coorchice/Desktop/ML/model/"
```

# 读取使用模型预测

```
# coding=utf-8

from inupt_data import *
import numpy as np
from PIL import Image
import os

train_times = 10000
num = 8
CKPT_DIR = "/Users/coorchice/Desktop/ML/model/" + str(train_times)
img = Image.open("num" + str(num) +".png").resize((28, 28), Image.ANTIALIAS).convert("L")
img.save("num" + str(num) + "_resize.png")
os.system("open num" + str(num) + "_resize.png")
# 重新调整数组的形状
flatten_img = np.reshape(img, 784)
arr = np.array([1 - flatten_img])
print arr

with tf.Session() as sess:
        # 还原模型
        x_data = tf.placeholder("float", [None, 784])
        y_data = tf.placeholder("float", [None, 10])
        W = tf.Variable(tf.zeros([784, 10]))
        b = tf.Variable(tf.zeros([10]))
        y = tf.nn.softmax(tf.matmul(x_data, W) + b)

        sess.run(tf.global_variables_initializer())
        saver = tf.train.Saver()
        # 读取模型
        ckpt = tf.train.get_checkpoint_state(CKPT_DIR)
        if ckpt and ckpt.model_checkpoint_path:
            # 恢复模型
            saver.restore(sess, ckpt.model_checkpoint_path)
            y = sess.run(y, feed_dict={x_data: arr})
            result = str(np.argmax(y, 1))
            print("\n预测结果：" + result)
            os.system("open num" + result + ".png")
        else:
            print "没有模型"
        sess.close()
```

运行预测结果为8.




