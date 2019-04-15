---
title: MyBatis
date: 2016-10-18 21:41:21
update: 2016-10-18 21:41:21
categories: MyBatis
tags: [mybatis, mysql]
---

### 判断字段值是否存在并录入数据sql

```xml
<!-- 录入短信套餐信息 -->
<insert id="addSmsPackage" parameterType="java.util.Map">
	INSERT INTO
	tp_package_info (
	f_station_id,
	f_type,
	f_buy_count,
	f_package_count,
	f_cur_count,
	f_first_month_count,
	f_sec_month_count,
	f_third_month_count,
	f_first_month_surplus,
	f_sec_month_surplus,
	f_is_first_accrued,
	f_is_sec_accrued,
	f_every_month,
	f_package_create_time
	) SELECT
	<if test="combo != null and combo != '' and combo == 1">
		#{userId},#{combo},0,50000,5000,5000,3000,3000,0,0,0,0,0,#{currentTime}
	</if>
	<if test="combo != null and combo != '' and combo == 2">
		#{userId},#{combo},0,50000,5000,5000,3000,3000,0,0,0,0,0,#{currentTime}
	</if>
	<if test="combo != null and combo != '' and combo == 0">
		#{userId},#{combo},0,0,0,0,0,0,0,0,0,0,0,#{currentTime}
	</if>
	FROM
	DUAL
	WHERE
	NOT EXISTS (
	SELECT
	*
	FROM
	tp_package_info st
	WHERE
	st.f_station_id = #{userId})
</insert>

```
<!-- more -->

### sql索引

创建唯一索引时，可以使用REPLACE INTO 进行修改或新增数据
13if判断不能加引号
```xml
SELECT
<INCLUDE REFID="Blob_Column_List" />
FROM txm_publish t WHERE 1 = 1 AND
t.f_type = "1" OR t.f_type = "2"
<if test="fUserTypeId==8 "><!—不能加引号 -->
	OR t.f_center_id = #{fUserId}
</if>
<if test="fUserTypeId != 8 ">
	OR EXISTS (
	SELECT
	f_manager_id
	FROM
	txm_user_detail d
	WHERE
	d.f_user_id = #{fUserId}
	AND d.f_manager_id = t.f_center_id)
</if>
AND t.f_publishstate = #{fPublishState}
```

### mybatis分页sql注意数据类型

一定要BIGINT类型，传进的map中pageStart和pageSize必须将其设置为int
```xml
<IF TEST="pageStart !=-1"> LIMIT
#{pageStart,jdbcType=BIGINT},#{pageSize,jdbcType=BIGINT}
</IF>
```

### mybatis日期转换

```xml
<SQL ID="Base_Column_List"> f_parcel_id, f_exp_id, f_branch_id,
f_station_id, f_mail_no, f_company_id, f_company_name, f_recipient_name,
f_recipient_mobile, f_frame_code, f_take_code, f_num, f_input_way,
f_input_source, date_format(f_arrive_time,'%Y-%c-%d %h:%i:%s') AS
'f_arrive_time' , f_parcel_status, f_is_detention, f_out_way, f_out_source,
f_app_notice_status, f_weixin_notice_status, f_sms_notice_status,
f_out_time, f_last_time, f_type, f_comment
</SQL>
<SELECT ID="queryParcleList" RESULTMAP="BaseResultMap">select
   <INCLUDE REFID="Base_Column_List" /> from tp_parcel_info WHERE 1=1
   <IF TEST="mi.startTime!='' and mi.startTime!=null"> and
   date_format(f_arrive_time, '%Y%m%d%') &gt;= date_format(#{mi.startTime},
   '%Y%m%d%')
   </IF>
   <IF TEST="mi.endTime!='' and mi.endTime!=null"> and
   date_format(f_arrive_time, '%Y%m%d%') &lt;= date_format(#{mi.endTime},
   '%Y%m%d%')
   </IF>
   <IF TEST="mi.fCompanyId!='-1' and mi.fCompanyId!=null"> and f_company_id
   = #{mi.fCompanyId}
   </IF>
   <IF TEST="mi.fParcelStatus!='-1' and mi.fParcelStatus!=null and mi.fParcelStatus !=''">
   and f_parcel_status = #{mi.fParcelStatus}
   </IF>
   <IF TEST="mi.fStationId !=null and mi.fStationId !=''"> and f_station_id
   = #{mi.fStationId}
   </IF>
   <IF TEST="pageNum!='-1' and pageSize!='-1'"> LIMIT #{pageNum},
   #{pageSize}
   </IF>
</SELECT>
```

### spring+mybatis

配置参考：https://my.oschina.net/wangt10/blog/508344

### mybatis插入返回逐渐id

```java
public interface PushMapper {
    void insertReId(Record record);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.baomidou.springwind.mapper.PushMapper">
    <sql id="Base_Column_List">
      id, content, sys_id, status, create_time
    </sql>
    <insert id="insertReId" parameterType="com.baomidou.springwind.entity.push.Record" useGeneratedKeys="true" keyProperty="id">
        insert into p_push_record (content,sysId,createTime)
        VALUES (#{content},#{sysId},#{createTime});
    </insert>
</mapper>
```

### 报错记录

```
org.apache.ibatis.builder.IncompleteElementException: Could not find result map java.util.LinkedHashMap
```
所有的xml文件中的方法里存在没有和DAO接口对应的返回类型。
DAO接口返回类型为map而xml中方法的返回类型为对象(resultType)，应该为resultMap，否则就会报错

请求参数必须是bean对象，插入成功后，该数据的逐渐id已经写入到请求对象(com.baomidou.springwind.entity.push.Record)中。


### Mybatis批量更新出现的问题

```xml
<update id="importStationDataFromExcel">
	<foreach collection="list" item="item" index="index" open=""
		close="" separator=";">
		update tp_user u set u.f_station_name = #{item.fStationName} where u.f_user_id = #{item.fUserId}
	</foreach>
</update>
```

组织sql语句，在赋值时报错，sql语句错误

```xml
### SQL: update tp_user     SET f_station_name = ?      WHERE f_user_id = ?     ;     update tp_user     SET f_station_name = ?      WHERE f_user_id = ?     ;     update tp_user     SET f_station_name = ?      WHERE f_user_id = ?
### Cause: com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Erreur de syntaxe près de 'update tp_user
			 SET f_station_name = 'nanjing' 
			 WHERE f_use' à la ligne 5
```

原因：
配置的 MySQL jdbc 链接字符串 默认不支持一次性执行多个sql 语句，
我们可以在配置文件中链接数据库的url添加一个参数allowMultiQueries=true，代表支持执行多个条sql，问题解决。
jdbc.url=jdbc:mysql://192.168.1.2:3/test4?characterEncoding=utf-8&allowMultiQueries=true

### mybatis一对多映射

Mall实体
```java
import lombok.Data;
import javax.persistence.Table;
import javax.persistence.Transient;

@Data
@Table(name = "t_mall")
public class Mall {
	@Id
    @Column(name = "Id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @ApiModelProperty(value = "id", example = "0")
    private Integer id;
    private String mallName;
    private String mallCode;
}

```

MallCity实体

```java
import lombok.Data;
import java.util.List;

@Data
public class MallCity extends BaseEntity {
    private int cityCode;
    private String city;
    private List<Mall> malls;
    private int sort;
}

```

mapper接口

```java
import com.red.star.macalline.data.core.base.MyMapper;
import com.red.star.macalline.data.entity.Fans;
import com.red.star.macalline.data.entity.Mall;
import com.red.star.macalline.data.entity.MallCity;
import com.red.star.macalline.data.entity.MallExpand;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import java.util.List;

public interface MallMapper extends MyMapper<Mall> {

    List<MallCity> findCityContaiMall();

}

```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.red.star.macalline.data.mappers.MallMapper">
    <resultMap type="com.red.star.macalline.data.entity.MallCity"
               id="findCityContaiMallMap">
        <id property="id" column="id"/>
        <result property="cityCode" column="city_code"/>
        <result property="city" column="city"/>
        <collection property="malls" ofType="com.red.star.macalline.data.entity.Mall" column="city_code" select="mallsByCityCode">
            <id column="id" property="id"/>
            <result column="mallName" property="mall_name"/>
            <result column="mallCode" property="mall_code"/>
        </collection>
    </resultMap>
    <select id="mallsByCityCode" resultType="com.red.star.macalline.data.entity.Mall">
        select id,mall_name,mall_code from t_mall tm where tm.city_code = #{cityCode};
    </select>

    <select id="findCityContaiMall" resultMap="findCityContaiMallMap">
        select id,city,city_code from t_mall tm group by tm.city;
    </select>

</mapper>
```

### mybatis插入时有则更新无则新增

```xml
INSERT INTO `student`(`name`, `age`) VALUES('Jack', 19)
  ON DUPLICATE KEY 
  UPDATE `age`=19;
``` 

当唯一字段name=Jack已经存在则执行UPDATE `age`=19;
当唯一字段name=Jack没有时，则新增