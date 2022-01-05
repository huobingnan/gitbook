---
title: "哈夫曼树与哈夫曼编码"
date: 2021-10-31
published: false
tags:
   - "数据结构"
   - "算法"
---

## 概述
哈夫曼树(Huffman Tree)也是一种树，与其他树不同的是哈夫曼树的构造方式。在哈夫曼树的构造过程中有一个参考指标——**带权路径长度**。

**路径**是指从树中一个节点出发到另一个节点之间的分支，分支的数目称为**路径长度**。**带权路径长度**是考虑到节点之间的权重大小之后的一个指标参数，数值上它等于路径长度与该路径上所有节点的权重乘积。

$
WPL = \sum_{i = 1}^{n} W_i \times L_i
$

哈夫曼树是带权节点集合中构造出的WPL最小的二叉树。Huffman树本质上也是一种二叉树，同时由于其WPL最小，所以也称之为最优二叉树。

哈夫曼编码是在哈夫曼树这种数据结构基础之上的一种编码方式。常见的编码方式主要分为两种——等长编码和变长编码。

等长编码是指将待编码数据全部映射为长度大小一致的二进制序列，这样的编码编解码都很方便，也容易实现。但是缺点也很明显，这样的编码得到的字节序列会较大，在存储与传输过程中比较消耗资源。比较常见的ASCII码就是一种等长度编码的形式。

变长编码是指将待编码数据由一定规则指定编码为长度不一致的二进制序列，变长编码的优势在于能够实现对数据的无损压缩（相较于等长度编码，变长编码后的体积更小而且不损伤数据的完整性），但是编解码比较困难。

## 哈夫曼树的构造

哈夫曼树的构造算法需要所有带权节点构成森林（树节点的集合）作为输入，集合中每个树节点的左子树与右子树均为空。

1. 从集合中选择一个权重最小的节点M0和权重次小的节点M1。

	构造一个新的节点N0，N0左子树为M0,右子树为M1。
	
	N0节点的权重大小为M0的权重加上M1的权重。
	
	将M0和M1从集合中删除，并将新构造的N0节点放入集合中。
	
2.  重复步骤1，直至集合中只剩下一个节点

## 哈夫曼树的特点

从哈夫曼树的构造中可以看出，哈夫曼树有以下特点：

1. 叶子节点存储有效信息。

2. 哈夫曼树中不存在出度为1的节点，即每个节点要么没有左右子树，要么都有左右子树。

3. 若初始森林中树节点的个数为N，则哈夫曼树中的节点个数为2N - 1。

## 哈夫曼编码

![HuffmanEncoding](https://cdn.jsdelivr.net/gh/huobingnan/my_image_hosting@master/HuffmanEncoding.ron3lfqo5t.png)


哈夫曼树构造完成之后，就已经确定了对特定数据的哈夫曼编码。

数据的编码由该数据在哈夫曼树中的路径确定，具体形式为：从根节点出发，沿左子树行走为0，沿右子树行走为1。如图所示，节点A的路径为{left, left, left}，故其编码为000。节点B的路径为{left, left, right}，故其编码为001。

从图中便可看出哈夫曼编码属于变长编码，数据的编码长度并不相同。

## 哈夫曼解码

数据经过编码之后可以得到一串由0，1组成的二进制序列。解码就是将这串二进制序列重新变为原始数据的过程。解码的过程中需要用到将数据编码的哈夫曼树，其具体的操作流程为：

1. 遍历二进制序列，遇到0则沿哈夫曼树向左走，遇到1则沿哈夫曼树向右走。

2. 若哈夫曼树的当前节点为叶子节点，则完成了一个数据的解码。将解码数据保存，重新回到哈夫曼的的根节点。

3. 重复步骤1，2直到序列被遍历完。

## 附录A——哈夫曼节点定义

```kotlin
class HuffmanNode<T>(  
	 var left: HuffmanNode<T>?,  // 左子树
	 var right: HuffmanNode<T>?, // 右子树
	 var weight: Int,            // 节点权重
	 var data: T?,               // 节点数据
	 var codeBuffer: StringBuilder?) {  // 使用字符串的形式来表述编码的二进制序列

	private lateinit var huffmanCode: String  

	constructor(weight: Int, data: T?): this(null, null, weight, data, StringBuilder())  

	fun isLeafNode(): Boolean { return left == null && right == null}  

	fun code(): String {  
		if (!this::huffmanCode.isInitialized) {  
			huffmanCode = codeBuffer?.reverse().toString()  
			codeBuffer = null  
		}  
		return huffmanCode  
	}  
}

```

## 附录B——哈夫曼树构造

```kotlin
fun <T> constructHuffmanTree(forest: List<HuffmanNode<T>>): HuffmanNode<T> {  
	// 构造小顶堆
	val comparator = { h1: HuffmanNode<T>, h2: HuffmanNode<T> ->  
		h1.weight - h2.weight  
	}  
	//  使用优先队列（小顶堆）构造哈夫曼树
	val priorityQueue = PriorityQueue(comparator)  
	for (node in forest) {  
		priorityQueue.add(node)  
	}
	// 更新哈夫曼节点的编码值
	val queue = ArrayDeque<HuffmanNode<T>>()  
	while (priorityQueue.size != 1) {  
		val m1 = priorityQueue.remove()  // 最小值
		val m2 = priorityQueue.remove()  // 次小值
		// update code and children code buffer  
		queue.add(m1)  
		while (queue.isNotEmpty()) {  
			val top = queue.poll()  
			top.codeBuffer?.append('0')  
			if (!top.isLeafNode()) {  
				queue.add(top.left!!)  
				queue.add(top.right!!)  
			} 
		} 
		queue.add(m2)  
		while (queue.isNotEmpty()) {  
			val top = queue.poll()  
			top.codeBuffer?.append('1')  
			if (!top.isLeafNode()) {  
			queue.add(top.left!!)  
			queue.add(top.right!!)  
			} 
		} 
		// 将新构造的节点放入优先队列
		priorityQueue.add(HuffmanNode(m1, m2, m1.weight + m2.weight, null, StringBuilder()))  
	}  
	return priorityQueue.remove()  
}
```

## 附录C——哈夫曼编码

```kotlin
fun huffmanEncoding(tree: HuffmanNode<Char>, originData: String): String {  
	val encodingSet = HashMap<Char, String>()  
	val stack = ArrayDeque<HuffmanNode<Char>>()  
	stack.push(tree)  
	// 采用深度优先遍历，缓存好所有叶子节点的编码值  
	while (stack.isNotEmpty()) {  
		val top = stack.pop()  
		if (top.isLeafNode()) {  
			encodingSet[top.data!!] = top.code()  
		} else {  
			stack.push(top.right!!)  
			stack.push(top.left!!)  
		} 
	} 
	val builder = StringBuilder()  
	originData.forEach {  
		builder.append(encodingSet[it])  
	}  
	return builder.toString()  
}
```

## 附录D——哈夫曼解码

```kotlin
fun huffmanDecoding(tree: HuffmanNode<Char>, encodeSeq: String): String {  
	val builder = StringBuilder()  
	var idx = 0  
	var p = tree  
	while (true) {  
		if (idx >= encodeSeq.length) {  
			break  
		}  
		p = if (encodeSeq[idx] == '0') {  
			p.left!!  
		} else {  
			p.right!!  
		}  
		if (p.isLeafNode()) {  
			builder.append(p.data)  
			p = tree  
		}  
		idx += 1  
	}  
	return builder.toString()  
}