# 33 | 我查这么多数据，会不会把数据库内存打爆？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入分析了全表扫描对数据库内存的影响，重点讨论了全表扫描对 server 层和 InnoDB 引擎的影响，并提出了相应的优化建议。文章首先解答了全表扫描对内存的影响，指出 MySQL 内部的内存占用不会超过 net_buffer_length 的大小，并介绍了查询结果的发送流程和状态变化。强调了客户端接收速度对 MySQL 服务端执行时间的影响，并建议对于大查询结果，使用 mysql_store_result 接口将结果保存到本地内存。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 全表扫描对 server 层的影响

## 全表扫描对 InnoDB 的影响

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/33.%E6%88%91%E6%9F%A5%E8%BF%99%E4%B9%88%E5%A4%9A%E6%95%B0%E6%8D%AE%E4%BC%9A%E4%B8%8D%E4%BC%9A%E6%8A%8A%E6%95%B0%E6%8D%AE%E5%BA%93%E5%86%85%E5%AD%98%E6%89%93%E7%88%86/  

