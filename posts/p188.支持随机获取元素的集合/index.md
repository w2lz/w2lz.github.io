# P188. 支持随机获取元素的集合


<!--more-->

## 描述

这个题目说的是，你要设计一个增加版的集合（Set），它除了支持插入元素（insert）和删除元素（remove）的操作，还能等概率地随机获取当前集合中的元素（getRandom）。并且这 3 个操作的平均时间复杂度都要求是 O(1)。

```markdown
以下是这个集合的使用示例：

// 初始化一个空集合
RandomizedSet set = new RandomizedSet();

// 成功插入数字 3，返回 true。set: [3]
set.insert(3);

// 3 已经存在于集合中，返回 false。set: [3]
set.insert(3);

// 成功插入数字 6，返回 true。set: [3, 6]
set.insert(6);

// 成功插入数字 9，返回 true。set: [3, 6, 9]
set.insert(9);

// 以 1/ 3 的概率返回 3，6 或 9。set: [3, 6, 9]
set.getRandom();

// 成功删除数字 3，返回 true。set: [6, 9]
set.remove(3);

// 8 不存在于集合中，返回 false。set: [6, 9]
set.remove(8);

// 以 1/2 的概率返回 6 或 9。set: [6, 9]
set.getRandom();
```

## LeetCode

[380. O(1) 时间插入、删除和获取随机元素](https://leetcode.cn/problems/insert-delete-getrandom-o1/description/)

## 难度

**中等**

## 题解

```java

```


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/p188.%E6%94%AF%E6%8C%81%E9%9A%8F%E6%9C%BA%E8%8E%B7%E5%8F%96%E5%85%83%E7%B4%A0%E7%9A%84%E9%9B%86%E5%90%88/  

