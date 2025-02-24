# 26 | 备库为什么会延迟好几个小时？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
MySQL 的并行复制策略是解决备库延迟问题的关键。在 MySQL 5.5 版本中，通过按表和按行分发策略实现多线程复制，但存在一些限制。随着版本的演进，MySQL 5.6 和 5.7 版本新增了按库并行和基于 WRITESET 的并行复制策略，提高了备库的同步速度。这些策略的实现原理和优缺点都得到了详细介绍，包括对大事务的影响和参数调整的建议。此外，文章还提到了 MariaDB 的并行复制策略，以及备库主备延迟表现为 45 度线段的原因。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## MySQL 5.5 版本的并行复制策略

### 按表分发策略

### 按行分发策略

## MySQL 5.6 版本的并行复制策略

## MariaDB 的并行复制策略

## MySQL 5.7 的并行复制策略

## MySQL 5.7.22 的并行复制策略

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/26.%E5%A4%87%E5%BA%93%E4%B8%BA%E4%BB%80%E4%B9%88%E4%BC%9A%E5%BB%B6%E8%BF%9F%E5%A5%BD%E5%87%A0%E4%B8%AA%E5%B0%8F%E6%97%B6/  

