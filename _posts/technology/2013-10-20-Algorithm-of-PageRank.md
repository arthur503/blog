---
layout: default
title: 算法：PageRank算法原理和实现【存疑】
---
想要计算微博用户之间关系和权重的问题，想到了PageRank算法。于是翻了下书《改变未来的九大算法》，看了下PageRank部分的原理，编程代码实现。

一、PageRank算法思想

1. 问题

PageRank算法的由来是为了解决这个问题：对于网络上的网页，如何判定各个网页的重要性（即权重）？

在Google的PageRank算法出现之前，搜索引擎是按照关键词匹配来返回搜索结果的。也就是说，对于你搜索的关键词，只要网页中包含该关键词，则返回该结果。这样导致的结果是，搜索结果中包含很多虽然包含搜索关键词但是相关度不高的网页，检索结果质量不高。那么，如何让质量高的网页排序在前面呢？

2. 思路

想要得到质量高的网页，我们首先要对“质量高”做下定义，即：什么样的网页我们认为是高质量的网页？

对于网络上亿级别的网页，人工判断网页质量显然是不现实的。在PageRank算法中，采用了超链接进行判断。超链接是网页上指向另一个网页的链接。PageRank算法认为：在网络中，如果一个网页有很多网页指向它，那么这个网页的质量就较高。如果用权重来表示网页质量的话，我们可以假设所有网页初始权重为1，被其他网页所指向的网页权重为链入链接网页的权重之和。这样，被链入更多的网页的权重就较高。

PageRank的基本思路如此，不过，要实现还有几个问题要解决。

3. 问题

3.1 有些链接是被用来显示差网页，而非好网页。如：“这个链接很糟糕！”所指向的连接不应该判定为质量高的网页。

解决：在现实中，超链接更多的是用于推荐而非批评。因此，PageRank算法在该处虽然有明显缺陷，在现实中仍然有效。

3.2 网页的权重为所有指向他的网页的权重之和，那么很容易有两个网页互相指向，造成“循环”。

解决：确实有这个问题，我们可以引入“随机访问者模型”。我们假象从一个随机访问互联网的人开始。他从因特网上所有网页链接中，任意选择一个网页开始访问，然后检查该网页的所有超链接，之后在这些超链接中随机选择一个进行访问。不过，我们假设，在每次访问下一个网页之时，他都有可能对任何已有的超链接网页厌倦，从而从因特网上所有的网页链接中随机选择一个网页进行访问。也就是说，访问者可能会跳出已有的超链接网页，从而随即的在因特网中选取一个网页进行访问。在PageRank算法的原型论文中，这个随即比例是15%。

随即访问者模型天生和超链接权重结合。根据随机访问者模型，链入超链接多的网页被访问的概率大，同时，由于有跳出概率（15%），因此，不会造成循环。我们之后会在代码中，显示不同跳出率时，网页的权重如何变化。

3.3 人们可以通过滥用链入超链接，人为的提高自己的网页排名。

这写网页被称为网络垃圾（web spam），是搜索引擎排名算法需要摒除的内容。网络中非人为干涉的网页通过PageRank可以很好的排名，通过其他算法检查到这些人为的链接并去除，保持搜索引擎结果准确性。这也是Google一直在做的。

3.4 计算的高效性

由于计算权值需要好多次迭代，在网页数量众多时，计算量巨大。因此，计算机并非通过“随机访问者模型”来计算PageRank值：他们使用能像随机访问者一样给出相同答案的数学技巧，但计算成本要低得多。【具体计算方法书中未介绍】

二、“随机访问者模型”的Java实现

实现代码见Github：[ArPageRank](https://github.com/arthur503/ArPageRank)。

测试代码中，总共有A~E五个节点。其中，A→B，B→E，E→A，C→A，D→A。另RANDOM_PERCENT = 0.15，访问次数REPEAT_TRAIN_COUNT = 1000000，结果如下：

	RANDOM_PERCENT is:0.150000
	Begin test pagerank...
	Visit WebPage A count is:332098	33.2098%.
	Visit WebPage B count is:312249	31.2249%.
	Visit WebPage C count is:30186	3.0186%.
	Visit WebPage D count is:30218	3.0218%.
	Visit WebPage E count is:295249	29.5249%.

可见，权值排序为A\>B\>E\>C=D，且A、B、E之间相差较小，三者与C、D相差较大，符合预期。

我调整了RANDOM_PERCENT为0.05~0.95之间，步长为0.1。

	RANDOM_PERCENT is:0.050000
	Begin test pagerank...
	Visit WebPage A count is:333220	33.322%.
	Visit WebPage B count is:326454	32.6454%.
	Visit WebPage C count is:9945	0.9945%.
	Visit WebPage D count is:10139	1.0139%.
	Visit WebPage E count is:320242	32.0242%.

	RANDOM_PERCENT is:0.150000
	Begin test pagerank...
	Visit WebPage A count is:332098	33.2098%.
	Visit WebPage B count is:312249	31.2249%.
	Visit WebPage C count is:30186	3.0186%.
	Visit WebPage D count is:30218	3.0218%.
	Visit WebPage E count is:295249	29.5249%.

	RANDOM_PERCENT is:0.250000
	Begin test pagerank...
	Visit WebPage A count is:330054	33.0054%.
	Visit WebPage B count is:297029	29.7029%.
	Visit WebPage C count is:50283	5.0283%.
	Visit WebPage D count is:50334	5.0334%.
	Visit WebPage E count is:272300	27.23%.

	RANDOM_PERCENT is:0.350000
	Begin test pagerank...
	Visit WebPage A count is:325164	32.5164%.
	Visit WebPage B count is:281620	28.162%.
	Visit WebPage C count is:70309	7.0309%.
	Visit WebPage D count is:69901	6.9901%.
	Visit WebPage E count is:253006	25.3006%.

	RANDOM_PERCENT is:0.450000
	Begin test pagerank...
	Visit WebPage A count is:318733	31.8733%.
	Visit WebPage B count is:265049	26.5049%.
	Visit WebPage C count is:90266	9.0266%.
	Visit WebPage D count is:90216	9.0216%.
	Visit WebPage E count is:235736	23.5736%.

	RANDOM_PERCENT is:0.550000
	Begin test pagerank...
	Visit WebPage A count is:308662	30.8662%.
	Visit WebPage B count is:249275	24.9275%.
	Visit WebPage C count is:110011	11.0011%.
	Visit WebPage D count is:109722	10.9722%.
	Visit WebPage E count is:222330	22.233%.

	RANDOM_PERCENT is:0.650000
	Begin test pagerank...
	Visit WebPage A count is:295211	29.5211%.
	Visit WebPage B count is:233096	23.3096%.
	Visit WebPage C count is:130363	13.0363%.
	Visit WebPage D count is:129733	12.9733%.
	Visit WebPage E count is:211597	21.1597%.

	RANDOM_PERCENT is:0.750000
	Begin test pagerank...
	Visit WebPage A count is:275985	27.5985%.
	Visit WebPage B count is:219496	21.9496%.
	Visit WebPage C count is:150510	15.051%.
	Visit WebPage D count is:149872	14.9872%.
	Visit WebPage E count is:204137	20.4137%.

	RANDOM_PERCENT is:0.850000
	Begin test pagerank...
	Visit WebPage A count is:250438	25.0438%.
	Visit WebPage B count is:207910	20.791%.
	Visit WebPage C count is:170462	17.0462%.
	Visit WebPage D count is:169975	16.9975%.
	Visit WebPage E count is:201215	20.1215%.

	RANDOM_PERCENT is:0.950000
	Begin test pagerank...
	Visit WebPage A count is:218762	21.8762%.
	Visit WebPage B count is:200791	20.0791%.
	Visit WebPage C count is:189348	18.9348%.
	Visit WebPage D count is:190218	19.0218%.
	Visit WebPage E count is:200881	20.0881%.

我们可以看到，随着RANDOM_PERCENT的增大，A~E五个节点之间的权重逐渐趋于平均。不过，到底RANDOM_PERCENT是如何规定为15%的？这点先存疑，以后看论文的时候再来解释。【存疑】

参考资料：

* 《改变未来的九大算法》
* [ArPageRank](https://github.com/arthur503/ArPageRank)



