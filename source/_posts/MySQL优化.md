---
title: MySQL优化
date: 2017-01-18 08:28:51
update: 2017-01-18 08:28:51
categories: MySQL
tags: [mysql]
---

### 优化
<!-- more -->
EXISTS 和 IN
<a href="https://volc1612.gitee.io/blog/2016/11/19/MySQL/">用法</a>

参考：http://www.voidcn.com/blog/u010331428/article/p-3922833.html
MySQL实现join的原理，原来MySQL内部采用了一种叫做 nested loop join的算法。Nested Loop Join 实际上就是通过驱动表的结果集作为循环基础数据，然后一条一条的通过该结果集中的数据作为过滤条件到下一个表中查询数据，然后合并结果。如果还有第三个参与 Join，则再通过前两个表的 Join 结果集作为循环基础数据，再一次通过循环查询条件到第三个表中查询数据，如此往复，基本上MySQL采用的是最容易理解的算法来实现join。所以驱动表的选择非常重要，驱动表的数据小可以显著降低扫描的行数。
一般情况下参与联合查询的两张表都会一大一小，如果是join，在没有其他过滤条件的情况下MySQL会选择小表作为驱动表，但是left join一般用作大表去join小表，而left join本身的特性决定了MySQL会用大表去做驱动表，这样下来效率就差了不少。

1.如果where条件中含有右表的非空条件（除开右表使用is null判断），则left join语句等同于join语句，可直接改写成join语句。  
SELECT* FROM A pi LEFT JOIN B si ON si.f_station_id = pi.f_station_id; -- 以左表为驱动表
SELECT* FROM A pi LEFT JOIN B si ON si.f_station_id = pi.f_station_id WHERE pi.f_station_id >= '7231' -- 以小表为驱动表等同于join

那么如何优化left join：  
1、条件中尽量能够过滤一些行将驱动表变得小一点，用小数据表去驱动大数据表  
2、右表的条件列一定要加上索引（主键、唯一索引、前缀索引等），最好能够使type达到range及以上（ref,eq_ref,const,system）  
3、无视以上两点，一般不要用left join

例如：
为防止查询优化器犯傻，则可以根据你SQL特点和字段名何曾看(A与B，A与C是1对1的关系，或者B,C表中可能不存在，不能是1-N的关系)，先对A表进行那个过滤和分页，再进行LEFT JOIN操作，则可以大致些成：
SELECT * 
FROM (SELECT A.columnname.. FROM A WHERE A...=..ORDER BY A.ap_like LIMIT 12) AS A
LEFT JOIN B .....
LEFT JOIN C......;

写成以小表驱动大表，这是描述的嵌套循环算法

=========================================

sql优化
1.
使用inner join 等值匹配连接查询
查询站点ID>=7231的所有包裹信息和站点信息
效率低：
-- 查询B表f_station_id大于7231的数据再根据条件匹配A表数据，匹配成功返回A表数据
SELECT * FROM A pi WHERE EXISTS ( SELECT * FROM B si WHERE si.f_station_id >= '7231' AND pi.f_station_id = si.f_station_id );
效率高：
SELECT* FROM A pi INNER JOIN B si USING(f_station_id) WHERE si.f_station_id >= '7231';

2.
使用JOIN时候，应该用小的结果驱动打的结果（left join 左边表结果尽量小，如果有条件应该放到左边先处理，right join同理反向）
Select * from A left join B ona.id=B.ref_id where B.ref_id>10;

可以优化为：
select * from (select * from A wehre id >10) T1 left join B onT1.id=B.ref_id;

3.
使用between and
效率低:
Select * from A order by id limit 10000000,10;
效率高：
Select * from A where id between 10000000 and 10000010;

4.
尽量用union all 代替union
A.union和union all的差异主要是前者需要将两个（或者多个）结果集合并后再进行唯一性过滤操作，这就会涉及到排序，增加大量的cpu运算，加大资源消耗及延迟。所以当我们可以确认不可能出现重复结果集或者不在乎重复结果集的时候，尽量使用union all而不是union.

5.
不要在列上进行运算
select * fromusers where YEAR(adddate)<2007;
这些导致索引失效进行全表扫描，因此我们可以改成：
Select * from users where adddate<’2007-01-01’;

6.
尽量不要使用NOT IN和<>操作

=========================================

1、应尽量避免在 where 子句中使用!=或<>操作符，否则将引擎放弃使用索引而进行全表扫描。

2、对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

3、应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描，如：
select id from t where num is null
可以在num上设置默认值0，确保表中num列没有null值，然后这样查询：
select id from t where num=0

4、尽量避免在 where 子句中使用 or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描，如：
select id from t where num=10 or num=20
可以这样查询：
select id from t where num=10
union all
select id from t where num=20

5、下面的查询也将导致全表扫描：(不能前置百分号)
select id from t where name like '%%'
若要提高效率，可以考虑全文检索。

6、in 和 not in也要慎用,否则会导致全表扫描，如：
select id from t where num in(1,2,3)
对于连续的数值，能用 between 就不要用 in 了：
select id from t where num between 1 and 3

7、应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。如：
select id from t where num/2=100
应改为:
select id from t where num=100*2


8、应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描。如：
select id from t where substring(name,1,3)='abc'–name以abc开头的id
select id from t where datediff(day,createdate,'2005-11-30')=0–'2005-11-30'生成的id
应改为:
select id from t where name like 'abc%'
select id from t where createdate>='2005-11-30' and createdate<'2005-12-1'

9、不要在 where 子句中的"="左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引。

10、在使用索引字段作为条件时，如果该索引是复合索引，那么必须使用到该索引中的第一个字段作为条件时才能保证系统使用该索引，否则该索引将不会被使 用，并且应尽可能的让字段顺序与索引顺序相一致。


11、并不是所有索引对查询都有效，SQL是根据表中数据来进行查询优化的，当索引列有大量数据重复时，SQL查询可能不会去利用索引，如一表中有字段 sex，male、female几乎各一半，那么即使在sex上建了索引也对查询效率起不了作用。

12、索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了 insert 及 update 的效率，因为 insert 或 update 时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。一个表的索引数最好不要超过6个，若太多则应考虑一些不常使用到的列上建的索引是否有 必要。

13、尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。这是因为引擎在处理查询和连接时会 逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。

14、尽可能的使用 varchar/nvarchar 代替 char/nchar ，因为首先变长字段存储空间小，可以节省存储空间，其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些。
	char 和 varchar

	CHAR(M)定义的列的长度为固定的
	VARCHAR(M)定义的列的长度为可变长字符串
	超限自动截取
	效率来说基本是char>varchar>text，但是如果使用的是Innodb引擎的话，推荐使用varchar代替char
	
	
	使用：
	1.根据字符的长度来判断，是考虑其长度的是否相近来确定选择char还是varchar，如何字段的长度基本都是一样或者其长度总是近似的可以选用char
	2.是从碎片角度进行考虑 用可变长度的字符型数据时，数据库管理员要时不时的对碎片进行整理。如执行数据库导出导入作业，来消除碎片。
	3.即使使用Varchar数据类型，也不能够太过于慷慨！ 比如你只使用到90个字符，VARCHAR(100)与VARCHAR(200)真的相同吗?结果是否定的。虽然他们用来存储90个字符的数据，其存储空间相同。但是对于内存的消耗是不同的。

15、任何地方都不要使用 select * from t ，用具体的字段列表代替"*"，不要返回用不到的任何字段。

16、尽量使用表变量来代替临时表。如果表变量包含大量数据，请注意索引非常有限（只有主键索引）。

17、避免频繁创建和删除临时表，以减少系统表资源的消耗。

18、临时表并不是不可使用，适当地使用它们可以使某些例程更有效，例如，当需要重复引用大型表或常用表中的某个数据集时。但是，对于一次性事件，最好使 用导出表。

19、在新建临时表时，如果一次性插入数据量很大，那么可以使用 select into 代替 create table，避免造成大量 log ，以提高速度；如果数据量不大，为了缓和系统表的资源，应先create table，然后insert。

20、如果使用到了临时表，在存储过程的最后务必将所有的临时表显式删除，先 truncate table ，然后 drop table ，这样可以避免系统表的较长时间锁定。

21、尽量避免使用游标，因为游标的效率较差，如果游标操作的数据超过1万行，那么就应该考虑改写。

22、使用基于游标的方法或临时表方法之前，应先寻找基于集的解决方案来解决问题，基于集的方法通常更有效。

23、与临时表一样，游标并不是不可使用。对小型数据集使用 FAST_FORWARD 游标通常要优于其他逐行处理方法，尤其是在必须引用几个表才能获得所需的数据时。在结果集中包括"合计"的例程通常要比使用游标执行的速度快。如果开发时 间允许，基于游标的方法和基于集的方法都可以尝试一下，看哪一种方法的效果更好。

24、在所有的存储过程和触发器的开始处设置 SET NOCOUNT ON ，在结束时设置 SET NOCOUNT OFF 。无需在执行存储过程和触发器的每个语句后向客户端发送 DONE_IN_PROC 消息。


### 数据库设计命名规范

|对象名|前缀|范例|
| --------   | --------  | --------  |
|表（table）|tbl_/t_（或不加前缀）  |userinfo/t_user_info/tbl_user_info|
|视图（view）    |v_/v   | v_user_info/vuserinfo|
|序列（sequence）  |  seq_  |  seq_user_info|
|簇（cluster）|  c_ | c_user_info|
|触发器（trigger）  |  trg_  |  trg_user_info|
|存储过程（procedure）| sp_/p_ | sp_user_info/p_user_info|
|函数（function）   | f_/fn_ | fn_user_info/f_user_info|
|物化视图（materialized view）| mv_ |mv_user_info|
|包和包体（package & package body）|    pkg_  |  pkg_user_info|
|类和类体（type & type body） | typ_   | typ_user_info|
|主键（primary key）| pk_ |pk_user_info|
|外键（foreign key）| fk_ |fk_user_info_fieldname|
|唯一索引（unique index） | uk_ |uk_user_info_fieldname|
|普通索引（normal index） | idx_  |  idx_user_info_fieldname|
|位图索引（bitmap index） | bk_ |bk_user_info_fieldname|
|同义词（synonym）    |依据所分配的表所属模块/模式  |
|数据库链接（database link） |   无特殊要求  | |   