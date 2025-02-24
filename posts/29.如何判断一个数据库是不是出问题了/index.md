# 29 | 如何判断一个数据库是不是出问题了？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
这篇文章介绍了如何判断数据库健康状态以及相应的监控和应对措施。首先讲解了主备切换的流程，包括主动切换和被动切换。重点讨论了如何判断主库是否出现问题，指出了简单的 select 1 并不能完全判断主库的正常运行状态。文章详细介绍了 InnoDB 的并发线程控制参数 innodb_thread_concurrency 的作用，以及如何通过设置一个健康检查表来检测并发线程数过多导致的数据库不可用情况。同时，提到了当 binlog 所在磁盘空间满了以后，更新语句和事务提交的影响，以及如何改进监控语句来应对这种情况。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## select 1 判断

## 查表判断

## 更新判断

## 内部统计

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/29.%E5%A6%82%E4%BD%95%E5%88%A4%E6%96%AD%E4%B8%80%E4%B8%AA%E6%95%B0%E6%8D%AE%E5%BA%93%E6%98%AF%E4%B8%8D%E6%98%AF%E5%87%BA%E9%97%AE%E9%A2%98%E4%BA%86/  

