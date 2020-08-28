transaction control language

事务控制语言

---

**事务**

事务单独单元的一个或多个SQL语句组成，在这个单元中，每个MySQL语句时相互依赖的。而整个单独单元作为一个不可分割的整体。如果单元中某条SQL语句一旦执行失败或产生错误，整个单元将会回滚。所有受到影响的数据将返回到事务开始以前的状态。吐过单元中所有SQL语句均执行成功，则事务被顺利执行

---

1. 概念：在mysql中的数据用各种不同 的技术储存在文件中
2. 通过show engines来查看mysql支持的存储引擎
3. 在mysql中用的最多的存储引擎有：innodb、myisam、memory等，其中innodb支持事务，而myisam、memory等不支持事务

---

事务的创建

```
隐式事务：事务没有明显的开启和结束的标识
比如insert、update、delete

显式事务：事务具有明显的开启和结束的标记
前提：必须先设置自动提交功能为禁用
set autocommit = 0；

1. 开启事务
set autocommit = 0；
start transaction；可选的，默认禁用提交功能
2. 编写SQL语句（select、delete、update、insert）
语句1；
语句2；
。。。

3. 结束事务
commit；提交事务
rollback；回滚事务
```

















