# 14 | Count(*) 这么慢，我该怎么办？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
MySQL 中的 count(*) 语句在不同引擎中有不同的实现方式。MyISAM 引擎直接返回存储在磁盘上的总行数，效率高；而 InnoDB 引擎需要逐行读取数据并累积计数，导致执行速度变慢。针对频繁统计表行数的需求，建议自行计数或使用缓存系统保存计数，如 Redis 服务，但存在数据不一致和丢失更新的问题。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## count(*) 的实现方式

## 用缓存系统保存计数

## 在数据库保存计数

## 不同的 count 用法

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/14.count%E8%BF%99%E4%B9%88%E6%85%A2%E6%88%91%E8%AF%A5%E6%80%8E%E4%B9%88%E5%8A%9E/  

