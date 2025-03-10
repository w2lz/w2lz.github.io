# 45 | 自增 Id 用完怎么办？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
MySQL 中的自增 id 存在上限问题，本文从表定义自增值 id、InnoDB 系统自增 row_id 和 Xid 三个方面分析了自增 id 达到上限后可能出现的情况。在表定义自增值 id 方面，当自增 id 达到上限后，可能导致主键冲突错误。在 InnoDB 系统自增 row_id 方面，达到上限后可能导致数据覆盖。而在 Xid 方面，可能会出现同一个 binlog 里面出现相同 Xid 的场景。此外，文章还介绍了 Innodb trx_id Xid 和 InnoDB 的 trx_id 的概念，以及 thread_id 的逻辑。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 表定义自增值 id

## InnoDB 系统自增 row_id

## Xid

## Innodb trx_id

## thread_id

## 小结


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/45.%E8%87%AA%E5%A2%9Eid%E7%94%A8%E5%AE%8C%E6%80%8E%E4%B9%88%E5%8A%9E/  

