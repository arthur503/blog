---
layout: default
title: Java基础：Java中的关键字之transient和volatile
---

#### transient

transient的英文含义是：短暂的，瞬变的。

> Java的serialization提供了一种持久化对象实例的机制。当持久化对象时，可能有一个特殊的对象数据成员，我们不想用serialization机制来保存它。为了在一个特定对象的一个域上关闭serialization，可以在这个域前加上关键字transient。   
> 
> transient是Java语言的关键字，用来表示一个域不是该对象串行化的一部分。当一个对象被串行化的时候，transient型变量的值不包括在串行化的表示中，然而非transient型的变量是被包括进去的。  
> 
> 当某个字段被声明为transient后，默认序列化机制就会忽略该字段。在进行序列化输出（如写入到文件中）时，输出结果为null。
> 
> 因此，java的transient关键字为我们提供了便利，你只需要实现Serilizable接口，将不需要序列化的属性前添加关键字transient，序列化对象的时候，这个属性就不会序列化到指定的目的地中。
> 
> 注意：static变量也是可以串行化的。


#### volatile

Volatile的英文含义是：挥发性的、不稳定的、反复无常的。

> Java语言规范中指出：为了获得最佳速度，允许线程保存共享成员变量的私有拷贝，而且只当线程进入或者离开同步代码块时才与共享成员变量的原始值对比。  
> 
> 这样当多个线程同时与某个对象交互时，就必须要注意到要让线程及时的得到共享成员变量的变化。   
> 
> Volatile修饰的成员变量在每次被线程访问时，都强迫从主内存中重读该成员变量的值。而且，当成员变量发生变化时，强迫线程将变化值回写到主内存。这样在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。   
> 
> 而volatile关键字就是提示VM：对于这个成员变量不能保存它的私有拷贝，而应直接与共享成员变量交互。   
> 
> 使用建议：在两个或者更多的线程访问的成员变量上使用volatile。当要访问的变量已在synchronized代码块中，或者为常量时，不必使用。   
> 
> 由于使用volatile屏蔽掉了VM中必要的代码优化，所以在效率上比较低，因此一定在必要时才使用此关键字。  



参考资料：

* [理解Java对象序列化(原)](http://www.blogjava.net/jiangshachina/archive/2012/02/13/369898.html)
* [java transient简介](http://blog.csdn.net/zxl315/article/details/5359151)
