---
title: mysql分组按条件获取数据
date: 2017-02-16 20:11:34
update: 2017-02-16 20:11:34
categories: MySQL
tags: [mysql]
---

1.查询含有两个及以上的圆通包裹的所有站点信息，并获取最近的两个包裹运单号运单的站点信息
参考：http://www.cnblogs.com/joeblackzqq/p/4334132.html

<!-- more -->
```mysql
SELECT ttt.f_station_id,
       ttt.f_station_name,
       group_concat(ttt.f_mail_no) 运单
FROM
  (SELECT tt.f_station_id,
          tt.f_mail_no,
          u.f_station_name
   FROM
     (SELECT a.f_station_id,
             a.f_mail_no,
             a.f_arrive_time
      FROM
        (SELECT i2.f_parcel_id,
                i2.f_station_id,
                i2.f_arrive_time,
                i2.f_mail_no
         FROM tp_parcel_info_201608 i2
         WHERE i2.f_company_id = '108' ) a
      WHERE 2 >=
          (SELECT count(1)
           FROM
             (SELECT i2.f_parcel_id,
                     i2.f_station_id,
                     i2.f_arrive_time,
                     i2.f_mail_no
              FROM tp_parcel_info_201608 i2
              WHERE i2.f_company_id = '108' ) b
           WHERE a.f_station_id = b.f_station_id
             AND a.f_arrive_time <= b.f_arrive_time )
      ORDER BY a.f_station_id,
               a.f_arrive_time DESC) tt
   LEFT JOIN tp_user u ON u.f_user_id = tt.f_station_id
   WHERE tt.f_station_id IN
   -- 含有两个及以上的圆通包裹数据的所有站点ID,用于排除不满足条件的站点ID
       (SELECT t.f_station_id
        FROM
          (SELECT count(1) c,
                  pi.f_station_id
           FROM tp_parcel_info_201608 pi
           WHERE pi.f_company_id = '108'
           GROUP BY pi.f_station_id) t
        WHERE t.c > 2 ) ) ttt
GROUP BY ttt.f_station_id
```
