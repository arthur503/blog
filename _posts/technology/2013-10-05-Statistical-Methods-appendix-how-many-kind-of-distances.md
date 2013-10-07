---
layout: default
title: 统计学习方法BR-附录：距离、相似度和熵的度量方法总结
---
在看CH3 KNN算法的时候，需要用到两个实例点之间的距离，这个距离怎么判断两个点之间的相似度，有几种度量方法？

关于距离度量的方法的专题其实已经想做好久了，正好趁这个机会总结出来。不过等查了资料，发现July早在[从K近邻算法、距离度量谈到KD树、SIFT+BBF算法](http://blog.csdn.net/v_july_v/article/details/8203674/)这篇文章里总结过，而且总结的非常详细（注：后来发现July有一部分的引用原创在这里[机器学习中的相似性度量](http://www.cnblogs.com/heaad/archive/2011/03/08/1977733.html)）。看完之后不禁觉得，前人已经做的足够好，自己再做也不会做的更好了的挫败感。不过，既然是学习知识，自己总结过的一遍总是要强过看过别人的十遍，因此，此处不惜重新过一遍，希望能从自己的角度更好的把这些距离度量方法理得更通顺、好记。所以，本文是以[从K近邻算法、距离度量谈到KD树、SIFT+BBF算法](http://blog.csdn.net/v_july_v/article/details/8203674/)为蓝本的，读者也可以直接去读July的文章。

这里讨论的距离度量应该是向量空间内的度量，两个点(即两个向量)之间的距离或相似性的度量。每种度量包括描述、定义和公式、优缺点、应用等部分。本文涵盖一下几个度量方法：

<ol>
<li>欧氏距离;</li>
<li>曼哈顿距离;</li>
<li>切比雪夫距离;</li>
<li>闵可夫斯基距离;</li>
<li>标准化欧氏距离;</li>
<li>马氏距离;</li>
<li>巴氏距离</li>
<li>汉明距离;</li>
<li>夹角余弦;</li>
<li>相关系数与相关距离。</li>
<li>杰卡德相似系数与距离;</li>
<li>皮尔逊系数；</li>
<li>熵；</li>
</ol>

#### 1. 欧氏距离（Euclidean distance）

1.1 描述

这是最常见的两点之间距离度量表示法，即欧几里得度量。欧氏距离定义在欧几里得空间（关于欧几里得空间空间可以查看博客：[统计学习方法BR-附录：希尔伯特空间、欧几里得空间和巴拿赫空间【存疑】](http://arthur503.github.io/blog/2013/10/03/Statistical-Methods-appendix-hilbert-space-and-more-spaces.html)）中。

1.2 定义和距离公式

	d(x,y) = (∑(i=1~n) (xi - yi)^2 )^(1/2)

即：所有点的对应维度之差的平方的求和再开方。欧氏距离也可以表示成向量运算的形式：

	d<sub>xy</sub> = ((a-b)(a-b)<sup>T</sup>)^(1/2)

即：两个向量之差与他们之差的转置的乘积的开方。

#### 2. 曼哈顿距离（Manhattan distance）

2.1 描述

根据维基百科，[曼哈顿距离](http://zh.wikipedia.org/wiki/%E6%9B%BC%E5%93%88%E9%A0%93%E8%B7%9D%E9%9B%A2)的由来和定义如下：

> 曼哈顿距离的命名原因是从规划为方型建筑区块的城市（如曼哈顿）间，最短的行车路径而来（忽略曼哈顿的单向车道以及只存在于3、14大道的斜向车道）。任何往东三区块、往北六区块的的路径一定最少要走九区块，没有其他捷径。

> 我们可以定义曼哈顿距离的正式意义为L1-距离或城市区块距离，也就是在欧几里得空间的固定直角坐标系上两点所形成的线段对轴产生的投影的距离总和。

曼哈顿距离其实就相当于两点在各个坐标轴上的投影距离之和。如下图所示：
<img src="http://upload.wikimedia.org/wikipedia/commons/thumb/0/08/Manhattan_distance.svg/300px-Manhattan_distance.svg.png">

2.2 定义和距离公式

其中，绿线表示欧式距离，红、蓝、黄表示相等的曼哈顿距离。对于n维向量x(x1...xn)和y(y1...yn)，其计算公式如下：

	d<sub>xy</sub> = ∑(i=1~n) |xi - yi|

要注意的是，曼哈顿距离依赖座标系统的转度，而非系统在座标轴上的平移或映射。

#### 3. 切比雪夫距离（Chebyshev distance）

3.1 描述

维基百科中，[切比雪夫距离](http://zh.wikipedia.org/wiki/%E5%88%87%E6%AF%94%E9%9B%AA%E5%A4%AB%E8%B7%9D%E7%A6%BB)的定义如下：

> 数学上，切比雪夫距离（Chebyshev distance）或是L∞度量是向量空间中的一种度量，二个点之间的距离定义为其各座标数值差的最大值。

> 若将国际象棋棋盘放在二维直角座标系中，格子的边长定义为1，座标的x轴及y轴和棋盘方格平行，原点恰落在某一格的中心点，则王从一个位置走到其他位置需要的步数恰为二个位置的切比雪夫距离，因此切比雪夫距离也称为棋盘距离。

3.2 定义和距离公式

切比雪夫距离的距离公式如下：

	D(p,q) := max<sub>i</sub> |pi - qi|
	
	等价于以下L-p度量的极值：
	D(p,q) = lim(k→∞) (∑(i=1~n) |pi-qi|^k)^(1/k)


（注：根据[List of mathematical symbols](http://en.wikipedia.org/wiki/List_of_mathematical_symbols)，“:=”是定义(is defined as)的意思。）

#### 4. 闵可夫斯基距离（Minkowski Distance）

4.1 描述

在书中提到，Lp距离即是Minkowski距离。闵氏距离不是一种距离，而是一组距离的定义。

在上面曼哈顿距离和切比雪夫距离中，都有提到过Lp距离。根据p取值的不同，闵式距离代表的含义也不同。我们来看一下闵式距离到底是什么距离度量方法。

闵可夫斯基距离并没有查到中文的维基百科词条，索性查阅英文的。[Minkowski distance](http://en.wikipedia.org/wiki/Minkowski_distance)中，对Minkowski distance的定义如下：

> The Minkowski distance is a metric on Euclidean space which can be considered as a generalization of both the Euclidean distance and the Manhattan distance.

是说，闵式距离是定义在欧几里得空间空间上的度量标准，可以被认为是欧氏距离和曼哈顿距离的推广。

> The Minkowski distance can also be viewed as a multiple of the power mean of the component-wise differences between P and Q.

即是说，闵式距离可以看做p和q之间，所有元素的不同的幂平均。

4.2 定义和距离公式

对于两个点p(x1,...,xn)和q(y1,...,yn)，闵式距离的距离公式如下：
	
	D<sub>pq</sub> = (∑<i=1,n> |xi-yi|^p)^(1/p)

也就是说，对p和q的各项之差的p次方求和后，开(1/p)次方。

维基百科中，给出了p值不同的图形，如下：
<img src="http://upload.wikimedia.org/wikipedia/commons/thumb/e/e6/Minkowski3.png/760px-Minkowski3.png">

图中清晰的展示了随着p值的增大，与原点的闵式距离为1的点的图形的演化过程。我们可以看到：

* p=1时，闵式距离即为曼哈顿距离；
* p=2时，闵式距离即为欧氏距离；
* p=∞时，闵式距离即为切比雪夫距离；

所以闵式距离可以看做是前三种距离的总结，前三种距离是闵式距离的特例。	

4.3 优缺点

在此小总结一下闵式距离的缺点，包括曼哈顿距离、欧氏距离和切比雪夫距离等，都存在明显的缺点。

> 举个例子：二维样本(身高,体重)，其中身高范围是150~190，体重范围是50~60，有三个样本：a(180,50)，b(190,50)，c(180,60)。那么a与b之间的闵氏距离（无论是曼哈顿距离、欧氏距离或切比雪夫距离）等于a与c之间的闵氏距离，但是身高的10cm真的等价于体重的10kg么？因此用闵氏距离来衡量这些样本间的相似度很有问题。
> 
> 简单说来，闵氏距离的缺点主要有两个：(1)将各个分量的量纲(scale)，也就是“单位”当作相同的看待了。(2)没有考虑各个分量的分布（期望，方差等)可能是不同的。

#### 5. 标准化欧氏距离（Standardized Euclidean distance）

5.1 描述

标准化欧氏距离是针对简单欧氏距离的缺点而做的一种改进方案。如前所述闵式距离的缺点，那么，标准化欧氏距离的思路是：既然数据各维分量的分布不同，那么我先将各个分量都“标准化”到均值、方差相等，之后再进行度量。

5.2 定义和距离公式

假设样本集X的均值为m，标准差为s，那么样本集X的标准化过程用公式描述为：

	X* = (X-m)/s

经过简单的推导，可以得到两个n维向量x(x1,...,xn)和y(y1,...,yn)之间的标准化欧式距离公式为：

	D(x,y) = (∑(k=1~n)((xk - yk)/sk)^2)^(1/2)

如果将方差的倒数看成是一个权重，这个公式可以看成是一种加权欧氏距离(Weighted Euclidean distance)。

5.3 优缺点

优点：考虑了均值、方差，标准化了距离。

#### 6. 马氏距离/马哈拉诺比斯距离（Mahalanobis Distance）

6.1 描述

在[Mahalanobis distance](http://en.wikipedia.org/wiki/Mahalanobis_distance)中介绍说：

> The Mahalanobis distance is a descriptive statistic that provides a relative measure of a data point's distance (residual) from a common point.

也就是说，马氏距离是提供了一个数据点与公共点之间的相对测量，它是一种描述性统计。

6.2 定义和距离公式

有M个样本向量X1~Xm，协方差矩阵记为S，均值记为向量μ，则其中样本向量X到u的马氏距离表示为：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823274286.png">

而其中向量Xi与Xj之间的马氏距离定义为：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823280193.png">

若协方差矩阵是单位矩阵（各个样本向量之间独立同分布）,则公式就成了：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823281650.png">

也就是欧氏距离了。即：若协方差矩阵是对角矩阵，公式变成了标准化欧氏距离。

6.3 优缺点

优点：量纲无关，排除变量之间的相关性的干扰。

#### 7. 巴氏距离（Bhattacharyya Distance）

7.1 描述

> 在统计中，Bhattacharyya距离测量两个离散或连续概率分布的相似性。它与衡量两个统计样品或种群之间的重叠量的Bhattacharyya系数密切相关。Bhattacharyya距离和Bhattacharyya系数以20世纪30年代曾在印度统计研究所工作的一个统计学家A. Bhattacharya命名。同时，Bhattacharyya系数可以被用来确定两个样本被认为相对接近的，它是用来测量中的类分类的可分离性。

7.2 定义和距离公式

对于离散概率分布 p和q在同一域 X，巴氏距离被定义为：
<img src="http://img.my.csdn.net/uploads/201211/21/1353505211_7582.png">

其中BC(p,q)是Bhattacharyya系数：
<img src="http://img.my.csdn.net/uploads/201211/21/1353505223_4832.png">

对于连续概率分布，Bhattacharyya系数被定义为：
<img src="http://img.my.csdn.net/uploads/201211/21/1353505232_5383.png">

Bhattacharyya系数是两个统计样本之间的重叠量的近似测量，可以被用于确定被考虑的两个样本的相对接近。

由于现在还没用到，所以不多说，更多的资料请参见July的博客[从K近邻算法、距离度量谈到KD树、SIFT+BBF算法](http://blog.csdn.net/v_july_v/article/details/8203674/)和[Bhattacharyya distance](http://en.wikipedia.org/wiki/Bhattacharyya_coefficient)。

#### 8. 汉明距离（Hamming distance）

8.1 描述和定义

> 两个等长字符串s1与s2之间的汉明距离定义为将其中一个变为另外一个所需要作的最小替换次数。
> 
> 例如字符串“1111”与“1001”之间的汉明距离为2。

8.2 应用

> 信息编码（为了增强容错性，应使得编码间的最小汉明距离尽可能大）。

其实我感觉，汉明距离应该算是编辑距离的一种特例（即只包括编辑操作中的“替换”操作），关于编辑距离可以参考我的博文：[编辑距离：我和你到底有多远？（一）](http://arthur503.github.io/blog/2013/09/05/edit-distance-how-faraway-are-we-1.html)

至此，前半部分主要考虑了两个向量（点）之间的距离度量，下面我们来看相似度度量。

#### 9. 夹角余弦（Cosine）

9.1 描述

几何中夹角余弦可用来衡量两个向量方向的差异，机器学习中借用这一概念来衡量样本向量之间的差异。

9.2 定义和公式

在二维空间中向量A(x1,y1)与向量B(x2,y2)的夹角余弦公式：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823283429.png">

扩展到n维，则两个n维样本点a(x11,x12,…,x1n)和b(x21,x22,…,x2n)的夹角余弦为：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823293892.png">

即：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823294588.png">

夹角余弦取值范围为\[-1,1\]。夹角余弦越大表示两个向量的夹角越小，夹角余弦越小表示两向量的夹角越大。当两个向量的方向重合时夹角余弦取最大值1，当两个向量的方向完全相反夹角余弦取最小值-1。

9.3 应用

关于夹角余弦的应用可以参考吴军的数学之美系列[吴军. 数学之美 系列 12 - 余弦定理和新闻的分类](http://www.google.com.hk/ggblog/googlechinablog/2006/07/12_4010.html)

#### 10. 相关系数 ( Correlation coefficient )与相关距离(Correlation distance)

10.1 描述

相关系数是衡量随机变量X与Y相关程度的一种方法。

10.2 定义和公式

相关系数的定义为：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823322444.png">

相关系数的取值范围是\[-1,1\]。相关系数的绝对值越大，则表明X与Y相关度越高。当X与Y线性相关时，相关系数取值为1（正线性相关）或-1（负线性相关）。

相关距离的定义为：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823323390.png">

#### 11. 杰卡德相似系数(Jaccard similarity coefficient)和杰卡德距离(Jaccard distance)

11.1 定义和公式

杰卡德相似系数：两个集合A和B的交集元素在A，B的并集中所占的比例，称为两个集合的杰卡德相似系数，用符号J(A,B)表示。
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823303566.png">

杰卡德相似系数是衡量两个集合的相似度一种指标。

与杰卡德相似系数相反的概念是杰卡德距离(Jaccard distance)。杰卡德距离可用如下公式表示：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823310119.png">

杰卡德距离用两个集合中不同元素占所有元素的比例来衡量两个集合的区分度。

11.2 应用

可将杰卡德相似系数用在衡量样本的相似度上。

举例：样本A与样本B是两个n维向量，而且所有维度的取值都是0或1，例如：A(0111)和B(1011)。我们将样本看成是一个集合，1表示集合包含该元素，0表示集合不包含该元素。

* M11 ：样本A与B都是1的维度的个数
* M01：样本A是0，样本B是1的维度的个数
* M10：样本A是1，样本B是0 的维度的个数
* M00：样本A与B都是0的维度的个数

依据上文给的杰卡德相似系数及杰卡德距离的相关定义，样本A与B的杰卡德相似系数J可以表示为：
<img src="http://img.my.csdn.net/uploads/201212/14/1355467146_1157.png">

这里M11+M01+M10可理解为A与B的并集的元素个数，而M11是A与B的交集的元素个数。而样本A与B的杰卡德距离表示为J'：
<img src="http://img.my.csdn.net/uploads/201212/14/1355467157_8555.png">

#### 12. 皮尔逊系数(Pearson Correlation Coefficient)

我们在第10条中，已经描述了什么是相关系数(Correlation coefficient)与相关距离(Correlation distance)，此处需要用到相关概念。

12.1 描述

在统计学中，皮尔逊积矩相关系数（英语：Pearson product-moment correlation coefficient，又称作 PPMCC或PCCs, 用r表示）用于度量两个变量X和Y之间的相关（线性相关），其值介于-1与1之间。

通常情况下通过以下取值范围判断变量的相关强度：

	相关系数    相关程度

	0.8-1.0     极强相关
	0.6-0.8     强相关
	0.4-0.6     中等程度相关
	0.2-0.4     弱相关
	0.0-0.2     极弱相关或无相关

在自然科学领域中，该系数广泛用于度量两个变量之间的相关程度。它是由卡尔·皮尔逊从弗朗西斯·高尔顿在19世纪80年代提出的一个相似却又稍有不同的想法演变而来的。这个相关系数也称作“皮尔森相关系数r”。

12.3 定义和公式

两个变量之间的皮尔逊相关系数定义为两个变量之间的协方差和标准差的商：
<img src="http://img.my.csdn.net/uploads/201211/21/1353507664_9478.png">

以上方程定义了总体相关系数, 一般表示成希腊字母ρ(rho)。基于样本对协方差和方差进行估计，可以得到样本标准差, 一般表示成r：
<img src="http://img.my.csdn.net/uploads/201211/21/1353507674_8005.png">

一种等价表达式的是表示成标准分的均值。基于(Xi, Yi)的样本点，样本皮尔逊系数是:
<img src="http://img.my.csdn.net/uploads/201211/21/1353507683_2154.png">

假设有两个变量X、Y，那么两变量间的皮尔逊相关系数可通过以下公式计算：
<img src="http://img.my.csdn.net/uploads/201211/22/1353579743_6051.jpg">

其中E是数学期望，cov表示协方差，N表示变量取值的个数。

另外，还有其他三个等价的计算公式，此处只列出一个。

还有包括皮尔逊相关系数的适用范围、如何理解皮尔逊相关系数、皮尔逊相关的约束条件等，具体可以查阅July的博客[从K近邻算法、距离度量谈到KD树、SIFT+BBF算法](http://blog.csdn.net/v_july_v/article/details/8203674/)。

#### 13. 信息熵(Information Entropy)

信息熵并不属于一种相似性度量。那为什么放在这篇文章中啊？

以我的理解，在有些时候，对两个向量的距离进行判断，或者对两个向量进行相似度度量，或者需要对某些点进行取舍的时候，并非一定按照距离或相似度的比较来取舍，有些是按照整体数据集的信息熵的变化大小来取舍的。

例如，在决策树模型中，判断如何选择哪个特征进行分类，使用的就是信息增益或信息增益比来进行判断。

距离和相似度都是用来取舍的一种度量方法，信息熵也算是一种。因此，把他们放到一起来比较。

13.1 描述

信息熵是衡量分布的混乱程度或分散程度的一种度量。分布越分散(或者说分布越平均)，信息熵就越大。分布越有序（或者说分布越集中），信息熵就越小。

13.2 定义和公式

计算给定的样本集X的信息熵的公式为：
<img src="http://pic002.cnblogs.com/images/2011/63234/2011030823325084.png">

其中，n表示样本集X的分类数，p<sub>i</sub>表示X中第i类元素出现的概率。

信息熵越大表明样本集S分类越分散，信息熵越小则表明样本集X分类越集中。。当S中n个分类出现的概率一样大时（都是1/n），信息熵取最大值log<sub>2</sub>(n)。当X只有一个分类时，信息熵取最小值0。

---

华丽丽的分割线，终于把距离、相似度和熵的度量方法列出来了。下面最后做一个总结。July在他的博客里做了总结，我补充了下，结果如下：

<ol>
<li>空间：欧氏距离;</li>
<li>路径：曼哈顿距离;</li>
<li>国际象棋国王：切比雪夫距离;</li>
<li>以上三种的统一形式:闵可夫斯基距离;</li>
<li>加权：标准化欧氏距离;</li>
<li>排除量纲和依存：马氏距离;</li>
<li>测量两个离散或连续概率分布的相似性：巴氏距离</li>
<li>编码差别：汉明距离;</li>
<li>向量差距：夹角余弦;</li>
<li>相关：相关系数与相关距离。</li>
<li>集合近似度：杰卡德相似系数与距离;</li>
<li>度量两个变量X和Y之间的相关（线性相关）：皮尔逊系数；</li>
<li>衡量分布的混乱程度或分散程度：熵。</li>
</ol>

思维导图：
<img src="http://arthur503.github.io/blog/assets/pic/201310/2013-10-05-Statistical-Methods-appendix-how-many-kind-of-distances.png">

好了，这些作为备用资料参考，以后有新的材料再来补充，收工。


参考资料：

* [机器学习中的相似性度量](http://www.cnblogs.com/heaad/archive/2011/03/08/1977733.html)
* [从K近邻算法、距离度量谈到KD树、SIFT+BBF算法](http://blog.csdn.net/v_july_v/article/details/8203674/)
* [统计学习方法BR-附录：希尔伯特空间、欧几里得空间和巴拿赫空间【存疑】](http://arthur503.github.io/blog/2013/10/03/Statistical-Methods-appendix-hilbert-space-and-more-spaces.html)
* [曼哈顿距离](http://zh.wikipedia.org/wiki/%E6%9B%BC%E5%93%88%E9%A0%93%E8%B7%9D%E9%9B%A2)
* [切比雪夫距离](http://zh.wikipedia.org/wiki/%E5%88%87%E6%AF%94%E9%9B%AA%E5%A4%AB%E8%B7%9D%E7%A6%BB)
* [List of mathematical symbols](http://en.wikipedia.org/wiki/List_of_mathematical_symbols)
* [Minkowski distance](http://en.wikipedia.org/wiki/Minkowski_distance)
* [Mahalanobis distance](http://en.wikipedia.org/wiki/Mahalanobis_distance)
* [编辑距离：我和你到底有多远？（一）](http://arthur503.github.io/blog/2013/09/05/edit-distance-how-faraway-are-we-1.html)
* [吴军. 数学之美 系列 12 - 余弦定理和新闻的分类](http://www.google.com.hk/ggblog/googlechinablog/2006/07/12_4010.html)