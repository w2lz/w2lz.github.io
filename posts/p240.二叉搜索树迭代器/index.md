# P240. 二叉搜索树迭代器


<!--more-->

## 描述

这个题目说的是，给你一棵二叉搜索树，你要为它实现一个迭代器。迭代器中包含两个公有方法，next() 方法返回二叉搜索树中下一个最小的数字，hasNext() 方法返回是否还存在下一个数字。

注意，next() 方法和 hasNext() 方法都要求平均时间复杂度是 O(1)，并且额外只能使用 O(h) 的辅助空间。其中，h 是二叉搜索树的高度。另外，你可以假设对 next() 方法的调用总是有效的，即不需要考虑在 next() 方法中处理不存在下一个数字的情况。

```markdown
比如说，给你的二叉搜索树 t 是：

t:
    1
  /   \
 0     4
      / \
     2   8

用 t 初始化迭代器，然后就可以调用 next() 和 hasNext()：

BSTIterator it = new BSTIterator(t);
it.next();    //  0
it.next();    //  1
it.next();    //  2
it.hasNext(); // true
it.next();    //  4
it.next();    //  8
it.hasNext(); // false
```

## LeetCode

[173. 二叉搜索树迭代器](https://leetcode.cn/problems/binary-search-tree-iterator/description/)

## 难度

**中等**

## 题解

```java

```


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/p240.%E4%BA%8C%E5%8F%89%E6%90%9C%E7%B4%A2%E6%A0%91%E8%BF%AD%E4%BB%A3%E5%99%A8/  

