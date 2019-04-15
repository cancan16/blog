---
title: JDBC
date: 2017-02-06 09:18:52
update: 2017-02-06 09:18:52
categories: JDBC
tags: [jdbc, java]
---

### 1.链接数据库工具类

<!-- more -->

```java
package com.bmx.omp.business.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.HashMap;
import java.util.Map;

public class JdbcUtils {

    private JdbcUtils() {
    }

    private static String url;
    private static String user;
    private static String password;

    private static ThreadLocal<Connection> tl = new ThreadLocal<Connection>();
    // 将四要素抽取至配置文件
    static {
	try {
	    // 读取配置文件 获得四要素
	    String driverClass = "com.mysql.jdbc.Driver";
	    url = "jdbc:mysql://999.999.1.1:3306/test?characterEncoding=utf-8";
	    user = "root";
	    password = "123456";
	    // 注册驱动
	    Class.forName(driverClass);
	} catch (Exception e) {
	    throw new ExceptionInInitializerError(e);
	}
    }

    // 获得数据库连接
    public static Map<String, Object> getConnection() throws SQLException {
	ThreadLocal<Connection> tl = new ThreadLocal<Connection>();
	// 返回当前线程绑定的连接 原因是该连接上有事务
	Connection conn = tl.get();
	// 如果conn 为 null 说明线程上未绑定连接
	if (conn == null) {
	    // 获得与数据库的连接
	    conn = DriverManager.getConnection(url, user, password);
	    // 绑定到当前线程
	    tl.set(conn);
	}
	Map<String, Object> m = new HashMap<String, Object>();
	m.put("thread", tl);
	m.put("conn", conn);
	System.out.println("创建线程" + tl);
	System.out.println("线程绑定的链接" + conn);
	return m;
    }

    // 开启事务
    public static void startTransaction() {
	try {
	    // 获得当前线程上绑定的连接
	    Map<String, Object> m = getConnection();
	    Connection conn = (Connection) m.get("conn");
	    // 开启事务
	    conn.setAutoCommit(false);
	} catch (SQLException e) {
	}
    }

    // 提交事务
    public static void commit() {
	// 获得当前线程上绑定的连接
	Connection conn = tl.get();
	// 判断
	if (conn != null) {
	    try {
		// 如果线程上有连接 提交该连接事务
		conn.commit();
	    } catch (SQLException e) {
	    }
	}
    }

    // 回滚事务
    public static void rollback() {
	// 获得当前线程上绑定的连接
	Connection conn = tl.get();
	// 判断
	if (conn != null) {
	    try {
		// 如果线程上有连接 回滚事务
		conn.rollback();
	    } catch (SQLException e) {
	    }
	}
    }

    // 关闭连接 关闭当前线程上绑定的连接
    public static void close() {
	// 获得当前线程上绑定的连接
	Connection conn = tl.get();
	// 判断
	if (conn != null) {
	    try {
		// 如果线程上有连接 关闭连接
		conn.close();
	    } catch (SQLException e) {
	    }
	    // 移除当前线程绑定的 connnection 对象
	    tl.remove();
	}
    }

    public static void release(Connection conn, Statement stmt, ResultSet rs) {
	if (rs != null) {
	    try {
		rs.close();
	    } catch (SQLException e) {
		e.printStackTrace();
	    }
	    rs = null;
	}

	if (stmt != null) {
	    try {
		stmt.close();
	    } catch (SQLException e) {
		e.printStackTrace();
	    }
	    stmt = null;
	}

	if (conn != null) {
	    try {
		conn.close();
	    } catch (SQLException e) {
		e.printStackTrace();
	    }
	    conn = null;
	}
    }

}
```
2.使用多线程绑定链接
```java
 @Override
    public Map<String, Object> queryParcelThread(ParcelBean mi) {
	Map<String, Object> retMap = new HashMap<String, Object>();
	try {
	    Map<String, Object> m1 = JdbcUtils.getConnection();
	    Connection connection1 = (Connection) m1.get("conn");
	    Map<String, Object> m2 = JdbcUtils.getConnection();
	    Connection connection2 = (Connection) m2.get("conn");
	    Map<String, Object> m3 = JdbcUtils.getConnection();
	    Connection connection3 = (Connection) m3.get("conn");
	    Statement sta1 = connection1.createStatement();
	    Statement sta2 = connection2.createStatement();
	    Statement sta3 = connection3.createStatement();
	    // 查询语句
	    String sql1 = "select " + "f_parcel_id,f_recipient_name " + "from " + "tp_parcel_info "
		    + "where f_station_id = 293";
	    String sql2 = "select " + "f_parcel_id,f_recipient_name " + "from " + "tp_parcel_info "
		    + "where f_station_id = 366";
	    String sql3 = "select " + "f_parcel_id,f_recipient_name " + "from " + "tp_parcel_info "
		    + "where f_station_id = 1002";
	    Date dt = new Date();
	    Long time = dt.getTime();
	    ResultSet rs1 = sta1.executeQuery(sql1);
	    ResultSet rs2 = sta2.executeQuery(sql2);
	    ResultSet rs3 = sta3.executeQuery(sql3);
	    Date dt2 = new Date();
	    Long time2 = dt2.getTime();
	    System.out.println("毫秒数 ：" + (time2 - time));
	    System.out.println(sql3);
	    // while (rs.next()) {
	    // String f_parcel_id = rs.getString(1);
	    // String f_recipient_name = rs.getString(2);
	    // System.out.println(f_parcel_id);
	    // System.out.println(f_recipient_name);
	    // }
	} catch (SQLException e) {
	    e.printStackTrace();
	}
	return retMap;
    }
```