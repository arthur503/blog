---
layout: default
title: Java容器：数据容器源码分析之ArrayList
---
先列一下之前主要迷惑的问题，包括：

1. 如何进行查找操作，有没有用到哈希？
2. 添加和删除操作之后，后面的每一位都要移动吗？岂不是效率较低？
3. 如何扩充数组的？

一、概述

ArrayList直接继承自AbstractList，实现的接口包括：List, RandomAccess, Cloneable, java.io.Serializable。

与LinkedList（详见：[Java基础：数据容器源码分析之LinkedList](http://arthur503.github.io/blog/2013/10/10/Java-data-containers-linkedlist.html)）相比，ArrayList的结构简单的多。其核心是一个Object数组，代码如下：

    private transient Object[] elementData;

所有的操作都是对数组进行操作。

作为一个List，与LinkedList相同，基本操作包括：构建、查找、获取和设置、添加（追加和插入）和删除（特定内容元素和特定位置元素）。

二、创建

由于ArrayList是数组结构，与LinkedList的双向链表结构不同。因此，在构建ArrayList时，需要确定数组的大小，即size(或capacity)。有参构造函数则依照参数构建，否则默认数组大小为10。代码如下：

    public ArrayList(int initialCapacity) {
        super();
        if (initialCapacity < 0)
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        this.elementData = new Object[initialCapacity];
    }

    /**
     * Constructs an empty list with an initial capacity of ten.
     */
    public ArrayList() {
        this(10);
    }

数组在变化（如添加、删除等操作）时，需要考虑size的大小的因素。

三、查找

由于ArrayList是按照数组存储，因此按照index查找元素即相当于直接获取，时间复杂度为O(1)。

而按照元素内容查找元素的index的方法包括indexOf()、lastIndexOf()。二者比较相似，我们只列indexOf()方法代码如下：

    public int indexOf(Object o) {
        if (o == null) {
            for (int i = 0; i < size; i++)
                if (elementData[i]==null)
                    return i;
        } else {
            for (int i = 0; i < size; i++)
                if (o.equals(elementData[i]))
                    return i;
        }
        return -1;
    }

可以看到，与Linkedlist中的indexOf()方法类似。只是在Linkedlist中，是按照node.next()顺序查找，ArrayList中是按照int i升序查找。二者的时间复杂度都是O(n)。

四、获取和设置

与LinkedList不同的是，由于是按照数组存储，因此对index位置的元素的获取和设置操作都不依赖于查找操作，可以直接根据index下标进行查找，时间复杂度均为O(1)。代码如下：

    public E get(int index) {
        rangeCheck(index);

        return elementData(index);
    }

    public E set(int index, E element) {
        rangeCheck(index);

        E oldValue = elementData(index);
        elementData[index] = element;
        return oldValue;
    }

五、添加和删除

如果问起ArrayList与LinkedList的不同之处，一般都会说ArrayList的随机访问速度快，但插入和删除的开销大；LinkedList与之相反。

我们在上一篇博客中，已经看过了LinkedList中，追加操作的时间复杂度是O(1)，插入操作、删除特定内容元素、删除特定位置元素的时间复杂度为O(n)。那么，ArrayList中，对此是如何处理呢？他们的时间复杂度又如何？

ArrayList中，add()和remove()方法的源代码如下：

    public boolean add(E e) {
        ensureCapacityInternal(size + 1);  // Increments modCount!!
        elementData[size++] = e;
        return true;
    }

    public void add(int index, E element) {
        rangeCheckForAdd(index);

        ensureCapacityInternal(size + 1);  // Increments modCount!!
        System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
        elementData[index] = element;
        size++;
    }

我们可以看到，每次添加元素时，都需要先调用ensureCapacityInternal()方法，其源码如下：

    private void ensureCapacityInternal(int minCapacity) {
        modCount++;
        // overflow-conscious code
        if (minCapacity - elementData.length > 0)
            grow(minCapacity);
    }

    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }    

之后调用System.arraycopy()将对应index之后的元素向后移动，然后把element插入到对应的index位置上。也就是说，插入操作需要先copy到一个size+1的新数组中，然后新数组index位置之后元素整体后移，再插入新元素在index位置上。在最差情况下，耗费时间为O(2n)左右，即O(n)的时间复杂度。

与插入操作相比，删除操作不需要创立新数组的开销，只需要将对应index位置的元素删除，将之后的元素前移，然后将需要删除的元素置为null即可，至于空间回收交给垃圾收集器来完成即可，时间复杂度为O(n)。删除操作源代码如下：

    public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // Let gc do its work

        return oldValue;
    }

    public boolean remove(Object o) {
        if (o == null) {
            for (int index = 0; index < size; index++)
                if (elementData[index] == null) {
                    fastRemove(index);
                    return true;
                }
        } else {
            for (int index = 0; index < size; index++)
                if (o.equals(elementData[index])) {
                    fastRemove(index);
                    return true;
                }
        }
        return false;
    }

我们可以看到，ArrayList中，如果在数组末尾删除元素，其实时间复杂度是O(1)的。而在中间index添加或删除元素操作的时间复杂度都为O(n)。

六、ArrayList与LinkedList中操作的时间复杂度对比

与[Java基础：数据容器源码分析之LinkedList](http://arthur503.github.io/blog/2013/10/10/Java-data-containers-linkedlist.html)介绍的LinkedList，我们比较一下二者的各种操作的时间复杂度。

	List 		索引查找	内容查找 	获取 	设置 	追加添加 	插入添加	末尾删除 	索引删除
	ArrayList 	O(1)		O(n) 		O(1) 	O(1) 	O(n) 		O(2n)		O(1) 		O(n)
	LinkedList  O(n/2) 		O(n) 		O(n)	O(n)	O(1) 		O(n/2) 		O(1) 		O(n/2)


总结：整体上来看，ArrayList中查找、获取和设置的速度较快，添加和删除速度较慢；LinkedList中查找、获取和设置的速度较慢，追加操作速度较快，插入操作、删除操作虽然与ArrayList一样是O(n)的时间复杂度，但根据之前的分析，还是比ArrayList要快一些。

最后回答一下开头的问题，如下：

> 1. 如何进行查找操作，有没有用到哈希？
> 2. 添加和删除操作之后，后面的每一位都要移动吗？岂不是效率较低？
> 3. 如何扩充数组的？

1. 根据元素index查找直接利用数组下标，O(1)的时间复杂度；根据元素内容查找为遍历查找，O(n)的时间复杂度。
2. 是的。效率较低，添加操作时间复杂度为O(2n)，删除操作时间复杂度为O(n)。
3. 在添加元素操作中，扩充数组使用ensureCapacityInternal()和grow()方法，即：将原数组拷贝到新的容量的数组中去。但注意：在删除元素操作中，并不拷贝到新数组，而是将后面元素前移之后，最后部分置为null，由垃圾收集器处理。由于扩充数组的时间复杂度为O(n)，所以，添加元素的时间复杂度为O(2n)，删除元素的时间复杂度为O(n)。

参考资料：

* JDK1.6 ArrayList.java
* [Java基础：数据容器源码分析之LinkedList](http://arthur503.github.io/blog/2013/10/10/Java-data-containers-linkedlist.html)

