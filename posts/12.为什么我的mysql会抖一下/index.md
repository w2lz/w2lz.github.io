# 12 | 为什么我的 MySQL 会“抖”一下？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文通过对 InnoDB 的工作机制进行比喻，解释了数据库“抖动”现象的原因。首先介绍了 InnoDB 的 WAL 机制，即写日志和内存数据页的刷新过程，分析了导致数据库刷新过程的几种情况，如 redo log 写满、系统内存不足等。指出这些情况会明显影响数据库性能，尤其是当查询需要淘汰大量脏页或者日志写满时，会导致查询响应时间明显变长甚至更新操作完全堵塞。最后，提到 InnoDB 需要有控制脏页比例的机制来尽量避免性能问题的发生。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 你的 SQL 语句为什么变“慢”了

## InnoDB 刷脏页的控制策略

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/12.%E4%B8%BA%E4%BB%80%E4%B9%88%E6%88%91%E7%9A%84mysql%E4%BC%9A%E6%8A%96%E4%B8%80%E4%B8%8B/  

