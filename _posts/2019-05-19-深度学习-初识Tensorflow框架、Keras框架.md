---
layout:     post                    # 使用的布局（不需要改）
title:      深度学习-初识Tensorflow框架、Keras框架               # 标题 
subtitle:    #副标题
date:       2019-05-19              # 时间
author:     BY Tony Huang                     # 作者
header-img: img/post-bg-os-metro.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 深度学习
    - TensorFlow
    - Keras
---
 这篇文章主要记述了博主初识深度学习的概念、安装Tensorflow,，Keras的过程和其中遇到的问题，并以MINIST（基于Tensorflow）和CNN图片分类（基于Keras）讲解学习两种框架的使用，最后对比一下两种框架以便在后期的学习更好的选择。

[TOC]

# 环境说明

## 环境：

 windows10、python3.7
 编译器：Spyder

## 其他：

 Annaconda：开源的包、环境管理器，里面集成了Python的库，如numpy、matplot等，博主之前装了Python3.7，又下载了Annaconda：在其中配置了Python3.6环境，很是方便，直接去官网Download自己需要的版本就好：[Annaconda官网](https://www.anaconda.com/)

# 前起准备

## 安装Tensorflow、Keras

在Annaconda->Annaconda Prompt中输入

```
pip install tensorflow
```

```
pip install keras
```

> ps:在安装的过程中可能会出现让你安装其他各种包的情况，如果在Prompt下载太慢，则可以复制下载链接到迅雷上下载，再回到Prompt中`pip install 包的名称`（注意路径），就可以很快完成

这篇文章中所使用的Keras版本为2.2.4，backend是Tensorflow

```
import keras
print(keras.__version__)
```

Tensorflow版本为1.13.1

```
import tensorflow as tf
print(tf.__version__)
```

## 小谈Keras

### TensorFlow

​        Google的TensorFlow开源深度学习框架可以理解为是一个“数学函数”集合和AI训练学习的执行框架，在世界深度学习框架范围内拥有最高的知名度，通过它我们只需要少量的代码就可以很好地完成AI模型的建立和训练，而不需要很强的编程功底，对科学研究人员是一大福利，我想这也许也是Python迅速崛起的原因，毕竟大多数人没有那么多时间去学习复杂的C++。

有关TensorFlow的内容欢迎访问[TensorFlow中文社区](http://www.tensorfly.cn/)

![TensorFlow](G:\个人创作\CSDN\TensorFlow_logo.jpg)

### Keras

#### 概念

​      Keras是一个高级神经网络API，用Python编写，支持python2.7-3.6，能够在Tensorflow,CNTK或Theano之上运行，它的开发重点是实现快速研究（简单来说就是比Tensorflow使用方便，框架更灵巧简约，运算速度更快）

#### Keras设计原则

- **用户友好**：Keras是专为人类设计的API，以用户体验为中心。Keras遵循减少认知并进行高效研究：它提供一致且简单的API，最大限度减少用户所需操作的东西，并且可以根据用户错误提供清晰且可操作的反馈。
- **模块化**：模型被理解为独立的，完全可配置的模块的序列或图形，可以通过尽可能少的限制将其插入到一起。特别是，神经层，成本函数，优化器，初始化方案，激活函数和正则化方案都是独立的模块，您可以将它们组合在一起以创建新模型。
- **易扩展性**:新模块易于添加（作为新类和函数），现有模块提供了充足的示例。为了能够轻松创建新模块，可以实现全面的表现力，使Keras适合高级研究。
- **使用Python**:没有声明格式的单独模型配置文件。模型在Python代码中描述，它紧凑，易于调试，并且易于扩展。

# 实例工作

## MINIST-手写数字识别

这个实例在深度学习中的地位≈“hello word”在C语言的地位，这几乎是初识Tensorflow的学生都会学习的一个教程。

### 简单说明MINIST

MINIST是由6万张训练图片和1万张测试图片构成的，每张图片都是28*28大小（单位：像素），而且都是黑白色构成，（这里的黑色是一个0-1的浮点数，黑色越深表示数值越靠近1），这些图片是采集的不同的人手写从0到9的数字。TensorFlow将这个数据集和相关操作封装到了库中。
![MINIST下载解压后文件](https://img-blog.csdnimg.cn/20190519181344787.jpg)
这些图片并不是传统意义上的png或者jpg格式的图片，因为png或者jpg的图片格式，会带有很多干扰信息（如：数据块，图片头，图片尾，长度等等），这些图片会被处理成很简易的二维数组，如图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190519181441127.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjAzNjYxNw==,size_16,color_FFFFFF,t_70)
可以看到，矩阵中有值的地方构成的图形，跟左边的图形很相似。之所以这样做，是为了让模型更简单清晰。特征更明显。

### 模型代码：

```
​```
import tensorflow as tf
mnist = input_data.read_data_sets('MNIST_data', one_hot=True)
x = tf.placeholder(tf.float32, [None, 784])
# w表示每一个特征值（像素点）会影响结果的权重
W = tf.Variable(tf.zeros([784, 10]))
b = tf.Variable(tf.zeros([10]))
y = tf.matmul(x, W) + b
# 是图片实际对应的值
y_=tf.placeholder(tf.float32, [None, 10])
cross_entropy = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(labels=y_, logits=y))
train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)
sess = tf.InteractiveSession()
tf.global_variables_initializer().run()
  # mnist.train 训练数据
for _ in range(1000):
  batch_xs, batch_ys = mnist.train.next_batch(100)
  sess.run(train_step, feed_dict={x: batch_xs, y_: batch_ys})
 
  #取得y得最大概率对应的数组索引来和y_的数组索引对比，如果索引相同，则表示预测正确
correct_prediction = tf.equal(tf.arg_max(y, 1), tf.arg_max(y_, 1))
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
 
print(sess.run(accuracy, feed_dict={x: mnist.test.images,y_: mnist.test.labels}))
​```
```

下面讲解几个概念单词：

#### 占位符

```
x = tf.placeholder(tf.float32, [None, 784])
```

这里的x就是一个占位符，是一个28*28的浮点数2维矩阵（2维张量），分配给它的`shape`为`[None, 784]`，其中`784`是一张展平的MNIST图片的维度（就是把2维矩阵（2维张量）转为为1维数组（1维张量）），`None`表示其值大小不定。

```
y_=tf.placeholder(tf.float32, [None, 10])
```

y_也是2维张量，其中每一行为一个10个数的一维向量,用于代表对应某一MNIST图片的类别，例如如果是真实数字是7的话，一行的结果为[0 0 0 0 0 0 1 0 0 0]。

#### 变量

```
W = tf.Variable(tf.zeros([784, 10]))
b = tf.Variable(tf.zeros([10]))
```

在TensorFlow中，一个变量代表着TensorFlow计算图中的一个值，能够在计算过程中使用，甚至进行修改，模型参数一般用Variable表示，我们在调用`tf.Variable`的时候传入初始值。在这个例子里，我们把`W`和`b`都初始化为零向量。`W`是一个784x10的矩阵（因为我们有784个特征和10个输出值）。`b`是一个10维的向量（因为我们有10个分类）。

#### 类别预测与损失函数

```
y = tf.matmul(x, W) + b
```

单个样本被预测出来是哪个数字的概率

```
cross_entropy = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(labels=y_, logits=y))
```

可以很容易的为训练过程指定最小化误差用的损失函数，我们的损失函数是目标类别和预测类别之间的交叉熵。

```
train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)
```

梯度下降算法

#### 训练模型

```
for _ in range(1000):
  batch_xs, batch_ys = mnist.train.next_batch(100)
  sess.run(train_step, feed_dict={x: batch_xs, y_: batch_ys})
```

mnist.train.next_batch(1000)是从训练集里一次提取100张图片数据来训练，然后循环1000次，以达到训练的目的。

#### 评估模型

首先让我们找出那些预测正确的标签。`tf.argmax` 是一个非常有用的函数，它能给出某个tensor对象在某一维上的其数据最大值所在的索引值。由于标签向量是由0,1组成，因此最大值1所在的索引位置就是类别标签，比如`tf.argmax(y,1)`返回的是模型对于任一输入x预测到的标签值，而 `tf.argmax(y_,1)` 代表正确的标签，我们可以用 `tf.equal` 来检测我们的预测是否真实标签匹配(索引位置一样表示匹配)。

```python
correct_prediction = tf.equal(tf.arg_max(y, 1), tf.arg_max(y_, 1))
```

这里返回一个布尔数组。为了计算我们分类的准确率，我们将布尔值转换为浮点数来代表对、错，然后取平均值。例如：`[True, False, True, True]`变为`[1,0,1,1]`，计算出平均值为`0.75`。

```python
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
```

最后，我们可以计算出在测试数据上的准确率，大概是91%。

```python
print(sess.run(accuracy, feed_dict={x: mnist.test.images,y_: mnist.test.labels}))
```

好，在下一节会继续介绍更好的内容，好了良好的好好好好

参考链接：

人人都可以做深度学习应用：入门篇（https://zhuanlan.zhihu.com/p/25482889）

Tensorflow之MNIST解析(https://www.cnblogs.com/lizheng114/p/7439556.html)

TensorFlow中文社区（http://www.tensorfly.cn/）
