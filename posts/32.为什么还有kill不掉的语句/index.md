# 32 | 为什么还有 Kill 不掉的语句？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
MySQL 中的 kill 命令有两种形式：kill query 和 kill connection，分别用于终止正在执行的语句和断开连接。然而，有时候执行 kill 命令却无法生效，导致语句仍在执行。这种情况可能是因为线程没有执行到判断线程状态的逻辑，或者是由于终止逻辑耗时较长。例如，在超大事务执行期间被 kill 时，回滚操作需要对事务执行期间生成的所有新数据版本做回收操作，耗时很长。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 收到 kill 以后，线程做什么？

## 另外两个关于客户端的误解

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/32.%E4%B8%BA%E4%BB%80%E4%B9%88%E8%BF%98%E6%9C%89kill%E4%B8%8D%E6%8E%89%E7%9A%84%E8%AF%AD%E5%8F%A5/  

