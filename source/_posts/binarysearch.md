---
title: 二分查找
categories:
  - 算法基础
tags:
  - 二分法
comments: false
mathjax: true
date: 2019-09-08 20:09:58
img:
---

## 基本思想

&emsp;&emsp;在一个数组中查找某个特定元素的时候，直观的方法就是从头开始向后进行遍历。但是，如果数组一开始就是有序的，那么我们就可以有更快的查找方式——二分查找。二分查找是**二分法**思想的一个典型应用，具体来说，对于一个有序数组，我们如果要查找一个元素，那么首先用数组的中间元素将数组切分为两部分，若带查找元素大于中间元素则在前一部分找，小于则在后一部分找……以此不断地切分数组搜索待查找元素。

&emsp;&emsp;二分查找的时间复杂度为($O(\log n)$)。

## 实现

二分查找的思想很容易理解，下面分别给出基于**迭代**和**递归**的实现。

- 二分查找的迭代实现

```java
public static int search(int[] arr, int key){
    int lo = 0;
    int hi = arr.length - 1;
    while(lo <= hi){
        int mid = lo + (hi - lo) / 2;   // 注意：求平均值若使用  (lo+hi)/2 可能存在求和溢出的问题
        if(arr[mid] > key)   hi = mid - 1;
        else if(arr[mid] < key)   lo = mid + 1;
        else return mid;
    }
    return -1;
}
```

- 二分查找的递归实现

```java
public static int search(int[] arr, int key, int lo, int hi){
    if(hi <= lo)   return (arr[lo] == key) ? lo : -1;
    int mid = lo + (hi - lo) / 2;
    if(arr[mid] > key){
        return search(arr, key, lo, mid-1);   // 搜索前半部分
    }
    else if(arr[mid] < key){
        return search(arr, key, mid+1, hi);   // 搜索后半部分
    }
    else return mid;
}
```

说明：函数的参数中 `arr` 为升序排序数组，`key`为待查找元素。求数组中间位置的索引的时候使用 `int mid = lo + (hi - lo) / 2;` 而不是 `int mid = (hi + lo) / 2;` 目的是为了防止当数组长度过大的时候因为 `hi+lo` 造成数据溢出。