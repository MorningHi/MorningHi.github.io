---
title: 希尔排序
categories:
  - 算法基础
tags:
  - 排序
  - java
comments: false
mathjax: true
date: 2019-08-22 19:06:43
img: 'img/blog/shell.jpg'
---

## 基本思想

- **h有序数组**

  一个数组中任意间隔为 $h$ 的元素都是有序的，那这个数组就是**h有序数组**。如下图所示这个数组就是一个 $h$ 有序数组，其中 $h=4$。可以看到，虽然整个数组是乱序的，但任意相隔 $h=4$ 的元素都是有序的。

  <center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/hsort.PNG" style=“zoom:60”></center>

- **希尔排序**

  希尔排序的思想就是，首先让数组 $h$ 有序，然后不断减小 $h$ 的值。试想一下，当 $h=1$ 的时候，数组 $h$ 有序也就意味着任意元素和它相邻的元素是有序的，这也就意味着整个数组已经有序了。希尔排序是第一个突破 $O(n^2)$ 的排序算法。

## 算法流程

1. 首先给 $h$设置一个初始值(通常可以去数组长度的一半)；
2. 遍历数组，使得所有相隔 $h$ 的元素组成的子序列有序；
3. 更新 $h$：$h=h/2$，重复步骤2，直到 $h<1$，排序完成。

## 演示

<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/ShellSort.gif"></center>

&emsp;&emsp;图中，第一遍排序时 $h=5$，第二遍 $h=2$，第三遍 $h=1$，第三遍排序完成后相邻两个元素有序，因此整个数组有序。

## 代码实现

```java
public static void shell_sort(int[] arr){
    int N = arr.length;
    // 数组为空或者长度为1不需要排序
    if(arr == null || N <= 1){
        return;
    }
    
    int h = N;
    while(h >=1){
        for(int i = h; i < N; i++){
            // 对于当前的h, 让数组h有序
            for(int j = i; j >= h; j -= h){
                if(arr[j] < arr[j-h]){
                    int temp = arr[j];
                    arr[j] = arr[j-h];
                    arr[j-h] = temp;
                }
            }
        }
        h /= 2;   // 缩减h的值
    }
}
```

## 分析

- **时间复杂度**

  之前在各种排序算法比较中虽然列出了希尔排序的复杂度，但实际情况是，希尔排序的性能与 $h$ 和 $h$ 之间的数学性质有很大的关系，有许多论文都在研究各种不同的 $h$ 递增序列，但到现在为止还无法证明某个序列是最好的。因此这里不深究其时间复杂度。

- **空间复杂度**

  希尔排序只使用了常数级别的额外空间，因此空间复杂度为 $O(n^2)$。

- **稳定性**

  希尔排序过程中各个子序列不影响，在排序中可能会造成相同元素相对位置的改变，因此是不稳定的。

### 参考文献

> 《算法(第四版)》

