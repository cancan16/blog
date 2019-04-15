---
title: oracle数据库
date: 2017-04-14 13:08:17
update: 2017-04-14 13:08:17
categories: ORACLE
tags: [oracle,powerdesigner]
---
### 1 oracle数据库对表设置自增ID

<!-- more -->
```sql
-- 创建序列
CREATE SEQUENCE SYS_MAIL_PLAN_WAIT_SEQUENCE
   increment by 1    -- 每次递增1
   start with 1       -- 从1开始
   nomaxvalue      -- 没有最大值
   minvalue 1       -- 最小值=1
   NOCYCLE;      -- 不循环
-- 创建触发器，每次新增SYS_MAIL_PLAN_WAIT表数据之前被执行，并将SYS_MAIL_PLAN_WAIT中PLAN_ID自增1
CREATE OR REPLACE TRIGGER SYS_MAIL_PLAN_WAIT_TRIGGER
    BEFORE INSERT ON SYS_MAIL_PLAN_WAIT
  FOR EACH ROW
  BEGIN
    SELECT SYS_MAIL_PLAN_WAIT_SEQUENCE.nextval INTO :new.PLAN_ID FROM DUAL;
  END;
  
INSERT INTO SYS_MAIL_PLAN_WAIT(PROS_ID) VALUES ('1');
INSERT INTO SYS_MAIL_PLAN_WAIT(PROS_ID) VALUES ('3');
```

### 2 批量更新
```sql
<update id="lockTaskPlan">
	begin
	<foreach collection="mailPlanList" item="item" index="index" separator=";" close="">
		UPDATE SYS_MAIL_PLAN_WAIT SET STATUS =
		#{item.status},thread_id = #{item.threadId} WHERE PLAN_ID = #{item.planId}
	</foreach>
	;end;
</update>
```
这种写法对于返回更新数量的数据库配置是不起作用的。
```
<!--由于项目配置中启用了BATCH执行器，UPDATE和DELETE返回的行数就丢失了，把执行器改为SIMPLE即可。-->
<!-- defaultExecutorType设置为BATCH有个缺陷就是无法获取update、delete返回的行数 -->
        <!-- <setting name="defaultExecutorType" value="SIMPLE" />-->
```

获取返回数量的批量更新如下写法
```sql
update table_name t1 set (a,b,c)=( select a,b,c from table_name_2 t2 where t1.a=t2.a) where exists(select 1 from table_name_2 t2 where t1.a=t2.a); 
```


### 3 navicat连接本地oracle

#### 3.1 Navicat Premium连接本地Oracle

想要使用navicat连接本地oracle数据库首先确保两点
1.oracle版本号11.2.0.1.0 64位,Navicat Premium也是64位，总之保证数据库客户端工具运行平台操作系统位数和oracle运行平台操作系统位数相同。
2.客户端instantclient_11_2和安装的oracle版本号一致，也是64位。

#### 3.2 下载instantclient_11_2
要下载<a href="http://www.oracle.com/technetwork/topics/winx64soft-089540.html">instantclient_11_2</a>，和navicat一样都是64位，找到对应的版本号，选择Basic类型下载
下载完成后解压到Navicat安装目录E:\Navicat Premium替换掉原来的instantclient_11_2，重启navicat


#### 3.3 修改文件
E:\database\base\product\11.2.0\dbhome_1\NETWORK\ADMIN\listener.ora和
E:\database\base\product\11.2.0\dbhome_1\NETWORK\ADMIN\tnsnames.ora两个文件，修改之前要先备份这两个文件
把这两个文件中的localhost全部替换为该计算机全名。
重新启动OracleServiceORCL和OracleOraDb11g_home1TNSListener服务

配置navicat
E:\Navicat Premium\instantclient_11_2\oci.dll
E:\database\base\product\11.2.0\dbhome_1\BIN\sqlplus.exe
![测试](http://www.volc1612.gitee.io/blog/images/oracle数据库/navicat.png) 


#### 3.4 连接数据库

连接类型Basic
端口号1521
服务名ORCL(和E:\database\base\product\11.2.0\dbhome_1\NETWORK\ADMIN\tnsnames.ora文件中的SERVICE_NAME值一致，不区分大小写)
用户名scott
密码tiger

遇到的问题
ORA-28000 scott账号被锁
执行命令
```
alter user scott account unlock;
```

ORA-28001 用户账号过期

在从计算机开始选项中打开SQL Plus，使用修复账号
用户名：system
密码为安装oracle时的登录密码
执行以下命令
```
alter user scott identified by tiger;
alter user scott account unlock;
```
重新连接问题解决

#### 3.5 指定数据库中的用户名过期

```
SQL> connect system/123456@localcas
connect 统一数据库登录名称/统一数据库登录密码@指定的数据库sid
已连接。
SQL> alter user SCOTT account unlock;
用户已更改。
SQL> COMMIT;
提交完成。
SQL>
```

### 4 oracle分页
```sql
select
	<include refid="MailColumns" />
	from
	(
	select sm.*,rownum rn from (
	select
	*
	from sys_mail where 1 = 1
	<if test="userId != null and userId != ''">
		and user_id = #{userId}
	</if>
	<if test="mailType != null and mailType != ''">
		and mail_type = #{mailType}
	</if>
	order by create_time desc) sm
	) a where 1 =1
	<if test="rowStart != null and rowStart != 0">
		and a.rn &gt;= #{rowStart}
	</if>
	<if test="rowEnd != null and rowEnd != 0">
		and a.rn &lt; #{rowEnd}
	</if>
```

### 5 分组排序取各组第一个

```sql
SELECT
	T.ID
FROM
	(
		SELECT
			am.ID,
			ROW_NUMBER () OVER (
				PARTITION BY am.APPID -- 分组字段
				ORDER BY
					am.NEWTIME DESC -- 排序字段
			) AS code_id
		FROM
			CAS_APP_MANAGER am
		WHERE
			1 = 1
	) T
WHERE
	T.code_id = 1
```

### 6 批量插入

```sql
insert into sys_user_mail
(id, user_id,
mail_id,recipient_org,recipient_dept,user_name,mail_status,user_account,create_time,update_time,
re_status)
<foreach collection="userMailBeanList" item="item" separator="union all">
	(SELECT
	#{item.id},#{item.userId},#{item.mailId},#{item.recipientOrg},#{item.recipientDept},#{item.userName},#{item.mailStatus},#{item.email},#{item.createTime},#{item.updateTime},#{item.reStatus}
	FROM DUAL)
</foreach>
```

### 7 创建数据库
参考：http://blog.csdn.net/wshysh/article/details/7649664

创建数据库过程中记住SID值，客户端连接：
数据库类型：Basic
主机名或IP地址：localhost
端口号:1521
服务名或ISD:选择SID,值为创建数据库时SID值
用户名：SCOTT
密码：TIGER

配置文件：
```
jdbc.type=oracle
jdbc.driver=oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:@localhost:1521:sid值
jdbc.username=SCOTT
jdbc.password=TIGER
```

### 8 修改oracle账户密码永不过期

========================
管理员账户打开cmd
使用system账号登录

#### 8.1 使用system账号登录
```
C:\Users\Administrator>sqlplus
SQL*Plus: Release 11.2.0.1.0 Production on 星期三 11月 15 17:22:21 2017

Copyright (c) 1982, 2010, Oracle.  All rights reserved.

请输入用户名:  system
输入口令:

连接到:
Oracle Database 11g Enterprise Edition Release 11.2.0.1.0 - 64bit Production
With the Partitioning, OLAP, Data Mining and Real Application Testing options

SQL>
```
#### 8.2 查看账户的proifle是哪个，一般是default
```
SQL> SELECT username,PROFILE FROM dba_users;

USERNAME                       PROFILE
------------------------------ ------------------------------
DB_RCS                         DEFAULT
GLJTLCX                        DEFAULT
JIBJTSJ                        DEFAULT
TRAFFIC                        DEFAULT
SCOTT                          DEFAULT
ORACLE_OCM                     DEFAULT
XS$NULL                        DEFAULT
MDDATA                         DEFAULT
DIP                            DEFAULT
APEX_PUBLIC_USER               DEFAULT
SPATIAL_CSW_ADMIN_USR          DEFAULT

USERNAME                       PROFILE
------------------------------ ------------------------------
SPATIAL_WFS_ADMIN_USR          DEFAULT
DBSNMP                         MONITORING_PROFILE
SYSMAN                         DEFAULT
FLOWS_FILES                    DEFAULT
MDSYS                          DEFAULT
ORDSYS                         DEFAULT
EXFSYS                         DEFAULT
WMSYS                          DEFAULT
APPQOSSYS                      DEFAULT
APEX_030200                    DEFAULT
OWBSYS_AUDIT                   DEFAULT

USERNAME                       PROFILE
------------------------------ ------------------------------
ORDDATA                        DEFAULT
CTXSYS                         DEFAULT
ANONYMOUS                      DEFAULT
XDB                            DEFAULT
ORDPLUGINS                     DEFAULT
OWBSYS                         DEFAULT
SI_INFORMTN_SCHEMA             DEFAULT
OLAPSYS                        DEFAULT
MGMT_VIEW                      DEFAULT
SYS                            DEFAULT
SYSTEM                         DEFAULT

USERNAME                       PROFILE
------------------------------ ------------------------------
OUTLN                          DEFAULT

已选择34行。

SQL>
```

#### 8.3 看账户的过期时间
```
SQL> SELECT * FROM dba_profiles WHERE profile='DEFAULT' AND resource_name='PASSWORD_LIFE_TIME';

PROFILE                        RESOURCE_NAME                    RESOURCE
------------------------------ -------------------------------- --------
LIMIT
----------------------------------------
DEFAULT                        PASSWORD_LIFE_TIME               PASSWORD
180
SQL> 
```
#### 8.4 修改用户密码的过期时间
```
SQL> ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;

配置文件已更改

SQL>
```

#### 8.5 查看修改过后的用户密码过期时间为UNLIMITED(无限制)

```
SQL> SELECT * FROM dba_profiles WHERE profile='DEFAULT' AND resource_name='PASSWORD_LIFE_TIME';

PROFILE                        RESOURCE_NAME                    RESOURCE
------------------------------ -------------------------------- --------
LIMIT
----------------------------------------
DEFAULT                        PASSWORD_LIFE_TIME               PASSWORD
UNLIMITED
SQL>
```
========================
### 9 oracle备份数据库和还原数据库
导出：
exp traffic/123456@traffic file=E:\daochu.dmp
导入(还原DMP备份文件)：
命令：imp system/system@orcl full=y BUFFER=81920 file="C:\Users\Administrator\Desktop\shengjushuju\B_LC_JIBJTSJ201710\B_LC_JIBJTSJ201710.dmp" ignore=y
在执行还原之前需要创建用户和空间并分配空间和权限给这个用户。

### 9.1 创建用户分配表空间

```
创建用户
create user DB_RCS identified by njdongba;
创建自动扩充空间
create tablespace DB_RCS datafile 'E:\database\base\product\11.2.0\db_rcs_data.dbf' size 200M autoextend on next 10M maxsize unlimited EXTENT MANAGEMENT local  autoallocate segment space management auto;
为用户分配表空间
alter user DB_RCS default tablespace DB_RCS;
为用户分配权限
grant create session,create table,create view,create sequence,unlimited tablespace to DB_RCS;
```

### 10 oracle截取，分组再排序
#### 10.1 oracle截取，分组再排序

```
SELECT
	T8.*
FROM
	(
		SELECT
			ROWNUM ID,
			T7.*
		FROM
			(
				SELECT
					CASE
				WHEN INSTR (T6.wholeNum, '-') > 0 -- INSTR('','') 判断后面的字符或字符串在前面字符串中的位置，返回int型角标+1，如 select INSTR ('123-15', '15') from dual; 返回值为5
										
					THEN
					TO_NUMBER (  -- TO_NUMBER() String型字符'123'串转为int型123
						SUBSTR ( -- SUBSTR('',1,2) 在字符串中截取角标为1-2的字符串，并返回字符串
							T6.wholeNum,
							0,
							INSTR (T6.wholeNum, '-') - 1
						)
					)
				ELSE
					TO_NUMBER (T6.wholeNum)
				END || '-' || -- 符号 || 为字符串拼接符
					ROW_NUMBER () OVER (
					PARTITION BY T6.city -- 分组字段
					ORDER BY
						T6.TEMP_ID -- 排序字段
				) CURRENTNUM, -- CURRENTNUM为别名
				T6.*
			FROM
				(
					SELECT
						ROWNUM TEMP_ID,
						T5.*
					FROM
						(
							SELECT
								STA1.F_WHOLE_NUM wholeNum,
								STA1.F_STATIONID stationId,
								STA1.F_STATIONNAME stationName,
								STA1.F_DEVICEID deviceId,
								STA1.F_CITY city,
								STA1.F_LONGITUDE longitude,
								STA1.F_LATITUDE latitude,
								STA1.F_ROUTEID routeId,
								STA1.F_MARK mark,
								STA1.F_PROMARK proMark,
								CHE.F_MARKERROR markError,
								DAT.F_PARTINGERROR partingError,
								DEV.F_ISOUT isOut,
								STA1.F_ROADLEVEL roadLevel,
								CHE.F_OPERATIONSTATUS operationStatus,
								ROA.F_TEXTURE texture,
								DEV.F_FACTORY factory,
								DEV.F_DEVICETYPE deviceType,
								DEV.F_SITE site,
								DEV.F_POWERTYPE powerType,
								DEV.F_TRANSFERTYPE transferType,
								DEV.F_ANGLESTATUS anglestatus,
								DEV.F_STABILITY stability,
								DEV.F_BARTYPE barType,
								DEV.F_VERTICALITY verticality,
								DEV.F_CORROSION corrosion,
								DEV.F_SOLARTYPE solarType,
								DEV.F_FACADE facade,
								DEVS.F_LANDFORM landForm,
								ROA.F_MIXCONDITION mixCondition,
								ROA.F_LIMIVELOCITY limiVelocity,
								DEVS.F_ISSCENIC isScenic,
								DEVS.F_ISMEDIUM isMedium,
								DEVS.F_ISSECURITYDEVICE isSecurityDevice,
								DEVS.F_ISSHARPTURN isSharpTurn,
								DEVS.F_ISCROSS isCross,
								DEVS.F_CROSSDISTANCE crossDistance,
								DEVS.F_CROSSCONTROL crossControl,
								DEVS.F_ISTOWN isTown,
								ROA.F_ROADCONDITION roadCondition,
								CHE.F_CHECKLANECOUNT checkLaneCount,
								t3.F_ROADID "roadId",
								DAT.F_TIMEEND timeEnd,
								DAT.F_TIMESTART timeStart,
								CHE.F_WEATHER weather,
								BUI.F_EXPLAIN EXPLAIN,
								BUI.F_MEMO memo,
								STA1.F_DEPT dept,
								STA1.F_MILEAGE mileage,
								ROA.F_ROADWIDTH roadWidth,
								STA1.F_LEVEL "level",
								ROA.F_SPEED speed,
								ROA.F_TRAVOLUME travolume,
								DEV.F_UPRIGHT upright,
								CHE.F_ISIMPORTANT isImportant,
								DAT.F_DATACOLLECTIONUP dataCollectionUp,
								DAT.F_DATACOLLECTIONDOWN dataCollectionDown,
								DAT.F_AVGSPEEDERRORUP avgSpeedErrorUp,
								DAT.F_AVGSPEEDERRORDOWN avgSpeedErrorDown,
								DAT.F_CARFLOWRATEUP carFlowRateUp,
								DAT.F_CARFLOWRATEDOWN carFlowRateDown,
								T4.F_ZREO zeroRate,
								DEV.F_ANTISEPTIC antiseptic,
								BUI.F_CONSTRUCTIONNAME constructionName,
								BUI.F_DONEDATE doneDate,
								BUI.F_CHANGECOUNT changeCount,
								BUI.F_CHANGEDEPT changeDept,
								BUI.F_CHANGEDONEDATE changeDoneDate,
								BUI.F_MAINTAINDEPT maintainDept,
								BUI.F_MAINTAINCONTACTS maintainContacts,
								BUI.F_MAINTAINMOBILE maintainMobile
							FROM
								JD_STATIONINFO STA1
							LEFT JOIN JD_ARTIFICIAL_INFO ART ON ART.F_DEVICEID = STA1.F_DEVICEID
							AND ART.F_FLOWSTART >= TO_DATE (
								'2017-09-01 00:00:00',
								'yyyy-MM-dd hh24:mi:ss'
							)
							AND ART.F_FLOWEND < TO_DATE (
								'2017-10-01 00:00:00',
								'yyyy-MM-dd hh24:mi:ss'
							)
							LEFT JOIN JD_DATA_RESULT DAT ON DAT.F_TIMESTART = ART.F_FLOWSTART
							AND DAT.F_TIMEEND = ART.F_FLOWEND
							AND DAT.F_DEVICEID = ART.F_DEVICEID
							LEFT JOIN (
								SELECT
									wm_concat (F_ROADID) F_ROADID
										-- wm_concat() 列转为行的方法，如表:有两列数据
												-- id  name  age 
												-- 1   张三  23
												-- 2   李四  24
										-- wm_concat(name) 则返回 '张三,李四' 一行字符串
								FROM
									(
										SELECT
											T .F_ROADID
										FROM
											(
												SELECT
													JAS.F_ROADID,
													ROW_NUMBER () OVER (
														PARTITION BY JAS.F_ROADID
														ORDER BY
															JAS.F_ROADID DESC
													) AS code_id
												FROM
													JD_ARTIFICIAL_SPEED JAS
												WHERE
													1 = 1
												AND JAS.F_SPEEDDATESTART >= TO_DATE (
													'2017-09-01 00:00:00',
													'yyyy-MM-dd hh24:mi:ss'
												)
												AND JAS.F_SPEEDDATEEND < TO_DATE (
													'2017-10-01 00:00:00',
													'yyyy-MM-dd hh24:mi:ss'
												)
											) T
										WHERE
											T .code_id = 1
									) t2
							) t3 ON 1 = 1
							LEFT JOIN JD_BUILD BUI ON BUI.F_DEVICEID = STA1.F_DEVICEID
							LEFT JOIN JD_CHECK CHE ON CHE.F_DEVICEID = STA1.F_DEVICEID
							LEFT JOIN JD_DEVICE DEV ON DEV.F_DEVICEID = STA1.F_DEVICEID
							LEFT JOIN JD_DEVICE_SITE DEVS ON DEVS.F_DEVICEID = STA1.F_DEVICEID
							LEFT JOIN JD_ROAD_INFO ROA ON ROA.F_DEVICEID = STA1.F_DEVICEID
							LEFT JOIN (
								SELECT
									*
								FROM
									(
										SELECT
											JZR.F_ZREO,
											JZR.F_DEVICE_ID
										FROM
											JD_ZERO_RATE JZR
										WHERE
											JZR.F_YEAR = 2017
									)
							) T4 ON T4.F_DEVICE_ID = ART.F_DEVICEID
							WHERE
								1 = 1
							ORDER BY -- 多条件排序
								CASE
								WHEN INSTR (STA1.F_WHOLE_NUM, '-') > 0 THEN
									TO_NUMBER (
										SUBSTR (
											STA1.F_WHOLE_NUM,
											0,
											INSTR (STA1.F_WHOLE_NUM, '-') - 1
										)
									)
								ELSE
									TO_NUMBER (STA1.F_WHOLE_NUM)
								END, -- 条件1 将STA1.F_WHOLE_NUM字段分割并取取出想要排序的字符，再转化成int型
								CASE
								WHEN INSTR (STA1.F_WHOLE_NUM, '-') > 0 THEN
									TO_NUMBER (
										SUBSTR (
											STA1.F_WHOLE_NUM,
											INSTR (STA1.F_WHOLE_NUM, '-') + 1
										)
									)
								ELSE
									TO_NUMBER (STA1.F_WHOLE_NUM)
								END -- 条件2
						) T5
				) T6
			) T7
	) T8
WHERE
	1 = 1
```
#### 10.2 字符分割成多列语句

```
with t as
 (select '0141150312061303,0141150312061306,0141150312061302,0141150315123001,0141150312061305,0141150314122202,0141150313101101,0141150312061311,0141150312121802,0141150312061310,0141150314122201,0141150312061312,0141150312061309,0141150313122301,0141150312061308,0141150312061307,0141150313122302,0141150316110701,0141150312061304,0141150316110702,0151145309120004,0151145311120017,0151145311120018,0151145311120019,0151145313090090,0151145309120003,0151145307070006,0151145311120016,0151145307070003,0151145311120012,0151145311120013,0151145307070005,0151145307070007,0141150316111801,0151145311120010,0151145311120050,0151145315120003,0151145309120002,0151145215120002,0151145311120015,0151145307070001,0151145307070002,0151145311120014,0141150309030036,0141150309030035,0171170315090810,0141150309030032,0141150309030037,0141150309030038,010106140807,0141150309030039,0141150310040049,0141150309030040,0141150309030041,0141150309030042,0141150312010009,0AB0004784,0141150312010012,0141150312010007,0141150310040058,0141150312010006,0141150312010011,0101130313010202,0101130313010201,0141150313012503,0141150312010010,0141150312010002,0141150312010005,0101130313010204,0141150313012501,0141150313012502,0171170315090710,0141150312010001,0171170315090711,0141150312010003,0141150309030033,0141150316101203,0141150312102908,0141150316101204,0141150311090002,0141150316101202,0141150309100051,0141150309100050,0141150309100049,0141150309100048,0140050309100047,0141150309100046,0141150311110093,0141150316101205,0141150314101403,0141150311110091,0141150309100045,0141150309100044,0141150312102902,0141150311090006,0141150312102903,0141150314101401,0141150309100043,0141150314101402,0141150611090004,0141150312102906,0141150311050187,0141150311110092,0141150311050189,0141150312102904,0141150316101207,0141150312102901,0141150312102905,0141150311090001,0141150311020051,0141150310040040,0141150316101206,0141150311050188' a from dual)
select substr(a,
              decode(level - 1, 0, 0, instr(a, ',', 1, level - 1)) + 1,
              (decode(level,
                      regexp_count(a, ',') + 1,
                      length(a) + 1,
                      instr(a, ',', 1, level))) -
              (decode(level - 1, 0, 0, instr(a, ',', 1, level - 1)) + 1)) F_DEVICEID
  from t
connect by level <= regexp_count(a, ',') + 1;
```

### 11 group by 注意事项

GROUP BY注意点

```
表名 t
id name age
1  a     4
2  b     5
3  c     6
like MIN, MAX or SUM).
select name from t group by id; ×
select name from t group by id,name; √
在使用group时，select和from之间不能直接添加某个字段，需要使用函数，如：MIN, MAX, SUM。
```

======
### 12 left join on后面的条件与where后的条件的区别
```
select * from td
  left join (
select case_id as sup_case_id , count(*) supervise_number from 
 td_kcdc_case_sup_info 
 group by  case_id
  ) sup
 on  
   sup.sup_case_id = td.case_id
 where 1=1   /*不能去掉， 否则认为and 后的条件为 联合查询时的条件， 不能起过滤作用，由于left join因此td表中记录将全部查出来*/

 and td.con = 'xxxx'
```
总结：
1. 对于left join，不管on后面跟什么条件，左表的数据全部查出来，因此要想过滤需把条件放到where后面。
2. 对于inner join，满足on后面的条件表的数据才能查出，可以起到过滤作用。也可以把条件放到where后面。
	
### 13 查询某字段不等于字符串的数据的sql

```
-- 判断name不等于'xn'的数据，会把name为null的和name不等于'xn'的数据查出来
select * from test where instr(concat(name,'xx'),'xn') = 0
```

### 14 树形结构查询
```
SELECT
	cor. ID,cor.parent_id,cor. NAME
FROM
	cas_organization cor
WHERE
	1 = 1
AND INSTR (CONCAT (cor.remarks, 'xx'),'Y') = 0 -- remarks字段不等于'Y'的数据
AND INSTR (CONCAT (cor.del_flag, 'xx'),'1') = 0
START WITH cor.parent_id = '164294abc7ca649b9eb200000c9f10bf' -- 这里是父id，如果是id(子id：cor.id = '164294abc7ca649b9eb200000c9f10bf')则查询结果包含该数据
CONNECT BY PRIOR cor. ID = cor.parent_id
```

### 15 powerdesigner连接数据库
```
参考：
https://www.cnblogs.com/clivehua/articles/4593211.html
```

### 16 用户权限
```
-- 为用户分配操作所有表的权限和创建运行存储过程的权限
grant create any table to username;
grant create any procedure to username; 
grant execute any procedure to username;
```

### 17 PowerDesigner中文注释
PowerDesigner
从comment替换name
Tools->Execute Commands->Edit/Run Script
```
Option   Explicit   
ValidationMode   =   True   
InteractiveMode   =   im_Batch  
  
Dim   mdl   '   the   current   model  
  
'   get   the   current   active   model   
Set   mdl   =   ActiveModel   
If   (mdl   Is   Nothing)   Then   
      MsgBox   "There   is   no   current   Model "   
ElseIf   Not   mdl.IsKindOf(PdPDM.cls_Model)   Then   
      MsgBox   "The   current   model   is   not   an   Physical   Data   model. "   
Else   
      ProcessFolder   mdl   
End   If  
  
Private   sub   ProcessFolder(folder)   
On Error Resume Next  
      Dim   Tab   'running     table   
      for   each   Tab   in   folder.tables   
            if   not   tab.isShortcut   then   
                  tab.name   =   tab.comment  
                  Dim   col   '   running   column   
                  for   each   col   in   tab.columns   
                  if col.comment="" then  
                  else  
                        col.name=   col.comment   
                  end if  
                  next   
            end   if   
      next  
  
      Dim   view   'running   view   
      for   each   view   in   folder.Views   
            if   not   view.isShortcut   then   
                  view.name   =   view.comment   
            end   if   
      next  
  
      '   go   into   the   sub-packages   
      Dim   f   '   running   folder   
      For   Each   f   In   folder.Packages   
            if   not   f.IsShortcut   then   
                  ProcessFolder   f   
            end   if   
      Next   
end   sub  
```

### 控制用户操作数据库的权限
```oracle
创建表，创建序列，创建视图，创建过程的权限 TO 用户名
grant create table,create sequence,create view,create procedure to jeesite
```
### oracle删除NAME字段相同的数据，只保留一条数据
```SQL 
DELETE
FROM
	REMOVERE
WHERE
	(NAME) IN (
		SELECT
			NAME
		FROM
			REMOVERE
		GROUP BY
			NAME
		HAVING
			COUNT (NAME) > 1
	)
AND ROWID NOT IN (
	SELECT
		MIN (ROWID)
	FROM
		REMOVERE
	GROUP BY
		NAME
	HAVING
		COUNT (*) > 1
)
```

### 根据某一字段类型统计单表数量
```
select count(1) c,
COUNT(CASE WHEN bxx.F_C_DANGQHJ IN ('0','1','2') THEN 'un_deal' END) c1,
COUNT(CASE WHEN bxx.F_C_DANGQHJ = '3' THEN 'deal' END) c2
from B_XTXC_XUNCSJ bxx where bxx.F_VC_XUNCRZID = '6aac8ef7cea24e1dac5410734343cb5432'
```