---
title: MySQL
date: 2016-11-19 10:50:02
update: 2017-01-04 10:50:02
categories: MySQL
tags: [mysql]
---

# 部门表dept和员工表empMySQL练习

<!-- more -->

> 建表

```sql
/*
Navicat MySQL Data Transfer

Source Server         : 10.1.1.241
Source Server Version : 50547
Source Host           : 10.1.1.241:3306
Source Database       : test

Target Server Type    : MYSQL
Target Server Version : 50547
File Encoding         : 65001

Date: 2016-11-15 15:41:17
*/

SET FOREIGN_KEY_CHECKS=0;

-- ----------------------------
-- Table structure for bonus
-- ----------------------------
DROP TABLE IF EXISTS `bonus`;
CREATE TABLE `bonus` (
  `empno` decimal(4,0) DEFAULT NULL,
  `job` varchar(9) DEFAULT NULL,
  `sal` decimal(10,0) DEFAULT NULL,
  `comm` decimal(10,0) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of bonus
-- ----------------------------

-- ----------------------------
-- Table structure for dept
-- ----------------------------
DROP TABLE IF EXISTS `dept`;
CREATE TABLE `dept` (
  `deptno` decimal(2,0) DEFAULT NULL,
  `dname` varchar(14) DEFAULT NULL,
  `loc` varchar(13) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of dept
-- ----------------------------
INSERT INTO `dept` VALUES ('10', 'ACCOUNTING', 'NEW YORK');
INSERT INTO `dept` VALUES ('20', 'RESEARCH', 'DALLAS');
INSERT INTO `dept` VALUES ('30', 'SALES', 'CHICAGO');
INSERT INTO `dept` VALUES ('40', 'OPERATIONS', 'BOSTON');

-- ----------------------------
-- Table structure for emp
-- ----------------------------
DROP TABLE IF EXISTS `emp`;
CREATE TABLE `emp` (
  `empno` decimal(4,0) NOT NULL,
  `ename` varchar(10) DEFAULT NULL,
  `job` varchar(9) DEFAULT NULL,
  `mgr` decimal(4,0) DEFAULT NULL,
  `hiredate` datetime DEFAULT NULL,
  `sal` decimal(7,2) DEFAULT NULL,
  `comm` decimal(7,2) DEFAULT NULL,
  `deptno` decimal(2,0) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of emp
-- ----------------------------
INSERT INTO `emp` VALUES ('7369', 'SMITH', 'CLERK', '7902', '1980-12-17 00:00:00', '960.00', null, '20');
INSERT INTO `emp` VALUES ('7499', 'ALLEN', 'SALESMAN', '7698', '1981-02-20 00:00:00', '2764.80', '300.00', '30');
INSERT INTO `emp` VALUES ('7521', 'WARD', 'SALESMAN', '7698', '1981-02-22 00:00:00', '2160.00', '500.00', '30');
INSERT INTO `emp` VALUES ('7566', 'JONES', 'MANAGER', '7839', '1981-04-02 00:00:00', '3570.00', null, '20');
INSERT INTO `emp` VALUES ('7654', 'MARTIN', 'SALESMAN', '7698', '1981-09-28 00:00:00', '2160.00', '1400.00', '30');
INSERT INTO `emp` VALUES ('7698', 'BLAKE', 'MANAGER', '7839', '1981-05-01 00:00:00', '4924.80', null, '30');
INSERT INTO `emp` VALUES ('7782', 'CLARK', 'MANAGER', '7839', '1981-06-09 00:00:00', '2940.00', null, '10');
INSERT INTO `emp` VALUES ('7788', 'SCOTT', 'ANALYST', '7566', '1982-12-09 00:00:00', '3600.00', null, '20');
INSERT INTO `emp` VALUES ('7839', 'KING', 'PRESIDENT', null, '1981-11-17 00:00:00', '6000.00', null, '10');
INSERT INTO `emp` VALUES ('7844', 'TURNER', 'SALESMAN', '7698', '1981-09-08 00:00:00', '2592.00', '0.00', '30');
INSERT INTO `emp` VALUES ('7876', 'ADAMS', 'CLERK', '7788', '1983-01-12 00:00:00', '1320.00', null, '20');
INSERT INTO `emp` VALUES ('7900', 'JAMES', 'CLERK', '7698', '1981-12-03 00:00:00', '1641.60', null, '30');
INSERT INTO `emp` VALUES ('7902', 'FORD', 'ANALYST', '7566', '1981-12-03 00:00:00', '3600.00', null, '20');
INSERT INTO `emp` VALUES ('7934', 'MILLER', 'CLERK', '7782', '1982-01-23 00:00:00', '1560.00', null, '10');

-- ----------------------------
-- Table structure for salgrade
-- ----------------------------
DROP TABLE IF EXISTS `salgrade`;
CREATE TABLE `salgrade` (
  `grade` decimal(10,0) DEFAULT NULL,
  `losal` decimal(10,0) DEFAULT NULL,
  `hisal` decimal(10,0) DEFAULT NULL
) ENGINE=MyISAM DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of salgrade
-- ----------------------------
INSERT INTO `salgrade` VALUES ('1', '700', '1200');
INSERT INTO `salgrade` VALUES ('2', '1201', '1400');
INSERT INTO `salgrade` VALUES ('3', '1401', '2000');
INSERT INTO `salgrade` VALUES ('4', '2001', '3000');
INSERT INTO `salgrade` VALUES ('5', '3001', '9999');
```
<!-- more  -->
> 练习

```sql
-- 1.更新名称为'SALES'部门下的所有员工薪水上涨20%
UPDATE emp e SET e.sal = e.sal * (1 + 0.2) WHERE e.deptno = ( SELECT d.deptno FROM dept d WHERE d.dname = 'SALES' );
-- 2.找出部门10中所有经理(MANAGER) 和部门20中所有办事员（CLERK）的详细资料
SELECT * FROM emp e WHERE ( e.deptno = '10' AND e.job = 'MANAGER' ) OR ( e.deptno = '20' AND e.job = 'CLERK');
-- 3.找出部门10中所有经理，部门20中所有的办事员，既不是经理又不是办事员，但是薪金大于或等于2000的所有员工的资料
SELECT * FROM emp WHERE (deptno = 10 AND job = 'MANAGER') OR (deptno = 20 AND job = 'CLERK') OR ( job NOT IN ('MANAGER', 'CLERK') AND sal >= 2000);
-- 4.找出收取佣金（comm）的员工的不同工作：[DISTINCT->消除重复的关键字]
SELECT DISTINCT job FROM emp e WHERE e.comm IS NOT NULL;
-- 5.找个各月倒数第三天受雇的所有员工
SELECT * FROM emp e WHERE e.hiredate = DATE_FORMAT(LAST_DAY(e.hiredate) - 2, '%Y-%m-%d %H:%i:%s');
	-- 需要注意的是有两点转换格式的大小写：
	-- 1.
	-- LAST_DAY(e.hiredate) 获取到的日期格式：2016-09-30
	-- LAST_DAY(e.hiredate) - 2 获取到的日期格式：20160928
	-- 2.转换格式的大小写：
	-- DATE_FORMAT('2016-11-15', '%Y-%m-%d %H:%i:%s');->2016-11-15 00:12:00
	-- DATE_FORMAT('2016-11-15', '%Y-%m-%d %h:%I:%S');->2016-11-15 12:00:00
-- 6.找出早于2012年前受雇的员工
	-- mysql:
	SELECT * FROM emp e WHERE DATE_FORMAT(e.hiredate, '%Y%m%d%H%i%s') < '2012';
	-- oracle:
	SELECT * FROM emp WHERE MONTHS_BETWEEN(hiredate,'20120000')>0;
	-- MONTHS_BETWEEN('2012-01-01', '2016-11-12');表示2012-01-01到2016-11-12的月数，以小数形式返回
-- 7.按照首字母大写其余字母小写的方式显示员工姓名
SELECT CONCAT( UPPER(LEFT(e.ename, 1)), LOWER( ( RIGHT (e.ename, LENGTH(e.ename) - 1) ) ) ) as ename FROM emp e;	
-- 8.显示正好为5个字符的员工的姓名
SELECT e.ename ad ename FROM emp e WHERE LENGTH(e.ename) = 5;	
-- 9.显示姓名不带'R'的员工的姓名
SELECT ename FROM emp WHERE ename NOT LIKE '%R%';	
-- 10.显示所有员工姓名的前3个字符
SELECT SUBSTR(e.ename, 1, 3) FROM emp e;
SELECT LEFT(e.ename, 3) FROM emp e;
-- 11.显示所有员工的姓名，并且用“x” 替换替换所有的 “A”
SELECT REPLACE(ename,'A','x') FROM emp;
-- 12.显示员工的姓名和受雇日期，并根据其服务年限，把资料最老的员工排在第在前面
SELECT ename,hiredate FROM emp ORDER BY hiredate; -- mysql默认是ASC升序	
-- 13.显示所有员工的姓名，加入公司的年份和月份，按受雇日期所在的年排序，若年份相同则讲最早月份的员工排在最前面
SELECT e.ename, DATE_FORMAT(e.hiredate,'%Y') YEAR, DATE_FORMAT(e.hiredate,'%m') MONTH FROM emp e ORDER BY YEAR, MONTH;
-- 14.显示在一个月为30天的情况所有员工的日薪金，并且忽略余数
SELECT e.ename, ROUND(e.sal/30) 日薪金 FROM emp e;	
-- 15.找出在（任何年份）的2月受聘的所有员工
SELECT * FROM emp e WHERE MONTH(e.hiredate) = 2;	
-- 16.对于每个员工，显示其加入公司的天数
SELECT datediff(SYSDATE(),'2016-11-15 09:41:18') 两日期之间天数1, TO_DAYS(SYSDATE()) - TO_DAYS('2016-11-15 09:41:18') 两日期之间天数2 FROM emp e;	
17.以年月的方式显示所有员工的服务年限
SELECT (YEAR(SYSDATE())-YEAR(e.hiredate)) 年份, (YEAR(SYSDATE())-YEAR(e.hiredate))*12 + MONTH(SYSDATE())-MONTH(e.hiredate) 月份 FROM emp e;	
-- 18.列出至少有三个员工的所有部门和部门信息
	-- 方式一:
	SELECT d.deptno, d.dname, d.loc, count(1) 部门人数 FROM emp e LEFT JOIN dept d ON d.deptno = e.deptno GROUP BY e.deptno HAVING COUNT(e.empno) > 3;
	-- 方式二
	SELECT d.*, ed.count 部门人数 FROM dept d, (SELECT deptno, COUNT(empno) COUNT FROM emp GROUP BY deptno HAVING COUNT(empno) > 3) ed WHERE d.deptno = ed.deptno;
-- 19.列出薪金比“ALLEN”多的所有员工
SELECT * FROM emp e WHERE e.sal > (SELECT e1.sal FROM emp e1 WHERE e1.ename='ALLEN');	
-- 20.列出所有员工的姓名及其上级的姓名
SELECT e.ename 员工姓名, e1.ename 员工上级姓名 FROM emp e LEFT JOIN emp e1 ON e.mgr=e1.empno;	
-- 21.列出受雇日期早于直接上级的所有员工的编号，姓名，部门名称
SELECT * FROM emp e WHERE e.hiredate < (SELECT e1.hiredate FROM emp e1 WHERE e1.empno=e.mgr);
-- 22.列出部门名称和这些部门员工的信息，同时列出那些没有员工的部门
SELECT d.dname, e.* FROM dept d LEFT JOIN emp e ON e.deptno = d.deptno;	
-- 23.列出“CLERK”的姓名和部门名称，部门人数
SELECT e.ename, d.dname, ep.cou FROM emp e LEFT JOIN dept d ON d.deptno=e.deptno LEFT JOIN (SELECT e1.deptno, COUNT(empno) cou FROM emp e1 GROUP BY deptno) ep ON ep.deptno=d.deptno WHERE e.job = 'CLERK';	
-- 24.列出最低薪金大于1500的各种工作以及从事此工作的全部雇员人数
SELECT e.job, ep.count FROM emp e LEFT JOIN (SELECT e1.job, count(e1.job) COUNT FROM emp e1 GROUP BY e1.job) ep ON e.job=ep.job WHERE e.sal>1500 GROUP BY e.job;	
-- 25.列出薪金高于工资平均薪金的所有员工，所在部门，上级领导，公司的工资等级
SELECT e.empno, d.dname 所在部门名称,e1.ename 上级领导,e.sal 工资, s.grade 工资等级 FROM emp e LEFT JOIN dept d ON d.deptno=e.deptno LEFT JOIN emp e1 ON e.mgr=e1.empno LEFT JOIN salgrade s ON e.sal BETWEEN s.losal AND s.hisal WHERE e.sal> (SELECT AVG(e1.sal) FROM emp e1);
-- 26.列出和“SCOTT”从事相同工作的所有员工及部门名称
SELECT e1.empno, e1.job, e1.deptno, d.dname FROM emp e1 LEFT JOIN dept d ON d.deptno=e1.deptno WHERE e1.job= (SELECT e.job FROM emp e WHERE e.ename='SCOTT');	
-- 27.列出薪金等于部门30中员工薪金的所有员工的姓名和薪金
SELECT ename, sal FROM emp WHERE sal IN (SELECT sal FROM emp WHERE deptno='30') AND deptno!='30';
-- 28.列出薪金高于在部门30工作的所有员工的薪金的员工姓名和薪金、部门名称
SELECT e.ename 员工姓名, e.sal 薪金, d.dname FROM emp e LEFT JOIN dept d ON d.deptno=e.deptno WHERE e.sal> (SELECT max(e1.sal) FROM emp e1 WHERE e1.deptno='30');
-- 29.列出每个部门工作的员工数量、平均工资和平均服务期限
SELECT d.dname 部门名称, count(e.deptno) 员工数量, AVG(e.sal) 平均工资, AVG(((YEAR (SYSDATE()) - YEAR (e.hiredate)) * 12 + MONTH (SYSDATE()) - MONTH (e.hiredate)) / 12) 工作平均年限 FROM dept d LEFT JOIN emp e ON e.deptno = d.deptno GROUP BY e.deptno;	
-- 30.列出各个部门的MANAGER 的最低薪金
SELECT ep.*, MIN(ep.sal) 部门最低薪水 FROM (SELECT d.dname 部门名称,e.* FROM emp e LEFT JOIN dept d ON d.deptno=e.deptno WHERE e.job='MANAGER') ep GROUP BY ep.部门名称;
-- 31.查询出某个员工的上级主管，并要求这些主管中的薪水超过3000
SELECT e.* FROM emp e LEFT JOIN emp e1 ON e1.deptno=e.mgr WHERE e.sal>3000;
-- 32.求出部门名称中带有’S‘字符的部门员工的工资合计，部门人数
SELECT d.dname 部门名称, sum(e.sal) 工资合计, count(e.deptno) FROM dept d LEFT JOIN emp e ON e.deptno=d.deptno WHERE d.dname LIKE '%S%' GROUP BY d.deptno;	
-- 33.给任职日期超过10年的人加薪10%
UPDATE emp e SET e.sal=e.sal*1.1 WHERE (TIMESTAMPDIFF(MONTH,e.hiredate,SYSDATE()))/12 > 10;	
SELECT TIMESTAMPDIFF(DAY,'2012-10-01','2013-01-13'); -- 获取两日期之间天数
SELECT DATEDIFF('2013-01-13','2012-10-01') FROM dual; -- 获取两日期之间天数
SELECT TIMESTAMPDIFF(MONTH,'2012-10-01','2013-01-13'); -- 获取两日期月数
```

# EXISTS和IN

效率差
```sql
SELECT
	1
FROM
	tp_parcel_info f
LEFT JOIN tp_station_info s ON s.f_station_id = f.f_station_id
WHERE
	1 = 1
AND f.f_parcel_status IN (0)
AND f.f_last_time >= '20160901000000'
AND f.f_last_time <= '20161131235959'
AND f.f_company_id = '108'
AND EXISTS (
	SELECT
		1
	FROM
		tp_express_branch_com_dz d
	WHERE
		d.f_exp_id = '2729'
	AND f.f_station_id = d.f_station_id
	AND d.f_branch_state = 0
)
ORDER BY
	f.f_last_time DESC
```
效率高
```sql
SELECT
	f.f_parcel_id,
	f.f_exp_id,
	f.f_branch_id,
	f.f_station_id,
	f.f_mail_no,
	f.f_company_id,
	f.f_company_name,
	f.f_recipient_name,
	f.f_recipient_mobile,
	f.f_frame_code,
	f.f_take_code,
	f.f_num,
	f.f_input_way,
	f.f_input_source,
	f.f_arrive_time,
	f.f_parcel_status,
	f.f_is_detention,
	f.f_out_way,
	f.f_out_source,
	f.f_app_notice_status,
	f.f_weixin_notice_status,
	f.f_sms_notice_status,
	f.f_out_time,
	f.f_last_time,
	f.f_type,
	f.f_comment,
	s.f_station_name
FROM
	tp_parcel_info f
LEFT JOIN tp_station_info s ON s.f_station_id = f.f_station_id
WHERE
	1 = 1
AND f.f_parcel_status IN (0)
AND f.f_last_time >= '20160901000000'
AND f.f_last_time <= '20161131235959'
AND f.f_company_id = '108'
AND f.f_station_id IN (
	SELECT
		d.f_station_id
	FROM
		tp_express_branch_com_dz d
	WHERE
		d.f_exp_id = '2729'
	AND d.f_branch_state = 0
)
ORDER BY
	f.f_last_time DESC
```
> 例如

```sql
-- 例如：表A（小表），表B（大表）
-- 1：
select * from A where cc in (select cc from B)
-- 效率低，用到了A表上cc列的索引；
select * from A where exists(select cc from B where cc=A.cc)
-- 效率高，用到了B表上cc列的索引。
-- 相反的
-- 2：
select * from B where cc in (select cc from A)
-- 效率高，用到了B表上cc列的索引；
select * from B where exists(select cc from A where cc=B.cc)
-- 效率低，用到了A表上cc列的索引。
```

> 总结	

<font color=#FF0000 face="Menlo">in是把外表和内表作hash连接，而exists是对外表作loop循环，每次loop循环再对内表进行查询，一直以来认为exists比in效率高的说法是不准确的。如果查询的两个表大小相当，那么用in和exists差别不大；如果两个表中一个较小一个较大，则子查询表大的用exists(大表)，子查询表小的用in(小表);
跟显示字段的多少没有太大区别。
</font>

### MySQL树结构

* 根据某节点ID查询所有父节点(包含该节点)
```mysql
SELECT T2.id,
T2.pid,
T1.lvl
FROM (
	SELECT @r                                                    AS _id,
		(SELECT @r := pid FROM tb_p_resources WHERE id = _id) AS pid,
		@l := @l + 1                                          AS lvl
	FROM (SELECT @r := 14, @l := 0) vars,
		tb_p_resources h
	WHERE @r != 0
) T1
	JOIN tb_p_resources T2 ON T1._id = T2.id
ORDER BY T1.lvl DESC
```

* 根据某节点查询所有子节点(不包含该节点)

```sql
SELECT
	id 
FROM
	(
SELECT
	t0.id,t0.pid
FROM
	(
SELECT
	tpu.id,
	tpu.NAME,
	( SELECT p_unit_id FROM tb_p_unit_relation_t tpurt WHERE tpurt.c_unit_id = tpu.id ) pid 
FROM
	tb_p_unit_t tpu 
WHERE
	tpu.`status` = 0 
	) t0 
WHERE
	t0.pid > 0 
ORDER BY
	t0.pid,
	t0.id DESC 
	) realname_sorted,
	( SELECT @pv := 3 ) initialisation 
WHERE
	( FIND_IN_SET( pid, @pv ) > 0 AND @pv := concat( @pv, ',', id ) )
```

### 行转列

```sql
SELECT
	SUBSTRING_INDEX( SUBSTRING_INDEX( '重庆,南京,苏州,常州,杭州,济南,东莞,佛山,中山,厦门,福州,武汉,大连,沈阳,天津,石家庄,哈尔滨,北京,上海', ',', help_topic_id + 1 ), ',', - 1 ) AS city 
FROM
	mysql.help_topic 
WHERE
	help_topic_id < LENGTH( '重庆,南京,苏州,常州,杭州,济南,东莞,佛山,中山,厦门,福州,武汉,大连,沈阳,天津,石家庄,哈尔滨,北京,上海' ) - LENGTH( REPLACE ( '重庆,南京,苏州,常州,杭州,济南,东莞,佛山,中山,厦门,福州,武汉,大连,沈阳,天津,石家庄,哈尔滨,北京,上海', ',', '' ) ) + 1
```

![mysql-a](https://volc1612.gitee.io/blog/images/mysql/mysql-a.png)

### A表中的字段，模糊匹配B中字段

查询B表中的name字段包含A表中的city字段

```sql
select A.city,B.`name`,B.id from (
SELECT
	SUBSTRING_INDEX( SUBSTRING_INDEX( '重庆,南京,苏州,常州,杭州,济南,东莞,佛山,中山,厦门,福州,武汉,大连,沈阳,天津,石家庄,哈尔滨,北京,上海', ',', help_topic_id + 1 ), ',', - 1 ) AS city 
FROM
	mysql.help_topic
WHERE
	help_topic_id < LENGTH( '重庆,南京,苏州,常州,杭州,济南,东莞,佛山,中山,厦门,福州,武汉,大连,沈阳,天津,石家庄,哈尔滨,北京,上海' ) - LENGTH( REPLACE ( '重庆,南京,苏州,常州,杭州,济南,东莞,佛山,中山,厦门,福州,武汉,大连,沈阳,天津,石家庄,哈尔滨,北京,上海', ',', '' ) ) + 1
) A inner join floor B on B.`name`  like concat("%",A.city,"%");
```

![mysql-b](https://volc1612.gitee.io/blog/images/mysql/mysql-b.png)