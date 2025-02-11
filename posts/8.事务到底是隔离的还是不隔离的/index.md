# 08 | 事务到底是隔离的还是不隔离的？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了事务隔离级别对于事务可见性的影响，并重点介绍了可重复读隔离级别下的事务视图和行锁的概念。文章详细解释了在 MySQL 中 MVCC 实现时使用的一致性读视图的概念，以及 InnoDB 如何利用多版本数据实现“秒级创建快照”的能力。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## “快照”在 MVCC 里是怎么工作的？

## 更新逻辑

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/8.%E4%BA%8B%E5%8A%A1%E5%88%B0%E5%BA%95%E6%98%AF%E9%9A%94%E7%A6%BB%E7%9A%84%E8%BF%98%E6%98%AF%E4%B8%8D%E9%9A%94%E7%A6%BB%E7%9A%84/  

