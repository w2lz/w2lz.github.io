# 34 | 到底可不可以使用 Join？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了在实际生产中使用 join 语句的问题，重点关注了两个问题：DBA 不允许使用 join 的原因以及在大小不同的表做 join 时应该选择哪个表作为驱动表。文章首先介绍了 join 语句的执行过程，以及使用 straight_join 固定连接方式执行查询的方法。随后详细分析了 Index Nested-Loop Join 算法的执行流程，并通过对比单表查询的方式，得出了使用 join 语句性能更优的结论。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## Index Nested-Loop Join

## Simple Nested-Loop Join

## Block Nested-Loop Join

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/34.%E5%88%B0%E5%BA%95%E5%8F%AF%E4%B8%8D%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8join/  

