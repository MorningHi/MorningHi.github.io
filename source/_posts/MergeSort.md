---
title: 归并排序
categories:
  - 算法基础
tags:
  - 排序
  - java
comments: false
mathjax: true
date: 2019-08-23 15:48:12
img: 'img/blog/merge.jpg'
---

## 基本思想

&emsp;&emsp;用一句话描述归并排序就是：将两个有序的数组归并(Merge)为一个有序数组。归并排序是**分治思想**的一种典型应用。归并排序可以通过**自顶向下**的方式实现，也可以通过**自底向上**的方式实现。

- **自顶向下**

  自顶向下的归并排序首先将数组分成两个子数组，分别递归调用这两部分进行单独排序，最后合并子数组。下面的动图演示就是自顶向下的归并排序。

- **自底向上**

  和自顶向下的归并排序化整为零的思路不同，自底向上的归并排序循序渐进的解决问题。具体来说，首先对原数组进行两两归并(即数组中元素按顺序两两为一组，对每一组分别进行归并)，然后是四四归并……，一直到数组有序。

## 演示

- 自顶向下的归并排序

<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/mergeSort.gif" style="zoom:20"></center>
- 自底向上的归并排序

  <center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/mergeBU.jpg" ></center>

## 代码实现

- 归并函数

  归并操作是归并排序的一个核心操作，其目的是将两个有序的子数组合并成一个有序数组。这里首先给出归并操作的函数。

  ```java
  private static void merge(int[] arr, int lo, int mid, int hi){
      /*
      lo: 归并部分的左边界
      hi: 归并部分的右边界
      mid: 两个归并部分的分界
      */
      int i = lo, j = mid + 1;
      // 复制arr[lo]... arr[hi]到辅助数组aux[]，aux[]是归并排序类的类私有变量
      for(int k = lo; k <= hi; k++){
          aux[k] = arr[k];
      }
  
      for(int k = lo; k <= hi; k++){
          if(i > mid)   arr[k] = aux[j++];   // 左半边用尽则直接取右半边元素
          else if(j > hi)   arr[k] = aux[i++];   // 右半边用尽则直接取左半边元素
          else if(aux[j] < aux[i])   arr[k] = aux[j++];// 左边元素大于右边元素， 取右边元素
          else  arr[k] = aux[i++];   // 右边元素大于等于左边元素，取左边元素
      }
  }
  ```

  

- 自顶向下的排序

  sortTD()函数是提供调用的归并排序函数，在其内部递归调用对子数组的排序函数

  ```java
   // 自顶向下的归并排序
   public static void sortTD(int[] arr){
   // 数组为空或者长度为1不需要排序
   if(arr == null || arr.length < 2){
   return;
   }
  
  aux = new int[arr.length];   // 给辅助数组分配和原数组大小相同的空间
  sort(arr, 0, arr.length - 1);  // 递归排序函数的入口
  }
  ```

  sort()函数是私有的，因为外部不需要调用，它不断的对自己进行递归调用达到化整为零的效果。

  ```java
  private static void sort(int[] arr, int lo, int hi){
       /*
      lo: 排序数组左边界
      hi: 排序数组右边界
      */
      if(hi <= lo){
          return;
      }
      int mid = (lo + hi) / 2;
      sort(arr, lo, mid);   // 对左半边进行排序
      sort(arr, mid+1, hi); // 对右半边进行排序
      /* 由于左右两边此时都是有序的，所以当arr[mid] <= arr[mid+1]时
          表明数组整体也是有序的，不需要进行合并*/
      if(arr[mid] <= arr[mid+1]){
          return;
      }
      merge(arr, lo, mid ,hi);  //合归并左右两部分
  }
  ```

- 自底向上的排序

  自底向上的归并排序代码较为简单，从第一轮每个子数组大小为1开始，每一轮的子数组大小翻倍。

  ```java
  // 自底向上的归并排序
  public static void sortBU(int[] arr){
      // 数组为空或者长度为1不需要排序
      if(arr == null || arr.length < 2){
          return;
      }
  
      int N = arr.length;
      aux = new int[N];   // 给辅助数组分配和原数组大小相同的空间
      for(int sz = 1; sz < N; sz += sz){
          //从两两归并开始， 不断增大归并的规模
          for(int lo = 0; lo < N-sz; lo += sz+sz){
              merge(arr, lo, lo+sz-1, Math.min(lo+sz+sz-1, N-1));
          }
      }
  }
  ```

## 分析

- **时间复杂度**

  采用了分治的思想后，归并排序的时间复杂度达到了$O(nlogn)$，并且最好最差情况下都是$O(nlogn)$。

- **空间复杂度**

  由于归并排序需要一个和待排序数组辅助数组来存放临时数组，因此其空间复杂度为$O(n)$，也就是需要的额外空间和原数组大小成正比。

- **稳定性**

  无论是自顶向下的归并排序还是自底向上的归并排序，对于相同的元素，排序都不会改变他们的相对顺序，因此归并排序是稳定的。



&emsp;&emsp;对于小规模问题，使用自顶向下的归并排序会频繁的递归调用排序函数，因此在使用归并排序时可以使用插入排序处理小规模的子数组，提高算法效率。

### 参考文献

> 《算法(第四版)》

