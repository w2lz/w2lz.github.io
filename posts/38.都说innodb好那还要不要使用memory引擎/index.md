# 38 | 都说 InnoDB 好，那还要不要使用 Memory 引擎？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
内存引擎和 InnoDB 引擎在数据组织方式上存在显著差异。InnoDB 引擎将数据存储在主键索引上，而内存引擎则将数据和索引分开存放。这导致了内存表的数据是按照写入顺序存放的，而 InnoDB 表的数据总是有序存放的。此外，内存表不支持行锁，只支持表锁，这会影响并发访问的性能。尽管内存引擎速度快且支持 hash 索引，但在生产环境中使用时需要注意锁粒度问题和数据持久化问题。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 内存表的数据组织结构

## hash 索引和 B-Tree 索引

## 内存表的锁

## 数据持久性问题

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/38.%E9%83%BD%E8%AF%B4innodb%E5%A5%BD%E9%82%A3%E8%BF%98%E8%A6%81%E4%B8%8D%E8%A6%81%E4%BD%BF%E7%94%A8memory%E5%BC%95%E6%93%8E/  

