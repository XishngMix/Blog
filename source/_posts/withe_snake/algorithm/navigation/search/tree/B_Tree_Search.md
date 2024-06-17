---
title: B树查找算法
date: 2022-06-26 09:19:17
tags:
- 查找算法
- B树
categories:
- 算法
- 查找算法
- 树查找
---
# B树查找

# 背景说明：

> 二叉树在数据量庞大的时候，树的深度难以想象，造成了磁盘的IO读取性能急剧衰减，为了提高在大数据领域和数据库方面的适用性，提出了多路数，每个节点存储多个数据，同时可以存在多个子节点，这样树的层数得到了极大的缩减
> 

## 多路数 - B树

### 一、B树的定义

> 条件：
> 
> 
> > a、根节点至少有2个子节点
> > 
> > 
> > b、每个中间节点都包含k-1个元素和k个孩子，其中 m/2 <= k <= m
> > 
> > c、每一个叶子节点都包含k-1个元素，其中 m/2 <= k <= m
> > 
> > d、所有的叶子结点都位于同一层
> > 
> > e、每个节点中的元素从小到大排列，节点当中k-1个元素正好是k个孩子包含的元素的值域分划
> > 

### 二、B树的插入操作

插入操作是指插入一个关键字（也可以是一个复杂的自定义结构体）。如果B树中已存在需要插入的关键字，则不再插入。若B树不存在这个关键字,则一定是在叶子结点中进行插入操作。

1、根据要插入的关键字的值，找到叶子结点并插入。

2、判断当前结点关键字的个数是否小于等于m-1，若满足则结束，否则进行第3步。

3、以结点中间的关键字为中心分裂成左右两部分，然后将这个中间的关键字插入到父结点中，这个关键字的左子树指向分裂后的左半部分，这个关键字的右子支指向分裂后的右半部分，然后将当前结点指向父结点，继续进行第3步。
下面以4阶B树为例，介绍B树的插入操作，在4阶B树中，结点最多有3个关键字,最少有1个key关键字

1. 在空树中插入38

![在空树中插入38](http://pic.xishng.top/img/202201171145510.png)

1. 继续插入21,40。此时关键字该个数仍小于小于等于m-1

![继续插入21,40](http://pic.xishng.top/img/202201171146986.png)

1. 继续插入96

插入后超过了最大允许的关键字个数3，所以以关键字值为40（中间节点38,40都可以）为中心进行分裂，结果如下图所示，分裂后当前结点指针指向父结点，满足B树条件，插入操作结束。

![继续插入96](http://pic.xishng.top/img/202201171147921.png)

1. 继续插入20,39。

含有20,21,38,39的节点需要以38分裂。并向父结点进位38，然后当前结点指向父结点.查看父节点是否满足B树。满足定义，结束。不满足，继续分裂父节点。

![继续插入20,39。](http://pic.xishng.top/img/202201171147463.png)

1. 继续插入41,42,46

含有41,42,46,96的节点需要以46分裂。并向父结点进位46。

![继续插入41,42,46](http://pic.xishng.top/img/202201171147846.png)

1. 继续插入43,44
    1. 含有41,42,43,44的节点需要以43分裂。并向父结点进位43,此时当前结点指向父结点.检测到父节点不满足B树，继续分裂父节点，如下b操作
        
        ![继续插入43](http://pic.xishng.top/img/202201171148432.png)
        
    2. 分裂父节点
    
    ![继续插入44](http://pic.xishng.top/img/202201171148075.png)
    

### 三、B树的删除操作

删除操作是指删除该B树中的某个节点中的指定关键字，如果B树中不存对应的关键字，则删除失败。

> 如果当前需要删除的关键字位于非叶子结点上，则用后继最小关键字覆盖要删除的关键字，然后在后继关键字所在的子支中删除该后继关键字。此时后继关键字一定位于叶子结点上，这个过程和二叉搜索树删除结点的方式类似。删除这个记录后执行第2步
2、该结点关键字个数大于等于Math.ceil(m/2)-1，结束删除操作，否则执行第3步。
3、如果兄弟结点关键字个数大于Math.ceil(m/2)-1，则父结点中的关键字下移到该结点，兄弟结点中的一个关键字上移，删除操作结束。
否则，将父结点中的关键字下移与当前结点及它的兄弟结点中的关键字合并，形成一个新的结点。原父结点中的key的两个孩子指针就变成了一个孩子指针，指向这个新结点。然后当前结点的指针指向父结点，重复上第2步。
有些结点它可能即有左兄弟，又有右兄弟，那么我们任意选择一个兄弟结点进行操作即可。
下面以4阶B树为例，介绍B树的删除操作，4阶B树中，结点最多有3个key,最少有1个key
> 
> 1. 删除21，删除后结点中的关键字个数仍然大于等1，所以删除结束。
>     
>     ![删除21](http://pic.xishng.top/img/202201171148440.png)
>     
> 2. 删除39，删除后，该节点关键字个数小于1，看兄弟节点有多的没，右邻兄弟有多的。则将父节点的40下移，将右邻兄弟节点的最小关键字41上移。
>     
>     ![删除39](http://pic.xishng.top/img/202201171148317.png)
>     
> 3. 删除40,兄弟们都不够了。开始合并，将该节点剩余的（这里一个也没剩余）和右兄弟（和左兄弟也行）以及父节点的相关关键字进行合并。
>     
>     ![删除40](http://pic.xishng.top/img/202201171149379.png)
>     
> 4. 删除38,38位于非叶子结点上，则用后继最小关键字41覆盖要删除的关键字，然后在后继关键字所在的子支中删除41。查看删除关键字的节点是否满足B树定义，满足，删除结束。不满足，进行调整。
> 
> ![删除38](http://pic.xishng.top/img/202201171149618.png)
> 

### 四、代码

```golang
package main

import "fmt"

const M int = 4                //M阶B树
const Min int= M/2 -1          //每个节点至少有的关键字个数

type BT struct {
	parent   *BT       //指向父节点的指针
	keyNum   int       //关键字个数
	key      [M+1]int     //关键字向量,key[0]未用
	child    [M+1]*BT     //子树指针向量
}

//在B树中查找关键字为value的节点，查找成功，返回在节点的位置和该节点
func (t *BT) Search(value int) (bool,int,*BT) {
	node := &BT{}
	var i int
	if t == nil {
		return false,0,nil
	}
	for t != nil {
		for i=t.keyNum; i>0&&value<=t.key[i]; i-- {
			if value == t.key[i] {
				return true,i,t
			}
		}
		if t.child[i] == nil {
			node = t
		}
		t = t.child[i]
	}
	return false,i,node
}
//分裂节点t
func (t *BT) Split() *BT{
	newNode := BT{}
	parent := t.parent
	if parent == nil {
		parent = &BT{}
	}
	mid := t.keyNum/2+1
	newNode.keyNum = M - mid
	t.keyNum = mid -1
	j := 1
	k:=mid+1
	for ;k<=M;k++ {  //新生成的右节点
		newNode.key[j] = t.key[k]
		newNode.child[j-1] = t.child[k-1]
		j = j+1
	}
	newNode.child[j-1] = t.child[k-1]
	newNode.parent = parent
	t.parent = parent
	//将该节点中间节点插入到父节点
	k=parent.keyNum
	for ;t.key[mid]<parent.key[k];k-- {
		parent.key[k+1] = parent.key[k]
		parent.child[k+1] = parent.child[k]
	}
	parent.key[k+1] = t.key[mid]
	parent.child[k] = t
	parent.child[k+1] = &newNode
	parent.keyNum++
	if parent.keyNum >=M {
		return parent.Split()
	}
	return parent
}

/*
在树中插入关键字value
1、先查看树中是否有此关键字
2、通过第1步也能确定会插入到哪个节点中
2、查看插入节点后是否需要分裂节点
*/
func (t *BT) Insert(value int) *BT{
	var i int
	ok,_,node:= t.Search(value)  //1、先在整颗树中找到要插入到哪个节点中
	if !ok {                    //树中不存在此节点
		node.key[0] = value
		for i = node.keyNum;i>0 && value<node.key[i];i-- {
			node.key[i+1] = node.key[i]
		}
		node.key[i+1] = value
		node.keyNum++
		if node.keyNum < M {     //没有超过节点最大数，结束插入
			return t
		}else {                  //否则，分裂该节点
			parent := node.Split()
			for parent.parent != nil {
				parent = parent.parent
			}
			return parent
		}
	}
	return t
}
/*删除关键字
	1）要删除的key位于非叶子结点上，则用后继key覆盖要删除的key，然后删除该后继key。
	2）该结点key个数大于等于Math.ceil(m/2)-1，结束删除操作，否则执行第3步。
	3）如果兄弟结点key个数大于Math.ceil(m/2)-1，则父结点中的key下移到该结点，
	3）兄弟结点中的一个key上移，删除操作结束。
	否则，将父结点中的key下移与当前结点及它的兄弟结点中的key合并，形成一个新的结点。
	然后当前结点的指针指向父结点，重复上第2步。
*/
//在整颗树中找到要删除的关键字在哪个节点中
func (t *BT) Delete(value int) *BT{
	ok,i,node:= t.Search(value)  //先
	if ok {
		t = node.DeleteNode(value,i)//在这个节点中删除这个节点
	}
	return t
}
//删除关键字
func (t *BT) DeleteNode(value int,i int) *BT{
    	if t.child[i] != nil { //非终端节点
			valueTemp,nodeTemp := t.FindAfterMinNode(i) //找到后继最下层非终端结点的最小关键字和相应节点
    		t.key[i] = *valueTemp           //将最下层非终端结点中的最小关键字取代要删除的关键字
    		nodeTemp.DeleteNode(*valueTemp,1)// 然后删除最下层非终端结点中的最小关键字
		}else {
			for k := i;k<t.keyNum;k++ {
				t.key[k] = t.key[k+1]
				t.child[k] = t.child[k+1]
			}
			t.keyNum--
			if t.keyNum < (M-1)/2 && t.parent!=nil {
				ok,t := t.Restore()
				if !ok {
					t = t.MergeNode()
				}
			}
		}
		for t.parent != nil {
			t  = t.parent
		}
		return t
}
//调整B树,该节点与父亲节点和兄弟节点之间的调整
func (t *BT) Restore() (bool,*BT){
	parent := t.parent
	j:=0
	for ;parent.child[j]!=t;j++ {}
	if j>0 {   //p有左邻兄弟节点
		b := parent.child[j-1]
		if b.keyNum > (M-1)/2 {//左兄弟有多余关键字
			for k:=t.keyNum;k>=0;k-- { //将t中关键字和指针都右移动,给父节点移动下来的关键字空出位置
				t.key[k+1] = t.key[k]
				//t.child[k+1] = t.child[k]
			}
			t.key[1] = parent.key[j]
			parent.key[j] = b.key[b.keyNum] //b中关键字上移至parent
			t.keyNum++
			b.keyNum--
			return true,parent
		}
	}
	if j<parent.keyNum { //p有右邻兄弟节点
		b := parent.child[j+1]
		if b.keyNum > (M-1)/2 {//右邻兄弟有多余关键字
			t.key[t.keyNum+1] = parent.key[j+1] //父节点关键字下移
			parent.key[j+1] = b.key[1]           //兄弟节点关键字上移
			for k:=1;k<b.keyNum;k++ {
				b.key[k] = b.key[k+1]
			}
			t.keyNum++
			b.keyNum--
			return true,parent
		}
	}
	return false,t //没有调整成功,需要合并
}
func (t *BT) MergeNode() *BT{
	b := &BT{}
	parent := t.parent
	j:=0
	for ;parent.child[j]!=t;j++ {}
	if j>0 {      //t有左兄弟，与左兄弟合并
		b = parent.child[j-1]
		b.keyNum++
		b.key[b.keyNum] = parent.key[j]//父结点中关键字下移
		for k:=1;k<=t.keyNum;k++ {     //t节点中关键字移到左兄弟节点中
			b.keyNum++
			b.key[b.keyNum] = t.key[k]
		}
		parent.keyNum--
		for k:=j;k<parent.keyNum;k++ {  //改变父节点
			parent.key[k] = parent.key[k+1]
			parent.child[k] = parent.child[k+1]
		}
	}else {       //与右兄弟合并
		b = parent.child[j+1]
		t.keyNum++
		t.key[t.keyNum] = parent.key[j]//父结点中关键字下移
		for k:=1;k<=b.keyNum;k++ {     //兄弟节点中关键字移到t节点中
			t.keyNum++
			t.key[t.keyNum] = b.key[k]
		}
		parent.keyNum--
		for k:=j;k<=parent.keyNum;k++ {  //改变父节点
			parent.key[k] = parent.key[k+1]
			parent.child[k] = parent.child[k+1]
		}
	}
	return parent
}
//查找后继最下层非终端结点的最小关键字
func (t *BT) FindAfterMinNode(i int) (*int,*BT) {
	leaf := t
	if leaf == nil {
		return nil,nil
	}else {
		leaf = leaf.child[i]
		for leaf.child[0] != nil {
			leaf = leaf.child[0]
		}
	}
	return &leaf.key[1],leaf
}
func (t *BT) BTreeTraverse() {
	queue := []*BT{}
	queue = append(queue,t)
	i := 0       //当前要出队列的下标s
	for  i < len(queue) {
		current := queue[i]
		i=i+1
		fmt.Print("[")
		for k:=1;k<=current.keyNum;k++ {
			fmt.Printf(" %d ",current.key[k])
		}
		fmt.Print("]\\n")
		for k:=0;k<=current.keyNum;k++ {
			if current.child[k] != nil {
				queue = append(queue,current.child[k])
			}
		}
	}
}
//test
func main() {
	//插入
	bsTree := BT{nil,2,[M+1]int{0,21,38,0,0},[M+1]*BT{}}
	newTree := bsTree.Insert(96)
	newTree = newTree.Insert(40)
	newTree = newTree.Insert(20)
	newTree = bsTree.Insert(39)
	newTree = newTree.Insert(41)
	newTree = newTree.Insert(42)
	newTree = newTree.Insert(46)
	newTree = newTree.Insert(43)
	newTree = newTree.Insert(44)
	newTree.BTreeTraverse()
	//删除
	newTree = newTree.Delete(38)
	newTree = newTree.Delete(39)
	newTree = newTree.Delete(42)
	newTree.BTreeTraverse()
}

```