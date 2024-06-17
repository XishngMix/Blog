---
title: 二分查找算法
date: 2022-06-26 09:19:17
tags:
- 查找算法
- 基础
categories:
- 算法
- 查找算法
- 通用查找
---
# 二分查找

**二分查找法**，在一个**有序的数列**里，把中间元素v与查找元素target相比较：

- 若相等则返回
- 若大于target则在v的右端继续使用二分查找法
- 若小于target则在v的左端继续使用二分查找法

![示意图](http://pic.xishng.top/img/202201171718949.png)

示例代码如下：

```golang
package main

import "fmt"

//二分查找（前提必须在有序的数组里，查找target）
//如果找到target，返回相应的索引index
//如果没有找到target，返回-1
//时间复杂度O(logN)

func binarySearch(arr *[]int, target int, l int, r int) int {
	//在arr[l...r]中查找target
	for l <= r {
		//middleIndex := r+l/2 //注意：这里容易产生bug（r+l溢出int最大值）,改写成如下方式
		middleIndex := l + (r-l)/2
		if (*arr)[middleIndex] == target {
			return middleIndex
		}else if (*arr)[middleIndex] > target {
			//在arr[l...middleIndex - 1]中查找target
			r = middleIndex - 1
		}else {
			//在arr[middleIndex + 1...r]中查找target
			l = middleIndex + 1
		}
	}
	return -1
}

func main() {
	arr := []int{1,2,5,8,11,13}
	index := binarySearch(&arr, 11,0, len(arr)-1)
	fmt.Println(index)
}
```