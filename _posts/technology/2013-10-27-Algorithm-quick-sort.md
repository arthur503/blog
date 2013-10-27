---
layout: default
title: 算法：快速排序
---
快速排序Java代码如下，需注意问题见代码注释：

	package com.arthur.sort;

	import com.arthur.utils.StringUtils;

	public class FastSort {
		
		public static void main(String[] argv){
			int[] array = {10,2,1,34,21,13,10,5,100};
			System.out.println(StringUtils.arrayToString(array));
			FastSort sf = new FastSort();
			int[] result = sf.sort(array, 0, array.length-1);
			System.out.println(StringUtils.arrayToString(result));
		}
		
		public FastSort(){
			
		}
		
		public int[] sort(int[] array, int low, int high){
			if(low < high){
				int mid = partition(array, low, high);		//先分成左右两组
				sort(array, low, mid-1);					//左组排序
				sort(array, mid+1, high);					//右组排序
			}
			return array;
		}

		private int partition(int[] array, int low, int high) {
			// TODO Auto-generated method stub
			int pointer = low;								//注意：pointer初始指向为low
			int key = array[pointer];						//选定初始值key	
			while(low < high){
				while(array[high] > key && low < high){		//从右组逐个判断直至找到小于key的位置
					high --;
				}
				array[pointer] = array[high]; 				//对应位置的值移动
				pointer = high;
	//			System.out.println("1:"+StringUtils.arrayToString(array));
				
				while(array[low] <= key && low < high){		//从左组逐个判断直至找到大于key的位置
					low ++;
				}
				array[pointer] = array[low];				//对应位置的值移动
				pointer = low;				
	//			System.out.println("2:"+StringUtils.arrayToString(array));
			}
			array[pointer] = key;
	//		System.out.println("3:"+StringUtils.arrayToString(array)+"\n");
			return low;										//返回对应的pointer的位置
		}
		
		
	}
