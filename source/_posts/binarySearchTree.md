---
title: 二叉查找树
categories:
  - 算法基础
  - java
tags:
  - 二叉树
comments: false
date: 2019-09-22 17:14:52
img:
---

## 定义

- **数组和链表**

  数组和链表是两种使用频率非常高的数据结构，但两者的结构也决定了他们的应用场景。数组的元素在内存中的地址是连续的，而构成链表的结点在内存中的地址不连续，数组的元素可以通过索引进行随机访问，链表则需要遍历访问，因此**数组的查询速度比链表快**；另一方面链表的长度可变，插入和删除元素的效率非常高，因此在**增删操作较多的情况下链表性能比数组好**。

- **二叉查找树**

  二叉查找树(Binary Search Tree)是一种结合了数组查找的高效性和链表插入删除的灵活性的数据结构。简单来说，一个二叉查找树是一棵二叉树，并且满足其每个结点的值都大于其左子树所有结点的值但小于其右子树所有结点的值。下图是一个结点存储整数的二叉查找树的例子。

![](https://img-blog.csdnimg.cn/20190922174530756.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0x1Y2t5X0dv,size_18,color_FFFFF1,t_50)

## 实现

&emsp;&emsp;接下来从二叉查找树的结点开始实现一个二叉查找树及其相关操作。

- **结点表示**
&emsp;&emsp;一个简单的二叉查找树中的结点包含以下属性：节点的值、指向左子树的链接和指向右子树的链接。其中，某个结点的左链接指向一棵所有节点值均小于结点自身值的子树，同样，右链接指向一棵所有节点值均大于自身值的子树。结点类表示如下：

```java
private class Node{
    private int val;       // 节点值
    private Node left;     // 指向结点左子树的链接
    private Node right;    // 指向节点右子树的链接

    public Node(int val){
        this.val = val;
    }
}
```

- **构建二叉查找树和插入元素**
&emsp;&emsp;构建二叉查找树实质上就在一个空的二叉树上插入元素，所以这里将构建二叉查找树和插入元素放到一起分析。
&emsp;&emsp;给定一个元素，为了保证将其插入二叉查找树后不破坏二叉查找树的性质，我们需要从根节点开始比较。如果根结点为空则返回由待插入值构成根结点，如果根结点大于当前值说明当前值应该插入到根节点的左子树，如果根结点小于当前值则应该插入到根节点的右子树。这显然就是**递归**的思想，可以直接根据递归的思想写出下面的代码：
```java
private Node insert(Node root, int val){ 
    if(root == null){   //root为空则用当前值构成root返回
        Node newNode = new Node(val);
        root = newNode;
        return root;   
    }
    Node current = root;
    // 当前值大于当前节点的值，则插入当前节点左子树中
    if(val > current.val){
        current.right = insert(current.right, val);
    }
    // 否则插入当前节点的右子树中
    else{
        current.left = insert(current.left, val);
    }
    return root;
}
```
接下来再看看**迭代**的实现方式：
```java
private Node insert(Node root, int val){
    Node newNode = new Node(val);
    if(root == null){      // 如果当前位置为空，将新节点放在当前位置
        root = newNode;
        return root;
    }
    Node parent = root;
    Node current = root;
    while(true){
        parent = current;
        if(current.val > val){                // 插入值比当前节点值大，应该位于左子树
            current = current.left;
            if(current == null){
                parent.left = newNode;
                break;
            }
        }
        else{
            current = current.right;        // 插入值比当前节点值小，应该位于右子树
            if(current == null){
                parent.right = newNode;
                break;
            }
        }
    }
    return root;
}
```
迭代的实现方式不断利用二叉查找树的性质遍历节点，知道找到给定值应当插入的位置，最后返回根节点。无论是递归还是迭代的方式，插入节点都对于已有节点没有影响，是不是像极了链表。

- **查找元素**
&emsp;&emsp;查找过程利用二叉查找树的性质，就是一句话：大于当前节点就去右边找，小于当前节点就去左边找。
**递归**的查找方式如下：
```java
public boolean search(Node current, int val){
    // 二叉树为空或者只有根节点一个节点且根节点值不等于要查找的值，返回false
    if(current == null)   return false;
    if(val < current.val){
        return search(current.left, val);   //小于当前值在左边找
    }
    else{
        return search(current.left, val);  // 大于当前值在右边找
    }
}
```
**迭代**的查找方式如下：
```java
public boolean search(int val){
    // 二叉树为空或者只有根节点一个节点且根节点值不等于要查找的值，返回false
    if(root == null || (root.left == null && root.right == null && root.val != val))   return false;
    Node current = root;
    while(current != null && current.val != val){
        if(val < current.val){
            current = current.left;   //小于当前值在左边找
        }
        else{
            current = current.right;  // 大于当前值在右边找
        }
    }
    return !(current == null);   // 如果current为空表明没有找到
}
```
- **删除元素**
&emsp;&emsp;删除元素是二叉查找树中一个比较复杂的操作，因为在删除某个元素后仍需要将剩余元素构成一棵二叉查找树，因此删除元素后涉及到树结构的调整。这里首先来看看怎么删除二叉查找树中的最大值和最小值。
  1. 删除二叉查找树中的最小值
   要删除最小值首先需要查找最小值，由二叉查找树的性质可以看到二叉查找树的最小值就是其最左边的结点。如下图，左边二叉查找树的最小值为11，右边二叉查找树的最小值为17，均为其最左边的结点值(可以看到并**不一定是叶子结点**)。
  
   <center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/tree_search/minBST.JPG" style="zoom:20"></center>
   
  2.  对于左边的二叉查找树删除最小结点只需要将值为11的结点从二叉树中移除，并不需要调整结构；但是对于右边的树，删除最小结点后由于最小值结点还有孩子结点，需要将其孩子结点连接到树中。代码如下：
```java
public void deleteMin() throws Exception{
    if(root == null){
        throw new Exception("Can't delete node from an empty Tree!");    // 二叉树为空
    }
    root = deleteMin(root);
}
private Node deleteMin(Node current){
    if(current.left == null){   // current结点的左子树为空，则最小值为current节点，将其指向右节点即可
        return current.right;
    }
    // 否则递归地删除左子树中的最小节点
    current.left = deleteMin(current.left);
    return current;
}
```
  2. 删除二叉查找树中的最大值
      理解了删除二叉查找树的最小值结点，删除最大值结点也就很容易。二叉查找树的最大值结点为其最右边的结点。递归的删除方法如下：
```java
public void deleteMax() throws Exception{
    if(root == null){
        throw new Exception("Can't delete node from an empty Tree!");    // 二叉树为空
    }
    root = deleteMax(root);
}
private Node deleteMax(Node current){
    if(current.right == null){        // 右子树为空，则最大值为current节点
        return current.left;
    }
    // 递归地删除其右子树最小值结点
    current.right = deleteMax(current.right);
    return current;
}
```
  3. **删除任意元素**

如下图所示，现在需要删除左边二叉查找树中值为17的结点，分析可知要保证满足二叉查找树的性质则删除结点后树应该如下图右所示：

<center><img src="https://github.com/MorningHi/ImgRepository/raw/master/Hexoblog/tree_search/deleteBST.JPG" style="zoom:20"></center>

可以分析一下不同情况：

a) 假如图中值为17的结点没有左子树也没有右子树，则直接从树中删除即可；

b) 假如值为17的结点有左子树，没有右子树，则删除结点17后需要让其父节点指向其左孩子结点，由于二叉查找树中每个结点的子树也都满足二叉查找树的性质，因此这样处理之后整个树仍然是一棵二叉查找树；

c) 假如图中值为17的结点没有右子树但有左子树，和情况b类似，直接让其父节点指向其右孩子结点即可；

d) 最后一种情况，也是最复杂并且一般的情况，图中值为17的结点既有左子树又有右子树，删除后让其父节点指向其中一个孩子节点显然是不行的。这时候，删除结点17后，原本结点应当被右子树中最小的结点替代，再将其右子树删除最小结点后连接到当前结点，这样仍旧满足左子树结点值均小于当前结点，右子树的结点值均大于当前结点。

代码如下：

```java
public void delete(int val) throws Exception{
    root = delete(root, val);
}
private Node delete(Node current, int val) throws Exception{
    if(current == null){         // 说明需要删除的值不在树中
        throw new Exception("Value doesn't exist!");
    }
	// 1. 查找要删除的元素
    if(current.val > val)      current.left = delete(current.left, val);          // 在左子树中
    else if(current.val < val) current.right = delete(current.right, val);        // 在右子树中
    else{
        if(current.left == null)      return current.right;
        if(current.right == null)     return current.left;
        Node temp = current;   // 2.将指向要删除节点的链接保存为temp
        current = min(current.right);     // 3.找到要删除节点右子树中最小的节点代替当前节点
        current.right = deleteMin(current.right);     // 4. 删除最小值后的右子树作为当前节点的右子树；
        current.left = temp.left;  // 5. 将当前结点的指向原节点的左子树
    }

    return current;
}
```

其中的`min(Node current)`表示找以current为根节点的二叉树的最小值的函数。总结一下就是以下5步：

1. 查找要删除的结点current；
2. 将指向要删除节点的链接保存为temp;
3. 用待删除结点右子树中的最小结点(`min(current.right)`)代替待删除结点；
4. 删除最小值后的右子树作为当前节点的右子树；
5. 将当前结点的指向原节点的左子树。



**完整实现代码**可以在我的[Github](https://github.com/MorningHi/BasicAlgorithms/blob/master/tree_and_search/BinarySearchTree.java)上下载。

### 参考资料

> 《算法》第四版 