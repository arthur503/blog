---
layout: default
title: Java基础：数据容器源码分析之BitSet
---
之前在写布隆过滤器的时候，就想要一个管理位的class，当时是自己写的对位的处理。现在看到BitSet，对bit的操作实现的更舒服。

##### 默认参数

BitSet中存储位的值所使用的word是long变量，也就是说，一个word有64个位。BitSet使用long数组存储。BitSet中的默认值如下：

    /**
     * The internal field corresponding to the serialField "bits".
     */
    private long[] words;

    /*
     * BitSets are packed into arrays of "words."  Currently a word is
     * a long, which consists of 64 bits, requiring 6 address bits.
     * The choice of word size is determined purely by performance concerns.
     */
    private final static int ADDRESS_BITS_PER_WORD = 6;
    private final static int BITS_PER_WORD = 1 << ADDRESS_BITS_PER_WORD;
    private final static int BIT_INDEX_MASK = BITS_PER_WORD - 1;

由于long存储64位值，因此每个word需要寻址位数为6（2^6 = 64）。

##### 构造方法

BitSet构造方法有两种，空参数构建和int参数构建。空参数构建时默认为64个bit，有参数时按照参数设定的bit位数构建。代码如下：

    public BitSet() {
        initWords(BITS_PER_WORD);
        sizeIsSticky = false;
    }

    public BitSet(int nbits) {
        // nbits can't be negative; size 0 is OK
        if (nbits < 0)
            throw new NegativeArraySizeException("nbits < 0: " + nbits);

        initWords(nbits);
        sizeIsSticky = true;
    }

    private void initWords(int nbits) {
        words = new long[wordIndex(nbits-1) + 1];
    }

我们可以看到，BitSet的初始化参数可以为0，但不能为负数。若为空，默认初始化为BITS_PER_WORD=64位。

##### 位操作

BitSet中，对位的操作有两类，包括：取值和设值。

1. 取值

取值的方法为get()，代码如下：

    /**
     * Returns the value of the bit with the specified index. The value
     * is {@code true} if the bit with the index {@code bitIndex}
     * is currently set in this {@code BitSet}; otherwise, the result
     * is {@code false}.
     *
     * @param  bitIndex   the bit index
     * @return the value of the bit with the specified index
     * @throws IndexOutOfBoundsException if the specified index is negative
     */
    public boolean get(int bitIndex) {
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        checkInvariants();

        int wordIndex = wordIndex(bitIndex);
        return (wordIndex < wordsInUse)
            && ((words[wordIndex] & (1L << bitIndex)) != 0);
    }

可以看到，get()方法中先通过wordIndex()方法确定bitIndex的位置，之后返回对应位置的bit值。如果bitIndex超过了最大值，则返回false。

2. 设值

设值操作包括三种：设定为true、设定为false、设定为相反值。

set()默认设定bitIndex为true，代码如下：

    public void set(int bitIndex) {
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        int wordIndex = wordIndex(bitIndex);
        expandTo(wordIndex);

        words[wordIndex] |= (1L << bitIndex); // Restores invariants

        checkInvariants();
    }

clear()方法为设定bitIndex为false，代码如下：

    public void clear(int bitIndex) {
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        int wordIndex = wordIndex(bitIndex);
        if (wordIndex >= wordsInUse)
            return;

        words[wordIndex] &= ~(1L << bitIndex);

        recalculateWordsInUse();
        checkInvariants();
    }

此外，set(int bitIndex, boolean value)还可以根据用户的参数设定bitIndex为true或false。


    public void set(int bitIndex, boolean value) {
        if (value)
            set(bitIndex);
        else
            clear(bitIndex);
    }

flip方法为设定bitIndex为对应位的相反值，可以使用对应位与1异或的方法，代码如下：

    /**
     * Sets the bit at the specified index to the complement of its
     * current value.
     *
     * @param  bitIndex the index of the bit to flip
     * @throws IndexOutOfBoundsException if the specified index is negative
     * @since  1.4
     */
    public void flip(int bitIndex) {
        if (bitIndex < 0)
            throw new IndexOutOfBoundsException("bitIndex < 0: " + bitIndex);

        int wordIndex = wordIndex(bitIndex);
        expandTo(wordIndex);

        words[wordIndex] ^= (1L << bitIndex);

        recalculateWordsInUse();
        checkInvariants();
    }

##### 位操作寻址和数组的扩展

之前代码中已经看到，若要对位操作，先需要确定某位所在的word的wordIndex，方法如下：

    /**
     * Given a bit index, return word index containing it.
     */
    private static int wordIndex(int bitIndex) {
        return bitIndex >> ADDRESS_BITS_PER_WORD;
    }

这个操作基本每个方法都会用到。如果用户输入的bitIndex超过了总大小，BitSet不是返回错误，而是会通过expandTo()方法自动扩充为原来的2倍！代码如下：

    /**
     * Ensures that the BitSet can accommodate a given wordIndex,
     * temporarily violating the invariants.  The caller must
     * restore the invariants before returning to the user,
     * possibly using recalculateWordsInUse().
     * @param wordIndex the index to be accommodated.
     */
    private void expandTo(int wordIndex) {
        int wordsRequired = wordIndex+1;
        if (wordsInUse < wordsRequired) {
            ensureCapacity(wordsRequired);
            wordsInUse = wordsRequired;
        }
    }

    /**
     * Ensures that the BitSet can hold enough words.
     * @param wordsRequired the minimum acceptable number of words.
     */
    private void ensureCapacity(int wordsRequired) {
        if (words.length < wordsRequired) {
            // Allocate larger of doubled size or required size
            int request = Math.max(2 * words.length, wordsRequired);
            words = Arrays.copyOf(words, request);
            sizeIsSticky = false;
        }
    }

根据BitSet，我们可以写bitmap或布隆过滤器一类的算法实现。

参考资料：

* JDK1.6 BitSet.java