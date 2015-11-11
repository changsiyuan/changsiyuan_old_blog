---
layout : post
title : Java 二叉查找树的实现
category : java
tagline : "author: ChangSiyuan"
tags : [Java]
---
{% include JB/setup %}

### 引言
- 树是重要的数据结构；
- 本文简单介绍了树和二叉树的概念；
- 本文详细实现了二叉查找树；

### 树的基本术语：
- 树（Tree）是由显示节点间关系的边（edge）相连而成的节点的集合；
- 这些节点排列在表明节点层次的各层（level）上；
- 顶层是一个称为根（root）的单独节点；
- 树的高度是树的层的数目，一般的，树的高度是从根到叶子的最长路径的长度加一；
- 树的每个后继层中的节点是其上一层节点的孩子（children）；
- 有孩子的节点是这些孩子的双亲（parent）；
- 以某结点为根的子树中的任一节点成为该节点的子孙/后代（descendent）；
- 从根到某节点所经分支上的所有节点成为该节点的祖先（ancestor）；
- 双亲相同的孩子们被称为兄弟（sibling）；
- 没有孩子的节点成为叶子（leaf）节点；
- 不是叶子节点的节点被称作非叶节点（nonleaf）；

### 树的分类：
- 一般树（general tree）：树中的每个节点可以有任意数量的孩子；
- n叉树（n-ary tree）：树中的每个节点的孩子数量不超过n；
- 二叉树（binary tree）：树种每个节点至多有两个孩子；

### 二叉树：
- 二叉树每个节点至多有两个孩子，分别为左孩子（left child）和右孩子（right child）；
- 二叉树节点的子树被称为左子树（left subtree）和右子树（right subtree）；
- 二叉树的左子树是其根的左子树，二叉树的右子树是其根的右子树；
- 二叉树中每个非叶节点都恰好有两个孩子，且最后一层全部填满，则称二叉树是满的（full）；
- 二叉树中除了最后一层外其余层都含有尽可能多的节点，并且最后一层上的节点是从左到右填满的，则称这棵树是完全的（complete）；

### 二叉查找树
- 每个节点的数据大于该节点左子树的数据；
- 每个节点的数据小于该节点右子树的数据；

### 二叉查找树的实现
- Node定义节点类

```
package binarytree;

public class Node<T> {
	Node<T> leftChid = null;
	Node<T> rightChild = null;
	T data;

	// 构造器
	public Node() {

	}
	
	//构造器
	public Node(T data){
		this.data = data;
	}

	public void display() {
		System.out.println(data + " ");
	}
	
	public Node<T> getLeftChid() {
		return leftChid;
	}

	public void setLeftChid(Node<T> leftChid) {
		this.leftChid = leftChid;
	}

	public Node<T> getRightChild() {
		return rightChild;
	}

	public void setRightChild(Node<T> rightChild) {
		this.rightChild = rightChild;
	}
}

```

- BinaryTree类实现了二叉查找树的大部分方法

```
package binarytree;

import java.util.Queue;
import java.util.Stack;
import java.util.concurrent.LinkedBlockingQueue;

public class BinaryTree<T extends Comparable> {
	public int maxHeight = 8; // 默认树的最大高度为8
	public int size = 0; // 树的节点数量
	Node<T> root = null;

	// 构造器
	public BinaryTree() {

	}

	// 构造器
	public BinaryTree(int maxHeight) {
		this.maxHeight = maxHeight;
	}

	/*
	 * 基本动作
	 */

	// 清空二叉树
	public void clear() {
		root = null;
		size = 0;
	}

	// 判断是否为空
	public boolean isEmpty() {
		return size == 0;
	}

	// 判断是否是满树
	public boolean isFull() {
		// 利用满树的高度和元素个数的关系判断
		// 判断size+1是否是2的幂次即可
		if (size == 0) {
			return false;
		}
		int p = size + 1;
		while (p % 2 == 0) {
			p = p / 2;
		}
		if (p == 1) {
			return true;
		} else {
			return false;
		}
	}

	// 获取树的高度
	public int getHeight() {
		return height(root);
	}

	public int height(Node<T> subTree) {
		if (subTree == null)
			return 0; // 递归结束：空树高度为0
		else {
			int i = height(subTree.getLeftChid());
			int j = height(subTree.getRightChild());
			return (i < j) ? (j + 1) : (i + 1);
		}
	}

	// 返回结点个数
	public int getSize() {
		return size;
	}

	// 返回结点个数，第二种实现方法
	public int getSize2() {
		return size(root);
	}

	public int size(Node<T> subTree) {
		if (subTree == null) {
			return 0;
		} else {
			return 1 + size(subTree.getLeftChid())
					+ size(subTree.getRightChild());
		}
	}

	// 获取叶子节点总数
	int leafNumber = 0;

	public int getLeafNum() {
		leafNumber = 0;
		leafNum(root);
		return leafNumber;
	}

	public void leafNum(Node<T> subTree) {
		if (subTree != null) {
			if (subTree.getLeftChid() == null
					&& subTree.getRightChild() == null) {
				leafNumber++;
			}
			leafNum(subTree.getLeftChid());
			leafNum(subTree.getRightChild());
		}
	}

	/*
	 * 获取数据
	 */

	// 获取根节点数据
	public T getRoot() {
		return root.data;
	}

	// 获取双亲节点数据
	public T getParent(T data) {
		if (root == null) {
			return null;
		} else if (root.data.equals(data)) {
			return root.data;
		} else {
			return parent(root, data);
		}
	}

	public T parent(Node<T> subTree, T data) {
		// 思路：先找左子树，如果找到则返回数值，左子树找不到（左子树寻找结果为null的话）再去右子树找，
		// 如果找到则返回数值，找不到返回null
		if (subTree == null) {
			return null;
		}
		if (subTree.getLeftChid() != null
				&& subTree.getLeftChid().data.equals(data)) {
			return subTree.data;
		} else if (subTree.getRightChild() != null
				&& subTree.getRightChild().data.equals(data)) {
			return subTree.data;
		} else {
			T p;
			if ((p = parent(subTree.getLeftChid(), data)) != null) {
				return p;
			} else {
				return parent(subTree.getRightChild(), data);
			}
		}
	}

	// 获取左孩子结点数据
	public T getLeftChild(T data) {
		if (root == null) {
			return null;
		} else {
			return leftChild(root, data);
		}
	}

	public T leftChild(Node<T> subTree, T data) {
		// 思路：先找左子树，如果找到则返回数值，左子树找不到（左子树寻找结果为null的话）再去右子树找，
		// 如果找到则返回数值，找不到返回null
		if (subTree == null) {
			return null;
		}
		if (subTree.data.equals(data) && subTree.getLeftChid() != null) {
			return subTree.getLeftChid().data;
		} else {
			T p;
			if ((p = leftChild(subTree.getLeftChid(), data)) != null) {
				return p;
			} else {
				return leftChild(subTree.getRightChild(), data);
			}
		}
	}

	// 获取右孩子结点数据
	public T getRightChild(T data) {
		if (root == null) {
			return null;
		} else {
			return rightChild(root, data);
		}
	}

	public T rightChild(Node<T> subTree, T data) {
		// 思路：先找左子树，如果找到则返回数值，左子树找不到（左子树寻找结果为null的话）再去右子树找，
		// 如果找到则返回数值，找不到返回null
		if (subTree == null) {
			return null;
		}
		if (subTree.data.equals(data) && subTree.getRightChild() != null) {
			return subTree.getRightChild().data;
		} else {
			T p;
			if ((p = rightChild(subTree.getLeftChid(), data)) != null) {
				return p;
			} else {
				return rightChild(subTree.getRightChild(), data);
			}
		}
	}

	// 获取节点中的最小值
	T tMin;

	public T getMin() {
		if (root == null) {
			return null;
		} else {
			tMin = root.data;
			min(root);
			return tMin;
		}
	}

	public void min(Node<T> subTree) {
		if (subTree != null) {
			if (subTree.data.compareTo(tMin) < 0) {
				tMin = subTree.data;
			}
			min(subTree.getLeftChid());
			min(subTree.getRightChild());
		}
	}

	// 获取节点中的最大值
	T tMax;

	public T getMax() {
		if (root == null) {
			return null;
		} else {
			tMax = root.data;
			max(root);
			return tMax;
		}
	}

	public void max(Node<T> subTree) {
		if (subTree != null) {
			if (subTree.data.compareTo(tMax) > 0) {
				tMax = subTree.data;
			}
			max(subTree.getLeftChid());
			max(subTree.getRightChild());
		}
	}

	/*
	 * 插入和删除
	 */

	// 插入节点: 每个节点的数据大于该节点左子树的数据、小于该节点右子树的数据；
	public void insert(T t) throws Exception {
		if (root == null) {
			root = new Node<T>(t);
			size++;
		} else if (isFull() && getHeight() == maxHeight) {
			throw new Exception("二叉树已满且高度达到最大值！");
		} else {
			insertNode(root, t);
		}
	}

	// 插入节点具体行为
	public void insertNode(Node<T> node, T data) {
		if (data.compareTo(node.data) <= 0) {
			if (node.getLeftChid() == null) {
				node.setLeftChid(new Node<T>(data));
				size++;
			} else {
				insertNode(node.getLeftChid(), data);
			}

		} else {
			if (node.getRightChild() == null) {
				node.setRightChild(new Node<T>(data));
				size++;
			} else {
				insertNode(node.getRightChild(), data);
			}
		}
	}

	// 删除某个子树
	public void destroy(T t) {
		if (root == null) {
			return;
		} else if (root.data.equals(t)) {
			clear();
		} else {
			destroyNode(root, t);
		}
	}

	public void destroyNode(Node<T> subTree, T t) {
		//思路：先检查当前节点的左子树是否符合要求，符合则删除，不符合再检查右子树是否符合要求，符合则删除
		//若左右子树都不符合，迭代查询下层节点的左右子树
		//如果当前节点为null或叶子节点，不予检查，直接返回
		if (subTree != null
				&& (subTree.getLeftChid() != null || subTree.getRightChild() != null)) {
			if (subTree.getLeftChid() != null
					&& subTree.getLeftChid().data.equals(t)) {
				subTree.setLeftChid(null);
				size--;
			}
			if (subTree.getRightChild() != null
					&& subTree.getRightChild().data.equals(t)) {
				subTree.setRightChild(null);
				size--;
			}
			destroyNode(subTree.getLeftChid(), t);
			destroyNode(subTree.getRightChild(), t);
		}
	}

	/*
	 * 递归遍历
	 */

	// 先序遍历
	public void getPreOrder() {
		preOrder(root);
	}

	public void preOrder(Node<T> subTree) {
		if (subTree != null) {
			visit(subTree);
			preOrder(subTree.getLeftChid());
			preOrder(subTree.getRightChild());
		}
	}

	public void visit(Node<T> node) {
		node.display();
	}

	// 中序遍历
	public void getInOrder() {
		inOder(root);
	}

	public void inOder(Node<T> subTree) {
		if (subTree != null) {
			inOder(subTree.getLeftChid());
			visit(subTree);
			inOder(subTree.getRightChild());
		}
	}

	// 后序遍历
	public void getPostOrder() {
		postOrder(root);
	}

	public void postOrder(Node<T> subTree) {
		if (subTree != null) {
			postOrder(subTree.getLeftChid());
			postOrder(subTree.getRightChild());
			visit(subTree);
		}
	}

	// 按层次遍历
	public void levelTraverse() {
		levelTraverse(root);
	}

	public void levelTraverse(Node<T> node) {
		Queue<Node<T>> queue = new LinkedBlockingQueue<Node<T>>();
		queue.add(node);
		while (!queue.isEmpty()) {
			Node<T> temp = queue.poll();
			if (temp != null) {
				temp.display();
				if (temp.getLeftChid() != null)
					queue.add(temp.getLeftChid());
				if (temp.getRightChild() != null)
					queue.add(temp.getRightChild());
			}
		}
	}

	/*
	 * 非递归遍历
	 */

	// 先序遍历
	public void nrPreOrder() {
		Stack<Node<T>> stack = new Stack<Node<T>>();
		Node<T> node = root;

		while (node != null || !stack.isEmpty()) {
			while (node != null) {
				node.display();
				stack.push(node);
				node = node.getLeftChid();
			}
			node = stack.pop();
			node = node.getRightChild();
		}
	}

	// 中序遍历
	public void nrInOrder() {
		Stack<Node<T>> stack = new Stack<Node<T>>();
		Node<T> node = root;
		while (node != null || !stack.isEmpty()) {
			while (node != null) {
				stack.push(node);
				node = node.getLeftChid();
			}
			node = stack.pop();
			node.display();
			;
			node = node.getRightChild();
		}
	}

	// 后序遍历
	public void nrPostOrder() {
		Stack<Node<T>> stack = new Stack<Node<T>>();
		Node<T> node = root;
		Node<T> preNode = null; // 表示最近一次访问的节点

		while (node != null || !stack.isEmpty()) {
			while (node != null) {
				stack.push(node);
				node = node.getLeftChid();
			}

			node = stack.peek();
			if (node.getRightChild() == null || node.getRightChild() == preNode) {
				node.display();
				node = stack.pop();
				preNode = node;
				node = null;
			} else {
				node = node.getRightChild();
			}
		}
	}
}

```

### BinaryTreeTest为测试类

```
package binarytree;

public class BinaryTreeTest {

	public static void main(String[] args) throws Exception {
		BinaryTree<Integer> b = new BinaryTree<Integer>();

		b.insert(0);
		System.out.println(b.isEmpty()); // false
		System.out.println(b.getSize()); // 1
		b.clear();
		System.out.println(b.isEmpty()); // true
		System.out.println(b.getSize()); // 0
		System.out.println("-----------");

		b.insert(11);
		b.insert(10);
		b.insert(14);
		System.out.println(b.isFull()); // true
		System.out.println(b.getHeight()); // 2
		System.out.println(b.getSize()); // 3
		System.out.println(b.getSize2()); // 3
		System.out.println(b.getLeafNum()); // 2
		System.out.println("-----------");
		b.insert(15);
		System.out.println(b.isFull()); // false
		System.out.println(b.getHeight()); // 3
		System.out.println(b.getSize()); // 4
		System.out.println(b.getSize2()); // 4
		System.out.println(b.getLeafNum()); // 2
		System.out.println("-----------");
		b.insert(12);
		System.out.println(b.isFull()); // false
		System.out.println(b.getHeight()); // 3
		System.out.println(b.getSize()); // 5
		System.out.println(b.getSize2()); // 5
		System.out.println(b.getLeafNum()); // 3
		System.out.println("-----------");
		b.destroy(15);
		System.out.println(b.isFull()); // false
		System.out.println(b.getHeight()); // 3
		System.out.println(b.getSize()); // 4
		System.out.println(b.getSize2()); // 4
		System.out.println(b.getLeafNum()); // 2
		System.out.println("-----------");
		b.insert(16);
		b.insert(17);
		b.insert(15);
		b.insert(18);
		System.out.println(b.isFull()); // false
		System.out.println(b.getHeight()); // 5
		System.out.println(b.getSize()); // 8
		System.out.println(b.getSize2()); // 8
		System.out.println(b.getLeafNum()); // 4
		System.out.println(b.getRoot()); // 11
		System.out.println("-----------");

		System.out.println(b.getParent(10)); // 11
		System.out.println(b.getParent(16)); // 14
		System.out.println(b.getParent(12)); // 14
		System.out.println(b.getParent(17)); // 16
		System.out.println(b.getParent(18)); // 17
		System.out.println(b.getParent(20)); // null
		System.out.println("-----------");

		System.out.println(b.getLeftChild(11)); // 10
		System.out.println(b.getLeftChild(10)); // null
		System.out.println(b.getLeftChild(14)); // 12
		System.out.println(b.getLeftChild(16)); // 15
		System.out.println(b.getLeftChild(15)); // null
		System.out.println(b.getLeftChild(17)); // null
		System.out.println(b.getLeftChild(18)); // null
		System.out.println("-----------");

		System.out.println(b.getRightChild(11)); // 14
		System.out.println(b.getRightChild(10)); // null
		System.out.println(b.getRightChild(14)); // 16
		System.out.println(b.getRightChild(12)); // null
		System.out.println(b.getRightChild(16)); // 17
		System.out.println(b.getRightChild(17)); // 18
		System.out.println(b.getRightChild(18)); // null
		System.out.println("-----------");

		System.out.println(b.getMin()); // 10
		System.out.println(b.getMax()); // 18
		System.out.println("-----------");

		b.getPreOrder();
		System.out.println("-----------"); // 11 10 14 12 16 15 17 18
		b.getInOrder();
		System.out.println("-----------"); // 10 11 12 14 15 16 17 18
		b.getPostOrder();
		System.out.println("-----------"); // 10 12 15 18 17 16 14 11
		b.levelTraverse();
		System.out.println("-----------"); // 11 10 14 12 16 15 17 18
		b.nrPreOrder();
		System.out.println("-----------"); // 11 10 14 12 16 15 17 18
		b.nrInOrder();
		System.out.println("-----------"); // 10 11 12 14 15 16 17 18
		b.nrPostOrder();
		System.out.println("-----------"); // 10 12 15 18 17 16 14 11
	}
}

```


