# 13 | 为什么表数据删掉一半，表文件大小不变？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了 MySQL 中 InnoDB 引擎下的数据库表空间回收问题，特别是在删除数据后表文件大小未发生变化的情况。首先介绍了 InnoDB 表的组成结构和参数 innodb_file_per_table 的作用，建议将该参数设置为 ON 以便更好地管理表空间。随后详细说明了数据删除流程，包括记录和数据页的复用，以及删除和插入数据可能导致的空洞问题。最后，介绍了通过重建表来收缩表空间的方法，包括使用 alter table 命令和优化流程。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 参数 innodb_file_per_table

## 数据删除流程

## 重建表

## Online 和 inplace

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/13.%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A1%A8%E6%95%B0%E6%8D%AE%E5%88%A0%E6%8E%89%E4%B8%80%E5%8D%8A%E8%A1%A8%E6%96%87%E4%BB%B6%E5%A4%A7%E5%B0%8F%E4%B8%8D%E5%8F%98/  

