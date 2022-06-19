---
title: 基于Tensorflow实现口罩识别
date: 2022-06-01 15:00:00 +0800
categories: [Tensorflow]
tags: [ML]
pin: true
author: Sebastian Lee

toc: true
comments: true
typora-root-url: ../../SebsNoteBook.github.io
math: false
mermaid: true



---

github仓库地址：

# 1.   数据预处理

## 1.1  格式化图片 

将rgb格式图片加载为PIL格式，再转换为Numpy数组。

然后使用preprocess_input 函数用于加速处理图像，对每个通道减去一定的均值

格式化数组为float32格式

使用keras.preprocessing.image模块中的图片生成器imagedatagenerator在batch中对数据进行增强，设定旋转范围为20、缩放范围为0.15、水平平移范围为0.2、垂直平移范围为0.2、透视变换的范围为0.15、水平翻转、填充模式为nearest，用于扩充数据集大小，增强模型的泛化能力，进行旋转，变形，归一化

## 1.2  格式化标签

将非数字化的标签使用sklearn下的LabelBinarizer()函数对其进行转换并获得二进制标签，然后使用to_categorical转化成独热码标签

## 1.3  划分训练集测试集

使用train_test_split函数划分训练集和测试集按照8：2划分



 

# 2.   神经网络的设计

## 2.1  神经网络的配置

卷积神经网络主要由以下几类层构成：

### 2.1.1     输入层

headModel = AveragePooling2D(pool_size=(7, 7))(headModel)

tf.layers.AveragePooling2D是针对2D输入的平均池层(如图像)

### 2.1.2     卷积层

headModel = Flatten(name="flatten")(headModel)

### 2.1.3     ReLU层

headModel = Dense(128,activation="relu")(headModel)

添加具有128个神经元的Dense隐藏层，使用relu激活函数，每个dense层管理自己的权重矩阵，其中包含神经元及其输入之间的所有连接权重。他还管理偏置项的一个向量（每个神经元一个）

### 2.1.4     池化层

headModel = Dropout(0.5)(headModel)

大规模的神经网络有两个缺点：费时和容易过拟合

值得一提的是 我们需要加入dropout层 在深度学习网络的训练过程中，对于神经网络单元，按照一定的概率将其暂时从网络中丢弃。对于随机梯度下降来说，由于是随机丢弃，故而每一个mini-batch都在训练不同的网络。

Dropout的出现很好的可以解决这个问题，每次做完dropout，相当于从原始的网络中找到一个更瘦的网络 是防止过拟合提高效果的一个大杀器

经过交叉验证，dropout率等于0.5的时候效果最好，原因是0.5的时候dropout随机生成的网络结构最多。

### 2.1.5     全连接层

headModel = Dense(2,activation="softmax")(headModel)添加一个包含两个神经元的Dense输出层，使用softmax激活函数

构建将放置在基础模型顶部的模型头部

## 2.2  后续处理

将头部FC模型置于基础模型之上（这将成为我们将训练的实际模型）

在基础模型中的所有层上循环并冻结它们，以便它们在第一次训练过程中不会更新

ADAM：为了加速机器的学习速度和效果**，**我们还使用了Adam优化器，结合AdaGrad和RMSProp两种优化算法的优点。对梯度的一阶矩估计（即梯度的均值）和二阶矩估计（即梯度的未中心化的方差）进行综合考虑，计算出更新步长。这是一个需要资源更少、令模型收敛更快的最优化算法Adam 优化算法是随机梯度下降算法的扩展式

## 2.3  MobileNetv2的原理

MobileNetv2架构是基于倒置残差结构(inverted residual structure)，原本的残差结构的主分支是有三个卷积，两个逐点卷积通道数较多，而倒置的残差结构刚好相反，中间的卷积通道数(依旧使用深度分离卷积结构)较多，旁边的较小。此外，发现去除主分支中的非线性变换是有效的，这可以保持模型表现力。

调整深度神经网络在精度和性能之间取得一个最佳平衡，方向可分为：

l 改进先进模型达到更加性能；

l 超参数调优以及各种网络修剪方法和连通性学习；

l 改变内容卷积块的联通结构，例如ShuffleNet引入稀疏性等。

追求的是找到关于神经网络操作使用的发展方向，这可以更简易的指导网络设计。网络设计基于MobileNetV1的，保留了该模型的简单性，显著的提高了其准确性，达到了针对移动应用的分类和检测应用的先进水平。

### 2.3.1     深度分离卷积(Depthwise Separable Convolutions)

将标准卷积拆分为深度分离卷积的基本思想：将标准卷积拆分为两个分卷积：第一层称为深度卷积(depthwise convolution)，对每个输入通道应用单通道的轻量级滤波器；第二层称为逐点卷积(pointwise convolution)，负责计算输入通道的线性组合构建新的特征。

 

![img](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image002.jpg)



 MobileNetv2中使用的卷积核大小k = 3，与标准卷积相比计算量减少了8~9倍，精度上有略微的损失。

### 2.3.2     Linear Bottlenecks

我们认为深度神经网络是由n个![img](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png)层构成，每层经过激活输出的张量为![img](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image006.png)。我们认为一连串的卷积和激活层形成一个兴趣流形(manifold of interest，这就是我们感兴趣的数据内容)，现阶段还无法定量的描述这种流行，这里以经验为主的研究这些流行性质。

长期以来我们认为：在神经网络中兴趣流行可以嵌入到低维子空间，通俗点说，我们查看的卷积层中所有单个d通道像素时，这些值中存在多种编码信息，兴趣流行位于其中的。我们可以通过变换，进一步嵌入到下一个低维子空间中(例如通过1×1卷积变换维数，转换兴趣流行所在空间维度)。

乍一看，这样的想法比较容易验证，可通过减少层维度从而降低激活空间的维度。MobileNetv1是通过宽度因子(width factor)在计算量和精度之间取折中。用上面的理论来说，宽度因子控制激活空间的维度，直到兴趣流行横跨整个空间。

然而，由于深度卷积神经网络的层是具有非线性激活函数的。以ReLU变换(F(x)=max(0,x))为例，会存在以下问题：

如果当前激活空间内兴趣流行完整度较高，经过ReLU，可能会让激活空间坍塌，不可避免的会丢失信息。(如下Figure 1的示意图)

如果经过ReLU变换输出是非零的，那输入和输出之间是做了一个线性变换的，即将输入空间中的一部分映射到全维输出，换句话来说，ReLU的作用是线性分类器。

下图展现了兴趣流行的变换示例:

#### ![在这里插入图片描述](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image008.png)

Input是一张2维数据，其中兴趣流行是蓝色的螺纹线；本例使用矩阵T TT将数据嵌入到n nn维空间中，后接ReLU，再使用![img](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image010.png)将其投影回2D平面。

可以看到设置n = 2, 3时信息丢失严重，中心点坍塌掉了。当n = 15..30之间，恢复的信息明显多了。

如果兴趣流行经过ReLU变换后得到非零的结果，这时ReLU对应着是一个线性变换

只有当输入流行可包含在输入空间的低维子空间中，ReLU才能完整的保持输入流行的信息。

存在的问题： 我们想要兴趣流行存在低维空间中，即想要提升效果，维度是要低一点。但是维度如果低的话，激活变换ReLU函数可能会滤除很多有用信息，而ReLU对于没有滤除的部分，即非零的部分的作用是一个线性分类器。

既然在低维空间中使用ReLU做激活变换会丢失很多信息，针对这个问题使用linear bottleneck(即不使用ReLU激活，做了线性变换)的来代替原本的非线性激活变换。到此，优化网络架构的思路也出来了：通过在卷积模块中后插入linear bottleneck来捕获兴趣流行。 实验证明，使用linear bottleneck可以防止非线性破坏太多信息。如果输入流行可通过激活空间嵌入到显著的低维子空间，那么通常ReLU变换可保留信息，同时为所需的复杂性引入一组可表达的函数。

从linear bottleneck到深度卷积之间的的维度比称为Expansion factor(扩展系数),该系数控制了整个block的通道数。 linear bottleneck的使用操作的流程演化图如下：

![img](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image012.jpg)

图(a)：普通模型架构使用标准卷积将空间和通道信息一起映射到下一层，参数和计算量会比较大

图(b)，MobileNetv1中将标准卷积拆分为深度卷积和逐点卷积，深度卷积负责逐通道的过滤空间信息，逐点卷积负责映射通道。将空间和通道分开了

图(c)和图(d)是MobileNetv2的结构(d是c的下一个连接状态)，同样是将标准卷积拆分为深度卷积和逐点卷积，在逐点卷积后使用了接1 × 1 1×11×1卷积，该卷积使用线性变换，总称为一层低维linear bottleneck，其作用是将输入映射回低维空间

### 2.3.3     Inverted residuals

由上面的分析， linear bottleneck中包含了所有的必要信息，对于Expansion layer(即linear到深度卷积部分)仅是伴随张量非线性变换的部分实现细节，我们可将shortcuts放在linear bottleneck之间连接。示意图如下：

![在这里插入图片描述](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image014.png)

选择这样的结构，可以提升梯度在乘积层之间的传播能力，有着更好的内存使用效率。

下表是bottleneck convolution的基本实现：

![在这里插入图片描述](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image016.png)首先是1×1 conv2d变换通道，后接ReLU6激活(ReLU6即最高输出为6，超过了会clip下来)

中间是深度卷积,后接ReLU

最后的1×1 conv2d后面不接ReLU，取而代之的是linear bottleneck

Inverted residuals结构示意图如下(方块的高度即代表通道数)：

![在这里插入图片描述](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image018.png)

可以看到相比与之前的残差模块，中间的深度卷积较宽，除了开始的升维的1×1卷积，做shortcut的1×1卷积通道数较少，呈现的是倒立状态，故称为Inverted residuals。

这样的结构在构建block时，自然的将输入和输出分离了。把模型的网络expressivity (expansion layers，由扩展层决定)和capacity(encoded by bottleneck inputs，由bottleneck通道决定)分开。

## 2.4  Adam优化算法

　　Adam算法和传统的随机梯度下降不同。随机梯度下降保持单一的学习率（即alpha）更新所有的权重，学习率在训练过程中并不会改变。而Adam通过计算梯度的一阶矩估计和二阶矩估计而为不同的参数设计独立的自适应性学习率。

　　Adam算法的提出者描述其为两种随机梯度下降扩展式的优点集合，即：

u 适应性梯度算法（AdaGrad）为每一个参数保留一个学习率以提升在稀疏梯度（即自然语言和计算机视觉问题）上的性能。

u 均方根传播（RMSProp）基于权重梯度最近量级的均值为每一个参数适应性地保留学习率。这意味着算法在非稳态和在线问题上有很有优秀的性能。

　　Adam算法同时获得了AdaGrad和RMSProp算法的优点。Adam不仅如RMSProp算法那样基于一阶矩均值计算适应性参数学习率，它同时还充分利用了梯度的二阶矩均值（即有偏方差/uncenteredvariance）。具体来说，算法计算了梯度的指数移动均值（exponentialmovingaverage），超参数beta1和beta2控制了这些移动均值的衰减率。

　　移动均值的初始值和beta1、beta2值接近于1（推荐值），因此矩估计的偏差接近于0。该偏差通过首先计算带偏差的估计而后计算偏差修正后的估计而得到提升。如果对具体的实现细节和推导过程感兴趣，可以继续阅读该第二部分和原论文。

Adam算法即一种对随机目标函数执行一阶梯度优化的算法，该算法基于适应性低阶矩估计。Adam算法很容易实现，并且有很高的计算效率和较低的内存需求。Adam算法梯度的对角缩放（diagonalrescaling）具有不变性，因此很适合求解带有大规模数据或参数的问题。该算法同样适用于解决大噪声和稀疏梯度的非稳态（non-stationary）问题。超参数可以很直观地解释，并只需要少量调整。本论文还讨论了Adam算法与其它一些相类似的算法。我们分析了Adam算法的理论收敛性，并提供了收敛率的区间，我们证明收敛速度在在线凸优化框架下达到了最优。经验结果也展示了Adam算法在实践上比得上其他随机优化方法。最后，我们讨论了AdaMax，即一种基于无穷范数（infinitynorm）的Adam变体。

　　

![img](file:///C:/Users/24583/AppData/Local/Temp/msohtmlclip1/01/clip_image019.jpg)

如上算法所述，在确定了参数α、β1、β2和随机目标函数f(θ)之后，我们需要初始化参数向量、一阶矩向量、二阶矩向量和时间步。然后当参数θ没有收敛时，循环迭代地更新各个部分。即时间步t加1、更新目标函数在该时间步上对参数θ所求的梯度、更新偏差的一阶矩估计和二阶原始矩估计，再计算偏差修正的一阶矩估计和偏差修正的二阶矩估计，然后再用以上计算出来的值更新模型的参数θ。

 

# 3.   总结

## 3.1  实现过程

标签处理选择将标签转化为独热码的形式。

图像处理选择将图片先由JPG转化为PIL格式，再将其转化为数组代替图像深度和空间处理。

神经网络选择使用MobileNetv2，因为其采用了新的Inverted residual bottleneck结构，能够构建一系列高效移动模型，基本的构建单元具有多种特性，同时在内存处理上有极好的性质，非常适合轻量级神经网络的开发。

引入了基于梯度下降的算法：Adam优化器，主要包含以下几个显著的优点：

l 高效的计算

l 所需内存少

l 梯度对角缩放的不变性（第二部分将给予证明）

l 适合解决含大规模数据和参数的优化问题

l 适用于非稳态（non-stationary）目标

l 适用于解决包含很高噪声或稀疏梯度的问题

l 超参数可以很直观地解释，并且基本上只需极少量的调参

Adam优化算法是随机梯度下降算法的扩展式，近来其广泛用于深度学习应用中，尤其是计算机视觉和自然语言处理等任务。Adam在很多情况下算作默认工作性能比较优秀的优化器。

 

## 3.2  创新点

Mobilenetv2提出了一种新型层结构： 具有线性瓶颈的倒残差结构(the inverted residual with linear bottleneck)。

该模块首先将输入的低维压缩表示(low-dimensional compressed representation)扩展到高维，使用轻量级深度卷积做过滤；随后用linear bottleneck将特征投影回低维压缩表示。 这个模块可以使用任何现代框架中。此外，该模块特别适用于移动设备设计，它在推理过程不实现大的张量，这减少了需要嵌入式硬件设计对主存储器访问的需求。

**
**

# 4.   引用

[1]Mark Sandler,Andrew Howard,Menglong Zhu, Andrey Zhmoginov, Liang-Chieh Chen，MobileNetV2: Inverted Residuals and Linear Bottlenecks [cs.CV]，https://arxiv.org/abs/1801.04381，2019-02-21

[2][Diederik P. Kingma](https://arxiv.org/search/cs?searchtype=author&query=Kingma%2C+D+P), [Jimmy Ba](https://arxiv.org/search/cs?searchtype=author&query=Ba%2C+J)，Adam: A Method for Stochastic Optimization[cs.LG]，https://arxiv.org/abs/1412.6980，2017-06-30

[3]Andrew G. Howard, Menglong Zhu, Bo Chen, Dmitry Kalenichenko, Weijun Wang, Tobias Weyand, Marco Andreetto, Hartwig Adam，MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications [cs.CV]，https://arxiv.org/abs/1704.04861，2017-4-17

[4]John Duchi,Elad Hazan,Yoram Singer,Authors Info & Claims，Adaptive Subgradient Methods for Online Learning and Stochastic Optimization，https://dl.acm.org/doi/10.5555/1953048.2021068，2011-07-01

[5] Tieleman, T., & Hinton, G. (2012). Lecture 6.5-rmsprop: Divide the gradient by a running average of its recent magnitude. COURSERA: Neural networks for machine learning, 4(2), 26-31.

 
