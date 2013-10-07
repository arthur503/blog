---
layout: default
title: Java基础：迭代器
---
Java中对容器进行遍历的时候，除了for循环，用的最多的应该是迭代器或者foreach语句了，下面总结一下对数据容器的迭代操作。

一、迭代器（Iterator和ListIterator）

迭代器是一个对象，它的工作在于遍历并选择序列中的对象，而客户端程序员不必知道或关心该序列底层的结构。

迭代器的真正作用在于：能够将遍历序列的操作与序列底层的结构分离。正由于此，我们可以说，迭代器统一了对容器的访问方式。我们可以在获得迭代器后，直接对迭代器的获取的元素操作，而不用关心迭代器内的元素类型信息到底是什么。从这个角度来看，迭代器非常强大。

迭代器是轻量级对象：创建它的代价小，但迭代器有使用限制：

1. 只能单向移动；
2. 使用next()方法获取下一个元素；使用hasNext()方法检查序列中是否还有元素；使用remove()方法将迭代器新近返回的元素删除。

迭代器还有子类，ListIterator是一个更加强大的Iterator子类型，它只能用于各种List类的访问，不过功能更加强大。增加的功能包括：

1. 可以双向移动而非单向移动；
2. 可以产生相对于迭代器在列表中指向的当前位置的前一个和后一个元素的索引；
3. 可以使用set()方法替换它访问过的最后一个元素；
4. 可以通过调用listIterator(n)方法创建一个一开始就指向列表索引为n的元素处的ListIterator。

我查了一下ListIterator的源码，从JDK1.6之后提供了descendingIterator，可以提供反向ListIterator。源代码如下：

    /**
     * @since 1.6
     */
    public Iterator<E> descendingIterator() {
        return new DescendingIterator();
    }

    /**
     * Adapter to provide descending iterators via ListItr.previous
     */
    private class DescendingIterator implements Iterator<E> {
        private final ListItr itr = new ListItr(size());
        public boolean hasNext() {
            return itr.hasPrevious();
        }
        public E next() {
            return itr.previous();
        }
        public void remove() {
            itr.remove();
        }
    }

我们可以看到，descendingIterator本质还是一个ListIterator，只不过把hasNext()、next()方法替换成了hasPrevious()和previous()方法。

二、迭代器接口

迭代器的接口有两个，Iterator和Iterable接口：

	package java.util;

	/**
	 * An iterator over a collection.  {@code Iterator} takes the place of
	 * {@link Enumeration} in the Java Collections Framework.  Iterators
	 * differ from enumerations in two ways:
	 *
	 * <ul>
	 *      <li> Iterators allow the caller to remove elements from the
	 *           underlying collection during the iteration with well-defined
	 *           semantics.
	 *      <li> Method names have been improved.
	 * </ul>
	 *
	 * <p>This interface is a member of the
	 * <a href="{@docRoot}/../technotes/guides/collections/index.html">
	 * Java Collections Framework</a>.
	 *
	 * @param <E> the type of elements returned by this iterator
	 *
	 * @author  Josh Bloch
	 * @see Collection
	 * @see ListIterator
	 * @see Iterable
	 * @since 1.2
	 */
	public interface Iterator<E> {

	    boolean hasNext();
	    E next();
	    void remove();
	}

 

	package java.lang;
	import java.util.Iterator;

	/**
	 * Implementing this interface allows an object to be the target of
	 * the "foreach" statement.
	 *
	 * @param <T> the type of elements returned by the iterator
	 *
	 * @since 1.5
	 */
	public interface Iterable<T> {

	    Iterator<T> iterator();
	}

我们可以看到，Iterator接口是自从JDK1.2就出现的，需要实现Iterator的三个方法。而Iterable接口是JDK1.5之后才出现的，需要实现一个创建Iterator的方法。那么，既然存在两个相同作用的接口，那么在实现中，到底是使用哪个接口呢？

我们查看Collection.java文件可以看到实现的是Iterable接口。

我们可以看到，如果实现了Iterable接口之后，还需要在iterator方法内部再返回一个Iterator，在这个Iterator内还是需要实现原来的三个方法，那为什么不一开始就直接实现Iterator接口呢？添加了Iterable接口不是更麻烦了吗？

在[Iterable接口与Iterator接口的困惑](http://www.iteye.com/problems/69081)中，对提过相似的问题有人回答如下：
> Iterable是1.5引入的新特性，Iterator是1.2就有了，二者都是为了迭代造作，Iterable只是包装了Iterator,从而允许实现此接口的对象成为foreach语句的目标，而且这样的话，更方便以后的扩展。

> 看一下JDK中的集合类，比如List一族或者Set一族， 
> 都是实现了Iterable接口，但并不直接实现Iterator接口。 
> 
> 仔细想一下这么做是有道理的。因为Iterator接口的核心方法next()或者hasNext() 
> 是依赖于迭代器的当前迭代位置的。 
> 
> 如果Collection直接实现Iterator接口，势必导致集合对象中包含当前迭代位置的数据(指针)。 
> 当集合在不同方法间被传递时，由于当前迭代位置不可预置，那么next()方法的结果会变成不可预知。 
> 除非再为Iterator接口添加一个reset()方法，用来重置当前迭代位置。 
> 
> 但即时这样，Collection也只能同时存在一个当前迭代位置。 
> 而Iterable则不然，每次调用都会返回一个从头开始计数的迭代器。 
> 多个迭代器是互不干扰的。 

> Iterable是集合框架的超级接口！ 
> （实现这个接口允许对象成为 "foreach" 语句的目标。）

> Iterable说白了就是为了可以for each。 
> 另外记得java是强类型语言，一个类实现了Iterable，那么虚拟机就知道，你这个类可以用foreach来循环。 

查了一下ArrayList的源码，iterator()方法的实现是每次返回一个新的Itr(其实就是一个迭代器)。

    public Iterator<E> iterator() {
        return new Itr();
    }

而且，编码测试后，发现确实是实现Iterable才可在foreach语句中使用。

总结一下，Iterable的优点：
1. 是Iterator接口的包装，超级接口，实现上更简洁；
2. 可以允许实现此接口的对象成为foreach语句的目标；
3. 每次调用返回一个从头开始的迭代器，多个迭代器之间互不干扰，不会出现当前迭代位置混乱的不可预知错误；



参考资料：
* 《Java编程思想》 CH11.6 迭代器
* [Iterable接口与Iterator接口的困惑](http://www.iteye.com/problems/69081)
* [ java中的Iterator和Iterable 区别](http://blog.csdn.net/simaweier/article/details/7884610)