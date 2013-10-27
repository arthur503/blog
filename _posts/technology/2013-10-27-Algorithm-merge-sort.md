---
layout: default
title: 算法：归并排序
---
归并排序Java代码如下，需注意问题见代码注释：

	package com.arthur.sort;

	import com.arthur.utils.Log;
	import com.arthur.utils.StringUtils;

	public class MergeSort {

		public static void main(String[] argv){
			int[] array = {10,2,1,34,21,13,10,5,100};
			System.out.println(StringUtils.arrayToString(array));
			MergeSort sf = new MergeSort();
			int[] result = sf.sort(array, 0, array.length-1);
			System.out.println(StringUtils.arrayToString(result));
			
		}
		
		public MergeSort(){
			
		}
		
		public int[] sort(int[] array, int low, int high){
			System.out.println("Sort array. low:"+low+" high:"+high);//+StringUtils.arrayToString(array));
			
			if(high-low <= 0){
				return array; 
			}
			if(high - low == 1){
				if(array[low] <= array[high]){
					return array;
				}
				int tmp = array[low];
				array[low] = array[high];
				array[high] = tmp;
				System.out.println("Sort result:"+StringUtils.arrayToString(array));
			}

			
			if(high-low > 1){
				int mid = (low + high)/2;
				sort(array, low, mid);
				sort(array, mid+1, high);
				merge(array, low, mid, high);
			}
			
			return array;
		}

		private void merge(int[] array, int low, int mid, int high) {
			// TODO Auto-generated method stub
			System.out.println("Merge array. low:"+low+" mid:"+mid+" high:"+high);
			
			int pointer1 = low;
			int pointer2 = mid+1;
			int p=low;														//array pointer. from low to high.
			int[] newArray = copy(array);
			while(pointer1 <= mid && pointer2 <= high){
				array[p++] = (newArray[pointer1] < newArray[pointer2]) ? newArray[pointer1++] : newArray[pointer2++];
			}
			
			//注意：下面条件为(pointer1 > mid)而非(pointer1 == mid).
			//因为上面完毕之后，可能p1==mid && p2==high，且仍有一个未被赋值。
			int newPointer = (pointer1 > mid) ? pointer2 : pointer1;		
			for(;p<=high;){
				array[p++] = newArray[newPointer++];
			}

			System.out.println("Merge result:"+StringUtils.arrayToString(array));
			
		}

		private int[] copy(int[] array) {
			// TODO Auto-generated method stub
			int[] result = new int[array.length];
			for(int i=0;i<result.length;i++){
				result[i] = array[i];
			}
			return result;
		}
		
	}

