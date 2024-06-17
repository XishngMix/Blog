---
title: 插入排序算法
date: 2022-06-26 09:19:17
tags:
- 排序算法
- 基础
categories:
- 算法
- 排序算法
---
# 插入排序算法

> 算法思想：左端的数字已经完成排序。取出那些尚未操作的最左端的数字将这个数字与已经完成排序的数字进行比较如果左边的数字较大则交换两个数字的位置。重复此操作直到出现一个比当前较小的数字或者数字已经到达最左端。 如下图所示
> 

> 时间复杂度：最坏情况下时间复杂度为O(n^2)。最好情况下为O(n)
> 

![插入排序示意图](https://pic.xishng.top/img/202201171539001.gif)

```golang
package main

import "fmt"

func main() {
    numbers := []int{6, 2, 7, 3, 8, 5}
    InsertSort(numbers)

    fmt.Println(numbers)
}

func InsertSort(values []int) {
    length := len(values)
    if length <= 1 {
        return
    }

    for i := 1; i < length; i++ {
        tmp := values[i] // 从第二个数开始，从左向右依次取数
        key := i - 1     // 下标从0开始，依次从左向右

        // 每次取到的数都跟左侧的数做比较，如果左侧的数比取的数大，就将左侧的数右移一位，直至左侧没有数字比取的数大为止
        for key >= 0 && tmp < values[key] {
            values[key+1] = values[key]
            key--
            //fmt.Println(values)
        }

        // 将取到的数插入到不小于左侧数的位置
        if key+1 != i {
            values[key+1] = tmp
        }
        //fmt.Println(values)
    }
}

```