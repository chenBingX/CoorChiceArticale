# [Tensorflow常用api](https://blog.csdn.net/lenbow/article/details/52152766)

-  **tf.Vaiable()**  
    创建一个变量，用于在一个 session.run 环境中共享持久状态。通常，我们使用声明一个变量来表示权重 w 或者偏置量 b。 
     
    ```
    w_1 = tf.Variable(tf.constant(1), name='w_1')
    
    w_2 = tf.Variable(tf.random_normal(shape=[2,3], mean=0, stddev=1), name='w_2')
    ```
    
- **tf.initialize_all_variables()** 
    初始化变量。通过 `tf.Vaiable()` 等声明的变量，需要在计算图启动之初先 run 一下，以初始化变量。  
    
    ```
    init = tf.initialize_all_varibale()
    sess.run(init)
    ```

- **tf.constant(value, shape, dtype, name)**  
    创建一个常量。
    
    ```
    constant_a = tf.constant([[1, 2, 3, 5, 6]])
     
    ```

- **tf.placeholder()**

    创建一个占位对象，用来装训练数据。  
    
    ```
    x_data = tf.placeholder(tf.float32, shape=[batch_size, IMAGE_PIXELS])
    ```


- **tf.zeros()**
    
    创建一个所有参数都为 0 的 tensor 对象。默认数据类型为 tf.float32。
    
    ```
    tf.zeros([2, 3]) => [[0,0,0],[0,0,0]]
    ```

- **tf.ones()**
    创建一个所有参数都为 1 的 tensor 对象。默认数据类型为 tf.float32。
    
    ```
    tf.ones([2, 3]) => [[1,1,1],[1,1,1]]
    ``` 
    
    
- **tf.truncated_normal(shape, mean, stddev)**  
    
    用于产生一个正太分布值。如果产生的正太分布的值与指定的均值mean的差值大于标准差 stddev 两倍的话，就重新产生新的值。  
    
    ```
    tf.truncated_normal(shape=[10,10], mean=0, stddev=0.1)
    ```

- **tf.matmul()**  
    两个矩阵乘法。  
    
    ```
    matrix1 = tf.constant([[3., 3.]])
    matrix2 = tf.constant([[2.],[2.]])
    
    product = tf.matmul(matrix1, matrix2)
    ```
 
    
- **tf.equel()**  
    
    比较两个矩阵元素是否相等，返回一个bool矩阵。  
    
    ```
     constant_a = tf.constant([[1, 2, 3, 5, 6]])
     constant_b = tf.constant([[1, 2, 3, 5, 7]])
     
     with tf.Session() as sess:
        sess.run(tf.equal(constant_a, constant_b))
        
      => [[TRUE, TRUE, TRUE, TRUE, FALSE]]
    
    ```


- **tf.reduce_sum(input_tensor, reduction_indices=None, 
keep_dims=False, name=None)**

计算传入的tensor元素的和。  

```
x = tf.constant([[1,1,1],[1,1,1]])

tf.reduce_sum(x)  => 6
tf.reduce_sum(x, 0) => [2,2,2]
tf.reduce_sum(x, 1) => [3,3]
```


- **tf.reduce_mean(input_tensor, 
reduction_indices=None, 
keep_dims=False, name=None)**

求传入 tensor 中元素的平均值。


- **tf.Session()**
获得一个 Session 对象，用于启动计算图。它为计算图提供了一个可靠的计算环境。 

```
sess = tf.session()
sess.run(op)
``` 

- **tf.InteractiveSession()**
通过这种方式获得的 Session，可以在图计算过程中插入新的计算图。  

```
sess = tf.InteractiveSession()
```

- **tf.nn.conv2d(input, filter, strides, padding, 
use_cudnn_on_gpu=None, data_format=None, name=None)**  

**input**: 输入图片，格式为 **[batch, 长，宽，通道数(1-灰度, 3-rgb, 4-argb)]**   

**filter**: 卷积核，格式为 **[长，宽，输入通道数，输出通道数]**。长 和 宽 指的是卷积核的大小；输入通道数需要和 input 的通道数保持一致；输出通道数是期望输出的通道数，根据情况自行指定。  

**strides**: 步长。格式为 **[1，长上步长，宽上步长，1]** 指的是卷积核每次移动多少会停下来卷积一次。

**padding**: 补充，用于处理边缘部分。取值有 'VALIDE' 和 'SAME'。  

![屏幕快照 2018-10-10 下午4.37.35](http://ogemdlrap.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-10-10%20%E4%B8%8B%E5%8D%884.37.35.png)


上面是 valid（不及）策略和 same（过）策略处理边缘的情况。


- **tf.nn.max_pool(value, ksize, strides, padding, name=None)**  
    
    **value**: 需要池化的输入参数，格式 **[batch, 长，宽，通道数]** 
    
    **ksize**: 池化窗口大小，格式为 **[1, 长，宽，1]**，第一个元素为1则相当于不在 batch 上池化，第二、三个元素表示在输入的长、宽上池化，第四个元素为1相当于不在 通道数 上池化。  
    
    **strides**: 和卷积核类似，表示每次在相应位置的滑动步长。格式为 **[1, 长，宽，1]**。
    
    **padding**: 边缘处理模式，和卷积一样。
    
- **tf.reshape(tensor,shape,name=None)**  
    改变一个 tensor 张量的形状。  
    
    ```
    x = tf.constant([1,2,3,4])
    tf.reshape(x, [2,2]) => [[1,2],[3,4]]
    ```
    特别的，如果 shape 中包含 -1 分量，先满足其它分量，最后 **(输入张量总数／shape中非-1分量的积)** 的结果就是-1所代表的维度的大小，shape至多能有一个分量为 -1。特殊的，如果是 [-1] 则变成一个一维德张量。
    
    ```
    x = tf.constant(
               [[[1, 1, 1],
                 [2, 2, 2]],
                [[3, 3, 3],
                 [4, 4, 4]],
                [[5, 5, 5],
                 [6, 6, 6]]])
               
    tf.reshape(x, [2, -1]) => 
        [[1, 1, 1, 2, 2, 2, 3, 3, 3],
         [4, 4, 4, 5, 5, 5, 6, 6, 6]]
    
    ```


# tf.estimator Api

集成了一些现成的模型算法图，可以直接训练模型。

```
import tensorflow as tf

# 声明一个线性分类器
classifier = tf.estimator.LinearClassifier()

# 传入数据，使用线性分类器训练模型
classifier.train(input_fn=train_input_fn, steps=2000)

# 用训练好的模型进行预测
predictions = classifier.predict(input_fn=predict_input_fn)
```




