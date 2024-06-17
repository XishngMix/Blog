---
title: 平衡二叉树查找算法
date: 2022-06-26 09:19:17
tags:
- 查找算法
- 二叉树
- B树
categories:
- 算法
- 查找算法
- 树查找
---
# 平衡二叉树

**树**是一种计算机数据结构中非常常用的一种结构，其中就包含了：平衡二叉树，这种树是一种特殊的二叉查找树(二叉查找树也就是，右孩子大于其父结点，左孩子小于其父结点的树)，但是简单的二叉查找树存在的问题就是不平衡，最差的查找效率为O(n)，故就有人发明了一种平衡的额二叉查找树。

### 特点

平衡二叉树是一种二叉查找树
每个结点的左子树的高度减去右子树的高度的绝对值不超过1
空树和左右子树都是平衡二叉树
相比红黑树，平衡二叉树比较适用于没有删除的情况

### 平衡因子

平衡二叉树是在二叉查查找树的基础上进行构建了，为了维持平衡二叉树的平衡，那么就需要一种机制来判断平衡二叉树是否是平衡的。这种机制就叫做平衡因子。

平衡二叉树的每个结点都会维持一个值，这个值就是平衡因子，这个平衡因子就是这个结点的左子树的高度减去右子树的高度得到的值。如果这个平衡因子的值的绝对值大于1了，说明这个树就不平衡了，那么就需要调整树的结构了。

我们可以从如上这个这个图中看的到：每个结点都维持了一个值，比如左边的AVL 树根结点的值为-1，这个-1是怎么来的呢，就是结点3的左子树的高度为 2, 右子树的高度为 3, 左子树的高度减去右子树的高度就得到这个结点的平衡因子。

如果某个结点的平衡因子的绝对值大于了 1 ，那么就说明这个平衡二叉树不平衡了，就需要调整平衡二叉树的结构。

![平衡因子](http://pic.xishng.top/img/202201171140140.png)

## 旋转

由于AVL树需要做到平衡，所以每次插入叶子节点，如果发现不平衡，都需要进行旋转以保持平衡。

下面是旋转图例，可以参考的看下：

![旋转](http://pic.xishng.top/img/202201171141598.png)

```golang
package main

import "fmt"

type AVL struct {
	value  int  //值
	height int  //深度
	left   *AVL //左子树
	right  *AVL //右子树
}

// Search 查找元素
func (t *AVL) Search(value int) bool {
	if t == nil {
		return false
	}

	if value < t.value {
		return t.left.Search(value)
	} else if value > t.value {
		return t.right.Search(value)
	} else {
		return true
	}
}

// Insert 添加元素
func (t *AVL) Insert(value int) *AVL {
	if t == nil {
		newNode := AVL{value, 1, nil, nil}
		return &newNode
	}
	if value < t.value {
		t.left = t.left.Insert(value)
		t = t.adjust()
	} else if value > t.value {
		t.right = t.right.Insert(value)
		t = t.adjust()
	} else {
		fmt.Println("the node exit")
	}
	t.height = max(t.left.getHeight(), t.right.getHeight()) + 1
	return t
}

/*
Delete
	删除元素
		*1、如果被删除结点只有一个子结点，就直接将A的子结点连至A的父结点上，并将A删除
		*2、如果被删除结点有两个子结点，将该结点右子数内的最小结点取代A。
		*3、查看是否平衡, 如果不平衡，就调整
*/
func (t *AVL) Delete(value int) *AVL {
	if t == nil {
		return t
	}
	compare := value - t.value
	if compare < 0 {
		t.left = t.left.Delete(value)
	} else if compare > 0 {
		t.right = t.right.Delete(value)
	} else { //找到结点,删除结点（）
		if t.left != nil && t.right != nil {
			t.value = t.right.getMin()
			t.right = t.right.Delete(t.value)
		} else if t.left != nil {
			t = t.left
		} else { //只有一个右孩子或没孩子
			t = t.right
		}
	}
	if t != nil {
		t.height = max(t.left.getHeight(), t.right.getHeight()) + 1
		t = t.adjust()
	}
	return t
}

// adjust 判断是否需要旋转
func (t *AVL) adjust() *AVL {
	if t.right.getHeight()-t.left.getHeight() == 2 {
		if t.right.right.getHeight() > t.right.left.getHeight() {
			t = t.leftRotate()
		} else {
			t = t.rightThenLeftRotate()
		}
	} else if t.left.getHeight()-t.right.getHeight() == 2 {
		if t.left.left.getHeight() > t.left.right.getHeight() {
			t = t.rightRotate()
		} else {
			t = t.LeftThenRightRotate()
		}
	}
	return t
}

// leftRotate 左旋
func (t *AVL) leftRotate() *AVL {
	headNode := t.right
	t.right = headNode.left
	headNode.left = t

	//更新结点高度
	t.height = max(t.left.getHeight(), t.right.getHeight()) + 1
	headNode.height = max(headNode.left.getHeight(), headNode.right.getHeight()) + 1
	return headNode
}

// rightRotate 右旋
func (t *AVL) rightRotate() *AVL {
	headNode := t.left
	t.left = headNode.right
	headNode.right = t

	//更新结点高度
	t.height = max(t.left.getHeight(), t.right.getHeight()) + 1
	headNode.height = max(headNode.left.getHeight(), headNode.right.getHeight()) + 1
	return headNode
}

// rightThenLeftRotate 右旋转,之后左旋转
func (t *AVL) rightThenLeftRotate() *AVL {
	//以失衡点右结点先右旋转
	sonHeadNode := t.right.rightRotate()
	t.right = sonHeadNode

	//再以失衡点左旋转
	return t.leftRotate()
}

// LeftThenRightRotate 左旋转,之后右旋转
func (t *AVL) LeftThenRightRotate() *AVL {
	//以失衡点左结点先左旋转
	sonHeadNode := t.left.leftRotate()
	t.left = sonHeadNode
	//再以失衡点左旋转
	return t.rightRotate()
}

// getAll 按顺序获得树中元素
func (t *AVL) getAll() []int {
	values := []int{}
	return addValues(values, t)
}

// addValues 将一个节点加入切片中
func addValues(values []int, t *AVL) []int {
	if t != nil {
		values = addValues(values, t.left)
		values = append(values, t.value)
		fmt.Println(t.value, t.height)
		values = addValues(values, t.right)
	}
	return values
}

// getMin 查找子树最小值
func (t *AVL) getMin() int {
	if t == nil {
		return -1
	}
	if t.left == nil {
		return t.value
	} else {
		return t.left.getMin()
	}
}

// getMax 查找子树最大值
func (t *AVL) getMax() int {
	if t == nil {
		return -1
	}
	if t.right == nil {
		return t.value
	} else {
		return t.right.getMax()
	}
}

// getMinNode 查找最小结点
func (t *AVL) getMinNode() *AVL {
	if t == nil {
		return nil
	} else {
		for t.left != nil {
			t = t.left
		}
	}
	return t
}

// getMaxNode 查找最大结点
func (t *AVL) getMaxNode() *AVL {
	if t == nil {
		return nil
	} else {
		for t.right != nil {
			t = t.right
		}
	}
	return t
}

// getHeight 得到树高
func (t *AVL) getHeight() int {
	if t == nil {
		return 0
	}
	return t.height
}

// max 最深节点高度
func max(a int, b int) int {
	if a > b {
		return a
	} else {
		return b
	}
}

func main() {
	bsTree := AVL{100, 1, nil, nil}
	newTree := bsTree.Insert(60)
	newTree = bsTree.Insert(120)
	newTree = bsTree.Insert(110)
	newTree = bsTree.Insert(130)
	newTree = bsTree.Insert(105)
	fmt.Println(newTree.getAll())

	newTree.Delete(110)
	fmt.Println(newTree.getAll())
}

```