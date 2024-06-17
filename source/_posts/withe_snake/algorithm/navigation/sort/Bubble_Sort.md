---
title: 冒泡排序算法
date: 2022-06-26 09:19:17
tags:
- 排序算法
- 基础
categories:
- 算法
- 排序算法
---
# 冒泡排序

> 方法的原理：冒泡排序算法清楚地演示了排序是如何工作的，同时又简单易懂。冒泡排序步骤遍历列表并比较相邻的元素对。如果元素顺序错误，则交换它们。重复遍历列表未排序部分的元素，直到完成列表排序。
简单点来讲冒泡排序算法主要思想就是每一次确定一个值。如果是从左开始比较就是通过两两比较找到最大值。如果是从右开始比较就是两两比较找到最小值。 如下图所示
> 

> 时间复杂度：因为冒泡排序重复地通过列表的未排序部分，所以它具有最坏的情况复杂度O(n^2)。
> 

![冒泡排序示意图](http://pic.xishng.top/img/202201171537392.gif)

```golang
package main

import "fmt"

func main() {
    values := []int{5,6,3,1,8,7,2,4}
    fmt.Println(values)
    BubbleAsort(values)
    BubbleZsort(values)
}

func BubbleAsort(values []int) {
    for i := 0; i < len(values)-1; i++ {
        for j := i+1; j < len(values); j++ {
            if  values[i]>values[j]{
                values[i],values[j] = values[j],values[i]
            }
        }
    }
    fmt.Println(values)
}

func BubbleZsort(values []int) {
    for i := 0; i < len(values)-1; i++ {
        for j := i+1; j < len(values); j++ {
            if  values[i]<values[j]{
                values[i],values[j] = values[j],values[i]
            }
        }
    }
    fmt.Println(values)
}

```