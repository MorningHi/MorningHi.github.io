---
title: 剑指Offer[24]：反转链表
categories:
  - 刷题笔记
tags:
  - java
  - 链表
comments: false
mathjax: true
date: 2019-09-16 21:24:31
img:
---

## 题目

&emsp;&emsp;定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。我们将链表的节点定义如下：

```java
class ListNode{
	private int val;       // 节点值
	private ListNode next;     // 下一个节点
}
```

&emsp;&emsp;LeetCode中也有[相应的题目](https://leetcode-cn.com/problems/reverse-linked-list/)

## 解法

### 迭代解法

&emsp;&emsp;迭代的方法很好理解：为了反转链表我们从前向后遍历链表，改变每一个节点的指针指向，让每个节点都指向前一个节点，最后返回尾节点。

&emsp;&emsp;迭代方法主要注意两个问题：第一个是头节点反转之后是尾节点，因此头节点应当指向`null`；第二个要注意的点是，每次让当前节点指向前一个结点后，会将链表截断为两部分，因此每次需要保存下一个节点。

迭代解法的代码如下：

```java
// 迭代法
public static ListNode reverse(ListNode head){
    ListNode curr = head;    // 当前节点
    ListNode prev = null;    // 当前节点的前一个节点

    while(curr != null){
        ListNode temp = curr.next;   // 临时指针指向当前节点的下一个节点
        curr.next = prev;   // 当前节点指向前一个节点
        prev = curr;      // 指针后移
        curr = temp;   
    }
    return prev;
}
```

迭代解法的时间复杂度为 $O(n)$，空间复杂度为 $O(1)$。

### 递归解法

&emsp;&emsp;递归解法理解起来稍微复杂一点。假设链表从第 $k+1$ 个元素开始的后半部分已经被反转，当前节点为 $n_k$，也就是当前链表如下：

$$n_{1} \rightarrow \ldots \rightarrow n_{k-1} \rightarrow n_{k} \rightarrow n_{k+1} \leftarrow n_{k+2} \leftarrow \ldots \leftarrow n_{m}$$

那么接下来要做的就是让 $$n_{k+1}$$ 的 下一个节点为$$n_k$$，另外就是每次的中间节点(上面的 $$n_{k+1}$$)是不指向其余节点的。

递归解法的代码如下：

```java
// 递归解法
public static ListNode reverse(ListNode head){
    if(head == null || head.next == null)    return head;
    ListNode p = reverse(head.next);
    head.next.next = head;
    head.next = null;
    return p;
} 
```

相比起迭代解法，递归法的代码短小精悍，其时间复杂度为 $O(n)$，由于递归使用了额外栈空间，因此递归法的空间复杂度为 $O(n)$。

### 扩展

&emsp;&emsp;LeetCode的[第92题](https://leetcode-cn.com/problems/reverse-linked-list-ii/)是反转链表的进阶版——反转链表的一部分，题目如下：

> 反转从位置 *m* 到 *n* 的链表。请使用一趟扫描完成反转。1 ≤ *m* ≤ *n* ≤ 链表长度。



以链表 1 -> 2 -> 3 -> 4 -> 5 -> 6 为例，假如要反转的部分是 2 到 5，反转之后的链表应当为  1 ->5 -> 4 -> 3 -> 2 -> 6 。反转过程通过下图来说明：

<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/LeetCode/reverselist.JPG" style="zoom:5"\></center>
&emsp;&emsp;首先通过计数找到反转的起始节点标记为 `curr`，并记录其前面节点 `prev`和后一个节点 `temp`，如图中第1行所示；接下来分三步：

1. 让 `curr.next`指向 `temp.next`；
2. 让 `temp.next`指向  `prev.next`；
3. 让  `prev.next`指向`temp` 。

执行完上面三步后会得到图中第2行的结构，接下来重复上面的三步直到 `curr`到达反转部分的末尾，反转完成。代码如下：

```java
// 反转链表的一部分
public static ListNode reverseBetween(ListNode head, int start, int end){
    if(start == end) return head;
    ListNode curr = head, newHead = new ListNode(-1);
    ListNode prev = newHead;
    newHead.next = head;

    int count = 1;     
    while(count < start){
        prev = prev.next;
        count++;
    }
    curr = prev.next; // 找到反转的起始节点

    while(count++ < end){
        ListNode temp = curr.next;   // 保存当前节点的下一个节点
        curr.next = curr.next.next;  
        temp.next = prev.next;
        prev.next = temp;
    }
    return newHead.next;
}
```

如果提前已知链表的长度，那么让起始位置为1，结束位置为链表长度就可以实现整个列表的反转，因此上面的程序能作为一个更一般化的反转链表的程序。其时间复杂度为 $O(n)$，空间复杂度为 $O(1)$。