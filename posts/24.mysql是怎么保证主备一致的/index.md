# 24 | MySQL 是怎么保证主备一致的？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入介绍了 MySQL 主备同步的基本原理和技术细节，重点围绕 binlog 内容、备库执行 binlog 与主库保持一致的原理展开。详细解释了主备切换流程、节点间的同步更新流程以及 binlog 的三种格式的特点和应用场景。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## MySQL 主备的基本原理

## binlog 的三种格式对比

## 为什么会有 mixed 格式的 binlog？

## 循环复制问题

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/24.mysql%E6%98%AF%E6%80%8E%E4%B9%88%E4%BF%9D%E8%AF%81%E4%B8%BB%E5%A4%87%E4%B8%80%E8%87%B4%E7%9A%84/  

