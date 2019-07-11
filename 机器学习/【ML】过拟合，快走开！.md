![image](http://upload-images.jianshu.io/upload_images/1869462-0e2e87d522d3efca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在机器学习的过程中，当参数数量比较多的时候，很可能就会出现过拟合的现象，使得模型在训练集中拟合效果很好，但在测试集上拟合效果就让人不那么愉快了。  


发生这种情况，就意味着训练出来的模型泛化性比较差，无法适应复杂的现实情况。那我们肯定就需要想办法防止或者减弱过拟合的发生。本篇 CoorChice 将会介绍几种防止过拟合的方法。  

# 正则化

在进行模型训练的的时候，最重要的一步就是通过不断减小损失函数的损失值来逼近全局最优解，简单的说就是让我们的特征权值能够正确的描述出真实情况。

可是往往训练出来的权值可能会有很多的噪声，即有很多无用的特征权值，在没有类似噪声的数据中很可能就会误判。也可能某些特征的权值特别大，导致只要出现一部分大的特征，模型就会忽略掉其大特征权值较小的有用权值，那这样的模型也是很容易造成误判。  

如何解决这种困境呢？答案可以是正则化，或者说规则化。正则化是代数几何中的一个概念。在机器学习中，加入正则化项表示加入了一个先验规则项。大白话来说就是，我们对结果是有一定预期的，不能完全随意的想咋搞咋搞。  

![image](http://upload-images.jianshu.io/upload_images/1869462-1cee935536006b13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通常会使用 **范数** 来作为损失函数的规则化项。  

![image](http://upload-images.jianshu.io/upload_images/1869462-99c08d198f7a49d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这就是范数公式，依据 p 的取值，可以产生 **0范数**、**1范数** 和 **2范数**等，即 p 取 0、1、2。不同的范数所应对场景也有所不同。



## 0范数、1范数

### 0范数
上述范数公式，当 p 取 0 时，就称为 0 范数，Lp 公式就变为如下形式：  

![image](http://upload-images.jianshu.io/upload_images/1869462-145d774f52d83973.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到，0 范数中，涉及到求解一个数的 0 次方根，这是很怪异的一个公式，很难说清它的意义是什么，以及应该如何求解。  

所以在0范数有一个优化的形式，使其具有一个明确的意义：

![image](http://upload-images.jianshu.io/upload_images/1869462-45743f211205f098.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


该公式的意义是，求解 w 向量空间中的非 0 元素个数。

它为什么能够防止过拟合呢？把它加入到损失函数中有如下式子：


![image](http://upload-images.jianshu.io/upload_images/1869462-be62cfa37e90b470.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  

> 此处插播一条说明，上面的 Loss 函数式由两个部分组成，c0部分就是传统的损失函数，称为 “经验风险”；后面的正则项称为 “结构风险”，它有点从结构上约束前半部分 “经验风险” 的意思。

可以看看当 p 趋近于 0 时范数图像： 
 
![image](http://upload-images.jianshu.io/upload_images/1869462-be45e6b1dbf8d164.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不难看出这样的趋势，当 p 与接近于 0 时，它的结果就越贴近于各个坐标轴。那么可以想象出，当 p 为 0 的时候，结果就都落在坐标轴上了。当损失部分的函数与其相切时，自然之可能切到坐标轴上，即只有一个坐标有值，其它全为 0。

这就意味这，在进行梯度下降不断减小 Loss 的过程中，需要考虑将更多权值 w 置为 0 ，这样才能使 0 范数项不断的减小。那么它的意义就浮现出来了，就是尽可能的将不重要的权值直接置 0，减少参数数量，尽量只保留有用的特征。即它使权重集具有了稀疏性（包含 0），起到了筛选特征的作用。  

![image](http://upload-images.jianshu.io/upload_images/1869462-005150367edfb0d1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看起来很棒吧，能自动的筛选特征！可是在对于特征权值 w 数量庞大的机器学习中，要动态的去学习到如何将那些 w 设置为 0 既能保证 Loss 不断的减小，又能保证 0 范数项也在不断的减小，到底应该让那些权值变小，而那些权值直接为 0 呢，可能性就太多了，多到几乎不可能搜索出一条最优的解路径来。这样的有解，但几乎很难求解出来的问题被称为 **NP-hard** 问题，意思就是虽然可以检查每次计算结果的正确性，但计算结果众多，以至于无法进行一一检查，而是这个问题相当于无法解的问题。  

### 1范数

在数学上，一般无法直接求解的问题就会使用近似的算法去不断的逼近真实的答案，得出一个近似的解，也是可以接受的。  

那 **1范数** 就是具有 **0范数** 优势，而又易于求解的一个近似算法。

同理，1范数就是当 p 等于 1 时的范数式：  

![image](http://upload-images.jianshu.io/upload_images/1869462-4d29c01248311619.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1范数的意思就很明了了，就是所有权值 w 的绝对值的和。加入1范数的 Loss 函数式如下：


![image](http://upload-images.jianshu.io/upload_images/1869462-52a972168930c5f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从 Loss 可以知道，为了使整个式子的值最小，需要 1范数 项的值也最小，那看看 1范数额图像：  

![image](http://upload-images.jianshu.io/upload_images/1869462-b1d79897f6bd7912.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图是三维空间中1范数的图像，是一个八面体，存在与坐标轴相交的顶点。推知，在更高维度的空间中，必然也存在很多这样与坐标轴相交的顶点。就是说，在梯度下降的求解过程中，也是能找到损失函数与这些个顶点相切的解的，那么 1范数 也有机会和 0范数 一样，能够筛选出有用的特征，而剔除噪声，从而提高模型的泛化能力。  

在计算过程中，从上面的图像可以看出，1范数存在不可导的点，也就是它的顶点们，这在计算上是不那么方便的。  

## 2范数


### 避免过拟合

既然1范数计算也不方便，那我们是不是可以再升级升级，看看更高的范数是不是可以计算方便一些呢？  

看看 p 取 2 的范数式：  


![image](http://upload-images.jianshu.io/upload_images/1869462-64207999c52b4676.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再看看图像是什么样的：

![image](http://upload-images.jianshu.io/upload_images/1869462-260fa32284a539f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

三维图像是个球！这意味着2范数在空间中是连续的且处处可导的函数。当然，它和坐标轴也是由交点的，这意味着和损失函数的切点也是有可能落在坐标轴上的，只不过从图像也能看出来，这样的概率比1范数更小罢了。  

加入2范数的 Loss 函数是这样的：

![image](http://upload-images.jianshu.io/upload_images/1869462-9d4c9f4102dff4d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也就是求 w 的平方和。这里把开根去掉了，其实没什么影响的，效果差不多但计算量却小了很多。  

2范数与损失函数的切点既然很难落到坐标轴上，那就是说它很难对特征进行筛选，很难削减无用的噪声特征，那它凭什么也能避免模型过拟合呢？  

CoorChice 会慢慢道来。  

我们考虑这样一个例子：用3个特征来识别小狗🐶，简单来说，就有3个特征权值，即 [w1,w2,w3]。 

```
w1 = 1，w2 = 1，w3 = 1 -> w1+w2+w3 = 3
w1 = 3，w2 = 0，w3 = 0 -> w1+w2+w3 = 3
```

以上是 w 取值的两种情况。虽然 w 的和都是 3，但在识别的过程中，第二种情况中的 w2、w3 就完全的被巨大的 `w1=3` 给盖过了。假设 w1 表示 4 条腿的特征，那么这个模型但凡遇到 4 条腿的东西就认为它是小狗了，显然是不科学的。这就是说，模型在训练集中准确率很高，但在复杂的实际情况中很容易误判。  

如果采用2范数，第一种情况的平方和是 3，而第二种情况的平方和就是 9 了！那么要让 Loss 不断减小，就需要不断的削弱 w1 这种强特征的权重，使得一些有用的弱特征也能过累加起来颠覆强特征，让模型不至于一遇到一个极强的特征，就忽略了众多较弱的特征，而造成误判。  

2范数对权重的削弱平均特性，可以使得模型不依赖于某一部分强特征，而是更加全面的去考虑综合的特征。这样模型的泛化能力自然就提高了，也就不容易出现过拟合的情况了。

### 避免梯度爆炸💥

在 [ 《【Get】用深度学习识别手写数字》 ](https://www.jianshu.com/p/ab68c7ba964b) 一文中，CoorChice 就提到了一开始训练时遇到的瓶颈，因为梯度发生了 **bong! bong! bong!** 的爆炸。直接就导致了训练完全没办法进行下去。但是在 CoorChice 加入正则化项之后，就成功了越过了梯度爆炸的雷区，顺利完成了训练。  

原因就在于，训练过程中，某些极强权值的大小太过夸张，导致连乘过程中数值巨大无比，就爆炸💥了了。2范数正则项刚好作用就是削弱这种极强的权值，所以在一定程度上，它能够有效的减少梯度爆炸发生的可能性。  

## 如何在 TensorFlow 中加入正则化项？

1.将所有权重 w 记录下来

```
tf.add_to_collection(tf.GraphKeys.WEIGHTS, w)
```
每创建一个权值就调用这个函数把权值加入到池中。  

2.构建正则化项，加入到 Loss 中

```
# 构建 l2 正则化项，设置其（缩放度）重要程度为 5.0 / 50000
regularization = tf.contrib.layers.l2_regularizer(scale=(5.0 / 50000))  

 # 让正则化生效
reg_term = tf.contrib.layers.apply_regularization(regularization)

# 在损失函数中加入正则化项
loss = (-tf.reduce_sum(y_data * tf.log(y_conv)) + reg_term)
```


# Dropout


Dropout 是一种被经常用到深度学习中，用于减弱过拟合的一种技术。它的核心原理就是，在训练过程中，让一层中的神经元按照指定的比率一部分失效，一部分仍然有效，具体是哪些个神经元会被失效或者有效，都是随机的。  

失效的神经元在一次迭代中就不会被更新。而在下一轮的训练中，又会有另外一部分神经元会被失效，不会被更新。  

这样做就相当于每次训练都减少了神经元的数量，同时由于每次可能抽取到的神经元不一样，就使得学习路径变的具有随机性。不同于没有 Dropout 的网络那样，每次只能全量的更新所有神经元，加入 Dropout 的神经网络中，神经元之间的共适应性会被削弱，在不断减小的梯度的要求下，就能够迫使网络学习出更加具有鲁棒性的特征。这样模型的适应性会更强，而不是只能适应一个套路。  

![image](http://upload-images.jianshu.io/upload_images/1869462-6c525022ca381223.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解释了一大堆，可能还是很抽象，这时不如来一张图吧！

![image](http://upload-images.jianshu.io/upload_images/1869462-a56eaba8e69a6168.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

是不是感觉清晰了很多。左边是没有 Dropout 的网络，神经元之间都是全量链接的。右边是有 Dropout 的网络，可以看到在一次训练中，有一部分神经元是断开链接的，即失效了。每次断开链接的神经元都是随机了，这就给网络增加了很多可能，就像是具有了变异的能力。  

通常，Dropout 需要设置一个超参数，指定每次需要有多少神经元失效。这个值一般回取 **0.5**，因为这样能够组合出多种类的情况来。  

在著名的 VGG-16 网络模型中，最后3层的全链接层中，都加入 Dropout。  

当然，使用 Dropout 的弊端也是因为随机性带来，随机很大概率上会导致训练次数的增加，使得训练时间变长。  

![image](http://upload-images.jianshu.io/upload_images/1869462-ce53bc55ae4c930f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 如何在 TensorFlow 中加入 Dropout？

```
tf.nn.dropout(input, keep_prob)
```

很简单，就是传入前面的输出，比如一个全链接层的输出，然后在第二个参数指定需要保留的神经元的比率。  

需要注意的是，Dropout 在目前版本的 **TensorFlow Lite** 中还没有被支持，所以考虑在 **TensorFlow Lite** 上跑模型的同学就不能使用了哦！😳

# 总结

本篇 CoorChice 介绍了 2 种减少过拟合发生的可能性的方法，**正则化** 和 **Dropout**。

- L0 和 L1 正则化能够具有稀疏能力，减少参数数量，提取出主要特征。但 L0 在复杂参数集中难以求解，所以如果有需求时，通常选择 L1 正则化代替。  

- L2 正则化能够削弱强特征，使能够考虑更加全面的特征。同时，L2 正则化还能一定程度上防止梯度爆炸💥的发生。


- Dropout 是一种比较具有通用性的防止过拟合的手段，但会增加训练时间，而且目前 TensorFlow Lite 对该操作还不支持。    



---

> - 嘿，随手点个赞吧 ❤️！ Lok'Tar 🛡  
 ![image](http://upload-images.jianshu.io/upload_images/1869462-3dede5759dbd7670.gif?imageMogr2/auto-orient/strip)  
> - CoorChice 会不定期的分享干货，快进入 [CoorChice的【个人主页】](https://juejin.im/user/57fc43b67db2a200595ffd94) 加关注吧。

