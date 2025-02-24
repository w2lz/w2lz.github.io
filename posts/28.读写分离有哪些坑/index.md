# 28 | 读写分离有哪些坑？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了数据库架构中的读写分离问题及解决方案。首先介绍了两种读写分离架构：客户端直连和带 proxy 的架构，并分析了它们的特点和优劣。随后讨论了由于主从延迟导致的“过期读”问题，并提出了多种处理过期读的方案，包括强制走主库、sleep 方案等。文章提出了更准确的方案，如判断主备无延迟方案、配合 semi-sync 方案等。在确保主备无延迟的方法中，通过对比位点和 GTID 的方法更准确。文章还介绍了 semi-sync replication 和等主库位点方案，以解决过期读问题。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 强制走主库方案

## Sleep 方案

## 判断主备无延迟方案

## 配合 semi-sync

## 等主库位点方案

## GTID 方案

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/28.%E8%AF%BB%E5%86%99%E5%88%86%E7%A6%BB%E6%9C%89%E5%93%AA%E4%BA%9B%E5%9D%91/  

