---
title: 红黑树查找
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
# 红黑树查找

> 红黑树查找：
> 
> 
> **它一种特殊的二叉查找树。红黑树的每个节点上都有存储位表示节点的颜色，可以是红(Red)或黑(Black)。红黑树是2-3树的一种简单高效的实现。**
> 
> **红黑树的特性**:
> 
> > （1）每个节点或者是黑色，或者是红色。（2）根节点是黑色。（3）每个叶子节点（NIL）是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]（4）如果一个节点是红色的，则它的子节点必须是黑色的。（5）从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。
> > 
> 
> 如图所示:
> 
> ![红黑树的特性](http://pic.xishng.top/img/202201171142665.png)
> 
> **基本思路:** 红黑树的基本操作是**添加、删除**。在对红黑树进行添加或删除之后，都会用到旋转方法。为什么呢？道理很简单，添加或删除红黑树中的节点之后，红黑树就发生了变化，可能不满足红黑树的5条性质，也就不再是一颗红黑树了，而是一颗普通的树。而通过旋转，可以使这颗树重新成为红黑树。简单点说，旋转的目的是让树保持红黑树的特性。
> 
> 旋转包括两种：**左旋** 和 **右旋**。
> 
> > **左旋示意图：**
> > 
> > 
> > ![左旋示意图](http://pic.xishng.top/img/202201171142147.png)
> > 
> 
> > **完整左旋示意图:**
> > 
> > 
> > ![完整左旋示意图](http://pic.xishng.top/img/202201171143136.png)
> > 
> 
> > **右旋示意图：**
> > 
> > 
> > ![右旋示意图](http://pic.xishng.top/img/202201171143565.png)
> > 
> 
> > **完整右旋示意图：**
> > 
> > 
> > ![完整右旋示意图](http://pic.xishng.top/img/202201171143373.png)
> > 
> 
> **无论是左旋还是右旋，被旋转的树，在旋转前是二叉查找树，并且旋转之后仍然是一颗二叉查找树。(二叉查找树：左子 < 根 < 右子)**
> 
> ![二叉查找树：左子 < 根 < 右子](http://pic.xishng.top/img/202201171143843.png)
> 
> - 可见：
>     - 左旋中的“左”，意味着“被旋转的节点将变成一个左节点”；
>     - 右旋中的“右”，意味着“被旋转的节点将变成一个右节点”。
> 
> ## 节点的插入：
> 
> > 将红黑树当作一颗二叉查找树，将节点插入将节点着色为红色通过旋转和重新着色等方法来修正该树，使之重新成为一颗红黑树。
> > 
> 
> 为什么着色成红色，而不是黑色呢？
> 
> > 在回答之前，我们需要重新温习一下红黑树的特性：
> > 
> > 
> > > (1) 每个节点或者是黑色，或者是红色。
> > (2) 根节点是黑色。
> > (3) 每个叶子节点是黑色。 [注意：这里叶子节点，是指为空的叶子节点！]
> > (4) 如果一个节点是红色的，则它的子节点必须是黑色的。
> > (5) 从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。
> > > 
> > 
> > 将插入的节点着色为红色，不会违背"特性(5)"！少违背一条特性，就意味着我们需要处理的情况越少。接下来，就要努力的让这棵树满足其它性质即可；满足了的话，它就又是一颗红黑树了。
> > 
> 
> **将插入节点着色为"红色"之后，不会违背"特性(5)"。那它到底会违背哪些特性呢？**
> 
> > 对于"特性(1)"，显然不会违背了。因为我们已经将它涂成红色了。
>  对于"特性(2)"，显然也不会违背。在第一步中，我们是将红黑树当作二叉查找树，然后执行的插入操作。而根据二叉查找数的特点，插入操作不会改变根节点。所以，根节点仍然是黑色。
> 对于"特性(3)"，显然不会违背了。这里的叶子节点是指的空叶子节点，插入非空节点并不会对它们造成影响。
> 对于"特性(4)"，是有可能违背的！
> > 
> 
> **那接下来，想办法使之"满足特性(4)"**，就可以将树重新构造成红黑树了。
>  
>  代码如下：
> 

```golang
const (
	RED   = true
	BLACK = false
)

// 节点
type RBNode struct {
	Color               bool // true == 红  false == 黑
	Parent, Left, Right *RBNode
	Value, Index        int
}

type RBTree struct {
	Root *RBNode
}

/*
* 对红黑树的节点(x)进行左旋转
*
* 左旋示意图(对节点 x 进行左旋)：
*      px                              px
*     /                               /
*    x                               y
*   /  \\      --(左旋)-.           / \\                #
*  lx   y                          x  ry
*     /   \\                       /  \\
*    ly   ry                     lx  ly
*
*
*/
func (t *RBTree) leftSpin(node *RBNode) {
	// 先提出自己的 右子
	y := node.Right

	// 自己的新右子 是前右子的左子
	node.Right = y.Left

	if nil != y.Left {
		y.Left.Parent = node
	}

	// 你以前的爹，就是我现在的爹
	y.Parent = node.Parent

	// 如果被旋转的节点是 之前树的根
	// 那么，新的跟就是 y 节点
	if nil == node.Parent {
		t.Root = y
	} else { // 被旋转的是普通节点时, 需要结合自身的父亲来确认自己之前是属于 左子还是右子
		if node.Parent.Left == node { // 被旋转节点之前是 左子时
			// 用 y 来作为之前 该节点父亲的 新左子
			node.Parent.Left = y
		} else { // 否则，是 右子
			node.Parent.Right = y
		}
	}

	// 将 node 设为 y 的左子
	y.Left = node
	// 将 y 设为 node 的新父亲
	node.Parent = y
}

/*
 * 对红黑树的节点(y)进行右旋转
 *
 * 右旋示意图(对节点 y 进行左旋)：
 *            py                               py
 *           /                                /
 *          y                                x
 *         /  \\      --(右旋)-.            /  \\                     #
 *        x   ry                           lx   y
 *       / \\                                   / \\                   #
 *      lx  rx                                rx  ry
 *
 */
func (t *RBTree) rightSpin(node *RBNode) {
	// 先提出自己的 左子
	x := node.Left
	node.Left = x.Right

	if nil != x.Left {
		x.Right.Parent = node
	}

	x.Parent = node.Parent

	// 如果被旋转的节点是 之前树的根
	// 那么，新的跟就是 x 节点
	if nil == node.Parent {
		t.Root = x
	} else {

		if node.Parent.Right == node {
			node.Parent.Right = x
		} else {
			node.Parent.Left = x
		}
	}

	x.Right = node

	node.Parent = x
}

func insertValue(tree *RBTree, val, index int) {
	node := &RBNode{Value: val, Index: index, Color: BLACK}
	if nil == tree.Root {
		tree.Root = node
	}else{
		tree.insert(node)
	}
}

func (t *RBTree) insert(node *RBNode) {
	//int cmp;
	var tmpNode *RBNode
	root := t.Root

	// 1. 将红黑树当作一颗二叉查找树，将节点添加到二叉查找树中。
	for nil != root {
		if node.Value < root.Value {
			root = root.Left
		} else {
			root = root.Right
		}
		tmpNode = root
	}

	node.Parent = tmpNode
	if nil != tmpNode {

		if node.Value < tmpNode.Value {
			tmpNode.Left = node
		} else {
			tmpNode.Right = node
		}
	} else {
		t.Root = node
	}

	// 2. 设置节点的颜色为红色
	node.Color = RED

	// 3. 将它重新修正为一颗二叉查找树
	t.adjustRBTree(node)
}

// 修正树
func (t *RBTree) adjustRBTree(node *RBNode) {
	var parent, gparent *RBNode // 父亲 和 祖父

	// 若“父节点存在，并且父节点的颜色是红色”
	for nil != node.Parent && RED == node.Parent.Color {
		parent = node.Parent
		gparent = parent.Parent

		//若“父节点”是“祖父节点的左孩子”
		if parent == gparent.Left {
			// Case 1条件：叔叔节点是红色
			uncle := gparent.Right
			if nil != uncle && RED == uncle.Color {
				uncle.Color = BLACK
				parent.Color = BLACK
				gparent.Color = RED
				node = gparent
				continue
			}

			// Case 2条件：叔叔是黑色，且当前节点是右孩子
			if node == parent.Right {
				var tmp *RBNode
				t.leftSpin(parent)
				tmp = parent
				parent = node
				node = tmp
			}

			// Case 3条件：叔叔是黑色，且当前节点是左孩子。
			parent.Color = BLACK
			gparent.Color = RED
			t.rightSpin(gparent)
		} else { //若“z的父节点”是“z的祖父节点的右孩子”
			// Case 1条件：叔叔节点是红色
			uncle := gparent.Left
			if nil != uncle && RED == uncle.Color {
				uncle.Color = BLACK
				parent.Color = BLACK
				gparent.Color = RED
				node = gparent
				continue
			}

			// Case 2条件：叔叔是黑色，且当前节点是左孩子
			if node == parent.Left {
				var tmp *RBNode
				t.rightSpin(parent)
				tmp = parent
				parent = node
				node = tmp
			}

			// Case 3条件：叔叔是黑色，且当前节点是右孩子。
			parent.Color = BLACK
			gparent.Color = RED
			t.leftSpin(gparent)
		}
	}
	// 将根节点设为黑色
	t.Root.Color = BLACK
}

/**
红黑树查找
 */
func RedBlackTreeSearch(arr []int, key int) int{
	// 先构造树
	tree := new(RBTree)
	for i, v := range arr {
		insertValue(tree, v, i)
	}
	// 开始二叉树查找目标key
	return tree.serch(key)
}

func (t *RBTree) serch(key int) int {
	return serch(t.Root, key)
}
func serch(node *RBNode, key int) int {
	if nil == node {
		return -1
	}
	if key < node.Value {
		return serch(node.Left, key)
	}else if key > node.Value {
		return serch(node.Right, key)
	}else {
		return node.Index
	}
}

```