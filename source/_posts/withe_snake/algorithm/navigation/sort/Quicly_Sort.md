---
title: 快速排序算法
date: 2022-06-26 09:19:17
tags:
- 排序算法
- 基础
categories:
- 算法
- 排序算法
---
# 快速排序

> 算法思想：快速排序算法与其他算法相比，它的特点是数字比较和交换的次数较少，在许多情况下可以高速地进行排序。
> 
> - **第一步**：选择数列中的一个数字作为排序的基准，这个数字被称为pivot。pivot通常选择一个随机的数字，在这里我们默认选择最右边的数字作为pivot。我们把这个数字做个标记记为p。
> - **第二步**：在最左边的数字上标记为左标记，最右边的数字标记为右标记。快速排序是一种使用这些标记递归的重复一系列操作的算法。
> - **第三步**：我们将左边的标记像右移动，当左边的标记的数字超过了pivot的数字时，停止移动。之后将右标记向左移动，当右标记的数字小于pivot时停止移动。当左右侧的标记都停止时，交换这两个数字的位置。（左标记的作用是找到一个大于pivot的数字，右标记是找到小于pivot的数字，通过交换数字可以在数列的左侧收集小于pivot的数字，右侧收集大于pivot的数字）。交换之后重复第二步操作。（ 情况1:当左标记找到比pivot大的数字 停止移动，右标记开始移动并且碰到了左标记所在的位置。则将当前位置的数字和pivot位置进行交换。情况2:当左标记移动和右标记碰撞时并不会停止移动，当左标记达到数列最右边时停止移动，这意味着pivot数字在这个数列里是最大值）
> - **第四步**：因为pivot已经找到了数列中的位置，则对pivot左侧和右侧的数列 分成两部分并递归的执行前三步的步骤。（当操作的数列只有一个数字 则认为已经完成了排序）

> 时间复杂度：最坏情况为O(n^2) 最好情况O(nlog(n)) 平均情况为O(nlogn)
> 

![快速排序示意图](http://pic.xishng.top/img/202201171540228.gif)

```golang
package main

import "fmt"

/*
	快读排序
	快排的思想 就是定一个目标值 确定这个目标值的位置 之后在对目标值两侧的数组
	递归的进行上述操作
	时间复杂度是最快和平均O(nlogn)  最慢 O(n^2)
*/
func quickSort(array []int, start, end int) {
	if start < end {

		left := start
		right := end
		pivot := array[start]
		for left < right {
			for left < right && array[right] >= pivot {
				right--
			}
			if left < right {
				array[left] = array[right]
				left++
			}

			for left < right && array[left] <= pivot {
				left++
			}
			if left < right {
				array[right] = array[left]
				right--
			}
		}
		array[left] = pivot
		quickSort(array, start, left-1)
		quickSort(array, left+1, end)
	}
	fmt.Println(array, "返回排序之后的array数组")
}

func main() {
	arrayList := []int{5, 4, 7, 2, 8, 10, 8}
	quickSort(arrayList, 0, len(arrayList)-1)
}

```