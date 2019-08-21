---
title: 冒泡排序
categories:
  - 算法基础
tags:
  - 排序
  - java
comments: false
mathjax: true
date: 2019-08-20 19:45:21
img: '/img/blog/maopao.jpg'
---

## 基本思想
&emsp;&emsp;冒泡排序是基于比较和交换的排序算法。它重复地比较两两元素，如果他们顺序错误就进行交换，直到没有交换发生表明数组已经有序。

&emsp;&emsp;之所以称为冒泡排序是因为在比较排序过程中，较小的元素会慢慢的前移，类似于碳酸饮料中二氧化碳的气泡上浮过程。

## 算法流程

1. 比较相邻元素，如果第一个元素比第二个元素大则交换它们的位置；
2. 从开始第一对到最后一对的每一对相邻元素做步骤1同样的工作，这一步之后最后一个元素应该是最大值；
3. 针对所有的元素重复以上的步骤；(实质上第 $i$ 趟排序之后至少最后 $i$ 个元素已经有序了，因此只需要对前 $n-i$ 个元素进行以上步骤)；
4. 重复步骤1~3，直到排序完成。

## 演示
<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/bubblesort.gif"></center>
## 实现代码
冒泡排序 Java 版本的实现如下(代码只给出了排序函数的实现):
```java
public static void bubble_sort(int[] arr){
    // 数组为空或者长度为1不需要排序
    if(arr == null || arr.length < 2){
        return;
    }
    
    int N = arr.length;
    for(int i = 0; i < N - 1; i++){
        for(int j = 0; j < N - i - 1; j++){
            // 如果相邻元素顺序不对则交换
            if(arr[j] > arr[j+1]){
                int temp = arr[j];
                arr[j] = arr[j+1];
                arr[j+1] = temp;
            }
        }
    }
}
```
**优化**：实质上，如果某一次遍历过程中没有发生元素交换就说明数组已经有序，此时就可以提前终止排序。因此，可以在每趟排序前设置一个flag，初始设置为false，如果发生了元素交换则更新flag=true，这样本趟排序完成后如果flag仍为false说明没有元素交换，则退出排序。代码如下：
```java
public static void bubble_sort(int[] arr){
    // 数组为空或者长度为1不需要排序
    if(arr == null || arr.length < 2){
        return;
    }
    
    int N = arr.length;
    for(int i = 0; i < N - 1; i++){
    	boolean flag = false;    // 标记本趟排序是否发生元素交换
        for(int j = 0; j < N - i - 1; j++){
            // 如果相邻元素顺序不对则交换
            if(arr[j] > arr[j+1]){
                int temp = arr[j];
                arr[j] = arr[j+1];
                arr[j+1] = temp;
                flag = true;
            }
        }
        if(!flag){		// 一趟排序完成后flag仍为false，说明数组已经有序
        	break;
        }
    }
}
```
## 复杂度

 - **时间复杂度**
 最好情况下：初始时刻数组就是有序的，在设置了标记的时候，第一趟排序后排序就终止了，因此只需要进行$n-1$次比较，不需要进行交换，最好的时间复杂度为 $O(n)$。(但是如果不设置标记，算法仍旧需要比较 $n(n-1)/2$ 次，时间复杂度为 $O(n-1)$)
 最坏情况下：初始时刻数组是倒序的，这样算法需要进行 $n(n-1)/2$ 次比较和 $n(n-1)/2$ 次交换，时间复杂度为 $O(n^2)$ 。
 - **空间复杂度**：冒泡排序仅在交换元素的时候使用了常数级别的额外空间，因此空间复杂度为 $O(1)$。 

## 稳定性
冒泡排序依据相邻元素的大小关系来决定是否交换，因此排序不会改变两个相同元素的顺序，所以是稳定的。

