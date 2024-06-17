---
title: 基数排序
date: 2022-06-26 09:19:17
tags:
- 排序算法
- 基础
categories:
- 算法
- 排序算法
---
# 基数排序

基数排序是一种对整数进行排序的排序算法，它的做法是首先对待排序列的个位进行按位插入，即是：如果一个数字的个位为1则插入1所对应的位置，其他的同理。然后对十位，一直到所有数字里面的最高位为止，这个排序才算完成。

具体的操作我用下面的示意图进行说明：

![基数排序示意图](http://pic.xishng.top/img/202201171550988.webp)

基数排序示意图

上图中我们设置了一个待排序的整数数列为 10， 1， 18， 7， 17， 23， 9， 26， 16， 8.

第一个需要设置循环次数，也就是我们上面所说的待排序列的最大数的位数。在这个例子里面是2.

然后进行按位插入，譬如10的个位为0，则插入到index=0 的那一行；1的个位为1，插入到index=1的那一行；18的个位为8，插入到index=8的那一行； ...... 以此类推，得到一个中间数组如上图中 中间的那个绿色的数组。然后进行第二次按位插入，譬如10的十位为1，则插入到index=1的位置；1的十位为0，插入到index=0的为止，以此类推，循环终止，得到最终的排序完毕的结果。

下面是golang的代码实现：

```golang
func RadixSort(nums []int) []int  {
	numberBit := howManyBit(maximum(nums))
	// 循环的次数
	// 定义一个rec 二维切片 rec[i][x] 用来接受尾数是 i的数字

	for i := 0; i < numberBit; i++ {
		rec := make([][]int, 10)

		for _, num := range nums {
			rec[(num/pow10(i))%10] = append(rec[(num/pow10(i))%10], num)
		}
		// flatten the rec slice to the one dimension slice
		numsCopy := make([]int, 0)
		for j := 0; j < 10; j++ {
			numsCopy = append(numsCopy, rec[j]...)
		}
		// refresh nums，使得他变为 经过一次基数排序之后的数组
		nums = numsCopy
	}
	return nums
}

func pow10(num int) int {
	res := 1
	base := 10
	for num != 0 {
		if num&1 ==1 {
			num -= 1
			res *= base
		}
		num >>= 1
		base *= base
	}
	return res
}

func maximum(list []int) int {
	max := 0
	for _, i2 := range list {
		if i2 > max {
			max = i2
		}
	}
	return max
}

func howManyBit(number int) int  {
	count := 0
	for number != 0 {
		number = number/10
		count += 1
	}
	return count
}

func main() {
	var theArray = []int{10, 1, 18, 30, 23, 12, 7, 5, 18, 233, 144}
	fmt.Print("排序前")
	fmt.Println(theArray)
	fmt.Print("排序后")
	fmt.Println(RadixSort(theArray))
}
```