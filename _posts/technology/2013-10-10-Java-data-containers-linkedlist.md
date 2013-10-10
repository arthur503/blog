---
layout: default
title: Java基础：数据容器源码分析之LinkedList
---
对数据容器的源码分析，先分析的LinkedList，因为之前对于Java中没有指针但还可以保存链表不太理解，因此来看看如何链接的。

看完之后，觉得主要有以下几点需要注意的：

* LinkedList的删除时间复杂度不是O(1)而是O(n)，添加操作中的追加操作为O(1)，插入操作为O(n)；
* LinkedList的实现是一个双向链表的环状结构，它的成员是一个个对Node的引用；
* Node是以静态方法声明的，在LinkedList加载时就自动加载了；
* remove(int i)的实现有个技巧，先判断i是在前半部分还是后半部分，然后在从header开始往前或往后找；

在LinkedList中，基本操作包括：创建、查找、获取和设置、添加（追加和插入）和删除（特定内容元素和特定位置元素）。在源代码中，表征位置的变量包括：first、last、prev、next。

一、概述

LinkedList本身是一个双向链表结构，包含的元素为Node。Node的定义在LinkedList内部，代码为：

    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }

我们可以看到，Node内部包含本身变量item，以及前后两个节点Node。形式上相当于C++中的指针，Java中没有指针的概念，此处为引用。

LinkedList中的双向链表为环状结构。在为空时，header的前后引用均为本身。在按照元素位置进行查找或添加元素时，可以利用这一点来提高效率。

LinkedList继承自AbstractSequentialList（继承自AbstractList），实现的接口包括：List, Deque, Cloneable, java.io.Serializable。其中，AbstractSequentialList继承自AbstractList，已经实现了List接口，放在此处只是更加明了。

LinkedList本身不是线程安全的，因此，在多线程中使用时必须使用外部方法同步。

LinkedList中的基本操作包括：创建、查找、获取、设置、添加、删除。我们分别来分析。

二、创建

LinkedList的创建包括两个构造方法：无参数和Collection为参数。如下：

    public LinkedList() {
    }

    public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }

可以看出，Collection为参数的方法，首先调用无参构造方法，然后调用添加Collection的方法来初始化。

三、查找(时间复杂度为O(n))

LinkedList中，查找某元素有两种形式：通过index查找和通过内容查找。

通过对象的index查找的方法为node()，代码如下：

    Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }

我们可以看到，这里先通过index和size/2对比，分成从前往后和从后往前查找，时间复杂度降低为O(n/2)。

通过对象的内容查找包括，indexOf()、lastIndexOf()方法，我们查看indexOf()代码如下：

    public int indexOf(Object o) {
        int index = 0;
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null)
                    return index;
                index++;
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item))
                    return index;
                index++;
            }
        }
        return -1;
    }

此时，时间复杂度就是O(n)了。    

四、获取和设置

获取和设置操作包括get()、set()方法，他们都依赖于查找操作，因此时间复杂度为O(n)。代码如下：

    public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }

    public E set(int index, E element) {
        checkElementIndex(index);
        Node<E> x = node(index);
        E oldVal = x.item;
        x.item = element;
        return oldVal;
    }

五、添加和删除

LinkedList中添加元素操作有两类：追加和插入。

添加操作涉及的方法有：add(), addAll(), addFirst(), addLast()。核心操作包括：linkFirst(), linkBefore().

追加操作相当于linkLast()，操作时间复杂度为O(1)；对特定位置index的插入操作，如果index与size相同，则相当于追加操作，否则需要查找到位置后再linkBefore()操作(时间复杂度为O(n))，插入操作总的时间复杂度为O(n)。代码如下：

    public void add(int index, E element) {
        checkPositionIndex(index);

        if (index == size)
            linkLast(element);
        else
            linkBefore(element, node(index));
    }

LinkedList中删除元素操作有两类：删除特定内容元素和删除特定位置元素。其核心操作为unlink()，代码如下：

    public E remove(int index) {
        checkElementIndex(index);
        return unlink(node(index));
    }

    public boolean remove(Object o) {
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }

可以看出，两种方法由于都需要寻找元素，所以时间复杂度均为O(n)。

注意：对于remove(Object o)方法，如果Object为Integer元素，必须把参数强制转化为Integer，否则按照int处理从而调用remove(int index)方法；

此处小结一下：添加和删除操作中，只有追加的时间复杂度是O(1)，其他如插入到特定位置、删除特定元素、删除特定位置元素的操作，由于需要寻找位置，时间复杂度都是O(n)。



参考资料：

* [java的LinkedList源代码分析 ](http://blog.sina.com.cn/s/blog_463b79ca01010w3g.html)
* [java的LinkedList的用法](http://java-admin.iteye.com/blog/1158750)
* [java源码分析之LinkedList](http://blog.csdn.net/jzhf2012/article/details/8540543)




