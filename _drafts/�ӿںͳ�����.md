---
layout: default
title: Java基础：接口和抽象类比较
---
在面向对象设计思想中，所有的对象都是用类来描述的。但是，反过来却不成立，并不是所有的类都是用来描述对象的。

在面向对象中，使用一个类来表示具有相同属性的物体。那么，有相似属性的物体怎么来表示呢？一般而言，是使用继承或接口。我们假设他们统一称为抽象体。

[详细解析Java中抽象类和接口的区别](http://dev.yesky.com/436/7581936.shtml)中，对抽象体的理解如下：

> 在面向对象领域，抽象体\[原文此处为“抽象类”，但指的即是abstract class和interface\]主要用来进行类型隐藏。 我们可以构造出一个固定的一组行为的抽象描 述，但是这组行为却能够有任意个可能的具体实现方式。这个抽象描述就是抽象类，而这一组任意个可能的具体实现则表现为所有可能的派生类。模块可以操作一个 抽象体。由于模块依赖于一个固定的抽象体，因此它可以是不允许修改的；同时，通过从这个抽象体派生，也可扩展此模块的行为功能。熟悉OCP的读者一定知 道，为了能够实现面向对象设计的一个最核心的原则OCP(Open-Closed Principle)，抽象类是其中的关键所在。

在大多数的表现结果中，继承或使用接口有很大的相似性（都实现了相同功能），二者都可以实现相同的功能，也都不能被实例化，部分情况下甚至可以互相替换。因此，二者很容易弄混。不过，尽管他们表现上很相似，实际上还是有本质区别的。

我个人理解的观点，abstract class是介于class和接口之间的存在。它具有类的属性，可以被单一继承，可以实现非abstract方法，可以有自己的数据成员；也具有一定接口的属性，可以要求子类实现规定的抽象方法，本身不可以实例化，。interface更加纯粹，只定义了需要实现的方法，不可被继承，所有方法都默认是public abstract类型，只能有静态不能被修改的数据成员（即static final类型）。

从语法定义层面看，二者对比列表如下：

	抽象类： 								接口：
	
	相同点：	
	都是抽象类，不能被实例化；
	实现类或子类都必须实现已经声明的抽象方法；

	不同点：
	可以单一继承							不可继承，但一个类可以实现多个interface
	有声明，也有实现						只有声明，没有实现
	可以没有abstract方法					所有方法都是public abstract类型
	可以有非abstract方法					所有方法都是public abstract类型，不能有实现方法
	子类可以有选择的实现抽象方法			实现类必须实现全部抽象方法
	可以有自己的数据成员(friendly型)		一般不定义数据成员。有则必须为static final类型，且必须赋初值，不可更改、
	
从设计理念上来看，继承是表示"is-a"的关系，接口是表示"has-a"的关系。我觉得，由于Java中的单一继承体系，因此，abstract class更多表现的是类的本质属性，而接口更多是功能属性。接口只关心做了什么，不关心怎么去做；继承更强调所属关系。

Sun docs解释：
> Abstract Classes versus Interfaces
> 
> Unlike interfaces, abstract classes can contain fields that are not static and final, and they can contain implemented methods. Such abstract classes are similar to interfaces, except that they provide a partial implementation, leaving it to subclasses to complete the implementation. If an abstract class contains only abstract method declarations, it should be declared as an interface instead.
> 
> Multiple interfaces can be implemented by classes anywhere in the class hierarchy, whether or not they are related to one another in any way. Think of Comparable or Cloneable, for example.
> 
> By comparison, abstract classes are most commonly subclassed to share pieces of implementation. A single abstract class is subclassed by similar classes that have a lot in common (the implemented parts of the abstract class), but also have some differences (the abstract methods).

在实际工程中，到底是选用abstract class还是interface，需要考虑对问题领域中概念的理解、对设计意图的反映是否合理、是否表现了概念间的不同关系等因素。

参考资料：

* [详细解析Java中抽象类和接口的区别](http://dev.yesky.com/436/7581936.shtml)
* [Java中接口与抽象类的区别](http://blog.csdn.net/a81895898/article/details/8373121)
