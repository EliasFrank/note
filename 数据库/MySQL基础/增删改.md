**DML语言**

数据操作语言

插入：insert

修改：update

删除：delete

---

**插入语句**

方式一：

语法：

```
insert into 表名（列名，...） values（值1， ...）
```

1. 插入的值的类型要与列的类型一致或兼容
2. 不可以为null的列必须插入值
3. 列的顺序可以调换
4. 列数和值的个数必须一致
5. 可以省略列名，默认是所有列。而且列的顺序和表中列的顺序一致

方式二：

```
语法：
insert into 表名
set 列名=值， 列名=值， 列名=值...
```

方式一和方式二的对比

```
方式一可以插入多行；
insert into 表名（列名，...） values（值1， ...）， values（值1， ...），values（值1， ...）
方式一支持子查询
insert into 表名（列名，...） select ...
```

---

**修改语句**

1. 修改单标的记录

   ```
   update 表名
   set 列=新值， 列=新值...
   where 筛选条件
   ```

2. 多表的更新

3. ```
   SQL92语法
   update 表1 别名，表2 别名
   set 列=值
   where 连接条件
   and 筛选条件
   
   SQL99语法
   update 表1 别名
   inner|left|right join 表2 别名
   on 连接条件
   set 列= 值
   where 筛选条件
   ```

---

**删除语句**

方式一：delete

1. 单表的删除

```
delete from 表名 where 筛选条件
```

2. 多表的删除

   ```
   SQL92语法
   delete 别名
   from 表1 别名， 表2 别名
   where 连接条件
   and 筛选条件
   
   SQL999语法
   delete 别名
   from 表1 别名
   inner|。。。 join 表2 别名 
   on 连接条件
   where 筛选条件
   ```

方式二：truncate

```
truncate table 表名
```

delete和truncate的区别

```
1. delete可以加where条件，truncate不能加
2. truncate删除，效率高一丢丢
3. 加入要删除表中的自增长类
	用delete删除后，再插入数据，自增长列从断点开始
	用truncate删除后，在插入数据，自增长列的值从1开始
4. truncate删除没有返回值，delete删除有返回值
5. truncate删除不能回滚，delete可以回滚
	
```











