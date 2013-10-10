---
layout: default
title: Java基础：序列化
---
序列化的知识，一直没太懂。之前的理解的是可以被持久化为文件，但理解是否正确以及如何实现的都不清楚。

在查Java关键字transient的时候，再次遇到这个问题，先简单看一下。在[理解Java对象序列化(原)](http://www.blogjava.net/jiangshachina/archive/2012/02/13/369898.html)中有较详细的讲解，此处先理解，以后再深入看源码。

一、对象序列化是什么？

> Java平台允许我们在内存中创建可复用的Java对象，但一般情况下，只有当JVM处于运行时，这些对象才可能存在，即，这些对象的生命周期不会比JVM的生命周期更长。但在现实应用中，就可能要求在JVM停止运行之后能够保存(持久化)指定的对象，并在将来重新读取被保存的对象。Java对象序列化就能够帮助我们实现该功能。
> 
> 使用Java对象序列化，在保存对象时，会把其状态保存为一组字节，在未来，再将这些字节组装成对象。必须注意地是，对象序列化保存的是对象的"状态"，即它的成员变量。由此可知，对象序列化不会关注类中的静态变量。
> 
> 除了在持久化对象时会用到对象序列化之外，当使用RMI(远程方法调用)，或在网络中传递对象时，都会用到对象序列化。Java序列化API为处理对象序列化提供了一个标准机制，该API简单易用，在本文的后续章节中将会陆续讲到。

二、Serializable的作用

> 为什么一个类实现了Serializable接口，它就可以被序列化呢？通过使用ObjectOutputStream来持久化对象，在该类中有如下代码：

	private void writeObject0(Object obj, boolean unshared) throws IOException {
	    
	    if (obj instanceof String) {
	        writeString((String) obj, unshared);
	    } else if (cl.isArray()) {
	        writeArray(obj, desc, unshared);
	    } else if (obj instanceof Enum) {
	        writeEnum((Enum) obj, desc, unshared);
	    } else if (obj instanceof Serializable) {
	        writeOrdinaryObject(obj, desc, unshared);
	    } else {
	        if (extendedDebugInfo) {
	            throw new NotSerializableException(cl.getName() + "\n"
	                    + debugInfoStack.toString());
	        } else {
	            throw new NotSerializableException(cl.getName());
	        }
	    }
	    
	}

> 从上述代码可知，如果被写对象的类型是String，或数组，或Enum，或Serializable，那么就可以对该对象进行序列化，否则将抛出NotSerializableException。

三、影响序列化

1. transient关键字

	当某个字段被声明为transient后，默认序列化机制就会忽略该字段。

2. writeObject()方法与readObject()方法

3. Externalizable接口

4. readResolve()方法



参考资料：

* [理解Java对象序列化(原)](http://www.blogjava.net/jiangshachina/archive/2012/02/13/369898.html)