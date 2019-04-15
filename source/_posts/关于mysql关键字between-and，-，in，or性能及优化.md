---
title: 关于mysql关键字between and，>=<，in，or性能及优化
date: 2016-12-22 13:37:32
update: 2016-12-22 13:37:32
categories: MySQL
tags: [mysql]
---

### 关于IN关键字的效率问题

<!-- more -->
查询表
```apache
mysql> select count(1) from tk_plan_wait;
+----------+
| count(1) |
+----------+
|    20312 |
+----------+
1 row in set (0.00 sec)
```
这个表的总记录数为20312行

查询表索引
```apache
mysql> show index from tk_plan_wait;
+--------------+------------+-----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table        | Non_unique | Key_name        | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+--------------+------------+-----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| tk_plan_wait |          0 | PRIMARY         |            1 | idx         | A         |       20703 |     NULL | NULL   |      | BTREE      |         |               |
| tk_plan_wait |          0 | tk_plan_wait_pk |            1 | PLAN_NUM    | A         |       20703 |     NULL | NULL   |      | BTREE      |         |               |
+--------------+------------+-----------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set (0.00 sec)
```
由于索引PLAN_NUM字段是unique唯一类型，可以看到其散列程度（Cardinality）是非常高的，因此该索引是很有效的。
其中字段rows为1，只扫描了一行，含有索引其执行效率很高。  

> 使用in关键字根据有索引字段查询  

使用优化器EXPLAIN执行含有索引的sql请看执行情况
```apache
mysql> EXPLAIN SELECT * FROM `tk_plan_wait` WHERE PLAN_NUM in('367b069a-4f2c-4d6e-899a-9c9690760d19');
+----+-------------+--------------+------------+-------+-----------------+-----------------+---------+-------+------+----------+-------+
| id | select_type | table        | partitions | type  | possible_keys   | key             | key_len | ref   | rows | filtered | Extra |
+----+-------------+--------------+------------+-------+-----------------+-----------------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | tk_plan_wait | NULL       | const | tk_plan_wait_pk | tk_plan_wait_pk | 152     | const |    1 |   100.00 | NULL  |
+----+-------------+--------------+------------+-------+-----------------+-----------------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.00 sec)
```
<p style="color:red">
其中字段rows为1，只扫描了一行，含有索引其执行效率很高。
</p>
 
> 使用in关键字根据没有添加索引字段查询  

```apache
mysql> EXPLAIN SELECT * FROM `tk_plan_wait` WHERE TASK_ARG1 in('t');
+----+-------------+--------------+------------+------+---------------+------+---------+------+-------+----------+-------------+
| id | select_type | table        | partitions | type | possible_keys | key  | key_len | ref  | rows  | filtered | Extra       |
+----+-------------+--------------+------------+------+---------------+------+---------+------+-------+----------+-------------+
|  1 | SIMPLE      | tk_plan_wait | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 20703 |    10.00 | Using where |
+----+-------------+--------------+------------+------+---------------+------+---------+------+-------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
```

<p style="color:red">
其中字段rows为20703，in在不使用含有索引字段查询的情况下是进行了全表扫描的。
</p>
待续...
参考：http://amao12580.github.io/post/2016/07/MySQL-in-operator-must-lead-to-full-scan/