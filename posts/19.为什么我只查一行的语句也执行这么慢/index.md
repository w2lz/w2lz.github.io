# 19 | 为什么我只查一行的语句，也执行这么慢？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入探讨了查询性能优化中可能出现的问题及解决方法。通过构造一个包含 10 万行记录的表，展示了即使是查询单行数据，也可能出现执行缓慢的情况。文章详细分析了表被锁住的情况，包括等 MDL 锁、等 flush 或等行锁导致的情况，并提供了相应的处理方法。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

一般情况下，如果说查询性能优化，你首先会想到一些复杂的语句，想到查询需要返回大量的数据。但有些情况下，“查一行”，也会执行得特别慢。

需要说明的是，如果 MySQL 数据库本身就有很大的压力，导致数据库服务器 CPU 占用率很高或 ioutil（IO 利用率）很高，这种情况下所有语句的执行都有可能变慢，不属于今天的讨论范围。

为了便于描述，还是构造一个表，基于这个表来说明今天的问题。这个表有两个字段 id 和 c，并且在里面插入了 10 万行记录。

```sql
mysql&gt; CREATE TABLE `t` (
  `id` int(11) NOT NULL,
  `c` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;

delimiter ;;
create procedure idata()
begin
  declare i int;
  set i=1;
  while(i&lt;=100000) do
    insert into t values(i,i);
    set i=i&#43;1;
  end while;
end;;
delimiter ;

call idata();
```

## 第一类：查询长时间不返回

如下图所示，在表 t 执行下面的 SQL 语句：

```sql
mysql&gt; select * from t where id=1;
```

查询结果长时间不返回。

![查询长时间不返回](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/8707b79d5ed906950749f5266014f22a.webp)



## 等 MDL 锁

## 等 flush

## 等行锁

## 第二类：查询慢

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/19.%E4%B8%BA%E4%BB%80%E4%B9%88%E6%88%91%E5%8F%AA%E6%9F%A5%E4%B8%80%E8%A1%8C%E7%9A%84%E8%AF%AD%E5%8F%A5%E4%B9%9F%E6%89%A7%E8%A1%8C%E8%BF%99%E4%B9%88%E6%85%A2/  

