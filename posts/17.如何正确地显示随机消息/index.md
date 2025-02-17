# 17 | 如何正确地显示随机消息？


{{&lt; admonition quote &#34;摘要&#34; true &gt;}}
本文深入介绍了在 MySQL 中实现随机消息显示的技术特点和优化方法。以一个英语学习 App 的性能问题为例，详细讲解了随机选择单词的 SQL 语句设计、执行流程和优化方法。文章首先介绍了内存临时表排序方法，并分析了其执行流程和扫描行数。作者还解释了内存临时表排序使用的 rowid 排序方法和 rowid 的概念。
{{&lt; /admonition &gt;}}

&lt;!--more--&gt;

假设现在让你做一个英语学习的 APP，这个英语学习 App 首页有一个随机显示单词的功能，也就是根据每个用户的级别有一个单词表，然后这个用户每次访问首页的时候，都会随机滚动显示三个单词。你会发现随着单词表变大，选单词这个逻辑变得越来越慢，甚至影响到了首页的打开速度。现在，如果让你来设计这个 SQL 语句，你会怎么写呢？

为了便于理解，对这个例子进行了简化：去掉每个级别的用户都有一个对应的单词表这个逻辑，直接就是从一个单词表中随机选出三个单词。这个表的建表语句和初始数据的命令如下：

```sql
mysql&gt; CREATE TABLE `words` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `word` varchar(64) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;

delimiter ;;
create procedure idata()
begin
  declare i int;
  set i=0;
  while i&lt;10000 do
    insert into words(word) values(concat(char(97&#43;(i div 1000)), char(97&#43;(i % 1000 div 100)), char(97&#43;(i % 100 div 10)), char(97&#43;(i % 10))));
    set i=i&#43;1;
  end while;
end;;
delimiter ;

call idata();
```

为了便于量化说明，在这个表里面插入了 10000 行记录。接下来我们来看看要随机选择 3 个单词，有什么方法实现，存在什么问题以及如何改进。

## 内存临时表

首先，你会想到用 order by rand() 来实现这个逻辑。

```sql
mysql&gt; select word from words order by rand() limit 3;
```

这个语句的意思很直白，随机排序取前 3 个。虽然这个 SQL 语句写法很简单，但执行流程却有点复杂的。先用 explain 命令来看看这个语句的执行情况。

![使用 explain 命令查看语句的执行情况](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/59a4fb0165b7ce1184e41f2d061ce350.webp)

Extra 字段显示 Using temporary，表示的是需要使用临时表；Using filesort，表示的是需要执行排序操作。因此这个 Extra 的意思就是，需要临时表，并且需要在临时表上排序。你觉得对于临时内存表的排序来说，它会选择哪一种算法呢？对于 InnoDB 表来说，执行全字段排序会减少磁盘访问，因此会被优先选择。

这里强调了“InnoDB 表”，你肯定想到了，对于内存表，回表过程只是简单地根据数据行的位置，直接访问内存得到数据，根本不会导致多访问磁盘。优化器没有了这一层顾虑，那么它会优先考虑的，就是用于排序的行越小越好了，所以，MySQL 这时就会选择 rowid 排序。

理解了这个算法选择的逻辑，再来看看语句的执行流程。同时，通过今天的这个例子来尝试分析一下语句的扫描行数。这条语句的执行流程是这样的：

1. 创建一个临时表。这个临时表使用的是 memory 引擎，表里有两个字段，第一个字段是 double 类型，记为字段 R，第二个字段是 varchar(64) 类型，记为字段 W。并且，这个表没有建索引。

2. 从 words 表中，按主键顺序取出所有的 word 值。对于每一个 word 值，调用 rand() 函数生成一个大于 0 小于 1 的随机小数，并把这个随机小数和 word 分别存入临时表的 R 和 W 字段中，到此，扫描行数是 10000。

3. 现在临时表有 10000 行数据了，接下来你要在这个没有索引的内存临时表上，按照字段 R 排序。

4. 初始化 sort_buffer。sort_buffer 中有两个字段，一个是 double 类型，另一个是整型。

5. 从内存临时表中一行一行地取出 R 值和位置信息，分别存入 sort_buffer 中的两个字段里。这个过程要对内存临时表做全表扫描，此时扫描行数增加 10000，变成了 20000。

6. 在 sort_buffer 中根据 R 的值进行排序。注意，这个过程没有涉及到表操作，所以不会增加扫描行数。

7. 排序完成后，取出前三个结果的位置信息，依次到内存临时表中取出 word 值，返回给客户端。这个过程中，访问了表的三行数据，总扫描行数变成了 20003。

接下来，通过慢查询日志（slow log）来验证一下我们分析得到的扫描行数是否正确。

```sql
# Query_time: 0.900376  Lock_time: 0.000347 Rows_sent: 3 Rows_examined: 20003
SET timestamp=1541402277;
select word from words order by rand() limit 3;
```

其中，Rows_examined：20003 就表示这个语句执行过程中扫描了 20003 行，也就验证了分析得出的结论。现在，我们来把完整的排序执行流程图画出来。

![随机排序完整流程图 1](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/2abe849faa7dcad0189b61238b849ffc.webp)

图中的 pos 就是位置信息，你可能会觉得奇怪，这里的“位置信息”是个什么概念？在上一篇文章中，我们对 InnoDB 表排序的时候，明明用的还是 ID 字段。这时候，就要回到一个基本概念：MySQL 的表是用什么方法来定位“一行数据”的。

如果把一个 InnoDB 表的主键删掉，是不是就没有主键，就没办法回表了？其实不是的。如果创建的表没有主键，或者把一个表的主键删掉了，那么 InnoDB 会自己生成一个长度为 6 字节的 rowid 来作为主键。这也就是排序模式里面，rowid 名字的来历。实际上它表示的是：每个引擎用来唯一标识数据行的信息。

- 对于有主键的 InnoDB 表来说，这个 rowid 就是主键 ID；

- 对于没有主键的 InnoDB 表来说，这个 rowid 就是由系统生成的；

- MEMORY 引擎不是索引组织表。在这个例子里面，可以认为它就是一个数组。因此，这个 rowid 其实就是数组的下标。

到这里，稍微小结一下：order by rand() 使用了内存临时表，内存临时表排序的时候使用了 rowid 排序方法。

## 磁盘临时表

那么，是不是所有的临时表都是内存表呢？其实不是的。tmp_table_size 这个配置限制了内存临时表的大小，默认值是 16M。如果临时表大小超过了 tmp_table_size，那么内存临时表就会转成磁盘临时表。磁盘临时表使用的引擎默认是 InnoDB，是由参数 internal_tmp_disk_storage_engine 控制的。当使用磁盘临时表的时候，对应的就是一个没有显式索引的 InnoDB 表的排序过程。

为了复现这个过程，可以把 tmp_table_size 设置成 1024，把 sort_buffer_size 设置成 32768, 把 max_length_for_sort_data 设置成 16。

```sql
set tmp_table_size=1024;
set sort_buffer_size=32768;
set max_length_for_sort_data=16;
/* 打开 optimizer_trace，只对本线程有效 */
SET optimizer_trace=&#39;enabled=on&#39;; 

/* 执行语句 */
select word from words order by rand() limit 3;

/* 查看 OPTIMIZER_TRACE 输出 */
SELECT * FROM `information_schema`.`OPTIMIZER_TRACE`\G
```

![OPTIMIZER_TRACE 部分结果](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/78d2db9a4fdba81feadccf6e878b4aab.webp)

然后，来看一下这次 OPTIMIZER_TRACE 的结果。

因为将 max_length_for_sort_data 设置成 16，小于 word 字段的长度定义，所以可以看到 sort_mode 里面显示的是 rowid 排序，这个是符合预期的，参与排序的是随机值 R 字段和 rowid 字段组成的行。

你可能会发现不对。R 字段存放的随机值就 8 个字节，rowid 是 6 个字节，数据总行数是 10000，这样算出来就有 140000 字节，超过了 sort_buffer_size 定义的 32768 字节了。但是，number_of_tmp_files 的值居然是 0，难道不需要用临时文件吗？

这个 SQL 语句的排序确实没有用到临时文件，采用是 MySQL 5.6 版本引入的一个新的排序算法，即：优先队列排序算法。接下来，就看看为什么没有使用临时文件的算法，也就是归并排序算法，而是采用了优先队列排序算法。

其实，现在的 SQL 语句，只需要取 R 值最小的 3 个 rowid。但是，如果使用归并排序算法的话，虽然最终也能得到前 3 个值，但是这个算法结束后，已经将 10000 行数据都排好序了。

也就是说，后面的 9997 行也是有序的了。但，我们的查询并不需要这些数据是有序的。所以，想一下就明白了，这浪费了非常多的计算量。而优先队列算法，就可以精确地只得到三个最小值，执行流程如下：

1. 对于这 10000 个准备排序的 (R,rowid)，先取前三行，构造成一个堆；

2. 取下一个行 (R’,rowid’)，跟当前堆里面最大的 R 比较，如果 R’小于 R，把这个 (R,rowid) 从堆中去掉，换成 (R’,rowid’)；

3. 重复第 2 步，直到第 10000 个 (R’,rowid’) 完成比较。

下面简单画了一个优先队列排序过程的示意图。

![优先队列排序算法示例](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/e9c29cb20bf9668deba8981e444f6897.webp)

上图是模拟 6 个 (R,rowid) 行，通过优先队列排序找到最小的三个 R 值的行的过程。整个排序过程中，为了最快地拿到当前堆的最大值，总是保持最大值在堆顶，因此这是一个最大堆。

第五幅图的 OPTIMIZER_TRACE 结果中，filesort_priority_queue_optimization 这个部分的 chosen=true，就表示使用了优先队列排序算法，这个过程不需要临时文件，因此对应的 number_of_tmp_files 是 0。

这个流程结束后，构造的堆里面，就是这个 10000 行里面 R 值最小的三行。然后，依次把它们的 rowid 取出来，去临时表里面拿到 word 字段，这个过程就跟上一篇文章的 rowid 排序的过程一样了。再看一下上面一篇文章的 SQL 查询语句：

```sql
select city,name,age from t where city=&#39;杭州&#39; order by name limit 1000  ;
```

你可能会问，这里也用到了 limit，为什么没用优先队列排序算法呢？原因是，这条 SQL 语句是 limit 1000，如果使用优先队列算法的话，需要维护的堆的大小就是 1000 行的 (name,rowid)，超过了设置的 sort_buffer_size 大小，所以只能使用归并排序算法。

总之，不论是使用哪种类型的临时表，order by rand() 这种写法都会让计算过程非常复杂，需要大量的扫描行数，因此排序过程的资源消耗也会很大。

## 随机排序方法

先把问题简化一下，如果只随机选择 1 个 word 值，可以怎么做呢？思路上是这样的：

## 小结

## 问题


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/17.%E5%A6%82%E4%BD%95%E6%AD%A3%E7%A1%AE%E5%9C%B0%E6%98%BE%E7%A4%BA%E9%9A%8F%E6%9C%BA%E6%B6%88%E6%81%AF/  

