# TensorBoard 

TensorFlow 为我们提供了一个强大的网络可视化工具，可以清晰的看到所构建出来的网络。  

# 名称范围和节点

通常，TensorFlow 的图可能有数千上万个节点，这在图上看起来可能会很糟糕。于是，tensorboard 提供了范围限定，可以把一组节点变量圈到一个范围内。  

```
# -----------------------构建第一层卷积-----------------------
with tf.name_scope('hidden1'):
    W_conv1 = weight_variable([5, 5, 1, 32])
    b_conv1 = bias_variable([32])
    x_image = tf.reshape(self.x_data, [-1, 28, 28, 1])
    h_conv1 = tf.nn.relu(conv2d(x_image, W_conv1) + b_conv1)
    h_pool1 = max_pool_2x2(h_conv1)
```

如上，在 cnn 中，我们可以按照网络层级来圈定范围。  

通过 tf.name_scope(name) 的多层嵌套，我们可以组织层级关系。

![tensorboard_image2](http://ogemdlrap.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-10-11%20%E4%B8%8B%E5%8D%884.26.38.png)

上图中，CoorChice 给网络圈了 4 个范围出来：  

- hidden1
- hidden2
- FC1
- output

双击可以展示出范围内的详细情况，包括节点、变量。


# 计算图中的符号
![tensorboard_pan_image1](http://ogemdlrap.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-10-11%20%E4%B8%8B%E5%8D%883.38.06.png)


# 统计运行时信息

通过追踪训练过程，我们可以统计到训练过程的信息，而不仅仅是一张网络图。  

实际上，思路就只需要在每次训练、检查的时候保存一下此时的信息就行。  

- **tf.summary.scalar(name, tensor)**

    这个函数可以记录一个 tensor 的信息，顺便再给它取一个好记的名字。  

- **tf.summary.FileWriter(logdir, graph)**

    这个函数可以创建一个用于收集 summary 的 FileWriter 对象，我们不断的在训练过程中捕捉到 summary。  
    
- **FileWriter.add_summary(summary, step)**

    这个函数用于添加一个 summary，并且标记它当前的步数。

- **tf.summary.merge_all()** 
    
    这个函数可以合并此前的所有 summary op。
    
下面看看例子：  

```
# 合并之前的 summary op
merged = tf.summary.merge_all()
# 创建用于收集 summary 的FileWriter
train_writer = tf.summary.FileWriter(save_path + "graph/train", sess.graph)
test_writer = tf.summary.FileWriter(save_path + 'graph/test')

# 初始化变量
sess.run(tf.initialize_all_variables())

for i in range(train_times):
    batch = mnist.train.next_batch(50)
    if i % 100 == 0:
        # 抓取准确率
        summary, train_accuracy = sess.run([merged, accuracy],
                                           feed_dict={x_data: batch[0], y_data: batch[1], keep_prob: 1.0})
        # 添加到 test 分类中
        test_writer.add_summary(summary, i)
    # 抓取训练信息
    summary, _ = sess.run([merged, train_step], feed_dict={x_data: batch[0], y_data: batch[1], keep_prob: 0.5})
    train_writer.add_summary(summary, i)
# 用完关闭
train_writer.close()

# 检查在测试集上的准确率
summary, test_accuracy = sess.run([merged, accuracy],
                                  feed_dict={x_data: mnist.test.images, y_data: mnist.test.labels, keep_prob: 1.0})
test_writer.add_summary(summary)
print("测试准确率 %g" % test_accuracy)
test_writer.close()
```

启动 tensorboard 后，我们可以直观的看到 loss 和 accuracy 的变化


![tensorboard_image_3](http://ogemdlrap.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-10-11%20%E4%B8%8B%E5%8D%883.38.06.png)


# 启动可视化图

在图保存好之后，就可以通过运行以下命令启动 

```
tensorboard --logdir="你保存的图的路径" --port=8080
```
后面的 `--port=8080` 是用来指定端口号的，如果不加，默认端口号为 `6006`。

运行后可以看到如下输出：

```
TensorBoard 1.8.0 at http://*****.local:2001 (Press CTRL+C to quit)
```
一般来说，通过输出中给出的链接就能打开页面了，但是CoorChice 在 mac 就始终不能够打开，不用怕，还可以通过这个链接打开：

```
http://127.0.0.1:2001
```
启动后就可以看到以下这个酷炫的界面了：

![tensorboard_pan](http://ogemdlrap.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-10-11%20%E4%B8%8B%E5%8D%883.50.38.png)





