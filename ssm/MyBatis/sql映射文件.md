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
