一、基于模型集成代码，修改预训练模型路径和配置等，完成dureader robust项目。
1、补充模型config及预训练模型路径部分，实现完整项目，参考第七节codes中的代码。（20'）

二、简答题
1. 简述bi-DAF输入层的构建方式：word embedding，char embedding，contextual embedding。（40'）

    word embedding 是词嵌入层，该层也映射每个单词到一个高维向量空间。使用预训练的词向量来获得每个单词的固定的词嵌入。字符和单词嵌入向量的连接，传递给一个两层的网络。该网络的输出是d维向量的两个序列，或者说是两个矩阵：文本X（d x T）和查询Q（d x J）

    char embedding 是字符嵌入层，映射每个单词到一个高维的向量空间。{X1, X2,…, Xt}表示输入的文本。{Q1, Q2,…, Qj}表示输入的查询。使用CNN获得每个单词的字符嵌入。字符嵌入为向量，可以看作CNN的一维输入，向量的size就是CNN的输入channel size。CNN的输出在整个宽度上经过最大池化操作后，获得每个单词的定长向量。

    contextual embedding 又称文本嵌入层，在之前的嵌入之上，使用LSTM来给单词之间的短暂交互建模。使用双向LSTM，并连接两个方向LSTM的输出。从文本词向量X（d x T）中得到H（2d x T），从查询词向量Q（d x J）中得到U（2d x J）。H和U中的2d维度，是由于连接了LSTM的正向和反向输出，每个方向都有一个d维的输出。

    模型的前三层，都是用来从查询和文本中，在不同层级的粒度上，计算特征。类似计算机视觉领域中卷积神经网络CNN中的多级特征计算。

2. 解释BERT中的概念：mask LM，positional encoding。（40’）
    mask LM 为了解决只能利用单向信息的问题，BERT使用的是Mask语言模型而不是普通的语言模型。Mask语言模型有点类似与完形填空——给定一个句子，把其中某个词遮挡起来，让人猜测可能的词。这里会随机的Mask掉15%的词，然后让BERT来预测这些Mask的词，通过调整模型的参数使得模型预测正确的概率尽可能大，这等价于交叉熵的损失函数。这样的Transformer在编码一个词的时候会参考上下文的信息。

    对于transformer模型的positional encoding有两种主流方式：绝对位置编码和相对位置编码。绝对位置编码直接对不同的位置随机初始化一个postion embedding，加到word embedding上输入模型，作为参数进行训练。使用绝对位置编码，不同位置对应的positional embedding固然不同，但是位置1和位置2的距离比位置3和位置10的距离更近，位置1和位置2与位置3和位置4都只相差1，这些关于位置的相对含义模型不能通过绝对位置编码获得，因此需要采用另一种更直观地方法——相对位置编码。这种相对位置编码比较直观的做法是Sinusoidal Position Encoding，使用正余弦函数表示绝对位置，通过两者乘积得到相对位置，这样设计的好处是位置的positional encoding可以被位置线性表示，反映其相对位置关系。
