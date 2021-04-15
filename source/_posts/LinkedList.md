---
title: LinkedList basis
category: Leetcode
tag: [LC,易错,JS]
---

简单的链表训练

<!--more-->

## 链表

### 基本实现:

```js
//结点的构造函数
function Node(e){
    this.e = e;
    this.next = null;
}
//搜索item元素所在node
function find (item){
    let currNode = this.head;
    while(currNode.e!=item){
        currNode = currNode.next;
    }
    return currNode;
}
//在item元素之前插入newelement
function insert (newElement,item){
    let newNode = new Node(newElement);
    let target = this.find(item);
    newNode.next= target.next;
    target.next = newNode;
}

function display(){
    let currNode = this.head;
    while(currNode){
        console.log(currNode.e);
        currNode = currNode.next;
    }
}


//链表的构造函数
function LList() {
    this.head = new Node('head');//头结点
    this.find = find; //定义函数?
    this.insert = insert;
    this.display = display;
}
```

### 典型例题:

**两两交换链表中的节点-24**

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

<!--more-->

示例:

```
给定 1->2->3->4, 你应该返回 2->1->4->3.
复制代码
```

来源：力扣（LeetCode）

链接：[leetcode-cn.com/problems/sw…](https://leetcode-cn.com/problems/swap-nodes-in-pairs)

```js
//实现对链表两个节点的交换
function switchwithnext(node){
    if(!node) return null;
    let nextnode = node.next;
    if(!nextnode) return node;
    node.next = switchwithnext(nextnode.next);
    nextnode.next = node;
    return nextnode;
}

function solution(head){
    let res = switchwithnext(head);
    return res;
}
```

难吗?还行,重点是next的理解,要画图.


