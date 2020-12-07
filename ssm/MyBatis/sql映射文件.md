增删改

![1598078486745](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1598078486745.png)

---

查询

1. 单个参数

   基本类型：取值   #{随便写}

   传入pojo

2. 多个参数

   public employee get...(int id, Stirng name)

   取值：#{参数名} 是无效的

   可用：0，1 （参数索引）或者param1、param2（第几个参数就是param几）

   原因：只要传入多个参数，mybaits会自动的将这些参数封装在一个map中，封装时使用的key就是参数的索引和参数的第几个表示

3. @Param：为参数指定key：命名参数（推荐这么做）

   我们可以告诉mybatis：封装参数map的时候别乱来，使用我们指定的key

4. 传入pojo（JavaBean）

   取值：#{pojo的属性名）

5. 传入map：将多个要使用的参数封装起来

   取值：#{key}

实际上mybatis有两种取值方式

#{属性名}：是参数预编译的方式，参数的位置都是用？替代，参数后来都是预编译设置进去的，安全，不会有SQL注入

${属性名}：不是参数预编译，而是直接和sql语句进行拼串，不安全

---

**自定义结果集**（resultMap）

自己定义每一列数据和JavaBean的映射规则

type=“”指定哪个JavaBean自定义封装规则，全类名

id=“”唯一标识，让别名在后面引用

```xml
<resultMap type="com.jxau.bean.cat" id="mycat">
	<!--指定主键列对应规则
		column=“id”：指定哪一列是主键列
  		property=“”：指定cat的哪个属性封装id这一列的数据
	-->
    <id property="id" column="id"/>
    <!--普通列-->
	<result property="name", column="cName"/>
</resultMap>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace:名称空间，写接口的全类名，相当于告诉MyBatis这个配置文件是实现哪个接口的-->
<mapper namespace="com.jxau.dao.EmployeeDao">
    <!--
cache：缓存
cache-ref：和缓存有关
delete、update、select、insert：增删改查
parameterMap: 复杂参数的映射（已废弃）
resultMap:结果映射：自定义结果集的封装规则
sql：抽取可重用的sql

-->
    <!--
    Employee getEmployee(int i);
    select：用来定义一个查询操作
    id：方法名，相当于这个配置是对于某个方法的实现
    returnType：指定方法运行后的返回值类型，查询操作必须指定
    #{属性名}：代表取出传递过来的某个参数的值
    -->
    <select id="getEmployee" resultType="Employee">
        select * from t_employee where id = #{i}
    </select>

    <select id="getEmployeeName" resultType="Employee">
        select * from t_employee where id = #{i} and empname=#{name}
    </select>

    <!--如果返回的是集合，resultType写集合元素的类型-->
    <select id="getAll" resultType="Employee">
        select * from t_employee
    </select>

    <!--查询单个记录，写map-->
    <select id="getSingleMap" resultType="map">
        select  *  from t_employee where id=#{id}
    </select>

    <!--查询多个记录，写元素属性-->
    <select id="getAllMap" resultType="Employee">
        select * from t_employee
    </select>
    <!--
    增删改不用写返回值类型，增删改是返回影响多少行
    mybatis自动判断，如果是数字，封装成（int， long）
    如果是boolean（影响0行自动封装false，否则true）
    #{属性名}：从传入的参数对象中取出对应属性的值
    -->
    <update id="updateEmployee">
        update t_employee
        set empname = #{empname}, gender=#{gender}, email=#{email}
        where id=#{id}
    </update>

    <delete id="deleteEmployee">
        delete  from t_employee where id=#{id}
    </delete>

    <!--
        让mybatis自动的将自增id赋值传入给employee对象的id属性
        useGeneratedKeys="true"
        keyProperty="id"  将刚才自增的id封装给哪个属性
        对于不能自增的数据库，先查询最大id，再赋值
        <selectKey order="BEFORE">select max(id) from xxx;</selectKey>
    -->
    <insert id="insertEmployee" useGeneratedKeys="true" keyProperty="id">

        INSERT INTO `t_employee` (`empname`, `gender`, `email`)
        VALUES (#{empname}, #{gender}, #{email})
    </insert>
    <!--int insertEmployee(Employee employee);-->
</mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.jxau.dao.KeyDao">
    <select id="getKeyById" resultMap="keylock" >
      select k.id kid, k.keyname, l.id lid, l.lockname from t_key k
	  LEFT JOIN t_lock l
	  on k.lockid = l.id
      where k.id=#{i}
    </select>
    <!--自定义封装规则，使用级联属性封装联合查询的结果-->
   <!-- <resultMap id="keylock" type="com.jxau.bean.Key">
        <id property="id" column="kid"/>
        <result property="keyName" column="keyname"/>
        <result property="lock.id" column="lid"/>
        <result property="lock.lockName" column="lockname"/>
    </resultMap>-->
    <resultMap id="keylock" type="com.jxau.bean.Key">
        <id property="id" column="kid"/>
        <result property="keyName" column="keyname"/>
        <association property="lock" javaType="com.jxau.bean.Lock">
            <id property="id" column="lid"/>
            <result property="lockName" column="lockname"/>
        </association>
    </resultMap>
</mapper>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.jxau.dao.LockDao">
    <select id="getLockById" resultMap="lock">
      select k.id kid, k.keyname, l.id lid, l.lockname from t_key k
	  LEFT JOIN t_lock l
	  on k.lockid = l.id
      where l.id=#{i}
    </select>
    <resultMap id="lock" type="com.jxau.bean.Lock">
        <id property="id" column="lid"/>
        <result property="lockName" column="lockname"/>
        <collection property="keys" ofType="com.jxau.bean.Key">
            <id property="id" column="kid"/>
            <result property="keyName" column="keyname"/>
        </collection>
    </resultMap>
</mapper>
```

