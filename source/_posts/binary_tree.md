---
title: 树（二）二叉树
date: 2019-02-25 13:01:36
tags:
    - 二叉树
categories:
    - 笔记
toc: true
---
## 二叉树的基本名词
满二叉树：树中每层都含有最多的结点
完全二叉树：简单来讲就是每一个结点编号都与满二叉树相同的二叉树，满二叉树是一种特殊的完全二叉树。**(lchild=father*2)**
## 二叉树的几种遍历
PreOrder Traversal
InOrder Traversal
PostOrder Traversal
LevelOrder Traversal
**规律：**显然，中序遍历可以与其余三种遍历方法的任何一种完成对二叉树的重建（唯一），而其余三种遍历方法任何一种两两结合都无法构建唯一的二叉树。
这很好理解，其余三种遍历方法告知了我们根节点的位置，而根据这一点我们可以由中序遍历确定左右子树，然后根据二叉树的递归定义完成重建。<!--more-->

## 相应OJ链接
### 1020 Tree Traversals
**试题概述：**
input：给定二叉树结点个数，二叉树的后序遍历与中序遍历
output：二叉树的层序遍历
[OJ试题传送门](https://pintia.cn/problem-sets/994805342720868352/problems/994805485033603072)
```
#include <iostream>
#include <queue>
using namespace std;
const int x = 31;
int n;
int in[x], post[x];//inorder and postorder

struct Nodes
{
	int data;
	Nodes *lchild, *rchild;
};

//recreate the BT
Nodes* creat(int postl, int postr, int inl, int inr) 
{
	if (postr<postl)
	{
		return NULL;
	}
	Nodes *root = new Nodes;
	root->data = post[postr];
	int k;
	for ( k = inl; k < inr; k++)
	{
		if (in[k] == post[postr]) {
			break;
		}
	}
	int numl = k - inl;
	root->lchild = creat(postl, postl + numl - 1, inl, k - 1);
	root->rchild = creat(postl + numl, postr - 1, k + 1, inr);
	return root;
}

int cou = 0; //用于计数

//用构建好的BT，打印level order 
void print_level(Nodes *root) {
	queue<Nodes*> q;
	q.push(root);
	while (!q.empty())
	{
		Nodes *node = new Nodes;
		node = q.front();
		q.pop();
		cout << node->data;
		cou++;
		if (cou<n)
		{
			cout << " ";
		}
		if (node->lchild!=NULL)
		{
			q.push(node->lchild);
		}
		if (node->rchild!=NULL)
		{
			q.push(node->rchild);
		}
	}
}

int main()
{
	cin >> n;
	for (int i = 0; i < n; i++)
	{
		cin >> post[i];
	}
	for (int i = 0; i < n; i++)
	{
		cin >> in[i];
	}
	Nodes *root = creat(0, n - 1, 0, n - 1);
	print_level(root);
	return 0;
}
```

### 1086 Tree Traversals Again
**试题概述：**
input：用堆栈描述二叉树的结构
output：打印二叉树的后序遍历
说明：这道题跟Test 1我认为是没有什么区别的，由题设可知这一系列堆栈的操作其实是给出了二叉树的PreOrder与InOrder，只需做一些简单字符串的处理将其提取出来即可，剩下的与Test 1基本没有区别（构造二叉树以及标准的遍历操作）。
[OJ试题传送门](https://pintia.cn/problem-sets/994805342720868352/problems/994805380754817024)
```
#include <iostream>
#include<stack>
#include<string>
using namespace std;
const int N = 31;
int	n;
int pre[N], in[N];

struct Nodes
{
	int data;
	Nodes *lchild, *rchild;
};

//recreate the BT through the preorder traversal and the inorder traversal
Nodes* creat(int preL, int preR, int inL, int inR) {
	if (preR<preL)
	{
		return NULL;
	}
	Nodes *root = new Nodes;
	root->data = pre[preL];
	int k;
	for ( k = inL; k < inR; k++)
	{
		if (in[k] == pre[preL])
			break;
	}
	int numL = k - inL;
	root->lchild = creat(preL + 1, preL + numL, inL, k-1);
	root->rchild = creat(preL + numL + 1, preR, k + 1, inR);
	return root;
}

//print the BT's postorder traversal
int cou = 0;
void print_post(Nodes* root) {
	if (root==NULL)
	{
		return;
	}
	print_post(root->lchild);
	print_post(root->rchild);
	cout << root->data;
	cou++;
	if (cou<n)
	{
		cout << " ";
	}
}

int main()
{
	cin >> n;
	string s;
	int x;
	int n1 = 0, n2 = 0;
	stack<int> ss;
	for (int i = 0; i < 2*n; i++)
	{
		cin >> s;
		if (s[1]=='u')
		{
			cin >> x;
			pre[n1++] = x;
			ss.push(x);
		}
		else
		{
			in[n2++] = ss.top();
			ss.pop();
		}
	}
	Nodes *root = creat(0, n - 1, 0, n - 1);
	print_post(root);
	return 0;
}
```