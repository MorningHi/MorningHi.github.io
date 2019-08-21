---
title: 插入排序
categories:
  - 算法基础
tags:
  - 排序
  - java
comments: false
date: 2019-08-21 20:44:31
mathjax: true
img: 'img/blog/insert.jpg'
---

## 基本思想

&emsp;&emsp;在玩扑克牌的时候，每当抽到一张牌的时候，就将其插入到手中已经有序的拍中，插入排序和这种整理扑克牌的思路很像。每遍历到一个新的元素就将其插入到它之前的所有元素中的合适位置，保证其之前的序列有序。

## 算法流程

- 第一趟排序：将第二个元素插入到前一个元素的合适位置；
- 第二趟排序：将第三个元素插入到前两个元素中的正确位置；
- 第三趟排序：将第四个元素插入到前三个元素中的正确位置；
  ……
- 第 $n$ 趟排序：将第 $n$ 个元素插入到前 $n-1$ 个元素中的正确位置。

可以通过下面的动图更直观地看到排序过程。显然，在每一轮排序之后，至少前 $n$ 个元素已经有序了。

## 演示

<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/InsertionSort.gif"></center>

## 实现代码

```java
public static void insertion_sort(int[] arr){
    // 数组为空或者长度为1不需要排序
    if(arr == null || arr.length < 2){
        return;
    }

    int N = arr.length;
    for(int i = 1; i < N; i++){
        // 每次循环将第j+1个元素插入到前j个元素中，保证前j+1个元素有序
        int temp = arr[i], j = i;
        while(temp < arr[j-1] && j > 0){   // 大于当前元素的全部后移
            arr[j] = arr[j-1];
            j--;
        }
        arr[j] = temp;
    }
}
```



## 复杂度

- **时间复杂度**

  最好情况下：如果一开始数组就是有序的，那么插入排序只需要 $n-1$ 次比较，不需要交换，因此最好情况下时间复杂度为 $O(n)$;

  最差情况下：如果一开始数组是倒序的，那么插入排序需要比较 $n(n-1)/2$ 次比较和 $n(n-1)/2$ 次交换，最差情况下时间复杂度为 $O(n^2)$;

  平均来看，插入排序的时间复杂度为 $O(n^2)$。

- **空间复杂度**：插入排序同样只需要常熟级的额外空间，因此空间复杂度为 $O(1)$。

## 稳定性

&emsp;&emsp;插入排序不会改变数组中相同元素的相对位置，因此是稳定的。





