# 41 | 怎么最快地复制一张表？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文介绍了在 MySQL 中最快地复制一张表的方法，包括使用 mysqldump 命令导出 INSERT 语句、直接导出.csv 文件以及使用 mysqldump 的--tab 参数导出表结构定义文件和 CSV 数据文件的方法。此外，还介绍了在 MySQL 5.6 版本中引入的可传输表空间的方法，实现物理拷贝表的功能。文章总结了三种方法的优缺点，指出物理拷贝方式速度最快，但有一定的局限性；而逻辑备份方式则更为灵活，支持跨引擎使用。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## mysqldump 方法

## 导出 CSV 文件

## 物理拷贝方法

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/41.%E6%80%8E%E4%B9%88%E6%9C%80%E5%BF%AB%E5%9C%B0%E5%A4%8D%E5%88%B6%E4%B8%80%E5%BC%A0%E8%A1%A8/  

