---
title: 计数排序/桶排序/基数排序
categories:
  - 算法基础
tags:
  - 排序
  - java
comments: false
mathjax: true
date: 2019-08-26 16:54:24
img: 'img/blog/counting.jpg'
---

&emsp;&emsp;计数排序、桶排序和基数排序是三个线性时间非比较类排序算法，他们相同的特点在于不是通过比较元素的大小来确定他们的排列顺序，并且均能够达到线性时间复杂度$O(n+k)$，之所以这样根本在于他们都使用了额外的桶(bucket, 不是某种特定数据结构，只是一种叫法)来辅助存储数据。但是这三种排序算法的应用场景都比较有限，下面具体来讲一下这三种排序算法。

## 计数排序

### 基本思想

&emsp;&emsp;以一个整数数列为例，如果我们知道它的最大值和最小值，那么也就意味着数组中的所有元素都位于 [最小值，最大值] 这个区间，我们对这个区间的每一个数在数组中出现的次数，然后在从小到大展开，这样就完成了排序，这就是计数排序(counting sort)的基本思想。可以看到，计数排序**不通过比较元素的大小关系来进行排序，因此是一种非比较类排序算法**。

### 算法流程

1. 首先遍历数组找出最大值maxValue和最小值minValue，并根据最大最小值确定需要的桶(bucket)的个数 maxValue-minValue+1，这样每个桶就对应[minValue, maxValue]中的一个值；
2. 遍历数组计数[minValue, maxValue]中每一个值在数组中出现的次数，存放到对应的桶中；
3. 释放桶，每个位置的数字放回原数组。

### 演示

<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/CountingSort.gif"></center>
### 代码实现

&emsp;&emsp;计数排序的代码如下。这里使用了数组中最大最小值来确定需要的桶的个数，也有程序直接默认0为数组的最小值，但这样就无法处理带负数的数组，同时对于最小值远大于0的非负数组也会造成空间浪费。

```java
public static void counting_sort(int[] arr){
    // 数组为空或者长度为1不需要排序
    if(arr == null || arr.length < 2){
        return;
    }

    int maxValue = arr[0], minValue = arr[0];   // 找出最大最小值
    for(int n : arr){
        if(n > maxValue)    maxValue = n;
        else if(n < minValue)    minValue = n;
    }

    int bucketLen = maxValue - minValue + 1;   //  需要的桶的个数
    int[] bucket = new int[bucketLen];
    for(int n : arr){
        // 桶中每个位置用来计数原数组中等于"当前桶索引+minValue"的数的个数
        bucket[n-minValue]++;    
    }

    int sortedIndex = 0;   // 将桶里的元素放回原数组
    for(int i = 0; i < bucketLen; i++){
        while(bucket[i]-- > 0){
            arr[sortedIndex++] = i + minValue;
        }
    }
}
```

### 分析

- 时间复杂度

  计数排序中共有三个for循环，前两个循环都是从前向后遍历数组，时间复杂度为 $O(n)$，第三个for循环从桶中取回元素到数组中，时间复杂度为 $O(n+k)$ ，其中 $k$ 是需要的桶的个数。总的来说基数排序的时间复杂度为 $O(n+k)$ ，最好最坏情况下都为 $O(n+k)$ 。

- 空间复杂度

  计数排序使用了额外的长度为 $k$ 的数组来存放计数结果，空间复杂度为 $O(k)$。

- 稳定性

  计数排序是稳定的排序算法。

## 桶排序

### 基本思想

&emsp;&emsp;桶排序和计数排序的思路很象，只不过计数排序的“桶”只是用来计数每个元素的次数，而桶排序中的“桶”是真实存放元素的。桶排序中每个桶都是一个可以存放元素的容器，用来将原数组中的元素按照一定的**映射规则**放到桶里并且满足第 $i$ 个桶里的元素均小于第 $j$ 个桶里的元素($i<j$)，然后对每一个桶里的元素分别进行排序，这样在所有的桶排序完成后再将元素取出放回到原数组。

### 算法流程

1. 首先根据元素范围和映射规则确定需要使用的桶的个数 $k$ ;
2. 遍历数组并根据映射规则将每个元素放到对应的桶中；
3. 对每个桶中的元素单独进行排序；
4. 待所有桶中的元素均排序完成后，将元素依次放回原数组，排序完成。

&emsp;&emsp;上面一直在提一个“**映射规则**”，这个映射规则的目的就是确定某个元素应该被放到哪个桶里，从而当 $i<j$ 时满足第 $i$ 个桶里的元素均小于第 $j$ 个桶里的元素，这样保证在所有桶里元素均排序完成后将元素放回数组也是有序的。

&emsp;&emsp;映射规则是人为设计的具体来说，如果我们预设桶的容量为 $m$，也就是每个桶中最多存放 $m$ 个元素，假设数组最大值和最小值分别为 $max$ 和 $min$，那么桶排序需要的桶的个数就为 $(max-min)/m$，接下来通过以下映射确定元素 $x$ 应当被放到哪个桶中：

$$i=\frac{x-min}{m}$$

可以理解为我们将 $[min,max]$ 区间划分为了 $k$ 个连续的子区间，然后将数组中的数放到所属的区间内，接下来对每个区间内的数单独排序，最后使得整个数组有序。

### 代码实现

```java
public static void bucket_sort(int[] arr){
    // 数组为空或者长度为1不需要排序
    if(arr == null || arr.length < 2){
        return;
    }

    int bucketSize = 10;   // 设定桶的容量
    int maxValue = arr[0], minValue = arr[0];   // 找出最大最小值
    for(int n : arr){
        if(n > maxValue)    maxValue = n;
        else if(n < minValue)    minValue = n;
    }

    // 创建桶的集合
    ArrayList<LinkedList<Integer>> buckets = new ArrayList<>();
    int bucketLen = (int)Math.floor((maxValue - minValue) / bucketSize) + 1;   //  需要的桶的个数
    for(int i = 0; i < bucketLen; i++){
        buckets.add(new LinkedList<Integer>());
    }

    //  将数组中的元素根据所处区间放到桶中
    for(int n : arr){
        int index = (int)Math.floor((n - minValue) / bucketSize); // 确定当前元素应该放到第几个桶中
        buckets.get(index).add(n);
    }

    // 对每个桶中的元素进行排序
    for(LinkedList<Integer> bucket : buckets){
        if(bucket.size() > 1){ 
            Collections.sort(bucket); // 桶中元素大于1才需要排序
        }
    }

    // 从桶中按顺序取出元素放回数组
    int index = 0;
    for(LinkedList<Integer> bucket : buckets){
        for(int n : bucket){
            arr[index++] = n;
        }
    }
}
```

&emsp;&emsp;对于每个桶中元素单独排序的时候，由于桶的容量较小，因此使用像插入排序之类的基础排序方法性能也比较好。(代码中作弊使用了java自带的排序函数)

### 分析

- 时间复杂度

  桶排序的平均时间复杂度为 $O(n+k)$ ，最好的时间复杂度为 $O(n+k)$ ，最坏的情况下时间复杂度为 $O(n^2)$。 当 $k==n$ 时，桶排序就变成了计数排序；当 $k==1$ 时，所有元素放在一个桶里，桶排序就演化成了内部排序的实现方式。

- 空间复杂度

  桶排序使用了 $k$ 个桶来存放数组的所有元素，空间复杂度为 $O(n+k)$。

- 稳定性

  桶排序的稳定性取决于内部排序算法。

## 基数排序

### 基本思想

&emsp;&emsp;基数排序是另一种使用了桶来辅助排序的非比较类排序。从直观上来讲，对于一个均为正数的整型数组，每个数字的每一位都是0-9中的某个数字。这样我们从最低位(个位)开始将每个数字放到最低位数字对应的桶中(编号0-9)，这样按桶的顺序得到一个新排列的数组，接下来按照十位的数字再放到相应桶中，一直到最高位，最终就得到一个有序的数组。

### 算法流程

这里以一个元素均为正整数的数组为例，基数排序的步骤如下：

1. 遍历数组，找出数组中最大数的位数；
2. 生成编号为0-9的桶用来存放元素；
3. 遍历数组，得到每个数字最低位的数字 $i$，将这个数放入编号为 $i$ 的桶中；
4. 按桶的编号顺序将桶中的元素放回数组；
5. 遍历数组，得到每个元素的次低位元素，按照步骤3的规则放入对应的桶中；
6. 按照同样的方式依次得到每个元素更高位的数，放入桶中，再按序放回数组，直到处理完最大数的最高位。

### 演示

<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/sort/RadixSort.gif"></center>
&emsp;&emsp;上图演示了最高位数为两位的正整数排序过程，对于更大的数，使用相同的方法不断将元素放到桶中再取出就可以达到排序的效果。

### 代码实现

&emsp;&emsp;首先从最基础的待排序元素均为非负整数的排序过程来看，代码如下：

```java
 public static void radix_sort(int[] arr){
     // 数组为空或者长度为1不需要排序
     if(arr == null || arr.length < 2){
         return;
     }

     int maxBit = getMaxBit(arr);  // 计算数组中最大的数的位数
     int bitCount = 0, div = 1, mod = 10;
     while(bitCount++ < maxBit){
         // 生成桶集合，共10个桶代表0-9的数字
         ArrayList<Integer>[] buckets = new ArrayList[10];
         for(int i = 0; i < 10; i++){
             buckets[i] = new ArrayList<Integer>();
         }
         for(int n : arr){
             // (n%mod)/div取每个数当前位的值：0-9 
             int current = (n % mod) / div + 10;
             buckets[current].add(n);  // 根据当前位的数将元素放入对应的桶中
         }

         // 将桶中的数放回数组
         int index = 0;
         for(int i = 0; i < 10; i++){
             if(!buckets[i].isEmpty()){
                 for(int n : buckets[i]){
                     arr[index++] = n;
                 }
             }
         }
         div *= 10;
         mod *= 10;
     }

 }

private static int getMaxBit(int[] arr){
    int maxValue = arr[0], bit = 0;
    for(int n : arr){
        if(n > maxValue)  maxValue = n;   // 找出数组中最大的数
    }
    while(maxValue > 0){
        bit++;
        maxValue /= 10;
    }
    return bit;
}
```

`getMaxBit(int[] arr)`函数用来得到数组中最大数的位数。同时需要注意在获取每个元素当前位的数时不能改变元素的值，在代码中通过两行元素实现：

```java
int current = (n % mod) / div + 10;
buckets[current].add(n);  
```

初始 mod=10，div=1，以152为例，第一次循环得到个位的值为 `(152 % 10)/1 = 2`，每次循环之后mod和div均变为原来的十倍，这样取十位的值就是： `(152 % 100)/10 = 5`，取百位的值就是 `(152 % 1000)/100 = 1`.

&emsp;&emsp;但是上面的程序只能处理非负元素序列的排序，对于有符号的整数序列需要做出一定的修改。一种思路是将负数先按其绝对值进行排序，最后再对负数部分排列就可以。另一种思路就是扩展桶的数量的实现，对于非负整数我们用编号为0-9的桶来存放元素，那么对于有符号整数我们也可以用编号为0-18的19个桶来存放元素，每次取元素当前位值的同时带上元素的符号，这样0-8号桶存放当前位为 -9到-1的元素，9-18号桶存放当前位为0-9的元素。此外还要注意最大位数需要根据绝对值最大的元素来决定。

举个栗子：对于58和-128，首先确定最大位数为3，首先取个位值分别为8和-8，分别属于17号和1号桶，十位值分别为5和-2，分别属于14号桶和7号桶，百位值为0和-1，分别属于9号桶和8号桶。具体实现程序如下：

```java
public static void radix_sort(int[] arr){
    // 数组为空或者长度为1不需要排序
    if(arr == null || arr.length < 2){
        return;
    }

    int maxBit = getMaxBit(arr);  // 计算数组中最大的数的位数
    int bitCount = 0, div = 1, mod = 10;
    while(bitCount++ < maxBit){
        // 生成桶集合，共19个桶，0-8存放负数，9-18存放正数
        @SuppressWarnings("unchecked")
        ArrayList<Integer>[] buckets = new ArrayList[19];
        for(int i = 0; i < 19; i++){
            buckets[i] = new ArrayList<Integer>();
        }
        for(int n : arr){
            /* 
                 * (n%mod)/div取每个数当前位的值,这个值带符号
                 * 然后 +9，将原本的负数移动到区间[0-8], 原来的正数移动到[9-18]
                 */
            int current = (n % mod) / div + 9;
            buckets[current].add(n);
        }

        // 将桶中的数放回数组
        int index = 0;
        for(int i = 0; i < 19; i++){
            if(!buckets[i].isEmpty()){
                for(int n : buckets[i]){
                    arr[index++] = n;
                }
            }
        }
        div *= 10;
        mod *= 10;
    }

}

private static int getMaxBit(int[] arr){
    int maxValue = arr[0], bit = 0;
    for(int n : arr){
        if(Math.abs(n) > maxValue)  maxValue = Math.abs(n);   // 找出数组中绝对值最大的数
    }
    while(maxValue > 0){
        bit++;
        maxValue /= 10;
    }
    return bit;
}
```

### 分析

- 时间复杂度

  基数排序平均时间复杂度为 $O(n * k)$，最好时间复杂度为 $O(n * k)$，最坏时间复杂度为 $O(n * k)$。

- 空间复杂度

  基数排序需要额外 $k$ 个桶来存放所有的 $n$ 个元素，空间复杂度为 $O(n + k)$。

- 稳定性

  对于数组中相同的元素，基数排序在每次向桶里放入元素和取出元素的时候不会改变它们的原有相对顺序，因此基数排序时稳定的。

## 总结

&emsp;&emsp;计数排序、桶排序和基数排序达到线性时间复杂度的根本在于用空间换时间的策略，但是通过上面的具体解释也可以看出，它们通常只适用于整数元素序列的排序。如果是实数范围的元素很难进行排序。因此通常只在空间充足且对于整数元素排序的时候考虑它们。