# 16 | “Order By”是怎么工作的？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了在开发应用中常见的根据指定字段排序来显示结果的需求，以及针对这种需求的 SQL 语句“order by”是如何执行的。文章首先介绍了全字段排序的执行流程，包括使用 explain 命令查看语句的执行情况、sort_buffer 的内存排序和临时文件排序等细节。接着讨论了当排序的单行长度较大时，MySQL 采用的另一种算法——rowid 排序，详细解释了其执行流程和优化效果。通过对比两种排序算法的执行过程和优化效果，可以更好地理解“order by”语句的执行原理和影响因素。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 全字段排序

## rowid 排序

## 全字段排序 VS rowid 排序

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/16.order-by%E6%98%AF%E6%80%8E%E4%B9%88%E5%B7%A5%E4%BD%9C%E7%9A%84/  

