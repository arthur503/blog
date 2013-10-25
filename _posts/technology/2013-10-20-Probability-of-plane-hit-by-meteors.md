---
layout: default
title: 概率：飞机被流星撞击的概率有几何？
---
在看刘思喆的博客的时候看到了这篇文章[飞机被流星击中而失事的概率](http://www.bjt.name/2009/06/france-flight-poisson/)，觉得题目很好玩，没看答案自己先算了一下。

##### 题目：自己做出所需的合理假设和参数，进行计算一架飞机被流行撞到的几率是多少？

顺道吐槽一句，这乍看之下像是产品经理的题目。

我的假设如下：

1. 飞机的表面积为S0；
2. 地球半径为R，飞机飞行高度为H。所以在飞机飞行球面积S1=4π(R+H)^2；
3. 根据[流星的速度](http://tech.qq.com/a/20111212/000372.htm)，依照流行与地球运行方向，进入大气层速度可在每秒10公里到70公里之间，远大于飞机速度（在音速=340米每秒附近），因此，可以认为流行进入地球时，飞机是相对静止的。
4. 假设飞机平均飞行时间为T小时，每小时内进入地球的流星个数为N个，即：飞机飞行时间内进入地球的流星个数为NT个。

由3可知，一颗流星击中飞机的概率：p1 = S0/S1；

由4可知，飞机飞行期间没有被流星（共NT个）击中的几率为：p2 = (1-p1)^NT；

所以，飞机飞行期间被流星（共NT个）击中的几率为：p = 1 - p2 = 1 - (1-p1)^NT；

根据刘思喆的文章[飞机被流星击中而失事的概率](http://www.bjt.name/2009/06/france-flight-poisson/)，部分数据如下：

* 每小时有 125 颗流星"光临"地球，且都是超音速飞行的；
* 平均飞机的面积占地球总面积的5.7e-13；
* 法航 447 客机预计飞行的时间为 11 小时。

即：令p1=5.7e-13，T=11，N=125，计算如下：

	//某架飞机
	int N = 125;
	int T = 11;
	double p1 = 5.7e-13;
	double p2 = Math.pow(1-p1, N*T);
	double p = 1-p2;

	System.out.println("某架飞机在飞行球面上被一颗流星撞击的概率 p1 is:"+p1);
	System.out.println("某架飞机在飞行过程中不被流星撞击的概率 p2 is:"+p2);
	System.out.println("某架飞机在飞行过程中被流星撞击的概率 p is:"+p);

运行结果如下：

	某架飞机在飞行球面上被一颗流星撞击的概率 p1 is:5.7E-13
	某架飞机在飞行过程中不被流星撞击的概率 p2 is:0.9999999992162658
	某架飞机在飞行过程中被流星撞击的概率 p is:7.837341886585136E-10

在刘思喆的文章[飞机被流星击中而失事的概率](http://www.bjt.name/2009/06/france-flight-poisson/)中，对这种小概率事件是用Poisson分布去拟合的，其计算结果为： 7.8375e-10。二者结果很相近。

注意：这里是某一架飞机被流星撞击的概率。若是要求天空中任意飞机被流星撞击的概率(任意时刻天空中所有飞机面积与飞行球面积之比，需要任意时刻天空飞机数M)，则要重新计算p1 = M * S0/S1。之后，需要统计某段时间（如一天、一个月等）出现的流星总数（根据每小时125颗计算）。基于此，我们继续看下面一道问题：



##### 20年内至少有一架飞机被流星击中的概率为多少？

根据[How much of a threat are meteors to aviation?](http://blog.revolutionanalytics.com/2009/06/how-much-of-a-threat-are-meteors-to-aviation.html)：

> Here goes: at any given time, airliners cover 2 billionths of the Earth's surface. There are 125 meteors an hour, each with probability 2e-9 of striking some airplane. In 20 years, that's about 22 million independent possible impact events. 

在这里，其实有个隐含假设，我们计算一下：在任意给定时刻，天空中都有(2e-9 / 5.7e-13) = 3508.8架飞机，即M=3508（这是根据原文推断出来的假设条件）。这样，在任意时刻，一颗流星撞击飞机的概率：p1 = M * S0/S1 = 5.7e-13 * 3508 = 2e-9（若要计算任意时刻天空中的所有飞机可能被流星撞击的概率，将上面代码p1=2e-9取代5.7e-13即可）。又由于每小时进入大气层的流星数为125颗，在20年内，总共出现的流星数为：125 * 24 * 365 * 20 = 21900000，约为22million。

代码如下：

	//20 years
	double p1_20y = 2e-9;
	double p2_20y = Math.pow(1-p1_20y, 125 * 24 * 365 * 20);
	double p_20y = 1-p2_20y;


	System.out.println("\n任意时刻，飞行球面上的任意飞机的被一颗流星撞击的概率 p1 is:"+p1_20y);
	System.out.println("20年内，任意架飞机都不被流星撞击的概率 p2 is:"+p2_20y);
	System.out.println("20年内，至少一架飞机被流星撞击的概率 p is:"+p_20y);

运行结果为：

	任意时刻，飞行球面上的任意飞机的被一颗流星撞击的概率 p1 is:2.0E-9
	20年内，任意架飞机都不被流星撞击的概率 p2 is:0.9571453662214437
	20年内，至少一架飞机被流星撞击的概率 p is:0.04285463377855625

可以看出，20年内，至少一架飞机被流星击中的概率约为0.043，还是蛮大的。

参考资料：

* [流星的速度](http://tech.qq.com/a/20111212/000372.htm)
* [飞机被流星击中而失事的概率](http://www.bjt.name/2009/06/france-flight-poisson/)
* [How much of a threat are meteors to aviation?](http://blog.revolutionanalytics.com/2009/06/how-much-of-a-threat-are-meteors-to-aviation.html)
