# 15 | 答疑文章（一）：日志和索引相关问题


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文是 MySQL 实战专栏的答疑文章。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 日志相关问题

## 追问 1：MySQL 怎么知道 binlog 是完整的？

## 追问 2：redo log 和 binlog 是怎么关联起来的？

## 追问 3：处于 prepare 阶段的 redo log 加上完整 binlog，重启就能恢复，MySQL 为什么要这么设计？

## 追问 4：如果这样的话，为什么还要两阶段提交呢？干脆先 redo log 写完，再写 binlog。崩溃恢复的时候，必须得两个日志都完整才可以。是不是一样的逻辑？

## 追问 5：不引入两个日志，也就没有两阶段提交的必要了。只用 binlog 来支持崩溃恢复，又能支持归档，不就可以了？

## 追问 6：那能不能反过来，只用 redo log，不要 binlog？

## 追问 7：redo log 一般设置多大？

## 追问 8：正常运行中的实例，数据写入后的最终落盘，是从 redo log 更新过来的还是从 buffer pool 更新过来的呢？

## 追问 9：redo log buffer 是什么？是先修改内存，还是先写 redo log 文件？

## 业务设计问题

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/15.%E7%AD%94%E7%96%91%E6%96%87%E7%AB%A0%E4%B8%80%E6%97%A5%E5%BF%97%E5%92%8C%E7%B4%A2%E5%BC%95%E7%9B%B8%E5%85%B3%E9%97%AE%E9%A2%98/  

