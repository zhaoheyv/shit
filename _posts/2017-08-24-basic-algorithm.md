---
layout: post
title : 一些基础算法练习
author: 赵何宇
---

## 排序
### 快速排序
#### 原理：
> 1.Choose a pivot value.

> 2.Partition. 

> 3.Sort both parts.

#### 实现：
	int quicksort(int arry[], int left, int right){
	if(left<right)
	{
		int left_index = left;
		int right_index = left + 1;
		int target = arry[left];
		int temp;
		while(right_index <= right){
		    if(arry[right_index] < target){
		        temp = arry[right_index];
		        arry[right_index] = arry[left_index + 1];
		        arry[left_index + 1] = temp;
		        left_index ++;
		    }
		    right_index ++;
		}
		temp = arry[left_index];
		arry[left_index] = target;
		arry[left] = temp;
		quicksort(arry, left, left_index - 1);
		quicksort(arry, left_index + 1, right);
        }


### MergeSort
#### 原理：

#### 实现：
` 
   void mergeSort(int arry[], int length){
	int index_x = 0;
	int index_y = length/2;
	int index_m =0;
	int merge_arry[];

	if(index_y != index_x){
		while((index_x < (length/2)) && (index_y < length)){
			if (arry[index_x] < arry[index_y]){
				merge_arry[index_m] = arry[index_x];
				index_x ++;
			}
			else{
				merge_arry[index_m] = arry[index_y];
				index_y ++;
			}
			index_m ++;
		}
		while(index_x < length/2){
			merge_arry[index_m] = arry[index_x];
			index_x ++;
			index_m ++;
		}
		while(index_y < length){
			merge_arry[index_m] = arry[index_y];
			index_y ++;
			index_m ++;
		}	
	}
}
`
