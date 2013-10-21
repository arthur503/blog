---
layout: default
title: 统计学习方法BR-CH2：感知机【存疑】
---
感知机（Perceptron）这一章很简单，概括的讲，是使用梯度下降法极小化损失函数，从而得到线性可分数据集上的分离超平面函数，即为分类函数，可以用来对未知数据集进行分类。下面记录一下学习笔记，以及自己写的Java代码实现。

一、模型

感知机是根据输入实例的特征向量x对其进行二类分类的线性分类模型：

	f(x) = sign(w·x + b)

感知机模型对应于输入空间（特征空间）中的分类超平面w·x + b = 0。

二、策略

感知机的学习策略是最小化损失函数。损失函数的选取思路有两种：一是误分类的点数。但是，这样的算是函数不是参数w，b的连续可导函数，不易优化；二是误分类点到超平面的总距离，这是感知机所采用的方法。

感知机sign(w·x + b)学习的损失函数定义为：

	L(w,b) = - ∑(x<sub>i</sub> ∈ M)y<sub>i</sub>(w·x<sub>i</sub> + b)	其中，M为误分类点的集合。

这个损失函数就是感知机学习的经验风险函数。这个损失函数L(w,b)是非负的，误分类点越少，误分类点离超平面越近，损失函数越小。全部正确分类时为0。

三、算法

1. 原始形式

感知机学习算法是误分类驱动的，具体采用的是梯度下降法。首先选取任一个超平面w<sub>0</sub>,b<sub>0</sub>，然后用梯度下降法不断的极小化损失函数。极小化过程中，不是一次使M中所有误分类点的梯度下降，而是一次随机选取一个误分类点使其梯度下降。

算法步骤：

	1. 选取初值w<sub>0</sub>, b<sub>0</sub>；
	2. 在训练集中选取数据(x<sub>i</sub>, y<sub>i</sub>)；
	3. 如果y<sub>i</sub>(w·x + b) ≤ 0：
		w = w + ηy<sub>i</sub>x<sub>i</sub>
		b = b + ηy<sub>i</sub>
	4. 转至步骤2，直至训练集中没有误分类点。

注意，从算法中我们可以看到，感知机的结果不唯一，有无穷多解。根据不同的初值和不同的迭代次序，感知机的结果可能有所不同！

根据这一算法步骤，我写了Java实现代码，见后面代码部分。

另外，可以证明算法的收敛性，即对于线性可分数据集，感知机学习算法的原始形式经过有限次迭代，可以得到一个将训练数据集完全正确划分的分离超平面模型（具体证明参看书P31页）。

2. 对偶形式

说实话这部分还没看懂，一是不明白为什么要对偶；二是不明白为什么对偶得到的结果一致。根据现在查到的资料来看，感觉是对偶形式应该是使用拉格朗日乘数对原始形式的计算进行转化，得到的对偶形式比原始形式更容易计算。【不确定，此处存疑】

感知机的对偶形式等我看懂了再来补充，此处略去一百字。【待补充】

四、代码实现

之前写的Perceptron程序不够易读，重写的一下NewPerceptron，代码如下：

	package com.arthur.statistic.perceptron;

	public class NewPerceptron {
		
		public static void main(String[] argv){
			double[][] trainData = {{3,3,1},{4,3,1},{1,1,-1}};
			NewPerceptron perceptron = new NewPerceptron();
			perceptron.setTrainData(trainData);
			perceptron.startTrainData();
			
			double[] data = {1,4};
			System.out.println("Check data:"+getArrayString(data)+" result is:"+perceptron.check(data));
			double[] data2 = {-4,-2};
			System.out.println("Check data:"+getArrayString(data2)+" result is:"+perceptron.check(data2));
		}
		
		private double[][] trainData;
		private double STEP = 1;
		private double[] w;
		private double b;
		private int dimension;
		
		/**
		 * 之前的Perceptron代码太烂了，重写的Perceptron代码。
		 * @author zjl
		 */
		public NewPerceptron(){
		}

		private void setTrainData(double[][] trainData) {
			// TODO Auto-generated method stub
			this.trainData = trainData;
			dimension = trainData[0].length - 1;
			w = new double[dimension];
			b = 0;
			System.out.println("Initial Perceptron. w = "+getArrayString(w)+" b = "+b+" Step = "+STEP);
		}
		
		/**
		 * 训练数据集。默认为：double[][] trainData = {{3,3,1},{4,3,1},{1,1,-1}};
		 */
		public void startTrainData() {
			// TODO Auto-generated method stub
			
			double[] x = new double[dimension];
			double y;
			for(int i=0;i<trainData.length;i++){
				System.out.println("\nTrain data:"+getArrayString(trainData[i]));
				
				x = getX(trainData[i]);
				y = getY(trainData[i]);
				
				if(!checkData(w,b,x,y)){
					w = computeW(w,x,y);
					b = computeW(b,y);
					System.out.println("New Perceptron Params: w = "+getArrayString(w)+" b = "+b);
					startTrainData();
					return;
				}
				
			}
			System.out.println("\nTrain data Done! w is:"+getArrayString(w)+" b is:"+b+"\n");
		}


		/**
		 * checkData(w,b,x,1)(即：返回y=1的结果)
		 * 若结果为true，则表示为正例；否则为负例。
		 * @param data
		 * @return
		 */
		public boolean check(double[] data) {
			// TODO Auto-generated method stub
			double[] x = getX(data);
			return checkData(w,b,x,1);
		}


		private double[] computeW(double[] w, double[] x, double y) {
			// TODO Auto-generated method stub
			for(int i=0;i<w.length;i++){
				w[i] += x[i] * y * STEP;
			}
			return w;
		}

		private double computeW(double b, double y) {
			// TODO Auto-generated method stub
			return b + y * STEP;
		}
		
		private boolean checkData(double[] w, double b, double[] x, double y) {
			// TODO Auto-generated method stub
			boolean result = (innerProduct(w, x) + b ) * y > 0 ? true : false;
			System.out.println("CheckData result is:"+result);
			return result;
		}

		private double[] getX(double[] data) {
			// TODO Auto-generated method stub
			double[] x = new double[dimension];
			for(int i=0;i<x.length;i++){
				x[i] = data[i];
			}
			return x;
		}

		private double getY(double[] data) {
			// TODO Auto-generated method stub
			return data[dimension];
		}
		
		private int innerProduct(double[] a, double[] b){
			int result = 0;
			for(int i=0;i<a.length;i++){
				result += a[i] * b[i];
			}
			return result;
		}
		
		public static String getArrayString(double[] array){
			StringBuffer sb = new StringBuffer();
			sb.append("[");
			for(int i=0;i<array.length;i++){
				sb.append(array[i] + " ");
			}
			sb.insert(sb.length()-1, ("]"));
			return sb.toString();
		}
	}

运行结果如下：

	Initial Perceptron. w = [0.0 0.0]  b = 0.0 Step = 1.0

	Train data:[3.0 3.0 1.0] 
	CheckData result is:false
	New Perceptron Params: w = [3.0 3.0]  b = 1.0

	Train data:[3.0 3.0 1.0] 
	CheckData result is:true

	Train data:[4.0 3.0 1.0] 
	CheckData result is:true

	Train data:[1.0 1.0 -1.0] 
	CheckData result is:false
	New Perceptron Params: w = [2.0 2.0]  b = 0.0

	Train data:[3.0 3.0 1.0] 
	CheckData result is:true

	Train data:[4.0 3.0 1.0] 
	CheckData result is:true

	Train data:[1.0 1.0 -1.0] 
	CheckData result is:false
	New Perceptron Params: w = [1.0 1.0]  b = -1.0

	Train data:[3.0 3.0 1.0] 
	CheckData result is:true

	Train data:[4.0 3.0 1.0] 
	CheckData result is:true

	Train data:[1.0 1.0 -1.0] 
	CheckData result is:false
	New Perceptron Params: w = [0.0 0.0]  b = -2.0

	Train data:[3.0 3.0 1.0] 
	CheckData result is:false
	New Perceptron Params: w = [3.0 3.0]  b = -1.0

	Train data:[3.0 3.0 1.0] 
	CheckData result is:true

	Train data:[4.0 3.0 1.0] 
	CheckData result is:true

	Train data:[1.0 1.0 -1.0] 
	CheckData result is:false
	New Perceptron Params: w = [2.0 2.0]  b = -2.0

	Train data:[3.0 3.0 1.0] 
	CheckData result is:true

	Train data:[4.0 3.0 1.0] 
	CheckData result is:true

	Train data:[1.0 1.0 -1.0] 
	CheckData result is:false
	New Perceptron Params: w = [1.0 1.0]  b = -3.0

	Train data:[3.0 3.0 1.0] 
	CheckData result is:true

	Train data:[4.0 3.0 1.0] 
	CheckData result is:true

	Train data:[1.0 1.0 -1.0] 
	CheckData result is:true

	Train data Done! w is:[1.0 1.0]  b is:-3.0

	CheckData result is:true
	Check data:[1.0 4.0]  result is:true
	CheckData result is:false
	Check data:[-4.0 -2.0]  result is:false

可见，在学习步长Step=1.0的情况下，学习到的感知机的参数：w is:[1.0 1.0]  b is:-3.0。

五、思维导图
<img src="http://arthur503.github.io/blog/assets/pic/201310/2013-10-04-Statistical-Methods-ch2-perceptron.png">

参考资料：

* 《统计学习方法》- 李航
