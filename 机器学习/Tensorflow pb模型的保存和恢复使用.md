# Tensorflow pb模型的保存和恢复使用

## ckpt 模型的含义

- .meta  

    保存计算图
    
- .index

    保存了所有的变量名
    
    
- .data-xxx-of-xxx

    保存了所有变量的取值

## 将 ckpt 文件转换为 pb 文件

```
# coding=utf-8

import tensorflow as tf
from tensorflow.python.tools import freeze_graph
from cnn_models import CnnModel_MNIST

train_times = 35000
base_path = "/Users/coorchice/Desktop/ML/model/cnn/mnist/" + str(train_times)

# 先重置环境中的计算图
tf.reset_default_graph()

# 重建网络
network = CnnModel_MNIST()
# 获得最后一步的输出
y_conv = network.y_conv
# 设置输出的类型和名称，后面调用pb时可以使用
y_conv = tf.cast(y_conv, tf.uint8, 'out')

# 获得储存对象
saver = tf.train.Saver()
# 在tensorflow 1.11 中，训练生成的文件名为 `-次数`，只需要写这个就好
model_path = base_path + "/-" + str(train_times)
with tf.Session() as sess:
    # 从路径恢复模型
    saver.restore(sess, model_path)
    # 先把图保存到指定路径和指定名称的 pd 中
    # 这个 pb 文件仅仅保存了模型的图信息
    tf.train.write_graph(sess.graph_def, 'output_model/pb_model', 'model.pb')
    # 把图和参数结构在一起
    # 参数含义：
    # 1、input_graph：（必选）模型文件，可以是二进制的pb文件，或文本的meta文件，用input_binary来指定区分（见下面说明）
    # 2、input_saver：（可选）Saver解析器。保存模型和权限时，Saver也可以自身序列化保存，以便在加载时应用合适的版本。主要用于版本不兼容时使用。可以为空，为空时用当前版本的Saver。
    # 3、input_binary：（可选）配合input_graph用，为true时，input_graph为二进制，为false时，input_graph为文件。默认False
    # 4、input_checkpoint：（必选）检查点数据文件。训练时，给Saver用于保存权重、偏置等变量值。这时用于模型恢复变量值。
    # 5、output_node_names：（必选）输出节点的名字，有多个时用逗号分开。用于指定输出节点，将没有在输出线上的其它节点剔除。就是上面的y_conv转换为了out。
    # 6、restore_op_name：（可选）从模型恢复节点的名字。升级版中已弃用。默认：save / restore_all
    # 7、filename_tensor_name：（可选）已弃用。默认：save / Const:0
    # 8、output_graph：（必选）用来保存整合后的模型输出文件。注意是一个新的文件，整了图和参数
    # 9、clear_devices：（可选），默认True。指定是否清除训练时节点指定的运算设备（如cpu、gpu、tpu。cpu是默认）
    # 10、initializer_nodes：（可选）默认空。权限加载后，可通过此参数来指定需要初始化的节点，用逗号分隔多个节点名字。
    # 11、variable_names_blacklist：（可先）默认空。变量黑名单，用于指定不用恢复值的变量，用逗号分隔多个变量名字。
    freeze_graph.freeze_graph('output_model/pb_model/model.pb', '', False, model_path, 'out', 'save/restore_all',
                              'save/Const:0', 'output_model/pb_model/mnist_model.pb', False, "")

# 这次把变量数据和图都保存进去了，取出来就可以直接进行计算


```

## 使用pb模型文件

```
# pb模型文件路径
model_path = 'output_model/pb_model/mnist_model.pb'
with tf.Graph().as_default():
    # 初始化一个 GraphDef 对象
    output_graph_def = tf.GraphDef()
    # 打开 pb 模型文件
    with open(model_path, "rb") as f:
        # 读取 pb 文件内容到 GraphDef 中
        output_graph_def.ParseFromString(f.read())
        # 导入图到环境中
        tf.import_graph_def(output_graph_def, name="")
    with tf.Session() as sess:
        # 通过节点名称，从图中获取到节点。
        # 在定义模型时最好填上 name 参数，不然就需要要计算图中去查默认名称了
        x_data = sess.graph.get_tensor_by_name('Placeholder:0')
        keep_prob = sess.graph.get_tensor_by_name('dropout/Placeholder:0')
        y_conv = sess.graph.get_tensor_by_name('output/Softmax:0')
        # 获取到节点后，就正常调用就行
        y = sess.run(y_conv, feed_dict={x_data: arr, keep_prob: 1})
        # 取出概率最大的一个结果
        # argmax 返回最大值所在索引
        result = str(np.argmax(y, 1))
        print("\n期待结果:" + str(num) + "，预测结果：" + result)
        os.system("open num" + result + ".png")
```

