---
title: 归并排序算法
date: 2022-06-26 09:19:17
tags:
- 排序算法
- 基础
categories:
- 算法
- 排序算法
---
# 归并排序算法

> 算法思想：
> 
> - **第一步**：将数字分成两半，重复此步骤直到每个数字成为一部分。
> - **第二步**：合并数字，合并时按照数字的升序移动，使得合并后的数字在组内按升序排列。递归的重复组的合并操作，直到所有数字都在一个组中。

> 时间复杂度：O(nlogn)
> 

![归并排序示意图](http://pic.xishng.top/img/202201171542031.gif)

```golang
package main

import "fmt"

func main() {
	// 生成排序队列
	length := 20
	A := make([]int, length)
	j := length
	for i := 0; i < length; i++ {
		A[i] = j
		j--
	}
	// 打印输出前的队列
	fmt.Println("排序前", A)

	//进行排序
	MergeSort(A, 0, length-1)

	// 打进排序后的队列
	fmt.Println("排序后", A)
}

/*
MergeSort 归并排序
*/
func MergeSort(Arrary []int, left int, right int) {
	if left < right {
		median := 0
		if (right-median+1)%2 == 0 {
			median = (left + right - 1) / 2
		} else {
			median = (left + right) / 2
		}

		MergeSort(Arrary, left, median)
		MergeSort(Arrary, median+1, right)
		Merge(Arrary, left, median, right)
	}
}

/*
Merge 两列已排序的数组合并
*/
func Merge(Arrary []int, left int, median int, right int) {
	n1 := median - left + 1
	n2 := right - median

	L_Arr := make([]int, n1+1)
	R_Arr := make([]int, n2+1)

	for i := 0; i < n1; i++ {
		L_Arr[i] = Arrary[left+i]
	}
	L_Arr[n1] = 1000

	for i := 0; i < n2; i++ {
		R_Arr[i] = Arrary[median+1+i]
	}
	R_Arr[n2] = 1000
	leftNum := 0
	rightNum := 0
	for i := left; i < right+1; i++ {
		if L_Arr[leftNum] < R_Arr[rightNum] {
			Arrary[i] = L_Arr[leftNum]
			leftNum++
		} else {
			Arrary[i] = R_Arr[rightNum]
			rightNum++
		}
	}
}

```