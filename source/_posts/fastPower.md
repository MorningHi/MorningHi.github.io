---
title: 剑指Offer[16]：数值的整数次方
categories:
  - 刷题笔记
tags:
  - 位运算
  - 递归
comments: false
mathjax: true
date: 2019-09-07 12:57:31
img:
---

## 题目

&emsp;&emsp; 实现函数 `double Power(double base, int exponent)`, 求base的exponent次方，不使用库函数，同时不需要考虑大数问题。

## 分析

&emsp;&emsp;书中作者已经分析的很清楚了，主要有两个方面去考虑：

1. 指数(exponent)为0和负数的情况；
2. 求解的效率问题。

&emsp;&emsp;第一个方面是容易被忽略的，还有需要考虑底数为0时指数小于等于0是没有意义的；第二个方面就是通过数学分析来提高算法效率，具体来说就是，用以下思想来求 $a$ 的 $n$ 次方：

$$a^{n}=\left\{\begin{matrix}
a^{n/2}\cdot a^{n/2}, &   n\text{ is an even number}\\ 
a^{(n-1)/2}\cdot a^{(n-1)/2} \cdot a, &   n\text{ is an odd number}
\end{matrix}\right.$$

例如，求 $x$ 的32次方，只需要进行五次乘法：先求 $x$ 的平方，再在此基础上求 $x$ 的4次方，接下来在 $x$ 的4次方的基础上求 $x$ 的8次方，在 $x$ 的8次方的基础上求 $x$ 的16次方，最后由 $x$ 的16次方得到 $x$ 的32次方。

&emsp;&emsp;除了这个问题之外，还需要考虑一种情况就是**指数为int型最大值($2^{31}-1$)和最小值($-2^{31}$)**的情况。但是书中为什么没有分析这种情况呢？下面代码实现部分具体来看。

## 代码

### c++实现

&emsp;&emsp;《剑指Offer》一书是用C++语言实现的，这里首先给出c++版本的完整实现：

```c++
#include <stdio.h>
#include <iostream>
using namespace std;
bool g_InvalidInput = false;  // 用来标记非法输入，例如底数为0，指数小于等于零的情况

/*
double 是双精度类型的变量，不能直接用 == 判断两个double类型的数是否相等
因此设置精度为1e-16,两个double数差的绝对值小于1e-16就认为他们相等
*/
bool equal(double x1, double x2){
    return ((x1 - x2) < 1e-16) && ((x1 - x2) > -1e-16);
}

double PowerWithUnsignedExponent(double base, unsigned int exponent){
    if(exponent == 0)   return 1;
    if(exponent == 1)   return base;

    double result = PowerWithUnsignedExponent(base, exponent >> 1); // 使用移位运算代替除以2
    result *= result;
    if(exponent & 0x1 == 1){  // 通过位运算判断指数的奇偶性
        result *= base;
    }
    return result;
}

double Power(double base, int exponent){
    g_InvalidInput = false;
    if(equal(base, 0.0) && exponent <= 0){  // 非法输入的情况
        g_InvalidInput  = true;
        return 0;
    }

    // 通过unsigned int 将指数转化为其绝对值，这样正指数和负指数的情况就可以一起计算
    unsigned int absExponent = (unsigned int)(exponent);
    if(exponent < 0){
        absExponent = (unsigned int)(-exponent);
    }
    double result = PowerWithUnsignedExponent(base, absExponent);
    // 对于负指数，最终结果应当是按其绝对值计算得到结果的倒数
    if(exponent < 0){
        return 1.0 / result;
    }
    return result;
}


int main(){
    int exponent = -2;
    double base = 2.0;
    cout << Power(base, exponent) << endl;
    return 0;
}
```

这里有以下几个点需要注意：

1. 首先，通过`unsigned int`将指数转化为其绝对值，正数的绝对值为`(unsigned int)(exponent)`，负数的绝对值为`(unsigned int)(-exponent)`，并且由于`unsigned int`类型的数据范围为0~4294967295($2^{32}-1$)，`int`型数据范围为-2147483648~2147483647($-2^{31},2^{31}-1$)，数据转换也很好的处理了边界指数的问题；
2. 不能直接用 `==` 判断两个`double`类型数据相等，因为计算机在处理浮点数的时候会有精度误差；
3. 由于位运算的效率非常高，因此程序中通过右移运算符代替除以2的运算，通过和 0x1做与运算判断指数的奇偶性。

### java实现

&emsp;&emsp;c++学的太烂了，所以平时java用的多一点，于是看完书中讲解就想着用java实现一下，然后自然而然写出了以下代码：

```java
public class FastPower {

    public static void main(String[] args) {
        double base = 2;
        int exponent = -2;
        double result = Power(base, exponent);
        System.out.print(result);
    }

    public static double Power(double base, int exponent){
        if((Math.abs(base - 0.0) < 1e-16) && (exponent <= 0)){
            return 0;
        }
        
        // 指数为负数的时候，将指数变为其相反数，底数变为其倒数
        if(exponent < 0){
            exponent = -exponent;
            base = 1 / base;
        }
        double result = myPow(base, exponent);
        return result;
    }

    private static double myPow(double base, int exponent) {
        if(exponent == 0)  return 1;   // 递归的返回条件
        if(exponent == 1)  return base;
        double result = myPow(base, exponent >> 1);
        result *= result;
        if((exponent & 0x1) == 1){
            result *= base;
        }
        return result;
    }
}
```

乍一看好像没问题，运行一般的值也正常，直到将指数设置为$-2^{31}$，发现运行报错，如下图

<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/LeetCode/Exception.jpg"></center>
栈溢出？？？在递归的时候，如果栈溢出首先要考虑的就是递归是不是没有返回，这种情况下会一直递归下去造成栈溢出。但是程序里为什么会出现栈溢出呢？最后调试发现。。。。。分明对exponent取了相反数，为什么相反数还是本身？

&emsp;&emsp;到这里就发现问题了，对于int型的值$-2^{31}$，在取相反数后会超出int型的范围，因此又成了自己，然后在移位过程中有符号的`int`型负数，最高位一直是1，因此永远无法满足递归的返回条件。这样就理解为什么c++程序中使用了`unsigned int`，但是Java没有`unsigned int`类型，因此**用移位代替除以2的操作在指数为$-2^{31}$是不可行的**，需要特别注意。

因此，需要将程序中的`exponent >> 1`改为`exponent / 2`，此外，将移位操作符换为除以2之后，`if(exponent < 0)`中的执行语句也可以去掉`exponent = -exponent;`。