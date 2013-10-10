---
layout: default 
title: Java基础：OutOfMemoryError和StackOverflowError
---
这是IGT笔试的题目，分别写出可以造成两种错误的代码。由于之前测试过HashMap的容量的时候出现过内存不足的错误，所以能写出来，但StackOverflowError没什么印象了。

回来查了下StackOverflowError的资料，在德问社区上有人发帖子[java产生StackOverflowError的原因是什么?](http://www.dewen.org/q/6488)询问：

> 请问java.lang.StackOverflowError产生的原因有什么?
> 
> 已知的有死循环,循环的嵌套层数达到了环境的设定值
> 
> 谢谢

有人回复说：

> 死循环本身是不会StackOverflow的，只有无限递归的时候会出现。原则上循环嵌套次数本身是没有限制的，限制的是占用的栈空间，如果你的函数里定义了很多很多变量，栈空间就会用完得比较快。

还有人讲的更详细：

> JVM里会有两种StackOverflowError, 一种是对应JVM stack, 一种是对应Native Method stack. 我们一般说的都是JVM stack.
> 
> 每一个JVM线程维护自己的JVM stack. JVM stack里面存放 JVM栈帧. 栈帧中存放 数据和中间结果(本地变量数组, 操作符栈, 和对runtime 常量池的引用). 这些数据都比较小(对象都在堆中, 栈帧仅存放对象引用), 所以想单纯通过 在栈帧中存放大数据的方法 去引入StackOverflowError, 基本是不现实的.一般都是因为方法调用嵌套层数过大.
> 
> http://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.5.2
> 
> JVM stack的大小是可以调节的, sun的windows jvm6 x64,jvm栈默认大小为1024k.可以通过-Xss1024k来调节.
http://www.oracle.com/technetwork/java/hotspotfaq-138619.html#threads_oom

另外，在oracle的docs[Chapter 2. The Structure of the Java Virtual Machine](http://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.5.2)中，有如下解释：

> 2.5.2. Java Virtual Machine Stacks
> 
> The following exceptional conditions are associated with Java Virtual Machine stacks:
> 
> * If the computation in a thread requires a larger Java Virtual Machine stack than is permitted, the Java Virtual Machine throws a StackOverflowError.
> 
> * If Java Virtual Machine stacks can be dynamically expanded, and expansion is attempted but insufficient memory can be made available to effect the expansion, or if insufficient memory can be made available to create the initial Java Virtual Machine stack for a new thread, the Java Virtual Machine throws an OutOfMemoryError.
> 
> 2.5.3. Heap
> 
> The following exceptional condition is associated with the heap:
> 
> * If a computation requires more heap than can be made available by the automatic storage management system, the Java Virtual Machine throws an OutOfMemoryError.

也就是说，对于堆栈stack(或heap)来说，如果线程需要的空间大于允许值，则为StackOverflowError；如果stack空间可以动态增加，但最后内存还是不够，则为OutOfMemoryError。

我写了下无限递归代码进行测试：

	public static void main(String[] argv){
		int i = getInt(2);

	}
	
	public static int getInt(int i){
		return getInt(i++);
	}

跑完之后，结果如下：

	Exception in thread "main" java.lang.StackOverflowError

确实如此。

另外，也顺便又测了一下内存溢出的错误，代码如下：

	public static void main(String[] argv){
		HashMap<Integer, Integer> map = new HashMap<Integer,Integer>();
		for(int i=0;i<Integer.MAX_VALUE;i++){
			map.put(i, i);
		}
	}

输出结果为：

	Exception in thread "main" java.lang.OutOfMemoryError: Java heap space

和预期一致。


参考资料：

* [java产生StackOverflowError的原因是什么?](http://www.dewen.org/q/6488)
* [Chapter 2. The Structure of the Java Virtual Machine](http://docs.oracle.com/javase/specs/jvms/se7/html/jvms-2.html#jvms-2.5.2)