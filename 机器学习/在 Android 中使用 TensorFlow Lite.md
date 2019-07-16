# 在 Android 中使用 TensorFlow Lite

`tensorflow-android` 即 tensorflow mobile 在 19 年就会淘汰了。需要使用 17 年发布的 `TensorFlow Lite` 来替代。 

## .pb 转 .tflite

```
# 找到转换工具
from tensorflow.contrib.lite.python.lite import TocoConverter as TFLiteConverter

train_times = 30000

graph_def_file = 'output_model/_' + str(train_times) + "/mn_1.pb"
# 直接指定好输入节点和输出节点
# 使用时必须按照此时转换的顺序设置输入和输出
input_arrays = ["Placeholder"]
output_arrays = ["output/Softmax"]
# 冻结模型结构
converter = TFLiteConverter.from_frozen_graph(graph_def_file, input_arrays, output_arrays)
# 优化压缩，可压缩达 75% 左右
converter.post_training_quantize = True
# 转换为 tflite
tflite_model = converter.convert()
# 保存模型到指定路径
open('output_model/_' + str(train_times) + "/mn_1tf.tflite", "wb").write(tflite_model)
```

## 配置 AndroidStudio

一. 添加 `tensorflow-lite` 的依赖  


```
dependencies {
    implementation 'org.tensorflow:tensorflow-lite:+'
}
```



二. 禁止优化 `tflite`，不然模型就损坏了

```
android{
    aaptOptions {
        noCompress "tflite"
        noCompress "lite"
    }
    
    // 排除不需要的指令集
    packagingOptions {
        exclude '**/arm64-v8a/*.so'
        exclude '**/x86/*.so'
        exclude '**/x86_64/*.so'
    }
}
```

## 加载模型使用模型

```
// 从 文件中加载 .tflite 模型
val modelFilePath = cacheDir.path + "/" + mn_1tf
Utils.copyAssetResource2File(this, mn_1tf, modelFilePath)
// 支持从 File、ByteBuffer等直接加载模型
// 如果模型放到了 assets/ 下，需要转换一下
var interpreter = Interpreter(File(modelFilePath))
val sb = StringBuilder()
for (i in 0 until codeBmps?.size!!) {
    // 建立输入、输出，需要 shape 需要和模型完全一致
    val x_data = Utils.getTFLiteInts(codeBmps?.get(i))
    val outputs = Array(1) { FloatArray(10) }
    // 按照之前的定义的输入和输出顺序，
    // 依次放入变量，run模型
    interpreter.run(x_data, outputs)
    // run 完后，输出容器中就有数据了
    val r = Utils.argmax(outputs[0])
    sb.append(r)
}
tv_result.setText("识别结果：$sb")
```

`runForMultipleInputsOutputs(Object[] inputs, @NonNull Map<Integer, Object> outputs)` 支持多个输入和多个输出，当然，在转换模型的时候需要注意顺序。

## 注意

使用 `converter.post_training_quantize = True`  优化压缩模型后，需要使用以下版本的 tflite

```
// 压缩后需要使用 0.0.0-nightly 版本的 tflite，否则模型数据类型对应不上
implementation 'org.tensorflow:tensorflow-lite:0.0.0-nightly'
```

