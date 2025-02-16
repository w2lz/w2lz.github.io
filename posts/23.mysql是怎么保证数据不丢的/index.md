# 23 | MySQL 是怎么保证数据不丢的？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
MySQL 数据保证机制的技术特点主要围绕 binlog 的写入流程和相关参数的设置展开。在 binlog 的写入机制中，事务执行过程中先将日志写入 binlog cache，事务提交时再将 binlog cache 写入 binlog 文件中。每个线程有自己的 binlog cache，但共用同一份 binlog 文件。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## binlog 的写入机制

## redo log 的写入机制

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/23.mysql%E6%98%AF%E6%80%8E%E4%B9%88%E4%BF%9D%E8%AF%81%E6%95%B0%E6%8D%AE%E4%B8%8D%E4%B8%A2%E7%9A%84/  

