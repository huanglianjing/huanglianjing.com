---
title: "MySQL基础知识"
date: 2025-03-02T23:42:18+08:00
draft: false
showToc: true # 显示目录
TocOpen: true # 自动展开目录
categories: ["数据库"]
tags: ["​关系型数据库","MySQL"]
---

# 1. 基础架构

MySQL 属于客户端/服务器架构（C/S 架构），客户端向服务器发起请求，服务器负责响应客户端的请求、对存储的数据进行处理，服务器程序进程称为 MySQL 数据库实例（instance），可以同时处理多个客户端的连接和请求。

MySQL 服务器的基本架构主要分为 Server 层和存储引擎两部份。Server 层包括连接器、查询缓存、优化器、执行器等，涵盖核心服务功能如存储过程、触发器、视图、内置函数。存储引擎层负责数据的存储和提取，支持 InnoDB、MyISAM、MEMORY 等，默认存储引擎是 InnoDB。

![](https://blog-1304941664.cos.ap-guangzhou.myqcloud.com/article_material/database/mysql_architecture.jpg)

查询缓存很容易失效且命中率不高，不建议使用，在 MySQL 8.0 中已经删掉这块功能。

查看当前服务器支持的存储引擎：

```mysql
show engines;
```

## 1.1 Buffer Pool

InnoDB 在启动时会向操作系统申请一片称为 Buffer Pool 的连续内存，用于缓存从磁盘加载的索引数据页。

它的默认大小为 128MB，也可以通过配置来指定大小。

```ini
[server]
innodb_buffer_pool_size = 268435456
```

Buffer Pool 内存空间由控制块和缓冲页组成，它们一一对应，剩余的空间则是碎片。

InnoDB 通过几个链表来管理 Buffer Pool，free 链表管理空闲的缓冲页，flush 链表管理被修改待刷新到磁盘的脏页，LRU 链表用于在没有空闲缓冲页时淘汰缓冲页。

# 2. 编码

## 2.1 字符集和比较规则

字符集用于表示二进制数据和字符串的映射关系。

MySQL 常用字符集有：

* ASCII：128 个字符，每个字符固定 1 字节；
* latin1（ISO 8859-1）：256 个字符，适用于西欧语言，每个字符固定 1 字节；
* GB2312：包含中文、日文、俄文、希腊字母、拉丁字母等，兼容 ASCII，每个字符 1 或 2 个字节；
* GBK：对 GB2312 的扩充；
* utf8（utf8mb3）：支持大部分 UTF-8 字符，每个字符 1 - 3 个字节；
* utf8mb4：支持完整的 UTF-8 字符，每个字符 1 - 4 个字节，建议使用该字符集而不是 utf；

比较规则规定了字符串比较是否区分大小写、是否区分重音、二进制方式比较等，每种字符集对应若干比较规则，可以从名称中对应，如 utf8_general_ci、gb2312_chinese_ci、utf8_bin 等。

字符集和比较规则分为服务器、数据库、表、列四个级别。

MySQL 服务器默认字符集是 utf8，默认比较规则是 utf8_general_ci，也可以通过配置修改，可以通过 show variable 查看。

```ini
[server]
character_set_server=gb2312
collation_server=gb2312_chinese_ci
```

其他各级字符集和比较规则的设置：

```mysql
# 数据库级别
CREATE DATABASE <table> CHARACTER SET <charset> COLLATE <collation>;
ALTER DATABASE <table> CHARACTER SET <charset> COLLATE <collation>;

# 表级别
CREATE TABLE <table> CHARACTER SET <charset> COLLATE <collation>;
ALTER TABLE <table> CHARACTER SET <charset> COLLATE <collation>;

# 列级别
CREATE TABLE <table> (<row> <rowtype> CHARACTER SET <charset> COLLATE <collation>);
```

## 2.2 InnoDB 行格式

InnoDB 存储引擎支持多种行格式，它们在存储空间、空间效率、适用场景上不同。

REDUNDANT（冗余格式）是最早的原始行格式，存储效率较低。NULL 值也会占用固定空间。适用于早期版本兼容和较短 BLOB 字段场景。

COMPACT（紧凑格式）减少了部份存储空间，但 CPU 消耗略高。NULL 值使用二进制位标记，非 NULL 字段通过逆序排列，变长字段前 768 字节存本地，其余通过指针存在溢出页。包含事务 ID、回滚指针等隐藏列。适用于较多变长字段且平衡存储与查询性能的场景。

DYNAMIC（动态格式）优化了大字段处理，大字段如 VARCHAR、BLOB 完全存在溢出页，本地保留 20 字节指针，支持大索引。适用于大量长文本、二进制数据的场景。

COMPRESSED（压缩格式）基于 DYNAMIC 格式，增加了数据页压缩功能，减少了磁盘占用，但会增加 CPU 负载。适用于空间有限或海量数据的场景。

设置行格式：

```mysql
CREATE TABLE <table> ROW_FORMAT=<row type>;
ALTER TABLE <table> ROW_FORMAT=<row type>;
```

MySQL 从 5.7 开始默认行格式为 DYNAMIC，到了 8.0 移除了 REDUNDANT 和 COMPACT 的支持。频繁更新的表适合用 DYNAMIC，数据量大且只读适合用 COMPRESSED。

# 3. 数据库

MySQL 会自动创建几个系统数据库，其中包含了 MySQL 服务器运行所需的信息和运行状态。

* mysql：存储 MySQL 的用户帐户和权限信息，运行过程的日志信息，帮助信息以及时区信息；
* information_schema：保存 MySQL 服务器维护的所有其他数据库的信息，如有什么表、什么视图、触发器、列、索引等元数据；
* performance_schema：MySQL 服务器运行过程的状态信息，统计最近执行的语句、每阶段话费的时间和内存使用情况；
* sys：通过视图把 information_schema 和 performance_schema 结合起来；

# 4. 执行计划

MySQL 通过查询执行计划来查看语句执行的具体方式，在语句前加上 EXPLAIN 关键字查看。虽然增删改查语句都能查看，但是主要还是用于 SELECT 语句上。

```mysql
# 查询语句
select * from user where age = 10;

# 执行计划
explain select * from user where age = 10;

# JSON格式执行计划
explain format=json select * from user where age = 10;
```

输出的各列含义：

* table：表名；
* id：每个 SELECT 关键字分配的唯一 id，普通查询或连接查询只有一个 id，而子查询或 UNION 则会产生多个 id；
* select_type：表的类型；
  * SIMPLE：单表查询；
  * PRIMARY：对包含 UNION 或子查询的大查询中的第一个查询；
  * UNION：UNION 查询中除了第一个查询；
  * SUBQUERY：子查询，用于条件匹配中；
  * DERIVED：派生表，用于作为查询 from 的对象；
  * MATERIALIZED：优化器将子查询物化后进行连接查询；
* partitions：分区信息，表示分区表查询用到的分区，非分区表为 NULL；
* type：访问方法；
  * system：表只有一条记录且存储引擎（如 MyISAM、MEMORY）的统计数据是精确的；
  * const：根据主键或唯一索引与常数等值匹配；
  * eq_ref：连接查询时，被驱动表是通过主键或非 NULL 唯一索引进行等值匹配；
  * ref：通过普通二级索引与常量等值匹配，连接查询被驱动表通过普通二级索引与驱动表某列等值匹配；
  * ref_or_null：对普通二级索引等值匹配且该列可以是 NULL；
  * index：可以使用索引覆盖，但需要扫描全部索引记录；
  * index_merge：查询匹配多个字段都各自有索引，对索引结果进行合并时；
  * unique_subquery：IN 子查询语句，被转为 EXISTS 子查询，等值匹配使用主键或非 NULL 唯一索引时；
  * index_subquery：IN 子查询语句，被转为 EXISTS 子查询，等值匹配使用普通二级索引时；
  * range：使用索引匹配值的集合或范围区间；
  * fulltext：全文索引；
  * ALL：全表扫描；
* possible_key, key：查询时用的索引；
* key_len：索引列实际的最大字节数，如果可以为 NULL 则加一；
* ref：使用索引进行等值匹配时，和索引列匹配的类型；
  * const：常数；
  * func：函数；
  * 列的名称；
* rows：预计扫描的索引记录行数；
* filtered：使用索引扫描区间获取到的记录数，再满足其他搜索条件后，预测记录数占前者的百分比；
* Extra：额外信息；

# 5. 主备同步

MySQL 主备同步（Master-Slave Replication）通过将主库（Master）的数据同步到备库（Slave）来实现数据的冗余和故障切换。

主备同步基于 binlog 实现的，主库将所有数据变更记录到 binlog，备库从主库拉去 binlog，解析为 SQL 语言后在备库重放，从而实现数据同步。

备库和主库之间维持了一个长连接，备库通过 change master 命令设置主库的地址、用户名密码、binlog 偏移量，然后执行 start slave 命令，启动 io_thread 线程与主库建立连接，将获取的 binlog 写到 relay log（中转日志），启动 sql_thread 线程读取 relay log 并解析命令和执行。主库内部有一个线程专用语服务备库的长连接，从指定位置读取 binlog 和发送给备库。

![](https://blog-1304941664.cos.ap-guangzhou.myqcloud.com/article_material/database/mysql_master_slave.jpg)

# 6. 参考

* [01 | 基础架构：一条SQL查询语句是如何执行的？-MySQL 实战 45 讲-极客时间](https://time.geekbang.org/column/article/68319)
* [《MySQL是怎样运行的》](https://book.douban.com/subject/35231266/)

