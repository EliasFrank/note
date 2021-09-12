**查询常量值**

select 100;

select 'john'

**查询表达式**

select 100-99;

**查询函数**

select version();

**起别名**

1. 便于理解

2. 如果要查询的字段有重名的情况，使用别名可以区分开来

```sql
#方式一:使用as
select 100-99 AS result;
select last_name AS 姓, first_name AS 名 from emplyee;

#方式二：使用空格
select last_name 姓, first_name 名 from emplyee;
```

**去重**

```sql
select distinct department_id from employees;
```

**+号的作用**

```
select 100+90 两个操作数都为数值型，则做加法运算
select '123'+90 只要其中一方为字符型，试图将字符型数值转换成数值型
				如果转换成功，则继续做加法运算
				如果失败，则将字符型数值转换为0
select null+10	只要其中一方为null，则结果肯定为null

MySQL提供了字符串连接函数：
select concat(last_name, first_name) from employees;
```

**条件查询**

```sql
select 查询表名 from 表名 where 筛选条件
where中不允许使用别名
分类：
一、按条件表达式筛选
    简单条件运算符：> < = != <> >= <=
二、按逻辑表达式筛选
    逻辑运算符：&& || ！ and or not
三、模糊查询：①like ②between and ③in ④is null
```

```sql
like一般和通配符搭配使用：
% 任意多个字符，包含0个字符
_ 任意单个字符

可以通过自定义转义字符进行转义
select last_name from employees where last_name like '_$_%' escape '$';
```

```
between and
使用between and可以提高语句的简洁度
包含临界值
两个临界值不能调换顺序
select * from employees where employee_id between 100 and 120;
```

```
in
判断某字段的值是否属于in列表中的某一项
1. 使用in提高语句的简洁度
2. in列表的值类型必须一致或兼容
3. 不支持使用通配符
select * from employees where job_id in ('IT_PROT', 'AD_VP', 'AD_PRES');
```

```
is null
=或<>不能用于判断null值
is null 或 is not null可以判断null值
select * from employees where comminssion_pct is null;
```

**安全等于  <=>**

```
select * from employees where comminssion_pct <=> null;
select * from employees where salary <=> 12000;

is null: 仅仅可以判断null值，可读性较高
<=>: 既可以判断null值，又可以判断普通的数值，可读性较低
```

**排序查询**

```sql
select 查询列表 from 表 [where 条件] order by 排序列表 [asc|desc]
asc 是升序
desc 是降序
如果不写，默认是升序
order by字句中可以支持单个字段、多个字段、表达式、函数、别名
order by子句一般是放在查询语句的最后面，limit子句除外


按表达式排序
select *, salary*12*(1+IFNULL(commission_pct, 0)) 年薪 from employees
order by salary*12*(1+IFNULL(commission_pct, 0)) DESC;

按别名排序
select *, salary*12*(1+IFNULL(commission_pct, 0)) 年薪 from employees
order by 年薪 DESC;

按函数排序
select Length(last_name) 字节长度, last_name, salary
from employees
order by LENGTH(last_name) desc;

按多个字段排序，先排的写前面
select * from employees order by salary desc, employee_id desc;
```

