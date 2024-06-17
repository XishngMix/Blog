---
title: 插值查找算法
date: 2022-06-26 09:19:17
tags:
- 查找算法
- 基础
categories:
- 算法
- 查找算法
- 通用查找
---
# 差值查找算法

> 折半查找的进化版，自适应中间值 根据 (关键值 - 起始值) / (末位值 - 起始值) 的比例来决定中间值的下标，这样能够快速的缩小查找范围，会比直接折半好很多
> 

> 适用场景：
> 
> 
> > 有序的，非动态的序列
> > 

```golang
func insertSearch(arr []int, key int) int{
	low  := 0
	high := len(arr) - 1
	time := 0
	for low < high {
		time += 1
		// 计算mid值是插值算法的核心代码 实际上就是
		mid := low + int((high - low) * (key - arr[low])/(arr[high] - arr[low]))
		if key < arr[mid] {
			high = mid - 1
		}else if key > arr[mid] {
			low = mid + 1
		}else {
			return mid
		}
	}
	return -1
}

```