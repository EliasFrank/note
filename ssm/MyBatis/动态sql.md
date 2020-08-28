```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace:名称空间，写接口的全类名，相当于告诉MyBatis这个配置文件是实现哪个接口的-->
<mapper namespace="com.jxau.dao.EmployeeDao">

    <!--set标签可以实现动态更新，里面可以用if判断-->
    <!--foreach 遍历集合
     collection=""指定要遍历的集合的key
     item="" 每次遍历出的元素起一个变量名方便引用
     index=""索引
               遍历的如果是一个list：index指定的变量保存了当前索引
               如果是map，指定的变量保存了当前遍历元素的key
     close=""以什么符号结束
     open="" 以什么符号开始
     separator="" 分割符
    -->
    <select id="getEmployeeByArray" resultType="com.jxau.bean.Employee">
        select * from t_employee where id in
        <foreach collection="list1" open="(" close=")" separator="," item="aaa">
            #{aaa}
        </foreach>
    </select>
    <!--trim截取字符串
    prefix=“”前缀，为我们下面的sql整体添加一个前缀
    prefoxOverrides=“”取出整体字符串前面多余的字符
    suffix：为整体添加一个后缀
    suffixOverrides：去掉后面多余的字符串
    -->
    <select id="getEmployeeByAll" resultType="com.jxau.bean.Employee">
        select * from t_employee
        <trim prefix="where" prefixOverrides="and" suffixOverrides="and">
            <if test="id != null">
                id > #{id} and
            </if>
            <if test="empname != null and !empname.equals('')">
               empname like #{empname} and
            </if>
        </trim>
       <!-- <where>
            <if test="id != null">
                id > #{id}
            </if>
            <if test="empname != null and !empname.equals('')">
                and  empname like #{empname}
            </if>
        </where>-->
    </select>
</mapper>
```

