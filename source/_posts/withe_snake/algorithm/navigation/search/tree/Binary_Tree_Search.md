---
title: 二叉树查找
date: 2022-06-26 09:19:17
tags:
- 查找算法
- 二叉树
- 树
categories:
- 算法
- 查找算法
- 树查找
---
# 二叉树查找

> 二叉查找树是先对待查找的数据进行生成树，确保树的左分支的值小于右分支的值，然后再用所查数据和每个节点的父节点比较大小，查找最适合的范围。 这个算法的查找效率很高，但是如果使用这种查找方法要首先创建树。
> 
> 
> 适用场景：
> 
> > 前面的几种查找算法因为都是适用于有序集，在插入和删除操作上就需要耗费大量的时间。有没有一种既可以使得插入和删除效率不错，又可以比较高效的实现查找的算法。
> > 
> > 
> > **二叉查找树**（BinarySearch Tree，也叫二叉搜索树，或称二叉排序树Binary Sort Tree）或者是一棵空树，或者是具有下列性质的二叉树：
> > 
> > > 1）若任意节点的左子树不空，则左子树上所有结点的值均小于它的根结点的值；
> > > 
> > > 
> > > 2）若任意节点的右子树不空，则右子树上所有结点的值均大于它的根结点的值；
> > > 
> > > 3）任意节点的左、右子树也分别为二叉查找树。
> > > 
> > > - *二叉查找树性质：对二叉查找树进行中序遍历，即可得到有序的数列*。
> 
> 如代码：
> 

```golang
/**
基本思路：先把数组构造出一颗二叉树的样纸，然后查找的时候在从root往下对比
 */
func BSTsearch(arr []int, key int) int{
	// 先在内存中构造 二叉树
	tree := new(Tree)
	for i, v := range arr {
		Insert(tree, v, i)
	}
	// 开始二叉树查找目标key
	return searchKey(tree.Root, key)
}

// 节点结构
type Node struct {
	Value, Index int  // 元素的值和在数组中的位置
	Left, Right *Node
}

// 树结构
type Tree struct {
	Root *Node
}

// 把数组的的元素插入树中
func Insert(tree *Tree, value, index int){
	if nil == tree.Root {
		tree.Root = newNode(value, index)
	}else {
		InsertNode(tree.Root, newNode(value, index))
	}
}

// 把新增的节点插入树的对应位置
func InsertNode(root, childNode *Node) {
	// 否则，先和根的值对比
	if childNode.Value <= root.Value {
		// 如果小于等于跟的值，则插入到左子树
		if  nil == root.Left {
			root.Left = childNode
		}else {
			InsertNode(root.Left, childNode)
		}
	}else{
		// 否则，插入到右子树
		if nil == root.Right {
			root.Right = childNode
		}else {
			InsertNode(root.Right, childNode)
		}
	}
}

func newNode(value, index int) *Node {
	return &Node{
		Value: value,
		Index: index,
	}
}
// 在构建好的二叉树中，从root开始往下查找对应的key 返回其在数组中的位置
func searchKey(root *Node, key int) int {
	if nil == root {
		return -1
	}
	if  key == root.Value {
		return root.Index
	}else if key < root.Value {
		// 往左子树查找
		return searchKey(root.Left, key)
	}else {
		// 往右子树查找
		return searchKey(root.Right, key)
	}
}

```