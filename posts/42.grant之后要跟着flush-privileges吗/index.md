# 42 | Grant 之后要跟着 Flush Privileges 吗？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
MySQL 中 grant 和 flush privileges 命令的作用及影响是本文的重点。grant 命令用于赋予用户权限，包括全局、库级、表和列权限，并对已存在的连接产生不同影响。flush privileges 命令则用于重新加载权限数据，保持内存数据与磁盘数据一致。文章指出，规范使用 grant 和 revoke 语句时不需要随后加上 flush privileges 语句，而 flush privileges 适用于权限数据不一致的情况。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 全局权限

## db 权限

## 表权限和列权限

## flush privileges 使用场景

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/42.grant%E4%B9%8B%E5%90%8E%E8%A6%81%E8%B7%9F%E7%9D%80flush-privileges%E5%90%97/  

