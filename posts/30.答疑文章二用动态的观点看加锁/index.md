# 30 | 答疑文章（二）：用动态的观点看加锁


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了 InnoDB 的加锁规则，通过具体案例分析，动态解析了加锁过程。文章首先回顾了加锁规则，包括 next-key lock 作为基本单位、访问对象才会加锁等内容。随后，通过查询案例分析了不等号条件中的等值查询和并发执行可能出现的死锁问题。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

复习一下加锁规则。这个规则中，包含了两个“原则”、两个“优化”和一个“bug”：

- 原则 1：加锁的基本单位是 next-key lock，next-key lock 是前开后闭区间。

- 原则 2：查找过程中访问到的对象才会加锁。

- 优化 1：索引上的等值查询，给唯一索引加锁的时候，next-key lock 退化为行锁。

- 优化 2：索引上的等值查询，向右遍历时且最后一个值不满足等值条件的时候，next-key lock 退化为间隙锁。

- 一个 bug：唯一索引上的范围查询会访问到不满足条件的第一个值为止。

接下来的讨论基于下面这个表 t：

```sql
CREATE TABLE `t` (
  `id` int(11) NOT NULL,
  `c` int(11) DEFAULT NULL,
  `d` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `c` (`c`)
) ENGINE=InnoDB;

insert into t values(0,0,0),(5,5,5),
(10,10,10),(15,15,15),(20,20,20),(25,25,25);
```

## 不等号条件里的等值查询

先来一起来看下这个例子，分析一下这条查询语句的加锁范围：

```sql
begin;
select * from t where id&gt;9 and id&lt;12 order by id desc for update;
```

利用上面的加锁规则，我们知道这个语句的加锁范围是主键索引上的 (0,5]、(5,10]和 (10, 15)。也就是说，id=15 这一行，并没有被加上行锁。为什么呢？

我们说加锁单位是 next-key lock，都是前开后闭区间，但是这里用到了优化 2，即索引上的等值查询，向右遍历的时候 id=15 不满足条件，所以 next-key lock 退化为了间隙锁 (10, 15)。

但是，查询语句中 where 条件是大于号和小于号，这里的“等值查询”又是从哪里来的呢？要知道，加锁动作是发生在语句执行过程中的，所以你分析加锁行为的时候，要从索引上的数据结构开始。这里再把这个过程拆解一下。如下图所示，是这个表的索引 id 的示意图。

![索引 id 示意图](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/ac1aa07860c565b907b32c5f75c4f2bb.webp)

## 等值查询的过程

## 怎么看死锁？

## 怎么看锁等待？

## update 的例子

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/30.%E7%AD%94%E7%96%91%E6%96%87%E7%AB%A0%E4%BA%8C%E7%94%A8%E5%8A%A8%E6%80%81%E7%9A%84%E8%A7%82%E7%82%B9%E7%9C%8B%E5%8A%A0%E9%94%81/  

