**基于层次softmax训练词向量**

Hierarchical softmax （H-Softmax）本质上是用层级关系替代了扁平化的softmax层，每个叶子节点表示一个词语。于是，计算单个词语概率值的计算过程被拆解为一系列的概率计算，这样可以避免对所有词语进行标准化计算。用H-Softmax替换softmax层之后，词语的预测速度可以提升至少50倍，速度的提升对于低延时要求的实时系统至关重要，比如谷歌新推出的消息应用Allo。

我们可以把原来的softmax看做深度为1的树，词表V中的每一个词语表示一个叶子节点。计算一个词语的softmax概率需要对|V|个节点的概率值做标准化。如果把softmax改为二叉树结构，每个word表示叶子节点，那么只需要沿着通向该词语的叶子节点的路径搜索，而不需要考虑其它的节点。

平衡二叉树的深度是log(|V|)，因此，最多只需要计算log(|V|)个节点就能得到目标词语的概率值。注意，得到的概率值已经经过了标准化，因为二叉树所有叶子节点组成一个概率分布，所有叶子节点的概率值总和等于1。

具体说来，当遍历树的时候，我们需要能够计算左侧分枝或是右侧分枝的概率值。为此，给每个节点分配一个向量表示。与常规的softmax做法不同，这里不是给每个输出词语w生成词向量v'w，而是给每个节点n计算一个向量v'n。总共有|V|-1个节点，每个节点都有自己独一无二的向量表示，H-Softmax方法用到的参数与常规的softmax几乎一样。于是，在给定上下文c时，就能够计算节点n左右两个分枝的概率：

上式与常规的softmax大致相同。现在需要计算h与树的每个节点的向量v'n的内积，而不是与每个输出词语的向量计算。而且，现在只需要计算一个概率值，这里就是偏向n节点右枝的概率值。相反的，偏向左枝的概率值是1−p(right|n,c)

显然，树形的结构非常重要。若我们让模型在各个节点的预测更方便，比如路径相近的节点概率值也相近，那么凭直觉系统的性能肯定还会提升。沿着这个思路，Morin和Bengio使用WordNet的同义词集作为树簇。然而性能依旧不如常规的softmax方法。Mnih和Hinton将聚类算法融入到树形结构的学习过程，递归地将词集分为两个集合，效果终于和softmax方法持平，计算量有所减小。

值得注意的是，此方法只是加速了训练过程，因为我们可以提前知道将要预测的词语（以及其搜索路径）。在测试过程中，被预测词语是未知的，仍然无法避免计算所有词语的概率值。
在实践中，一般不用“左节点”和“右节点”，而是给每个节点赋一个索引向量，这个向量表示该节点的搜索路径。

**LSTM**

（1）词性标注，命名实体识别类任务

（2）生成文本，比如语音识别、机器翻译类任务

（3）循环神经网络也可以用于问答系统，机器翻译类任务