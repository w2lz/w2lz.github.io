# P192. 支持随机获取元素的集合（允许重复）


<!--more-->

## 描述

这个题目说的是，你要设计一个增加版的集合（Collection），它要支持以下操作：插入元素（insert）、删除元素（remove）以及随机获取元素（getRandom）。

注意，这 3 个操作的平均时间复杂度都要求是 O(1)。另外，这个集合中允许存储重复元素，一个元素被随机返回的概率与它在集合中的数量正相关。

```markdown
以下是这个集合的使用示例：

// 初始化一个空集合
RandomizedCollection c = new RandomizedCollection();

// 成功插入数字 3，返回 true。c: [3]
c.insert(3);

// 3 已经在集合中，返回 false。c: [3, 3]
c.insert(3);

// 成功插入数字 6，返回 true。c: [3, 3, 6]
c.insert(6);

// 2/3 的概率返回 3，1/3 的概率返回 6。
// c: [3, 3, 6]
c.getRandom();

// 成功删除数字 3，返回 true。c: [3, 6]
c.remove(3);

// 8 不在集合中，返回 false。c: [3, 6]
c.remove(8);

// 以 1/2 的概率返回 3 或 6。c: [3, 6]
c.getRandom();
```

## LeetCode

[381. O(1) 时间插入、删除和获取随机元素 - 允许重复](https://leetcode.cn/problems/insert-delete-getrandom-o1-duplicates-allowed/description/)

## 难度

**困难**

## 题解

```java

```


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/p192.%E6%94%AF%E6%8C%81%E9%9A%8F%E6%9C%BA%E8%8E%B7%E5%8F%96%E5%85%83%E7%B4%A0%E7%9A%84%E9%9B%86%E5%90%88%E5%85%81%E8%AE%B8%E9%87%8D%E5%A4%8D/  

