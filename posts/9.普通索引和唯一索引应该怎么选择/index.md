# 09 | 普通索引和唯一索引，应该怎么选择？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了普通索引和唯一索引在不同业务场景下的选择，重点从性能角度对比了它们在查询和更新语句中的影响。在查询过程中，普通索引需要额外的查找和判断操作，但由于 InnoDB 的数据是按数据页为单位读写，性能差距微乎其微。而在更新过程中，普通索引可以利用 change buffer 来减少磁盘读取，从而提升性能。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 查询过程

## 更新过程

## change buffer 的使用场景

## 索引选择和实践

## change buffer 和 redo log

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/9.%E6%99%AE%E9%80%9A%E7%B4%A2%E5%BC%95%E5%92%8C%E5%94%AF%E4%B8%80%E7%B4%A2%E5%BC%95%E5%BA%94%E8%AF%A5%E6%80%8E%E4%B9%88%E9%80%89%E6%8B%A9/  

