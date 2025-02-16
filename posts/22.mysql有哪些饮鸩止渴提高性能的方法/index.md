# 22 | MySQL 有哪些“饮鸩止渴”提高性能的方法？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了 MySQL 性能问题的解决方案，针对短连接风暴和查询更新语句导致的性能问题提出了解决方法。对于短连接风暴可能导致的连接数暴涨问题，提出了通过 kill connection 命令断开不工作的线程或者重启数据库并使用--skip-grant-tables 参数跳过权限验证阶段的方法，但强调了这些方法可能存在的风险和损失。针对查询和更新语句导致的性能问题，文章提出了通过创建索引、改写 SQL 语句以及使用 force index 等方法来解决。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 短连接风暴

## 慢查询性能问题

## QPS 突增问题

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/22.mysql%E6%9C%89%E5%93%AA%E4%BA%9B%E9%A5%AE%E9%B8%A9%E6%AD%A2%E6%B8%B4%E6%8F%90%E9%AB%98%E6%80%A7%E8%83%BD%E7%9A%84%E6%96%B9%E6%B3%95/  

