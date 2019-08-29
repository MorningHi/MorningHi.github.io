---
title: 十大排序算法总结
categories:
  - 算法基础
tags:
  - 排序算法
  - java
comments: false
mathjax: true
date: 2019-08-29 21:22:22
img: '/img/blog/sort.jpg'
---

## 定义

&emsp;&emsp;**排序** 是计算机内部经常进行的一项操作，目的是将一组无序的序列调整为一组有序的序列。Excel中将数据按照名称进行升序降序排列就是一种常见的排序操作。

## 相关概念

- **时间复杂度**：反映操作次数(计算量)随数据长度的变化规律，是数据规模的函数，用$O(·)$表示。
- **空间复杂度**：用来度量执行算法所需的存储空间随数据规模的变化情况，也用$O(·)$表示。
- **稳定性**：对于两个相等的元素 $a$ 和 $b$，如果排序算法不会改变他们的相对位置，则称排序算法是稳定的，否则这个算法就是不稳定的。

## 分类

&emsp;&emsp;根据排序过程是**否需要使用外部存储空间**可以将排序算法分为**内部排序**和**外部排序**。

&emsp;&emsp;根据排序算法的**复杂度**和是否是**基于对元素进行比较**进行排序又可以将算法分为**非线性时间比较类排序**和**线性时间非比较类排序**。

- **非线性时间比较类排序**：顾名思义就是根据比较来确定元素的排列顺序的排序算法，时间复杂度最低为$(O(nlogn))$
- **线性时间非比较类排序**：不通过比较元素的相对大小来确定元素的排列顺序的算法，时间复杂度可以突破对数级别以线性时间运行。



<img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/classes.png" style="zoom:90%" />

## 比较

<img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/comparison.png" style="zoom:70%" />

&emsp;&emsp;表中 $k$ 表示所需的桶(bucket)的个数——对于计数排序、桶排序和基数排序三种排序算法需要额外的**“buckets”**来辅助排序。

## 具体算法介绍

下面具体介绍了每一种排序算法：

(1)  [简单选择排序](https://morninghi.github.io/2019/08/20/SelectionSort/)

(2)  [冒泡排序](https://morninghi.github.io/2019/08/20/bubbleSort/)

(3)  [插入排序](https://morninghi.github.io/2019/08/21/InsertionSort/)

(4)  [希尔排序](https://morninghi.github.io/2019/08/22/ShellSort/)

(5)  [归并排序](https://morninghi.github.io/2019/08/23/MergeSort/)

(6)  [快速排序](https://morninghi.github.io/2019/08/23/QuickSort/)

(7)  [堆排序](https://morninghi.github.io/2019/08/24/HeapSort/)

(8)  [计数排序/桶排序/基数排序](https://morninghi.github.io/2019/08/26/CountingSort/)



&emsp;&emsp;所有的排序都默认是按照从大到小的顺序进行排序，完整的代码可以在[我的github](https://github.com/MorningHi/Algorithms-Edition4/tree/master/sort)上下载。此外，文章中的所有演示动图都来自于互联网。

