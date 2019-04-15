---
title: MySQL存储过程
date: 2017-01-20 19:13:26
update: 2017-01-22 19:13:26
categories: MySQL
tags: [mysql, 存储过程, java]
---

### 1 java调用存储过程
<!-- more -->
```java
//	String url = "jdbc:mysql://192.168.1.1:3306/xmbg?characterEncoding=utf-8";
//	String user = "root";
//	String password = "yangqiang88";
//	Connection conn = DriverManager.getConnection(url, user, password);
	Connection conn = dataSource.getConnection();
	conn.setAutoCommit(false);
	CallableStatement cstmt = conn.prepareCall("{ call test2(?) }");
	cstmt.registerOutParameter(1, Types.VARCHAR);
	cstmt.execute();
	conn.commit();
	String testPrint = cstmt.getString(1);
	System.out.println("返回值：：：：：： " + testPrint);
```
参考：
http://blog.csdn.net/evankaka/article/details/47276339
### 准备

#### 创建表：
```mysql
DROP TABLE IF EXISTS `t_user`;
CREATE TABLE `t_user` (
  `USER_ID` int(11) NOT NULL AUTO_INCREMENT,
  `USER_NAME` char(30) NOT NULL,
  `USER_PASSWORD` char(10) NOT NULL,
  `USER_EMAIL` char(30) NOT NULL,
  PRIMARY KEY (`USER_ID`),
  KEY `IDX_NAME` (`USER_NAME`)
) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of t_user
-- ----------------------------
INSERT INTO `t_user` VALUES ('1', '王小二', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('2', '葫芦娃', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('3', '红孩儿', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('4', '哪儿托', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('5', '丹童', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('6', '清风', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('7', '明月', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('8', '药童', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('9', '郓哥', '123', '123@qq.com');
INSERT INTO `t_user` VALUES ('10', '高峰', '123', '123@qq.com');
```
#### 带IN的存储过程  

```mysql
CREATE  PROCEDURE  SP_SEARCH(IN p_name CHAR(20))  
BEGIN  
IF p_name is null or p_name='' THEN  
SELECT * FROM t_user;  
ELSE  
SELECT * FROM t_user WHERE USER_NAME LIKE p_name;  
END IF;  
END 
```

调用存储过程
```mysql
CALL SP_SEARCH('王小二');
```
### 带OUT的存储过程
根据传入用户名称进行查询，统计次数返回给p_int变量
```mysql
CREATE  PROCEDURE  SP_SEARCH2(IN p_name CHAR(20),OUT p_int INT)  
BEGIN  
IF p_name is null or p_name='' THEN  
SELECT * FROM t_user;  
ELSE  
SELECT * FROM t_user WHERE USER_NAME LIKE p_name;  
END IF;  
SELECT FOUND_ROWS() INTO p_int;  
END  
```
分别执行调用：
查询高字开头的用户信息，定义参数@p_num，用于接收返回值，然后显示返回值
```mysql
CALL SP_SEARCH2('高%',@p_num);
SELECT @p_num;
```
#### 带INOUT的存储过程

```
CREATE PROCEDURE sp_inout(INOUT p_num INT)  
BEGIN  
SET p_num=p_num*10;  
END

-- 调用
SET @p_num=2;  
call sp_inout(@p_num);  
SELECT @p_num;
```
### 存储过程体

1.局部变量
DECLARE var_name,...,... type [DEFAULT value]  
Var_name:指定局部变量的名称
	Declare var1, var2, var3 INT;
	Declare cid int(10) [DEFAULT 0]; 
Type:用于声明局部变量的数据类型  
default子句:用于为局部变量指定一个默认值。若没有指定，默认为null.

使用说明：
局部变量只能在存储过程体的begin…end语句块中声明。
局部变量必须在存储过程体的开头处声明。
局部变量的作用范围仅限于声明它的begin..end语句块，其他语句块中的语句不可以使用它。
局部变量不同于用户变量，两者区别：局部变量声明时，在其前面没有使用@符号，并且它只能在begin..end语句块中使用；而用户变量在声明时，会在其名称前面使用@符号，同时已声明的用户变量存在于整个会话之中。

2.set语句
使用set语句为局部变量赋值
```mysql
Set var_name=expr  
Set cid=910; 
```

3.select … into 语句
Select col_name[,…] into var_name[,…] table_expr
Col_name:用于指定列名  
Var_name:用于指定要赋值的变量名  
Table_expr:表示select语句中的from字句及后面的语法部分
```mysql
SELECT sum(f_sms_count) into today_count from tp_user_send_msg_log s where s.f_user_id = uid
```
说明:存储过程体中的select…into语句返回的结果集只能有一行数据。

4.定义处理程序
是事先定义程序执行过程中可能遇到的问题。并且可以在处理程序中定义解决这些问题的办法。这种方式可以提前预测可能出现的问题，并提出解决方法。
DECLARE handler_type HANDLER FOR condition_value[,…] sp_statement  
handler_type:CONTINUE | EXIT | UNDO  
Condition_value:Sqlwarning | not found | sqlexception 

5.流程控制语句
#### 条件判断语句
	If语句:
	If search_condition then statement_list  
	[elseif search_condition then statement_list]…  
	[else statement_list]  
	End if 
	
Search_condition参数:条件判断语句
Statement_list参数:不同条件的执行语句
多重IF的存储过程实例
数据准备
学生表：
```mysql
DROP TABLE IF EXISTS `t_student`;
CREATE TABLE `t_student` (
  `STU_ID` int(11) NOT NULL AUTO_INCREMENT,
  `STU_NAME` char(10) NOT NULL,
  `STU_CLASS` int(11) NOT NULL,
  `STU_SEX` char(2) NOT NULL,
  `STU_AGE` int(11) NOT NULL,
  PRIMARY KEY (`STU_ID`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of t_student
-- ----------------------------
INSERT INTO `t_student` VALUES ('1', 'name1', '1', '1', '1');
INSERT INTO `t_student` VALUES ('2', 'name2', '2', '2', '2');
INSERT INTO `t_student` VALUES ('3', 'name3', '3', '3', '3');
INSERT INTO `t_student` VALUES ('4', 'name4', '4', '4', '4');
INSERT INTO `t_student` VALUES ('5', 'name5', '5', '5', '5');
INSERT INTO `t_student` VALUES ('6', 'name6', '6', '6', '6');
INSERT INTO `t_student` VALUES ('7', 'name7', '7', '7', '7');
INSERT INTO `t_student` VALUES ('8', 'name8', '8', '8', '8');
INSERT INTO `t_student` VALUES ('9', 'name9', '9', '9', '9');
INSERT INTO `t_student` VALUES ('10', 'name10', '10', '10', '10');
```
成绩表（STU_ID是学生表是外键关系）：
```mysql
DROP TABLE IF EXISTS `t_grade`;
CREATE TABLE `t_grade` (
  `STU_ID` int(11) NOT NULL,
  `STU_SCORE` int(11) NOT NULL,
  KEY `STU_ID` (`STU_ID`),
  CONSTRAINT `t_grade_ibfk_1` FOREIGN KEY (`STU_ID`) REFERENCES `t_student` (`STU_ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of t_grade
-- ----------------------------
INSERT INTO `t_grade` VALUES ('1', '70');
INSERT INTO `t_grade` VALUES ('2', '80');
INSERT INTO `t_grade` VALUES ('3', '70');
INSERT INTO `t_grade` VALUES ('4', '80');
INSERT INTO `t_grade` VALUES ('5', '70');
INSERT INTO `t_grade` VALUES ('6', '78');
INSERT INTO `t_grade` VALUES ('7', '98');
INSERT INTO `t_grade` VALUES ('8', '78');
INSERT INTO `t_grade` VALUES ('9', '67');
INSERT INTO `t_grade` VALUES ('10', '75');
```

然后写一个存储过程：返回各个分数等级的人
```mysql
CREATE  PROCEDURE SP_SCHOLARSHIP_LEVEL(IN p_level char(1))  
BEGIN  
IF p_level ='A'  THEN  
SELECT * FROM t_grade WHERE STU_SCORE >=90;  
ELSEIF  p_level ='B'  THEN  
SELECT * FROM t_grade WHERE STU_SCORE <90 AND  STU_SCORE>=80;  
ELSEIF  p_level ='C'  THEN  
SELECT * FROM t_grade WHERE STU_SCORE <80 AND  STU_SCORE>=70;  
ELSEIF  p_level ='D'  THEN  
SELECT * FROM t_grade WHERE STU_SCORE <60;  
ELSE  
SELECT * FROM t_grade;  
END IF;  
END 
```
调用：
```mysql
CALL SP_SCHOLARSHIP_LEVEL('A');
```

Case 语句
表达形式1
Case case_value 
When when_value then statement_list  
[When when_value then statement_list]…  
[else statement_list]  
End case
表达形式2
Case  
When search_condition then statement_list  
End case 

使用范例：
```mysql
CREATE  PROCEDURE SP_SCHOLARSHIP_LEVEL3(IN p_level char(1))  
BEGIN  
DECLARE p_num int DEFAULT  0;  
CASE p_level  
WHEN 'A'  THEN  
SET p_num=90;  
WHEN 'B'  THEN  
SET p_num=80;  
WHEN 'C'  THEN  
SET p_num=70;  
WHEN 'D'  THEN  
SET p_num=60;  
ELSE  
SET p_num=0;  
END CASE;  
SELECT * FROM t_grade g, t_student s WHERE g.STU_ID=s.STU_ID AND g.STU_SCORE >= p_num ;  
END
```

调用：
```mysql
CALL SP_SCHOLARSHIP_LEVEL3('d'); 
```
#### 循环语句
##### While语句、repeat语句和loop语句。
While语句：
[begin_label:]  
while search_condition do  
Statement_list  
End while  
[end_label]

判断条件search_condition是否为真,若为真,则执行statement_list中的语句，然后再进行判断，如若仍然为真则继续循环，直至条件判断不为真时循环结束。

```mysql
CREATE PROCEDURE sp_cal(IN p_num INT,OUT p_result INT)  
BEGIN  
 SET p_result=1;  
 WHILE p_num > 1 DO  
  SET p_result = p_num * p_result;  
  SET p_num = p_num-1;  
 END WHILE;  
END  
//调用并输出结果  
CALL sp_cal(5,@result);  
SELECT @result;
```
##### Repeat语句语法格式
[begin_label:]  
repeat  
Statement_list  
Until search_condition  
End repeat  
[end_label]

Repeat语句首先执行statement_list中的语句，然后判断条件search_condition是否为真，倘若为真，则结束循环，若不为真，继续循环。
Repeat先执行后判断，while先判断后执行。
使用范例：
```mysql
CREATE PROCEDURE sp_cal2(IN p_num INT,OUT p_result INT)  
BEGIN  
  SET p_result=1;  
  REPEAT  
    SET p_result = p_num * p_result;  
    SET p_num = p_num-1;  
    UNTIL p_num<=1  
  END REPEAT;  
END  
//调用并输出结果  
CALL sp_cal2(5,@result);  
SELECT @result; 
```
#### 调用存储过程
Call sp_name([parameter[,…]]);  
Sp_name被调用存储过程的名称  
Parameter:指定调用存储过程所要使用的参数。
#### 修改存储过程
Alter procedure proc_name[characteristic…] 
只能修改存储过程的特征，如果要修改存储过程的内容，可以先删除该存储过程，然后再重新创建
### 删除存储过程
Drop procedure if exists sp_name; 

### 函数
MySQL中，创建存储函数的基本形式如下：

CREATE FUNCTION sp_name([func_parameter[,...]])  
RETURNS type  
[characteristic ...] routine_body  
Return  

子句用于声明存储函数返回值的数据类型。存储过程是用户定义的一系列sql语句的集合，涉及特定表或其它对象的任务，用户可以调用存储过程，而函数通常是数据库已定义的方法，它接收参数并返回某种类型的值并且不涉及特定用户表。
调用存储函数
Select sp_name([func_parameter…])  
Select fn_search(2); 
删除存储函数drop
修改存储函数alter 修改存储函数的某些相关特征。
#### 函数使用例子
比较大小 ，返回大的数
```mysql
CREATE FUNCTION sp_cal_max(p_num1 INT,p_num2 INT)  
RETURNS INT  
BEGIN  
IF p_num1 >= p_num2 THEN  
RETURN p_num1;  
ELSE  
RETURN p_num2;  
END IF;  
END 
```
报错：you *might* want to use the less safe log_bin_trust_function_creators variable
执行：SET GLOBAL log_bin_trust_function_creators = 1;解决问题

调用：
```mysql
SET @p_num1=2;  
SET @p_num2=34;  
SELECT sp_cal_max(@p_num1,@p_num2); 
```

#### 存储过程和函数区别
<p style="fond:red">
1）一般来说，存储过程实现的功能要复杂一点，而函数的实现的功能针对性比较强。存储过程，功能强大，可以执行包括修改表等一系列数据库操作；用户定义函数不能用于执行一组修改全局数据库状态的操作。
2）对于存储过程来说可以返回参数，如记录集，而函数只能返回值或者表对象。函数只能返回一个变量；而存储过程可以返回多个。存储过程的参数可以有IN,OUT,INOUT三种类型，而函数只能有IN类~~存储过程声明时不需要返回类型，而函数声明时必须描述返回类型，且函数体中必须包含一个有效的RETURN语句。
3）存储过程，可以使用非确定函数，不允许在用户定义函数主体中内置非确定函数。
4）存储过程一般是作为一个独立的部分来执行（ EXECUTE 语句执行），而函数可以作为查询语句的一个部分来调用（SELECT调用），由于函数可以返回一个表对象，因此它可以在查询语句中位于FROM关键字的后面。 SQL语句中不可用存储过程，而可以使用函数。
5）存储过程查寻结果和返回值作用域在一个会话中,建立新的会话不能使用另一个会话中存储过程中的数据。
</p>

### 光标（游标）

查询语句可能查询出多条记录，在存储过程和函数中使用光标标来逐条读取查询结果集中的记录。光标的使用包括声明光标、打开光标、使用光标和关闭光标。光标必须声明光标、打开光标、使用光标和关闭光标。光标必须声明在处理程序之前，并且声明在变量和条件之后。

#### 声明光标
Declare cursor_name cursor forselect_statement;  
Cursor_name:光标名称  
Select_statement:select语句的内容  
Declare cur_employee cursor forselect name,age from employee; 
#### 打开光标
Open cursor_name  
Open cur_employee; 
#### 使用光标
Mysql中使用fetch关键字来使用光标，语法形式
Fetch cur_name intovar_name[,var_name…];  
Cur_name表示光标的名称
Var_name表示将光标中的select语句查询出来的信息存入该参数。Var_name必须在声明光标前就定义好。  
Fetch cur_employee intoemp_name,emp_age; 
#### 关闭光标
[sql] view plain copy
Close cursor_name;  
Close cur_employee; 
每个光标不再需要时都应该被关闭，使用close语句将会释放光标所使用的全部资源。在一个光标被关闭后，如果没有重新被打开，则不能被使用。对于声明过的光标，则不需要再次声明，可直接使用open语句打开。
#### 使用范例
（将表test_cur1数据复制到test_cur2）
创建表：
```mysql
CREATE TABLE `test_cur1` (    
  `id` int(11) NOT NULL auto_increment,    
  `type` char(11) default NULL,    
  `order1` char(11) default NULL,    
  PRIMARY KEY  (`id`)    
)   
INSERT INTO `test_cur1` VALUES (1, '145', 'd1');    
INSERT INTO `test_cur1` VALUES (2, '134', '1d');    
INSERT INTO `test_cur1` VALUES (3, '123', '1ad');    
INSERT  INTO `test_cur1` VALUES (4, '121', '1as');  
   
CREATE TABLE `test_cur2` (    
  `id` int(11) NOT NULL auto_increment,    
  `type` char(11) default NULL,    
  `order1` char(11) default NULL,    
  PRIMARY KEY  (`id`)    
)   
```
然后写光标了：

```mysql
create procedure get_cur ()  
BEGIN  
  DECLARE done INT DEFAULT 0;  
  DECLARE ID int(11);  
  DECLARE type char(11);  
  DECLARE order1 char(11);  
  DECLARE mycur CURSOR FOR SELECT * FROM test_cur1; -- 定义光标 
  DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done = 1;  -- 当游标取到最后一个数据的下一个不存在的数据时，done会被值为1，那么就要在repeat中进行done的判断，当done不为1的时候，才执行循环
  OPEN mycur; -- 打开光标
  REPEAT  -- 开始循环
    FETCH mycur INTO ID,type,order1; -- 去除光标内容到临时变量
    IF NOT done THEN  -- 当done不为0时为false not false为true进行循环
      INSERT INTO test_cur2 VALUES (ID,type,order1);
    END IF;    
  UNTIL done END REPEAT; -- until为false时继续进行下一循环，如果true时进行下一循环后结束
  CLOSE mycur;  
END 
```
调用存储过程
```mysql
call get_cur();
```
### 实用的存储过程
### 分割字符串
```mysql
drop PROCEDURE if exists procedure_split;
CREATE PROCEDURE `procedure_split`(
    inputstring varchar(1000),
    delim char(1)
)
begin
    declare strlen int DEFAULT length(inputstring);
    declare last_index int DEFAULT 0;
    declare cur_index int DEFAULT 1;
    declare cur_char VARCHAR(200);
    declare len int;
    drop temporary table if exists splittable;
    create TEMPORARY table splittable(
        value VARCHAR(20)
    ) ;
    WHILE(cur_index<=strlen) DO    
    begin
        if substring(inputstring from cur_index for 1)=delim or cur_index=strlen then
            set len=cur_index-last_index-1;
            if cur_index=strlen then
               set len=len+1;
            end if;
            insert into splittable(`value`)values(substring(inputstring from (last_index+1) for len));
            set last_index=cur_index;
        end if;
        set cur_index=cur_index+1;
    END;
    end while;
end ;
-- 调用：
call PROCEDURE_split('中国,开源,社区',',');
select * from splittable;

```
```mysql
drop PROCEDURE if exists result_mail_no;
CREATE PROCEDURE result_mail_no(IN mailno varchar(1000),IN fStationId CHAR(10))  
BEGIN
call PROCEDURE_split(mailno,',');
select pi.f_mail_no from tp_parcel_info pi where pi.f_parcel_status not in ('0', '4') AND pi.f_station_id = fStationId AND 
EXISTS (select 1 from splittable s WHERE s.value = pi.f_mail_no);
END;
-- 调用：
call result_mail_no('883960354731823677,3453','1618');
```



















