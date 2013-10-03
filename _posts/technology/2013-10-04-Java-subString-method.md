---
layout: default
title: Java：subString方法
---
Java基础问题，subString方法，很简单，但用错了好几次，能说第一次TB面试的时候这么简单的题都做错了么（T_T!）。问题主要是在于endIndex的处理上，对endIndex的位置把握不对。

先来看一下String.java中的源码，如下：

	/**
	     * Returns a new string that is a substring of this string. The
	     * substring begins at the specified <code>beginIndex</code> and
	     * extends to the character at index <code>endIndex - 1</code>.
	     * Thus the length of the substring is <code>endIndex-beginIndex</code>.
	     * <p>
	     * Examples:
	     * <blockquote><pre>
	     * "hamburger".substring(4, 8) returns "urge"
	     * "smiles".substring(1, 5) returns "mile"
	     * </pre></blockquote>
	     *
	     * @param      beginIndex   the beginning index, inclusive.
	     * @param      endIndex     the ending index, exclusive.
	     * @return     the specified substring.
	     * @exception  IndexOutOfBoundsException  if the
	     *             <code>beginIndex</code> is negative, or
	     *             <code>endIndex</code> is larger than the length of
	     *             this <code>String</code> object, or
	     *             <code>beginIndex</code> is larger than
	     *             <code>endIndex</code>.
	     */
	    public String substring(int beginIndex, int endIndex) {
	        if (beginIndex < 0) {
	            throw new StringIndexOutOfBoundsException(beginIndex);
	        }
	        if (endIndex > count) {
	            throw new StringIndexOutOfBoundsException(endIndex);
	        }
	        if (beginIndex > endIndex) {
	            throw new StringIndexOutOfBoundsException(endIndex - beginIndex);
	        }
	        return ((beginIndex == 0) && (endIndex == count)) ? this :
	            new String(offset + beginIndex, endIndex - beginIndex, value);
	    }

源码中，写的很明白，substring(int beginIndex, int endIndex)方法返回的子字符串的起始位置为beginIndex，结束位置为endIndex-1（注意：不是endIndex！）。

也就是说，对于一个String，其下角标范围是\[0, string.length()-1\]，但是如果想要返回整个字符串作为子串，那么使用subString方法应为：subString(0, string.length())，而不是subString(0, string.length()-1)！

因此，使用substring(int beginIndex, int endIndex)方法返回子字符串的默认下角标范围为\[beginIndex, endIndex-1\]，这样，子字符串长度即为：endIndex - beginIndex。

参考资料：

*	JDK 1.6 - String.java