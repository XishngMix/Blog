---
title: 计数排序
date: 2022-06-26 09:19:17
tags:
- 排序算法
- 基础
categories:
- 算法
- 排序算法
---
# 计数排序

## 介绍

计数排序的核心在于将输入的数据值转化为键存储在额外开辟的数组空间中。作为一种线性时间复杂度的排序，计数排序要求输入的数据必须是有确定范围的整数。

## 基本思想

计数排序和基数排序都是非比较类排序，过程比较简单，但是计数是比较巧妙的，借助第三个数组 `countsArr`  存元素出现的频率往后累加，对应减一即可得出该元素在排序数组中的位置。

## 算法复杂度

时间复杂度为 ：O(N + max + 1)

## 排序过程

![计数排序示意图](http://pic.xishng.top/img/202201171546782.gif)

```
[UNSORTED]:      [1 4 2 4 6 9 8 2]
[DEBUG countsArr]:  [0 1 3 3 5 5 6 6 7 8]
[SORTED]:        [1 2 2 4 4 6 8 9]

```

## 实用场景

计数排序的复杂度比任何基于比较的排序都要低，不过空间开销不容忽视。

# 演示

```golang
package main

import (
	"algorithms"
	"fmt"
)

func main() {
	arr := algorithms.GetArr(5, 20)
	//arr = []int{1, 4, 2, 4, 6, 9, 8, 2}
	fmt.Println("[UNSORTED]:\\t", arr)

	max := getMax(arr)
	sortedArr := make([]int, len(arr))
	countsArr := make([]int, max+1) // max+1 是为了防止 countsArr[] 计数时溢出

	// 元素计数
	for _, v := range arr {
		countsArr[v]++
	}

	// 统计独特数字个数并累加
	for i := 1; i <= max; i++ {
		countsArr[i] += countsArr[i-1]
	}

	fmt.Println("[DEBUG countsArr]:\\t", countsArr)

	// 让 arr 中每个元素找到其位置
	for _, v := range arr {
		sortedArr[countsArr[v]-1] = v
		//fmt.Print(countsArr[v]-1, " ")
		// 保证稳定性
		countsArr[v]--
	}
	//fmt.Println()
	fmt.Println("[SORTED]:\\t", sortedArr)
}

func getMax(arr []int) (max int) {
	max = arr[0]
	for _, v := range arr {
		if max < v {
			max = v
		}
	}
	return
}

```