---
layout: default
title: 智力题之秘书算法
---
查看爱奇艺的笔试题的时候看到的题目：一楼到十楼的每层电梯门口都放着一颗钻石，钻石大小不一，你乘坐电梯从一楼到十楼，每层楼电梯门都会打开一次，只能那一次钻石，问怎样才能拿到最大的一颗？

这跟之前的苏格拉底说的走过麦田里，选择最大的稻穗是类似的题目。查了一下，竟然还真有策略理论，而且很好玩！

在维基百科上，这叫做秘书问题，题目描述如下：

<blockquote>
	在概率及博弈论上，秘书问题（类似的名称有相亲问题、止步问题、见好就收问题、苏丹的嫁妆问题、挑剔的求婚者问题等）内容是这样的：要聘请一名秘书，有 n 个应聘者。每次面试一人，面试后就要及时决定是否聘他，如果当时决定不聘他，他便不会回来。面试后总能清楚了解应聘者的合适程度，并能和之前的每个人做比较。问什么样的策略，才使最佳人选被选中的概率最大。
</blockquote>

总结了一下题目的特性：对于所有候选者的情况未知的n位有限序列，每个候选者只会出现一次，且本人只有一次挑选机会。那么应该使用何种策略，才能使得到的收益最高？

这个问题的最优解是一个停止规则的问题。面试官先拒绝前面k个人（这k个人中最好的人假设为M，k的取值范围[1,n]），然后选出第一个比M更好的人。那么，问题变成为，如何对k取值，使得最后最佳人选被选中的概率最大。

我们来计算一下概率。假设最佳人选在第i个位置，那么他被选中的情况是：当且仅当前i-1个人中的最佳人选出现在前k个位置的时候。那么，选中最佳人选的概率为：

<blockquote>
	P(i) = ∑(i=1~n)P(i被选中|i是最佳人选) * P(i是最佳人选)
		 = ∑(i=1~k)0 * (1/n) + ∑(i=k+1~n)P(前i-1个人中的最佳人选出现在前k个位置之中|i是最佳人选) * P(i是最佳人选)
		 = 0 + ∑(i=k+1~n)(k/(i-1)) + (1/n)
		 = (k/n)∑(i=k+1~n)1/(i-1)
</blockquote>

我们令n趋近于无穷，用x表示k/n的值，令 t 为 i/n，dt 为 1/n，总和可以近似为如下积分：

<blockquote>
	P(k) = x∫(x~1)(1/t)dt
		 = -x * lnx
</blockquote>

对其求导，令导数为0，得到x的最优解为：1/e。

因此，遇到此类情况的最优解是：拒掉前面的n/e个人，之后，一旦发现比前面n/e个人都好的人，立即接受他（否则接受最后一个人）。这种策略的是有一个问题，即：如果最有结果出现在前n/e个人中，那错过之后，就再也遇不到更好的了。也就是说，有1/e的几率是失败的，只能以接受最后一人告终。


这一类问题，对未知性感觉和蓄水池抽样算法相似。相同之处在于都是对候选者情况未知，且对每个候选者只有一次挑选机会。不同之处在于：蓄水池抽样算法长度是无限的或不可知的，且要是要求所有人被挑选出来的概率是相同的而非是只选出最优结果。蓄水池抽样算法可以保证随后每个结果被选中的概率相同，但此未婚妻算法不能保证一定得到最优结果，只能保证是在这种条件下的最优策略，更像是一个博弈的结果。

这个问题还可以有很多变种,包括：
<ul>
<li>选择者可选多于一人</li>
<li>求职者的数目未知</li>
<li>求职者之间的关系可影响选择</li>
<li>被拒绝的求职者有一定机率能被叫回来</li>
<li>选择者满足于次好的人</li>
</ul>

我参照秘书问题写了代码，检测下运行结果，看看在这种策略下，有多大的概率选中最优候选人。代码如下：
<pre><code>
package com.arthur.secretary;

import java.util.Random;

public class SecretaryProblem {
		
	public static void main(String[] argv){

		Interviewer iv = new Interviewer();
		
		int TIMES = 10000;
		int TOP_PERCENT = 30;
		int bestCounter = 0;
		int topPercentCounter = 0;
		for(int i=0;i<TIMES;i++){
			iv.nextRound();
			if(iv.isBestChoice()){
				bestCounter ++;
			}
			if(iv.isTopPercent(TOP_PERCENT)){
				topPercentCounter ++;
			}
		}
		System.out.println("\n=====Result=====");
		System.out.println("Total Times:"+TIMES+".\nBest Choice Counter is:"+bestCounter+". Ratio is:"+(bestCounter*100/TIMES)+"%.");
		System.out.println("Top "+TOP_PERCENT+"% Percent Counter is:"+topPercentCounter+". Ratio is:"+(topPercentCounter*100/TIMES)+"%.");

	}
}

class Interviewer{

	private int BACK_UP_NUMBER = 50;
	private Secretary bu;
	private boolean isBestChoice = false;
	private int result;
	
	public Interviewer(){
		bu = new Secretary(BACK_UP_NUMBER);	
	}
	
	public void nextRound(){
		isBestChoice = false;
		bu.initial();
		choose();
	}

	public void choose(){
		int k = (int)(BACK_UP_NUMBER/Math.E);
		System.out.println("Category k is:"+k);
		
		int max_in_k = -1;
		for(int i=0;i<k;i++){
			if((bu.getSecretary(i) > max_in_k)){
				max_in_k = bu.getSecretary(i);
			}
		}
		
		result = -1;
		for(int i=k;i<BACK_UP_NUMBER;i++){
			if(bu.getSecretary(i) > max_in_k){
				result = bu.getSecretary(i);
				break;
			}
			if(i == BACK_UP_NUMBER - 1){
				result = bu.getSecretary(i);
				System.out.println("Last One!");
			}
		}
		if(bu.getBestSecretary() == result){
			isBestChoice = true;
		}
		System.out.println("Choose Secretary is:"+result);
	}
	
	public boolean isBestChoice(){
		return isBestChoice;
	}
	
	public boolean isTopPercent(int percent){
		return bu.isTopPercent(result, percent);
	}
	
}

class Secretary{
	
	private int number;
	private int[] array; 
	
	/**
	 * Should Initial before use!
	 * @param n
	 */
	public Secretary(int n){
		this.number = n;
		this.array = new int[n];
	}
	
	public boolean isTopPercent(int result, int percent) {
		// TODO Auto-generated method stub
		int k = (int) (array.length * (1 - (double)percent/100));
		System.out.println("Top "+percent+"% percent count is:"+k);
		int counter = 0;
		for(int i=0;i<array.length;i++){
			if(result > array[i]){
				counter ++;
			}
			if(counter >= k){
				return true;
			}
		}
		return false;
	}

	public int getBestSecretary() {
		// TODO Auto-generated method stub
		int max = 0;
		for(int i=0;i<array.length;i++){
			if(array[i] > max){
				max = array[i];
			}
		}
		return max;
	}

	public void initial(){
		Random random = new Random();
		for(int i=0;i<number;i++){
			array[i] = random.nextInt(number);
		}
		System.out.println("\nSecretary["+this.number+"] are:" + this.toString());
		System.out.println("Best Secretary is:" + this.getBestSecretary());	
	}
	
	public int getSecretary(int i){
		return array[i];
	}
	
	public String toString(){
		StringBuffer sb = new StringBuffer();
		for(int i=0;i<array.length;i++){
			sb.append(array[i]+" ");
		}
		return sb.substring(0, sb.lastIndexOf(" "));
	}
	
}
</code></pre>
运行结果是：
=====Result=====
Total Times:10000.
Best Choice Counter is:3669. Ratio is:36%.
Top 30% Percent Counter is:6561. Ratio is:65%.

也就是说，在此种策略下，有大概1/3多的概率选中最优候选者，有2/3左右的概率选中Top 30%的候选者，还是蛮高的。

参考资料：
<ul>
	<li><a href="http://www.zhihu.com/question/20641620">1楼到n楼的每层电梯门口都放着一颗钻石，钻石大小不一。你乘坐电梯从1楼到n楼，每层楼电梯门都会打开一次，只能拿一次钻石，问怎样才能拿到「你认为最大」的一颗？</a></li>
	<li><a href="http://www.guokr.com/article/6768/">死理性派恋爱法：拒绝掉前面37%的人</a></li>
	<li><a href="http://zh.wikipedia.org/wiki/%E7%A7%98%E6%9B%B8%E5%95%8F%E9%A1%8C">秘书问题</a></li>
	<li><a href="http://en.wikipedia.org/wiki/Secretary_problem">Secretary problem</a></li>
	<li><a href="http://www.math.upenn.edu/~ted/210F10/References/Expectations.pdf">OPTIMAL SELECTION BASED ON RELATIVE RANK*</a></li>
</ul>