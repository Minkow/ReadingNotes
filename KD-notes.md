## KD Notes

***

#### 1. Motivations

​	在KB上的knowledge discovery主要有以下两种解释:

​	I\. Input方向的知识发现，更准确点说应该叫做Knowledge Mining，这并不是我survey的重点。

​	II\. Knowledge Base/Knowledge Graph中的知识发现，也就是给定知识的知识发现。

​	

​	对于II中的情况，面向的问题也比较多，有代表性的问题包括潜在关系的发现(某两个节点应该出现的边)，新关系的发现，因果推理(具有时间序列性的关系)等问题。

​	业界目前主要的Methods分为基于逻辑和基于Embedding的两种方案。目前看到的论文基本都是用Embedding做的，反正我是没看到啥用逻辑做的...



#### 2. Embedding

​	ref:A Review of Relational Machine Learning for Knowledge Graphs

​	这是一篇Review，主要着眼于statistical models，review中主要把工作分为了以下三类：构造潜在变量/特征做的，直接暴力用显变量做的(直接用已经存在的关系/属性)，以及用Markov做的。三类工作分别基于对关系的相关性的假设，比如前面两种工作都是条件独立假设。

​	这篇Review中的KG比较适合于Real life中的KG，即比较稠密的KG(相较于专业领域知识图谱)，一对实体之间可能会有多组关系。

​	可以用一组张量表示整个KG空间，$Y\in\{0,1\}^{N_eN_eN_r}$, 这个张量矩阵绝大多数情况下都是稀疏的。单个变量记为$y_{ijk}​$

​	我们的目标是define一个score function $f(\cdot)$ ，将其sigmod后概率乘积作为最终训练或求解出的一个大张量作为输出。



#####(1) 潜在特征方法

​	这类方法的主要思想是，将每个实体 $e$ 用向量 $\vec e$ 表示，作为它的潜在特征向量(当然这玩意也要训练)

​	RESCAL做的工作就是对于每种关系训练一个大矩阵W 使得$f_{ijk}=e^T_iW_ke_j​$

​	评价是state-of-art，特别适用于小规模的数据集，特别适用于进行潜在关系挖掘(判断A->R->B是否成立)

​	基于RESCAL的工作，用MLP(这篇是16年的survey)进行降维也可以取得不错的结果，并且可以应付比较大的数据集。还有用neural tensor network做的，不出所料过拟合了。

​	副产品是，训练出来的实体向量很适合用作近义词/上下位词的运算，同时适用kmeans等无监督算法。

​	据说这个做法和社交网络中的做法有点类似，然而我并不懂社交网络.jpg

​	Besides，还有用latent space model做的，此时$f_{ijk}$会是一个相关于距离的函数，例子就是TranE，定义就是向量$i+j-k$的欧氏距离。

​	论文稍微比较了一下各种模型，吹了一通RESCAL。



#####(2) 图上特征方法

​	这部分比起上面的Embedding，更像图上的算法，广泛应用在领域KG中，如社交网络图谱等，大概可以分为local/global/quasi-local几种方法，用来衡量节点相似度或者score值。

​	local方法中常用的包括公共邻居，Adamic-Adar指数(邻居边加权，权值为度数的对数倒数，消解了较多链接节点的影响)，Preferential Attachment(28原则，直接将两个节点边数相乘)，local方法基本上是方便计算的，但是经常表现出局限性(废话)，基本思想就是利用节点本身的信息去比较。

​	global方法有一部分是基于路径传播的，比如Katz Index。