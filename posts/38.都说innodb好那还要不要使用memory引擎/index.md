# 38 | 都说 InnoDB 好，那还要不要使用 Memory 引擎？


{{< admonition quote "摘要" true >}}
内存引擎和 InnoDB 引擎在数据组织方式上存在显著差异。InnoDB 引擎将数据存储在主键索引上，而内存引擎则将数据和索引分开存放。这导致了内存表的数据是按照写入顺序存放的，而 InnoDB 表的数据总是有序存放的。此外，内存表不支持行锁，只支持表锁，这会影响并发访问的性能。尽管内存引擎速度快且支持 hash 索引，但在生产环境中使用时需要注意锁粒度问题和数据持久化问题。
{{< /admonition >}}

<!--more-->

## 内存表的数据组织结构

假设有以下的两张表 t1 和 t2，其中表 t1 使用 Memory 引擎，表 t2 使用 InnoDB 引擎。

```sql
create table t1(id int primary key, c int) engine=Memory;
create table t2(id int primary key, c int) engine=innodb;
insert into t1 values(1,1),(2,2),(3,3),(4,4),(5,5),(6,6),(7,7),(8,8),(9,9),(0,0);
insert into t2 values(1,1),(2,2),(3,3),(4,4),(5,5),(6,6),(7,7),(8,8),(9,9),(0,0);
```

然后，分别执行 select * from t1 和 select * from t2。

![两个查询结果 -0 的位置](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/3fb1100b6e3390357d4efff0ba4765e6.webp)

可以看到，内存表 t1 的返回结果里面 0 在最后一行，而 InnoDB 表 t2 的返回结果里 0 在第一行。出现这个区别的原因，要从这两个引擎的主键索引的组织方式说起。

表 t2 用的是 InnoDB 引擎，它的主键索引 id 的组织方式，InnoDB 表的数据就放在主键索引树上，主键索引是 B+ 树。所以表 t2 的数据组织方式如下图所示：

![表 t2 的数据组织](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/4e29e4f9db55ace6ab09161c68ad8c8d.webp)

主键索引上的值是有序存储的。在执行 select * 的时候，就会按照叶子节点从左到右扫描，所以得到的结果里，0 就出现在第一行。

与 InnoDB 引擎不同，Memory 引擎的数据和索引是分开的。来看一下表 t1 中的数据内容。

![表 t1 的数据组织](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/dde03e92074cecba4154d30cd16a9684.webp)

可以看到，内存表的数据部分以数组的方式单独存放，而主键 id 索引里，存的是每个数据的位置。主键 id 是 hash 索引，可以看到索引上的 key 并不是有序的。

在内存表 t1 中，当执行 select * 的时候，走的是全表扫描，也就是顺序扫描这个数组。因此，0 就是最后一个被读到，并放入结果集的数据。

可见，InnoDB 和 Memory 引擎的数据组织方式是不同的：

- InnoDB 引擎把数据放在主键索引上，其他索引上保存的是主键 id。这种方式，称之为索引组织表（Index Organizied Table）。

- 而 Memory 引擎采用的是把数据单独存放，索引上保存数据位置的数据组织形式，称之为堆组织表（Heap Organizied Table）。

从中可以看出，这两个引擎的一些典型不同：

1. InnoDB 表的数据总是有序存放的，而内存表的数据就是按照写入顺序存放的；

2. 当数据文件有空洞的时候，InnoDB 表在插入新数据的时候，为了保证数据有序性，只能在固定的位置写入新值，而内存表找到空位就可以插入新值；

3. 数据位置发生变化的时候，InnoDB 表只需要修改主键索引，而内存表需要修改所有索引；

4. InnoDB 表用主键索引查询时需要走一次索引查找，用普通索引查询的时候，需要走两次索引查找。而内存表没有这个区别，所有索引的“地位”都是相同的。

5. InnoDB 支持变长数据类型，不同记录的长度可能不同；内存表不支持 Blob 和 Text 字段，并且即使定义了 varchar(N)，实际也当作 char(N)，也就是固定长度字符串来存储，因此内存表的每行数据长度相同。

由于内存表的这些特性，每个数据行被删除以后，空出的这个位置都可以被接下来要插入的数据复用。比如，如果要在表 t1 中执行：

```sql
delete from t1 where id=5;
insert into t1 values(10,10);
select * from t1;
```

就会看到返回结果里，id=10 这一行出现在 id=4 之后，也就是原来 id=5 这行数据的位置。需要指出的是，表 t1 的这个主键索引是哈希索引，因此如果执行范围查询，比如

```sql
select * from t1 where id<5;
```

是用不上主键索引的，需要走全表扫描。那如果要让内存表支持范围扫描，应该怎么办呢？

## hash 索引和 B-Tree 索引

实际上，内存表也是支持 B-Tree 索引的。在 id 列上创建一个 B-Tree 索引，SQL 语句可以这么写：

```sql
alter table t1 add index a_btree_index using btree (id);
```

这时，表 t1 的数据组织形式就变成了这样：

![表 t1 的数据组织 -- 增加 B-Tree 索引](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/1788deca56cb83c114d8353c92e3bde3.webp)

新增的这个 B-Tree 索引看着就眼熟了，这跟 InnoDB 的 b+ 树索引组织形式类似。作为对比，可以看一下这下面这两个语句的输出：

![使用 B-Tree 和 hash 索引查询返回结果对比](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/a85808fcccab24911d257d720550328a.webp)

可以看到，执行 select * from t1 where id<5 的时候，优化器会选择 B-Tree 索引，所以返回结果是 0 到 4。使用 force index 强行使用主键 id 这个索引，id=0 这一行就在结果集的最末尾了。

其实，一般在我们的印象中，内存表的优势是速度快，其中的一个原因就是 Memory 引擎支持 hash 索引。当然，更重要的原因是，内存表的所有数据都保存在内存，而内存的读写速度总是比磁盘快。但是，接下来要说明，为什么不建议在生产环境上使用内存表。这里的原因主要包括两个方面：

1. 锁粒度问题；

2. 数据持久化问题。

## 内存表的锁

内存表不支持行锁，只支持表锁。因此，一张表只要有更新，就会堵住其他所有在这个表上的读写操作。

需要注意的是，这里的表锁跟之前介绍过的 MDL 锁不同，但都是表级的锁。接下来，通过下面这个场景，模拟一下内存表的表级锁。

![内存表的表锁 -- 复现步骤](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/f216e2d707559ed2ca98fbe21e509f29.webp)

在这个执行序列里，session A 的 update 语句要执行 50 秒，在这个语句执行期间 session B 的查询会进入锁等待状态。session C 的 show processlist 结果输出如下：

![内存表的表锁 -- 结果](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/14d88076dad6db573f0b66f2c17df916.webp)

跟行锁比起来，表锁对并发访问的支持不够好。所以，内存表的锁粒度问题，决定了它在处理并发事务的时候，性能也不会太好。

## 数据持久性问题

数据放在内存中，是内存表的优势，但也是一个劣势。因为，数据库重启的时候，所有的内存表都会被清空。

你可能会说，如果数据库异常重启，内存表被清空也就清空了，不会有什么问题啊。但是，在高可用架构下，内存表的这个特点简直可以当做 bug 来看待了。为什么这么说呢？先看看 M-S 架构下，使用内存表存在的问题。

![M-S 基本架构](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/5b910e4c0f1afa219aeecd1f291c95e9.webp)

来看一下下面这个时序：

1. 业务正常访问主库；

2. 备库硬件升级，备库重启，内存表 t1 内容被清空；

3. 备库重启后，客户端发送一条 update 语句，修改表 t1 的数据行，这时备库应用线程就会报错“找不到要更新的行”。

这样就会导致主备同步停止。当然，如果这时候发生主备切换的话，客户端会看到，表 t1 的数据“丢失”了。

在上图中这种有 proxy 的架构里，大家默认主备切换的逻辑是由数据库系统自己维护的。这样对客户端来说，就是“网络断开，重连之后，发现内存表数据丢失了”。

你可能说这还好啊，毕竟主备发生切换，连接会断开，业务端能够感知到异常。

但是，接下来内存表的这个特性就会让使用现象显得更“诡异”了。由于 MySQL 知道重启之后，内存表的数据会丢失。所以，担心主库重启之后，出现主备不一致，MySQL 在实现上做了这样一件事儿：在数据库重启之后，往 binlog 里面写入一行 DELETE FROM t1。

如果你使用是下图所示的双 M 结构的话：

![双 M 结构](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/4089c9c1f92ce61d2ed779fd0932ba57.webp)

在备库重启的时候，备库 binlog 里的 delete 语句就会传到主库，然后把主库内存表的内容删除。这样在使用的时候就会发现，主库的内存表数据突然被清空了。基于上面的分析，可以看到，内存表并不适合在生产环境上作为普通数据表使用。但是内存表执行速度快呀。这个问题，其实可以这么分析：

1. 如果表更新量大，那么并发度是一个很重要的参考指标，InnoDB 支持行锁，并发度比内存表好；

2. 能放到内存表的数据量都不大。如果考虑的是读的性能，一个读 QPS 很高并且数据量不大的表，即使是使用 InnoDB，数据也是都会缓存在 InnoDB Buffer Pool 里的。因此，使用 InnoDB 表的读性能也不会差。

所以，建议把普通内存表都用 InnoDB 表来代替。但是，有一个场景却是例外的。这个场景就是，在数据量可控，不会耗费过多内存的情况下，可以考虑使用内存表。

内存临时表刚好可以无视内存表的两个不足，主要是下面的三个原因：

- 临时表不会被其他线程访问，没有并发性的问题；

- 临时表重启后也是需要删除的，清空数据这个问题不存在；

- 备库的临时表也不会影响主库的用户线程。

现在回过头再看一下之前说的 join 语句优化的例子，当时建议的是创建一个 InnoDB 临时表，使用的语句序列是：

```sql
create temporary table temp_t(id int primary key, a int, b int, index(b))engine=innodb;
insert into temp_t select * from t2 where b>=1 and b<=2000;
select * from t1 join temp_t on (t1.b=temp_t.b);
```

了解了内存表的特性就知道了，其实这里使用内存临时表的效果更好，原因有三个：

1. 相比于 InnoDB 表，使用内存表不需要写磁盘，往表 temp_t 的写数据的速度更快；

2. 索引 b 使用 hash 索引，查找的速度比 B-Tree 索引快；

3. 临时表数据只有 2000 行，占用的内存有限。

因此，你可以对之前的 join 语句序列做一个改写，将临时表 temp_t 改成内存临时表，并且在字段 b 上创建一个 hash 索引。

```sql
create temporary table temp_t(id int primary key, a int, b int, index (b))engine=memory;
insert into temp_t select * from t2 where b>=1 and b<=2000;
select * from t1 join temp_t on (t1.b=temp_t.b);
```

![使用内存临时表的执行效果](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/a468ba6d14ea225623074b6255b99f92.webp)

可以看到，不论是导入数据的时间，还是执行 join 的时间，使用内存临时表的速度都比使用 InnoDB 临时表要更快一些。

## 小结

由于重启会丢数据，如果一个备库重启，会导致主备同步线程停止；如果主库跟这个备库是双 M 架构，还可能导致主库的内存表数据被删掉。因此在生产上，不建议使用普通内存表。

基于内存表的特性，还分析了它的一个适用场景，就是内存临时表。内存表支持 hash 索引，这个特性利用起来，对复杂查询的加速效果还是很不错的。

## 问题

问：假设你刚刚接手的一个数据库上，真的发现了一个内存表。备库重启之后肯定是会导致备库的内存表数据被清空，进而导致主备同步停止。这时，最好的做法是将它修改成 InnoDB 引擎表。假设当时的业务场景暂时不允许你修改引擎，你可以加上什么自动化逻辑，来避免主备同步停止呢？

答：假设的是主库暂时不能修改引擎，那么就把备库的内存表引擎先都改成 InnoDB。对于每个内存表，执行

```sql
set sql_log_bin=off;
alter table tbl_name engine=innodb;
```

这样就能避免备库重启的时候，数据丢失的问题。由于主库重启后，会往 binlog 里面写“delete from tbl_name”，这个命令传到备库，备库的同名的表数据也会被清空。因此，就不会出现主备同步停止的问题。

如果由于主库异常重启，触发了 HA，这时候之前修改过引擎的备库变成了主库。而原来的主库变成了新备库，在新备库上把所有的内存表（这时候表里没数据）都改成 InnoDB 表。

所以，如果不能直接修改主库上的表引擎，可以配置一个自动巡检的工具，在备库上发现内存表就把引擎改了。同时，跟业务开发同学约定好建表规则，避免创建新的内存表。


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/38.%E9%83%BD%E8%AF%B4innodb%E5%A5%BD%E9%82%A3%E8%BF%98%E8%A6%81%E4%B8%8D%E8%A6%81%E4%BD%BF%E7%94%A8memory%E5%BC%95%E6%93%8E/  

