# 19 | 为什么我只查一行的语句，也执行这么慢？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了查询性能优化中可能出现的问题及解决方法。通过构造一个包含 10 万行记录的表，展示了即使是查询单行数据，也可能出现执行缓慢的情况。文章详细分析了表被锁住的情况，包括等 MDL 锁、等 flush 或等行锁导致的情况，并提供了相应的处理方法。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

## 第一类：查询长时间不返回

## 等 MDL 锁

## 等 flush

## 等行锁

## 第二类：查询慢

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/19.%E4%B8%BA%E4%BB%80%E4%B9%88%E6%88%91%E5%8F%AA%E6%9F%A5%E4%B8%80%E8%A1%8C%E7%9A%84%E8%AF%AD%E5%8F%A5%E4%B9%9F%E6%89%A7%E8%A1%8C%E8%BF%99%E4%B9%88%E6%85%A2/  

