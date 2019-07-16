# TensorFlow基本概念

TensorFlow 是一个采用数据流图，用于数值计算的开源库。被用来进行机器学习和深度神经网络的研究。  

相比与其它数值计算框架，TensorFlow 的速度并不算快，但是它由 Google 大力支持，并且社区十分活跃。Google 也在不断的完善它和它的配套设施。TensorFlow 支持多集群异步/同步的计算。因此，TensorFlow 是进行机器学习的首选计算框架。  


![image](http://ogemdlrap.bkt.clouddn.com/tensors_flowing.gif)  

上图就是 TensorFlow 的数据流图。数据流图用节点和线的有向图来描述数学计算。  

- 节点：通常计算操作，输入/输出，读取/写入
- 线：表示节点之间的输入/输出


# 1. TensorBoard 可视化训练过程
```
tensorboard --logdir=path/to/logs
```

在训练完成后，通过以上命令可以打开面板，可视化本次训练过程。



# 2. TensorFlow 中的几个概念

# 2.1. Tensor

TensorFlow 程序使用 tensor 数据结构来代表所有的数据, 计算图中, 操作间传递的数据都是 tensor. 


# 2.2. 变量Variables

变量用于维护图执行过程中的状态信息.   

```
# 创建一个变量, 初始化为标量 0.
state = tf.Variable(0, name="counter")

# 创建一个 op, 其作用是使 state 增加 1

one = tf.constant(1)
new_value = tf.add(state, one)
update = tf.assign(state, new_value)

# 启动图后, 变量必须先经过`初始化` (init) op 初始化,
# 首先必须增加一个`初始化` op 到图中.
init_op = tf.initialize_all_variables()

# 启动图, 运行 op
with tf.Session() as sess:
  # 运行 'init' op
  sess.run(init_op)
  # 打印 'state' 的初始值
  print sess.run(state)
  # 运行 op, 更新 'state', 并打印 'state'
  for _ in range(3):
    sess.run(update)
    print sess.run(state)

# 输出:

# 0
# 1
# 2
# 3
```

在构建图的过程中，如果定义了变量节点，需要先初始化变量：  

```
# 构建一个初始化变量的节点op
init_op = tf.initialize_all_variables()


# 初始化变量
sess.run(init_op)
```

# 2.3. 一次获取多个 op 运行的值

```
input1 = tf.constant(3.0)
input2 = tf.constant(2.0)
input3 = tf.constant(5.0)
intermed = tf.add(input2, input3)
mul = tf.mul(input1, intermed)

with tf.Session():
  # 合并执行两个 op
  result = sess.run([mul, intermed])
  print result

# 输出:
# [array([ 21.], dtype=float32), array([ 7.], dtype=float32)]
```

# 2.4. Feed 操作

上述示例在计算图中引入了 tensor, 以常量或变量的形式存储. TensorFlow 还提供了 feed 机制, 该机制 可以临时替代图中的任意操作中的 tensor 可以对图中任何操作提交补丁, 直接插入一个 tensor.

feed 使用一个 tensor 值临时替换一个操作的输出结果. 你可以提供 feed 数据作为 run() 调用的参数. feed 只在调用它的方法内有效, 方法结束, feed 就会消失. 最常见的用例是将某些特殊的操作指定为 "feed" 操作, 标记的方法是使用 tf.placeholder() 为这些操作创建占位符.  

```
input1 = tf.placeholder(tf.types.float32)
input2 = tf.placeholder(tf.types.float32)
output = tf.mul(input1, input2)

with tf.Session() as sess:
  print sess.run([output], feed_dict={input1:[7.], input2:[2.]})

# 输出:
# [array([ 14.], dtype=float32)]
```




