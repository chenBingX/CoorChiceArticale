# PAI Tensorflow介绍

- TensorFlow服务化 PAI直接提供TensorFlow计算框架。用户无需自行下载或者在自己机器上安装TensorFlow计算框架。我们提供和开源版本一致的API，**你可以直接通过我们提供的接口将你的TensorFlow Training Script提交到MaxCompute计算集群上执行。**  

- 数据直读。用户能够通过TensorFlow直接读取其存储在ODPS上的训练数据，而无需下载到本地。我们的训练数据一般都需要进行预处理。通常这些数据预处理作业都是在CPU集群上执行。通过MaxCompute提交的Tensorflow作业能够直接读取其存储在CPU集群上的训练数据，而无需手动下载到本地。我们支持读取存储在MaxCompute Table的结构化数据和存储在MaxCompute Volume上的非结构化数据。此外，我们还支持直接读取OSS上的数据。


