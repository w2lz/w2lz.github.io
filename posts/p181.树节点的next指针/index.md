# P181. 树节点的 Next 指针


<!--more-->

## 描述

这个题目说的是，给你一棵满二叉树，每个树节点额外增加一个 next 指针，指向它右边的节点。一开始所有节点的 next 指针都为空，你要写一个函数处理这棵二叉树，使得所有节点的 next 指针都指向正确的节点。

注意，满二叉树中，所有叶子节点都在最后一层。并且除了叶子节点，所有其他节点都有两个子节点。

```markdown
// 包含 next 指针的树节点定义
public class Node {
  public int val;
  public Node left, right, next;
}

比如说，给你的满二叉树是：

     0
   /   \
  2     4
 / \   / \
6   8 10  12

一开始每个节点的 next 指针都为空。你要做的就是，让每个节点的 next 指针指向它右边的节点。对于每一层中最后一个节点，由于它右边没有节点，因此它的 next 指针仍然指向空即可。

      0 -> null
   /     \
  2  ->   4 -> null
 / \     / \
6-> 8->10-> 12 -> null
```

## LeetCode

[116. 填充每个节点的下一个右侧节点指针](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/description/)

## 难度

**中等**

## 题解

```java

```


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/p181.%E6%A0%91%E8%8A%82%E7%82%B9%E7%9A%84next%E6%8C%87%E9%92%88/  

