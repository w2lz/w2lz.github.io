# 41 | 怎么最快地复制一张表？


{{< admonition quote "摘要" true >}}
本文介绍了在 MySQL 中最快地复制一张表的方法，包括使用 mysqldump 命令导出 INSERT 语句、直接导出.csv 文件以及使用 mysqldump 的--tab 参数导出表结构定义文件和 CSV 数据文件的方法。此外，还介绍了在 MySQL 5.6 版本中引入的可传输表空间的方法，实现物理拷贝表的功能。文章总结了三种方法的优缺点，指出物理拷贝方式速度最快，但有一定的局限性；而逻辑备份方式则更为灵活，支持跨引擎使用。
{{< /admonition >}}

<!--more-->

怎么在两张表中拷贝数据。如果可以控制对源表的扫描行数和加锁范围很小的话，简单地使用 insert … select 语句即可实现。

当然，为了避免对源表加读锁，更稳妥的方案是先将数据写到外部文本文件，然后再写回目标表。这时，有两种常用的方法。为了便于说明，还是先创建一个表 db1.t，并插入 1000 行数据，同时创建一个相同结构的表 db2.t。

```sql
create database db1;
use db1;

create table t(id int primary key, a int, b int, index(a))engine=innodb;
delimiter ;;
  create procedure idata()
  begin
    declare i int;
    set i=1;
    while(i<=1000)do
      insert into t values(i,i,i);
      set i=i+1;
    end while;
  end;;
delimiter ;
call idata();

create database db2;
create table db2.t like db1.t
```

假设，要把 db1.t 里面 a>900 的数据行导出来，插入到 db2.t 中。

## mysqldump 方法

一种方法是使用 mysqldump 命令将数据导出成一组 INSERT 语句。可以使用下面的命令：

```sql
mysqldump -h$host -P$port -u$user --add-locks=0 --no-create-info --single-transaction  --set-gtid-purged=OFF db1 t --where="a>900" --result-file=/client_tmp/t.sql
```

把结果输出到临时文件。这条命令中，主要参数含义如下：

1. –single-transaction 的作用是，在导出数据的时候不需要对表 db1.t 加表锁，而是使用 START TRANSACTION WITH CONSISTENT SNAPSHOT 的方法；

2. –add-locks 设置为 0，表示在输出的文件结果里，不增加" LOCK TABLES t WRITE;" ；

3. –no-create-info 的意思是，不需要导出表结构；

4. –set-gtid-purged=off 表示的是，不输出跟 GTID 相关的信息；

5. –result-file 指定了输出文件的路径，其中 client 表示生成的文件是在客户端机器上的。

通过这条 mysqldump 命令生成的 t.sql 文件中就包含了如图 1 所示的 INSERT 语句。

![mysqldump 输出文件的部分结果](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/8acdcefcaf5c9940570bf7e8f73dbdde.webp)

可以看到，一条 INSERT 语句里面会包含多个 value 对，这是为了后续用这个文件来写入数据的时候，执行速度可以更快。

如果希望生成的文件中一条 INSERT 语句只插入一行数据的话，可以在执行 mysqldump 命令时，加上参数–skip-extended-insert。然后，可以通过下面这条命令，将这些 INSERT 语句放到 db2 库里去执行。

```sql
mysql -h127.0.0.1 -P13000  -uroot db2 -e "source /client_tmp/t.sql"
```

需要说明的是，source 并不是一条 SQL 语句，而是一个客户端命令。mysql 客户端执行这个命令的流程是这样的：

1. 打开文件，默认以分号为结尾读取一条条的 SQL 语句；

2. 将 SQL 语句发送到服务端执行。

也就是说，服务端执行的并不是这个“source t.sql"语句，而是 INSERT 语句。所以，不论是在慢查询日志（slow log），还是在 binlog，记录的都是这些要被真正执行的 INSERT 语句。

## 导出 CSV 文件

另一种方法是直接将结果导出成.csv 文件。MySQL 提供了下面的语法，用来将查询结果导出到服务端本地目录：

```sql
select * from db1.t where a>900 into outfile '/server_tmp/t.csv';
```

在使用这条语句时，需要注意如下几点。

1. 这条语句会将结果保存在服务端。如果执行命令的客户端和 MySQL 服务端不在同一个机器上，客户端机器的临时目录下是不会生成 t.csv 文件的。

2. into outfile 指定了文件的生成位置（/server_tmp/），这个位置必须受参数 secure_file_priv 的限制。参数 secure_file_priv 的可选值和作用分别是：
   
   - 如果设置为 empty，表示不限制文件生成的位置，这是不安全的设置；
   
   - 如果设置为一个表示路径的字符串，就要求生成的文件只能放在这个指定的目录，或者它的子目录；
   
   - 如果设置为 NULL，就表示禁止在这个 MySQL 实例上执行 select … into outfile 操作。

3. 这条命令不会帮你覆盖文件，因此需要确保 /server_tmp/t.csv 这个文件不存在，否则执行语句时就会因为有同名文件的存在而报错。

4. 这条命令生成的文本文件中，原则上一个数据行对应文本文件的一行。但是，如果字段中包含换行符，在生成的文本中也会有换行符。不过类似换行符、制表符这类符号，前面都会跟上“\”这个转义符，这样就可以跟字段之间、数据行之间的分隔符区分开。

得到.csv 导出文件后，就可以用下面的 load data 命令将数据导入到目标表 db2.t 中。

```sql
load data infile '/server_tmp/t.csv' into table db2.t;
```

这条语句的执行流程如下所示。

1. 打开文件 /server_tmp/t.csv，以制表符 (\t) 作为字段间的分隔符，以换行符（\n）作为记录之间的分隔符，进行数据读取；

2. 启动事务。

3. 判断每一行的字段数与表 db2.t 是否相同：
   
   - 若不相同，则直接报错，事务回滚；
   
   - 若相同，则构造成一行，调用 InnoDB 引擎接口，写入到表中。

4. 重复步骤 3，直到 /server_tmp/t.csv 整个文件读入完成，提交事务。

如果 binlog_format=statement，这个 load 语句记录到 binlog 里以后，怎么在备库重放呢？由于 /server_tmp/t.csv 文件只保存在主库所在的主机上，如果只是把这条语句原文写到 binlog 中，在备库执行的时候，备库的本地机器上没有这个文件，就会导致主备同步停止。所以，这条语句执行的完整流程，其实是下面这样的。

1. 主库执行完成后，将 /server_tmp/t.csv 文件的内容直接写到 binlog 文件中。

2. 往 binlog 文件中写入语句 load data local infile‘/tmp/SQL_LOAD_MB-1-0’INTO TABLE `db2`.`t`。

3. 把这个 binlog 日志传到备库。

4. 备库的 apply 线程在执行这个事务日志时：
   
   - 先将 binlog 中 t.csv 文件的内容读出来，写入到本地临时目录 /tmp/SQL_LOAD_MB-1-0 中；
   
   - 再执行 load data 语句，往备库的 db2.t 表中插入跟主库相同的数据。

执行流程如下图所示：

![load data 的同步流程](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/3a6790bc933af5ac45a75deba0f52cfd.webp)

注意，这里备库执行的 load data 语句里面，多了一个“local”。它的意思是“将执行这条命令的客户端所在机器的本地文件 /tmp/SQL_LOAD_MB-1-0 的内容，加载到目标表 db2.t 中”。也就是说，load data 命令有两种用法：

1. 不加“local”，是读取服务端的文件，这个文件必须在 secure_file_priv 指定的目录或子目录下；

2. 加上“local”，读取的是客户端的文件，只要 mysql 客户端有访问这个文件的权限即可。这时候，MySQL 客户端会先把本地文件传给服务端，然后执行上述的 load data 流程。

另外需要注意的是，select …into outfile 方法不会生成表结构文件，所以导数据时还需要单独的命令得到表结构定义。mysqldump 提供了一个–tab 参数，可以同时导出表结构定义文件和 csv 数据文件。这条命令的使用方法如下：

```sql
mysqldump -h$host -P$port -u$user ---single-transaction  --set-gtid-purged=OFF db1 t --where="a>900" --tab=$secure_file_priv
```

这条命令会在 $secure_file_priv 定义的目录下，创建一个 t.sql 文件保存建表语句，同时创建一个 t.txt 文件保存 CSV 数据。

## 物理拷贝方法

前面提到的 mysqldump 方法和导出 CSV 文件的方法，都是逻辑导数据的方法，也就是将数据从表 db1.t 中读出来，生成文本，然后再写入目标表 db2.t 中。那么有物理导数据的方法吗？比如，直接把 db1.t 表的.frm 文件和.ibd 文件拷贝到 db2 目录下，是否可行呢？答案是不行的。

因为，一个 InnoDB 表，除了包含这两个物理文件外，还需要在数据字典中注册。直接拷贝这两个文件的话，因为数据字典中没有 db2.t 这个表，系统是不会识别和接受它们的。

不过，在 MySQL 5.6 版本引入了可传输表空间 (transportable tablespace) 的方法，可以通过导出 + 导入表空间的方式，实现物理拷贝表的功能。

假设现在的目标是在 db1 库下，复制一个跟表 t 相同的表 r，具体的执行步骤如下：

1. 执行 create table r like t，创建一个相同表结构的空表；

2. 执行 alter table r discard tablespace，这时候 r.ibd 文件会被删除；

3. 执行 flush table t for export，这时候 db1 目录下会生成一个 t.cfg 文件；

4. 在 db1 目录下执行 cp t.cfg r.cfg; cp t.ibd r.ibd；这两个命令（这里需要注意的是，拷贝得到的两个文件，MySQL 进程要有读写权限）；

5. 执行 unlock tables，这时候 t.cfg 文件会被删除；

6. 执行 alter table r import tablespace，将这个 r.ibd 文件作为表 r 的新的表空间，由于这个文件的数据内容和 t.ibd 是相同的，所以表 r 中就有了和表 t 相同的数据。

至此，拷贝表数据的操作就完成了。这个流程的执行过程图如下：

![物理拷贝表](https://file.yingnan.wang/mysql/MySQL%E5%AE%9E%E6%88%9845%E8%AE%B2/ba1ced43eed4a55d49435c062fee21a7.webp)

关于拷贝表的这个流程，有以下几个注意点：

1. 在第 3 步执行完 flsuh table 命令之后，db1.t 整个表处于只读状态，直到执行 unlock tables 命令后才释放读锁；

2. 在执行 import tablespace 的时候，为了让文件里的表空间 id 和数据字典中的一致，会修改 r.ibd 的表空间 id。而这个表空间 id 存在于每一个数据页中。因此，如果是一个很大的文件（比如 TB 级别），每个数据页都需要修改，所以你会看到这个 import 语句的执行是需要一些时间的。当然，如果是相比于逻辑导入的方法，import 语句的耗时是非常短的。

## 小结

今天这篇文章一共介绍了三种将一个表的数据导入到另外一个表中的方法。来对比一下这三种方法的优缺点。

1. 物理拷贝的方式速度最快，尤其对于大表拷贝来说是最快的方法。如果出现误删表的情况，用备份恢复出误删之前的临时库，然后再把临时库中的表拷贝到生产库上，是恢复数据最快的方法。但是，这种方法的使用也有一定的局限性：
   
   - 必须是全表拷贝，不能只拷贝部分数据；
   
   - 需要到服务器上拷贝数据，在用户无法登录数据库主机的场景下无法使用；
   
   - 由于是通过拷贝物理文件实现的，源表和目标表都是使用 InnoDB 引擎时才能使用。

2. 用 mysqldump 生成包含 INSERT 语句文件的方法，可以在 where 参数增加过滤条件，来实现只导出部分数据。这个方式的不足之一是，不能使用 join 这种比较复杂的 where 条件写法。

3. 用 select … into outfile 的方法是最灵活的，支持所有的 SQL 写法。但，这个方法的缺点之一就是，每次只能导出一张表的数据，而且表结构也需要另外的语句单独备份。

后两种方式都是逻辑备份方式，是可以跨引擎使用的。

## 问题

问；前面介绍 binlog_format=statement 的时候，binlog 记录的 load data 命令是带 local 的。既然这条命令是发送到备库去执行的，那么备库执行的时候也是本地执行，为什么需要这个 local 呢？如果写到 binlog 中的命令不带 local，又会出现什么问题呢？

答：这样做的一个原因是，为了确保备库应用 binlog 正常。因为备库可能配置了 secure_file_priv=null，所以如果不用 local 的话，可能会导入失败，造成主备同步延迟。

另一种应用场景是使用 mysqlbinlog 工具解析 binlog 文件，并应用到目标库的情况。你可以使用下面这条命令：

```sql
mysqlbinlog $binlog_file | mysql -h$host -P$port -u$user -p$pwd
```

把日志直接解析出来发给目标库执行。增加 local，就能让这个方法支持非本地的 $host。


---

> 作者: [w2lz](https://github.com/w2lz)  
> URL: https://blog.yingnan.wang/posts/41.%E6%80%8E%E4%B9%88%E6%9C%80%E5%BF%AB%E5%9C%B0%E5%A4%8D%E5%88%B6%E4%B8%80%E5%BC%A0%E8%A1%A8/  

