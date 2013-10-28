---
layout: default
title: 算法：堆排序
---
堆包括小顶堆和大顶堆，分别表示最小值或最大值在第1位。在使用对应的堆排序之后，最小值和最大值被交换到了最后一位，变成了升序排序或降序排序。

我们以大顶堆为例，堆排序的基本思路是：

1. 初始化：循环n/2遍HeapAdjust()方法，将1~n的无序堆变成有序堆（即：大顶堆）；
2. 交换：将有序堆的第1个节点（即：最大值）交换到最后一位；只对第1个节点进行HeapAdjust()调整；
3. 遍历：重复n遍第2步，每次都将调整后的最大值放至该无序区的末尾，将前1~(n-1)的无序堆排序变成有序堆；
4. 依次循环直至遍历完成，得到升序排序的堆。

这个思路很简单，里面最关键也是最难懂的是如何把无序堆变成有序堆。下面来看步骤：

1. 在每一次HeapAdjust()调整中，从给定的节点i开始，比较节点i、2i、2i+1的最大值赋给节点i；
2. 之后节点沿着关键字较大的孩子往下筛选，直到二叉树的最底部；

这样，这一支的路线就调整完毕了。在堆排序方法中，第一步初始化需要从节点n/2开始，到节点1为止，调整n/2次；第二步中，由于堆已经是有序的，只有第一位的节点是无序的（被拿到了末尾），因此只调整节点1的支路即可；第三步中，每次均拿到最大值放到末尾，因此需要重复第二步n次。

复杂度分析：

有序堆变成无序堆，比较次数与二叉树的深度有关，因此时间复杂度是O(logn)的；而在初始化和遍历步骤中，总共需要调用HeapSort()方法3n/2次，因此，总的时间复杂度是O(nlogn)，而且是固定的时间复杂度。空间复杂度只在交换时需要额外空间，为O(1)。

另外，堆排序之后，原数组的顺序并不能得到保持，因此是不稳定排序。

写的代码如下：

	package com.arthur.sort;

	import com.arthur.utils.StringUtils;

	public class HeapSort {
		
		public static void main(String[] argv){
			int[] array = {0,-4,10,2,1,34,21,13,10,5,100};
			System.out.println(StringUtils.arrayToString(array));
			HeapSort sf = new HeapSort();
			int[] result = sf.sort(array);
			System.out.println(StringUtils.arrayToString(result));
			
		}
		
		/**
		 * @author zjl
		 */
		public HeapSort(){
			
		}
		
		/**
		 * 注：《大话数据结构》书中的array[0]不作为数据项，而是作为数据swap的额外空间。
		 * 此处程序将array[0]仍然作为数据项排序。
		 * @param array
		 * @return
		 */
		public int[] sort(int[] array){
			int N = array.length;
			for(int i=N/2;i>=0;i--){				//初始化，循环HeapAdjust()方法N/2次。
				HeapAdjust(array, i, N-1);
			}
			
			for(int i=N-1;i>0;i--){					//从N-1项（最后一项）开始，交换，HeapAdjust()调整，共N-1次。
				swap(array, 0, i);					//注意：交换也是从第0项开始。
				HeapAdjust(array, 0, i-1);			//注意：交换后，最大无序区位置为i-1.
			}
			return array;
		}

		private void swap(int[] array, int a, int b) {
			// TODO Auto-generated method stub
			int tmp = array[a];
			array[a] = array[b];
			array[b] = tmp;
		}

		/**
		 * 从start位置开始，往下进行堆调整，直至超出length范围。
		 * @param array
		 * @param start
		 * @param length
		 */
		private void HeapAdjust(int[] array, int start, int length) {
			// TODO Auto-generated method stub
			int tmp = array[start];								//先保存start位置的值
			int i;
			for(i=2*start+1;i<=length;i=2*i+1){					//左右孩子分别为2i+1和2i+2
				if(i<length && array[i] < array[i+1]){			//指针i指向左右孩子中最大的孩子
					i++;
				}
				if(tmp > array[i]){								//start项与最大的孩子值比较，若start项更大，则比较完毕，堆调整完毕
					break;
				}
				array[start] = array[i];						//否则，start项赋值为最大孩子的值
				start = i;										//start指针指向最大孩子的位置，继续往下循环比较，直至上面完成的break或超出范围为止
			}
			array[start] = tmp;									//注意：别忘了最后把start项（此时start项指向位置和初始的不一定相同了）赋初始保存的tmp值。
		}

	}



参考资料：

* [堆排序](http://www.cnblogs.com/dolphin0520/archive/2011/10/06/2199741.html)
* 《大话数据结构》
