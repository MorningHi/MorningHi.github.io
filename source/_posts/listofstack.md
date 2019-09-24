---
title: 剑指Offer[31]：栈的压入、弹出序列
categories:
  - 刷题笔记
tags:
  - 栈
comments: false
date: 2019-09-19 20:39:30
img:
---

## 题目
> 输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。
LeetCode中也有相同的题目：[验证栈序列](https://leetcode-cn.com/problems/validate-stack-sequences/)

## 解决思路
&emsp;&emsp;直观的想法就是构建一个栈，将第一个序列按顺序压入栈中，压入同时判断能否按照第二个序列的顺序弹出。整理一下这个思路，步骤如下：
1. 依次将第一个序列的元素压入栈中；
2. 判断这个元素是不是第二个序列中的将要pop的值；
3. 如果是则从栈中弹出此元素，将要pop的值的后移；否则压入第一个序列的下一个值，返回到第二步；
4. 最后如果栈为空说明第一个序列所有元素都被按序压入并按第二个序列的顺序弹出，返回True，否则返回False。

## 代码实现
```java
public boolean validateStackSequences(int[] pushed, int[] popped) {
    if(pushed.length != popped.length)  return false;

    int len = pushed.length;
    int i = 0, j = 0;
    Stack<Integer> s = new Stack();
    for(; i < len; i++){
        // 遍历pushed序列，按顺序压入栈
        s.push(pushed[i]);
        //若栈不为空且栈顶元素等于当前poped序列的元素，则弹栈并且遍历到popped序列的下一个元素
        while(!s.empty() && (s.peek() == popped[j])){
            s.pop();
            j++;
        }
    }
    // 最后如果栈为空表示两个序列满足条件
    return s.empty();
}
```