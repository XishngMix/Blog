---
title: 选择排序算法
date: 2022-06-26 09:19:17
tags:
- 排序算法
- 基础
categories:
- 算法
- 排序算法
---
# 选择排序

> 算法思想：线性搜索数列并找到最小值。将最小值替换为数列中最左端的数字并进行排序。如果最小值已经在最左端则不进行操作。重复此操作直到数列排序完成。
简单来说就是每次找到数列的最小值并和最左端还没确定的值进行替换。也可以找最大值和最右端没确定的值替换。 如下图所示
> 

> 时间复杂度：最坏时间复杂度：O(n^2)。
> 

![选择排序示意图](http://pic.xishng.top/img/202201171538702.gif)

```golang
package main

import "fmt"

func main() {
    numbers := []int{6, 2, 7, 5, 8, 9}
    SelectSort(numbers)

    fmt.Println(numbers)
}

func SelectSort(values []int) {
    length := len(values)
    if length <= 1 {
        return
    }

    for i := 0; i < length; i++ {
        min := i // 初始的最小值位置从0开始，依次向右

        // 从i右侧的所有元素中找出当前最小值所在的下标
        for j := length - 1; j > i; j-- {
            if values[j] < values[min] {
                min = j
            }
        }
        //fmt.Printf("i:%d min:%d\\n", i, min)

        // 把每次找出来的最小值与之前的最小值做交换
        values[i], values[min] = values[min], values[i]

        //fmt.Println(values)
    }
}

```