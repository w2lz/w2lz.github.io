# 40 | Insert 语句的锁为什么这么多？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
MySQL 的 insert 语句在执行过程中可能会涉及到不同的锁，导致一些特殊情况下的性能问题。文章深入剖析了在可重复读隔离级别下，binlog_format=statement 时执行 insert ... select 语句时，需要对表 t 的所有行和间隙加锁的情况。同时，讨论了 insert 循环写入的情况，以及对目标表的锁范围和执行流程。文章还介绍了执行 insert ... select 语句时的慢查询日志和 explain 结果，以及对 InnoDB 扫描行数的分析。最后，提出了针对这类语句的优化方法，以及使用内存临时表来优化的写法。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## insert … select 语句

## insert 循环写入

## insert 唯一键冲突

## insert into … on duplicate key update

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/40.insert%E8%AF%AD%E5%8F%A5%E7%9A%84%E9%94%81%E4%B8%BA%E4%BB%80%E4%B9%88%E8%BF%99%E4%B9%88%E5%A4%9A/  

