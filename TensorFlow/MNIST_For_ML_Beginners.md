# MNIST For ML Beginners
*本篇教程是为了机器学习和TensorFlow的初学者。如果你已经了解什么是MNIST和softmax(multinomial logistic)回归，你应该去阅读这个[快速节奏教程]()。确保在开始任意一个教程之前已经安装了TensorFlow。*

当我们开始学习如何编程的时候，有一个传统，第一件事是打印“Hello World”。就像编程有Hello World，机器学习有MNIST。

MNIST是一个简单计算机视觉数据库。它由一些手写数字图片组成，如下：

![MNIST](Image/MNIST.png)

它也包含每一张图片对应的标签，告诉我们对应的数字是多少。例如，上面四张图片的标签分别是5，0，4和1。

在本教程中，我们将要训练一个模型去观察图片并预测它是什么数字。我们的目标不是训练一个很精致的模型能够实现最先进的性能--尽管我们后面会给你代码来实现--而是初步的了解TensorFlow。因此，我们开始一个非常简单的模型，叫做Softmax回归。

本教程的实际代码非常短，所有有趣的东西都发生在三行之中。然而，理解背后的思想非常重要：TensorFlow如何工作以及核心机器学习概念。正因如此，我们将要非常仔细的处理代码。

## About this tutorial
本教程将逐行解释mnist_softmax.py代码中发生的情况。

你可以用不同的方式来使用这个教程。包括：
+ 复制和粘贴每一个代码片段，一行一行的输入到Python环境中，读每一行的说明。
+ 通读说明之前或之后运行整个`mnist_softmax.py`python文件，使用教程来理解不清楚的代码行。

我们将在本教程中完成什么：
+ 学习MNIST数据以及softmax回归
+ 创建一个基于查看图片每一个像素来识别数字的模型的函数
+ 使用TensorFlwo让它看数千个样本来训练模型识别数字（并运行我们的第一个TensorFlow会话来实现）
+ 使用测试数据来检验模型的准确性

## The MNIST Data
MNIST数据托管在[Yann LeCun's website](http://yann.lecun.com/exdb/mnist/)。如果你正在从本教程中复制和粘贴代码，从下面两行开始，它们会自动下载和读取数据：
```py
from tensorflow.examples.tutorials.mnist import input_data
mnist=input_data.read_data_sets("MNIST_data/",one_hot=True)
```
MNIST数据被分割为三部分：55000个数据点的训练数据（`mnist.train`），10000数据点的测试数据（`mnist.test`）,以及5000个验证数据（`mnist.validation`）。这种分割非常重要：在机器学习中我们有分离开的数据不去学习，确保我们的学习实际上是概括的。

就像前面提到的，每一个MNIST数据点有两部分：一个手写数字图片和对应的标签。我们称图片“x”标签“y”。训练数据集和测试数据集都包含图片和对应的标签；例如训练的图片为`mnist.train.images`训练的标签为`mnist.train.labels`。

每一张图片都是28像素乘以28像素。我们可以用一个大的数字数组来解释：

![MNIST-Matrix](Image/MNIST-Matrix.png)

