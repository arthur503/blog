---
layout: default
title: Java基础：访问权限修饰符
---
Java中有四种访问权限修饰符，public、protected、friendly、private，用于对方法(成员变量)和类的修饰（不是全部可用）。

注意：friendly不是Java关键字，而是从C++中来的，指的是默认的同一包内访问权限。


### 一、修饰成员变量和方法

#### 1. public：

* 成员变量和方法：表示共有，任何情况下都可以访问；

#### 2. protected：

* 成员变量和方法：必须在同一包中才能被访问；继承的子类/派生类也具体有相同的权限。

#### 3. friendly：

* 成员变量和方法：如果一个类的成员没有任何权限修饰，那么它们就是缺省包访问权限，用friendly来表示。必须在同一包中才能被访问（此时与protected等同）；

#### 4. private：

* 成员变量和方法：只能在本类中访问；


### 二、修饰类

类只可以声明为public或者friendly，不可以声明为protected或private。

用public修饰的类任何情况下都可以访问，用friendly修饰的类叫友好类，同一个包中的类可以用。


### 三、继承

这里的访问修饰符指的是修饰成员变量和方法。分为两种情况：

1. 子类与父类在同一个包中

	只有声明为private的变量与方法不可以被继承(访问)，而public、protected、friendly都可以被继承。

2. 子类和父类不在同一包中

	此时，private和friendly均不能被访问(继承)，而protected与public可以被继承。

所以，访问权限修饰符权限从高到低排列是public,protected,friendly, private。


### 四、总结friendly和protected的区别

friendly和protected都表示包内的访问权限（如果可以使用的话）。

#### 修饰成员变量和方法：

1. 无继承时：修饰成员变量和方法的时候没有区别，都是包内访问权限；
2. 有继承时：

2.1 子类和父类在同一个包内：protected和friendly都可以被继承；
2.2 子类和父类不在同一个包内：protected可以被继承，friendly不可被继承。

#### 修饰类：

* friendly可以修饰类(即缺省状态，friendly本身不是Java关键字)，为包内访问权限；
* protected不可以修饰类；

因此，不考虑对类的声明，访问权限控制符权限从高到低是：public、protected、friendly、private。我们可以列个表格如下：

	修饰符	修饰成员变量 	修饰类 	继承(包内)  继承(包外)
	public 		可以 		可以 	可以 		可以
	protected 	可以 		不可以 	可以 		可以
	friendly 	可以 		可以 	可以 		不可以
	private 	可以 	 	不可以	不可以 		不可以

---

因此，我们可以这样理解：

* 对类的修饰：只能是public和friendly的；
* 对成员变量和方法的修饰：public是对所有人开放；protected是对包内成员和*所有子女*开放；friendly是对包内成员和*包内子女*开放；private是只对自己开放。

---

参考资料：

* [转：java访问权限修饰符public protected friendly private用法总结](http://www.blogjava.net/hongzionline/archive/2007/09/19/146409.html)
* [Java访问权限修饰符friendly----同一个包内其它类可以访问(钦波总结大全)](http://huiqinbo.iteye.com/blog/577956)
* [JAVA的public,private,protected访问权限](http://linder0209.iteye.com/blog/1397851)