---
layout: default
title: 映射的世界之五：特殊哈希算法之相似哈希（SimHash）
---
一、相似哈希介绍

哈希算法一般作为信息指纹，用来鉴别两者是否是相同信息。在这些情况下，不同字符串的哈希值自然是越不同越好，尤其是相似的字符串的哈希值，最好是差距巨大，只改变一位也会导致哈希值的巨大变化，这样才更准确判断两个字符串是否不同。

但在有些情况下，我们希望哈希值能承载更多的信息。比如，某些情况（比如判断垃圾邮件列表是否相似、论文查重、查盗版视频等）下，我们需要计算两个文档是否近似相同（而不是是否相同）。如果采用一般的哈希算法，我们可以判断两个文档是否相同，并且只要有微小的一部分不同就会判别为不同。但是，我们希望得到的是两个文档的相似程度，此时，一般的哈希算法就无能为力了。哈希值需要承载更多的信息，即：两个字符串的哈希值不仅可以表达字符串是否相同，还可以表达两个字符串的不同程度如何。也就是说，越相似的字符串的哈希值越接近。

二、相似哈希算法

吴军在《数学之美》中，在“信息指纹及其应用”中有介绍相似哈希（SimHash）的算法原理。我简单摘要过来，如下：

对两个网页进行相似哈希判断，假定网页中有若干词：t1,t2,...,tk，他们的权重（比如TF-IDF值）分布为：w1,w2,...,wk。先计算出这些词的信息指纹，为便于说明，假定只用8位二进制的信息指纹f1和f2（实际中肯定比这个长，否则重复度太高）。

我们先来计算下网页的信息指纹，以第一个网页f1为例。

1. 初始化。将8位二进制的指纹f1扩展成8个实数，用r1,r2,...,r8表示，初值均为0；
2. 扩展。看t1的指纹（8位），如果t1的第i位为1，则在ri上加上w1；若为0，则在ri上减去w1。使用同样方法，遍历t1~tk（相当于将所有词在第i位的权重相加减得到ri），得到最终r1~rk的值；
3. 收缩。根据r1~rk的值，若大于零，则f1的对应位为1，否则为0。

同样的方法，我们还可以得到其他网页的信息指纹。

根据两个网页的相似哈希值，我们可以求出二者之间的相似哈希距离（使用汉明距离或欧几里得距离等），从而判断网页是否相似。

相似哈希的特点是：如果两个网页的相似哈希相差越小，这两个网页的相似性越高。如果两个网页相同，他们的相似哈希肯定相同。如果他们只有少数权重小的词不同，其余的都相同，那么他们的相似哈希也几乎相同。

三、相似哈希代码实现

我使用Java实现了简化的相似哈希算法。部分设定为：

1. 所有权重设定为1；
2. 使用字符串自带的hashCode()计算哈希值；
3. 使用32位二进制作为哈希指纹（即：用int存储）；
4. 使用汉明距离来计算哈希值间距；

我的SimHash的项目地址见：[SimHash](https://github.com/arthur503/SimHash)

main.java

	package com.arthur.simhash;

	public class Main {
		
		public static void main(String[] argv){
			String str1 = "this is string1";
			String str2 = "this is string2";
			
			SimHash sim = new SimHash();
			//compare 6 and 8 for test.
			sim.compareString(String.valueOf(6), String.valueOf(8));
			System.out.println();
			
			//compare str1 and str2 for test.
			sim.compareString(str1, str2);			
			
		}
		
	}

SimHash.java

	package com.arthur.simhash;

	public class SimHash {
		
		private int HASH_LENGTH = 32;
		
		/**
		 * In this method, use int(32 bits) to store hashcode.
		 * use 1 as all words weight for simple reason.
		 * use Hamming distance as hashcode distance.
		 * 
	 	 * @author arthur503
		 */
		public SimHash(){
			
		}
		
		public int compareString(String str1, String str2){
			System.out.println("SimHash compare string of: \""+str1+"\" AND \""+str2+"\"");
			int hash1 = getHashCode(str1);
			int hash2 = getHashCode(str2);
			
			int distance = getDistance(hash1, hash2);
			System.out.println("SimHash string distance of: \""+str1+"\" AND \""+str2+"\" is:"+distance);
			return distance;
		}

		/**
		 * Use hamming distance in this method.
		 * Can change to other distance like Euclid distance or p-distance, etc.
		 * @param hash1
		 * @param hash2
		 * @return
		 */
		private int getDistance(int hash1, int hash2) {
			// TODO Auto-generated method stub
			int distance = 0;
			for(int i=0;i<HASH_LENGTH;i++){
				int bit1 = hash1 & (1 << i);
				int bit2 = hash2 & (1 << i);
				if(bit1 != bit2){
					distance ++;
				}
			}
	//		System.out.println("Distance of hash1 and hash2 is:"+distance);
			return distance;
		}

		private int getHashCode(String str) {
			// TODO Auto-generated method stub
			int result = 0;

			//Step One: Expand.
			String[] array = str.split(" ");
	//		System.out.println("array length:"+array.length+". array0 is:"+array[0]);
			int hash = 0;
			int[] hashBits = new int[HASH_LENGTH];
			for(int i=0;i<array.length;i++){
				hash = array[i].hashCode();
				System.out.println("String \""+array[i]+"\""+" hashcode is:"+Integer.toBinaryString(hash));
				for(int j=HASH_LENGTH-1;j>=0;j--){
					int bit = (hash >> j) & 1;
	//				System.out.println("shift j is:"+j+" bit is:"+bit);
					
					//Different keyword may have different weight. add or minus their weight here.
					//For simple reason, all weight are assigned as 1 in this method.
					if(bit == 1){
						hashBits[HASH_LENGTH-1-j]++; 
					}else{
						hashBits[HASH_LENGTH-1-j]--;
					}			
					
				}
				
				//print hashbits for debug.
	/*			System.out.println("hashbits is:");
				for(int k=0;k<HASH_LENGTH;k++){
					System.out.println("k="+k+" "+hashBits[k]);
				}*/
			}
			
			//Step Two: Shrink.
			for(int i=0;i<HASH_LENGTH;i++){
				int bit = hashBits[i] > 0 ? 1 : 0;
				if(bit == 1){
					result |= 1 << (HASH_LENGTH-1-i);
				}
			}
			System.out.println("String \""+str+ "\" hashcode is:"+result
					+". Binary format is: "+Integer.toBinaryString(result));
			return result;
		}
		
		

	}

最后测试结果如下：

	SimHash compare string of: "6" AND "8"
	String "6" hashcode is:110110
	String "6" hashcode is:54. Binary format is: 110110
	String "8" hashcode is:111000
	String "8" hashcode is:56. Binary format is: 111000
	SimHash string distance of: "6" AND "8" is:3

	SimHash compare string of: "this is string1" AND "this is string2"
	String "this" hashcode is:1101100100111010011110
	String "is" hashcode is:110100101010
	String "string1" hashcode is:10001111110101101010001001000000
	String "this is string1" hashcode is:1445386. Binary format is: 101100000111000001010
	String "this" hashcode is:1101100100111010011110
	String "is" hashcode is:110100101010
	String "string2" hashcode is:10001111110101101010001001000001
	String "this is string2" hashcode is:1445386. Binary format is: 101100000111000001010
	SimHash string distance of: "this is string1" AND "this is string2" is:0

我们可以看到，对"this is string1" AND "this is string2"的对比中，只有：

	String "string1" hashcode is:10001111110101101010001001000000
	String "string2" hashcode is:10001111110101101010001001000001

这二者不同，而且差别很小，只在最后一位有区分。但对于整个字符串来说，最后的结果是相同的，可以判断二者相似。

参考资料：

* 《数学之美》- 吴军
* [SimHash](https://github.com/arthur503/SimHash)
