---
layout: default
title: 概率分布的分歧计算：Kullback–Leibler divergence与Jensen–Shannon divergence
---
#####KL分歧（Kullback–Leibler divergence） 

Kullback–Leibler divergence也叫相对熵（relative entropy），是在概率论与信息论中计算概率分布P和Q之间的距离度量方法，记做：D_{KL}(P||Q)。

1. 离散概率分布：

若P和Q是离散的概率分布，则KL分歧计算公式为：
<img src="http://upload.wikimedia.org/math/8/7/7/8770ad83aa7cc87d2ad9f5ca2579bea5.png">

也就是：概率分布P与Q的对数差的期望。其中：

1. P和Q的sum为1；
2. if Q(i)=0 implies P(i)=0 for all i (absolute continuity). 

可以看出，KL分歧是非对称方法，也就是说，概率分布P于Q的距离和Q与P的分歧并不是相同的：D_{KL}(P||Q) != D_{KL}(Q||P)。

2. 连续概率分布：

相对的，若P和Q是连续概率分布，则KL分歧计算公式为：
<img src="http://upload.wikimedia.org/math/5/c/3/5c32153f8aa758f4701b277f3d079adf.png">


#####JS分歧（Jensen–Shannon divergence） 

由于KL分歧具有不对称性，因此，基于KL分歧提出了一种变种，即JS分歧。JS分歧的计算公式如下：

	JSD(P||Q) = D(P||M)/2 + D(Q||M)/2

其中，D(P||Q)表示KL分歧，且：M=(P+Q)/2。

在JS分歧中，若log是以2为底，则JSD的范围为：
	
	0 ≤ JSD(P||Q) ≤ 1

不过在统计热力学中，使用更广泛的log是使用以e为底，也就是ln，此时JSD的范围是：

	0 ≤ JSD(P||Q) ≤ ln(2)

JS分歧还可以扩展为更广泛的定义如下：
<img src="http://upload.wikimedia.org/math/c/0/a/c0ab691b6d1e86f4786dc7f599679886.png">

注意：

1. JS分歧并不是JS距离，JS距离是JS分歧的均方根值（在wiki中有提到“The square root of the Jensen–Shannon divergence is a metric often referred to as Jensen-Shannon distance.”）；
2. JSD(P||Q) = D(P||M)/2 + D(Q||M)/2，其中M=(P+Q)/2。而非是：D(P||Q)/2 + D(Q||P)/2。

参考资料：
* [Kullback–Leibler divergence](http://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence)
* [Jensen–Shannon divergence](http://en.wikipedia.org/wiki/Jensen%E2%80%93Shannon_divergence)
* [Kullback-Leibler Divergence，KL距离](http://blog.csdn.net/pirage/article/details/8902832)