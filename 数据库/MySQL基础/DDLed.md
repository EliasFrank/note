**DDL语言**

数据定义语言，库和表的管理

一、库的管理

创建、修改、删除

二、表的管理

创建、修改、删除

创建：create

修改：alter

删除：drop

---

库的管理

1. 库的创建

   ```
   create database [IF NOT EXISTS] 库名
   ```

2. 库的修改

   ```
   更改库的字符集
   alter database 库名 character set gbk
   ```

3. 库的删除

   ```
   drop database 【if exists】 库名
   ```

---

**表的管理**

1. 表的创建

   ```
   create table 表名(
   	列名 列的类型 【（长度）约束】，
   	列名 列的类型 【（长度）约束】，
   	列名 列的类型 【（长度）约束】，
   	...
   	列名 列的类型 【（长度）约束】
   	
   )
   ```

2. 表的修改

   ```
   1. 修改列名
   alter table 表名 change column 列名 新列名 类型
   2. 修改列的类型或约束
   alter table 表名 modify column 列名 类型
   3. 添加新列
   alter table 表名 add column 列名 类型 
   4. 删除列
   alter table 表名 drop column 列名
   5. 修改表名
   alter table 表名 rename to 新表名
   ```

3. 表的删除

   ```
   drop table 表名
   ```

4. 复制表

   ```
   1. 仅仅复制表的结构
   create table 新表名 like 表名
   
   2.复制表的结构+数据
   create table 新表名
   select * from 表名
   
   3. 只复制部分数据
   create table 新表名
   select 字段
   from 表名
   where 筛选条件
   
   4. 仅仅复制某些字段
   create table 新表名
   select 字段
   from 表名
   where 0
   ```

----

**约束**

常见约束

含义：一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性

使用：

```
添加列级约束：直接进在字段名和类型后面追加约束类型即可，只支持：默认、非空、主键、唯一
添加表级约束：在各个字段的最下面
	constraint 约束名 约束类型（字段）
```

分类：六大约束

```
NOT NULL  非空，用于保证该字段的值不能为空
DEFAULT：默认，用于保证该字段有默认值
primary key主键，用于保证该字段的值具有唯一性，并且非空
UNIQUE 唯一：用于保证该字段的值具有唯一性，可以为空
check：检查约束（mysql不支持）
foreign key：外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值，在从表中添加外键约束，用于引用主表中某列的值
```

添加约束的时机

```
创建表时、修改表时
```

约束的添加分类：

```
列级约束，六大约束语法上都支持，但外键约束没有效果
```

```
表级约束：除了非空，默认，其他的都支持
```

---

**主键和唯一的异同**

|      | 保证唯一性 | 是否允许为空 | 一个表总可以有几个 | 是否允许组合 |
| :--: | :--------: | :----------: | :----------------: | :----------: |
| 主键 |     √      |      ×       |     至多有一个     | √，但不推荐  |
| 唯一 |     √      |      √       |     可以有多个     | √，但不推荐  |

---

**外键**

```
1. 要求在从表设置外键关系
2. 从表的外键列的类型和主表的关联的类型要求一致或兼容，名称无要求
3. 主表的关联列必须是一个key（一般是主键或唯一）
4. 插入数据时，先插入主表，再插入从表
5. 删除数据时，先删除从表，再删除主表
```

---

修改表时添加约束

```
添加非空约束
alter table xxx modify column 列名 varchar(20) not null

添加默认约束
alter table xxx modify column 列名 int default 5

添加主键
列级约束：alter table xxx modify column 列名 int primary key
表级约束：alter table xxx add primary key(列名)

添加唯一
列级约束：alter table xxx modify column 列名 int unique
表级约束：alter table xxx add unique(列名)

添加外键
alter table xxx add constraint 外键名 foreign key(列名) references 表名(列名)

```

修改表时删除约束

```
删除非空约束
alter table xxx modigy column 列名 varchar(20)  null;

删除默认约束
alter table xxx modify column 列名 int

删除主键
alter table xxx drop primary key
```

---

**标识列**

 又称自增长列

含义：可以不用手动的插入值，系统提供默认的序列值

特点

```
标识列必须和key搭配
一个表至多只有一个标识列
标识列的类型只能是数值型
标识列可以通过set_auto_increment==3设置步长
```

----

1. 级联删除

```
alter table 表名 add constraint 外键名 foreign key(列名) references major(列名) on delete cascade
```

2. 级联置空

   ```
   alter table 表名 add constraint 外键名 foreign key(列名) references major(列名) on set null
   ```

3. 

