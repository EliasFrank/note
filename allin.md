### 自我介绍

  面试官你好，我叫黄雷，就读于江西农业大学软件学院。大学三年来，我刻苦学习，成绩优异，曾多次获得奖学金。在编程方面，有着浓厚的兴趣。在兴趣的驱使下，我熟悉了java语言的使用，能使用 Spring、SpringMVC 和 MyBatis 等开源框架进行项目开发，对关系型数据库MySQL和非关系型数据库redis也有一定的了解。

  我待人真诚热情、为人随和、善于沟通、有良好的人际关系和很强的适应能力。在校期间参加了校工作室，工作态度认真负责，具有团队合作精神。在指导老师的带领下，我们立项并结题了一个国家级的大创项目。在此期间我们团结协作，孜孜不倦的学习，不断提升自己。

我的自我介绍结束了。请面试官多多指教，谢谢。

### java

#### 集合

List、Set、Map都是接口，前两个继承至Collection接口，Map为独立接口

List子类有ArrayList、LinkedList、Vector

Set子类有HashSet、LinkedHashSet、TreeSet

Map子类有Hashmap、TreeMap、LinkedHashMap、HashTable

Collection接口下还有个Queue接口，子类有优先级队列，双端队列和阻塞队列等。

##### ArrayList

底层数据结构是数组，查询快，增删慢，线程不安全

```java
transient Object[] elementData; 
//默认容量是10
private static final int DEFAULT_CAPACITY = 10;
//使用有参构造器实例化的时候，会直接创建一个object数组
if (initialCapacity > 0) 
    this.elementData = new Object[initialCapacity];
//使用空参构造器实例化时，直接初始化为一个空数组
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
public ArrayList() {
    this.e，lementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
//最大容量是整形的最大值-8，因为还要保存一些其他的信息
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
//扩容时，默认扩容为当前数组长度的1.5倍
int newCapacity = oldCapacity + (oldCapacity >> 1);
//查找数组中是否包含某个值，或者获取某个值的下标时，最后执行的都是下面这个函数，从前往后查找
int indexOfRange(Object o, int start, int end) {
	//...
    for (int i = start; i < end; i++) {
        //默认是使用object的equals比较对象是否相等
        if (o.equals(es[i])) {
            return i;
        }
    }
}
//数组的克隆
ArrayList<?> v = (ArrayList<?>) super.clone();
v.elementData = Arrays.copyOf(elementData, size);
v.modCount = 0;
return v;
//使用get方法时，会先判断要获取的下标是否越界
public E get(int index) {
    Objects.checkIndex(index, size);
    return elementData(index);
}
//使用set方法时，也会先判断是否越界。用的是size（数组元素的个数）而不是数组的长度（length）来判断是否越界
public E set(int index, E element) {
    Objects.checkIndex(index, size);
    //...	
}
//添加元素，最终调用的是
private void add(E e, Object[] elementData, int s) {
    //如果s超过数组长度，则进行扩容
    if (s == elementData.length)
        elementData = grow();
}
//在指定位置插入元素
System.arraycopy(elementData, index,
                 elementData, index + 1,
                 s - index);
elementData[index] = element;
//删除元素
private void fastRemove(Object[] es, int i) {
    //...
    if ((newSize = size - 1) > i)
    	System.arraycopy(es, i + 1, es, i, newSize - i);
    es[size = newSize] = null;
}
//判断两个集合是否相等，调用的是对方的迭代器遍历（传入的对象必须实现了List接口）
for (; from < to; from++) {
    if (!oit.hasNext() || !Objects.equals(es[from], oit.next())) {
        return false;
    }
}
//判断两个ArrayList是否相等
for (int i = 0; i < s; i++) {
    if (!Objects.equals(es[i], otherEs[i])) {
        equal = false;
        break;
    }
}
//计算hashCode
int hashCode = 1;
for (int i = from; i < to; i++) {
    Object e = es[i];
    hashCode = 31 * hashCode + (e == null ? 0 : e.hashCode());
}
//清空整个数组，是将整个数组中的元素置为null，size置为0，但是数组的length不变
for (int to = size, i = size = 0; i < to; i++)
    es[i] = null;
//添加整个集合，会先判断数组中剩余的空间大小是否能放下要添加集合的大小，不能放下则扩容
public boolean addAll(Collection<? extends E> c) {
    Object[] a = c.toArray();
    int numNew = a.length;
    if (numNew > (elementData = this.elementData).length - (s = size))
        elementData = grow(s + numNew);
    System.arraycopy(a, 0, elementData, s, numNew);
}
//移除某个集合中的所有元素
boolean batchRemove(Collection<?> c, boolean complement,
                    final int from, final int end) {
    final Object[] es = elementData;
    int w = r++;
    try {
        for (Object e; r < end; r++)
            if (c.contains(e = es[r]) == complement)
                es[w++] = e;
    } catch (Throwable ex) {
    } finally {
        shiftTailOverGap(es, w, end);
    }
}
```

##### LinkedList

底层数据结构是双向链表，查询慢，增删快，线程不安全

```java
//底层数据结构
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
//维护了头结点和尾节点
transient Node<E> first;
transient Node<E> last;
//清空当前链表
public void clear() {
    for (Node<E> x = first; x != null; ) {
        Node<E> next = x.next;
        x.item = null;
        x.next = null;
        x.prev = null;
        x = next;
    }
    first = last = null;
}
//get和set方法都会先调用这个方法判断是否越界
private void checkElementIndex(int index) {
    if (!isElementIndex(index))
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
private boolean isElementIndex(int index) {
    return index >= 0 && index < size;
}
//添加方法调用的是
private boolean isPositionIndex(int index) {
    return index >= 0 && index <= size;
}
//查找某个位置的元素时，会根据元素位置在前半段或者后半段，选择从头开始遍历还是从尾结点开始
Node<E> node(int index) {
    if (index < (size >> 1)) {
        Node<E> x = first;
    } else {
        Node<E> x = last;
    }
}
/**
以下方法继承至Deque（双端队列）
当定义变量为List<Integer> list = new LinkedList<>();时不能使用以下方法

检索但不删除链表的头部的方法
peek、peekFirst 如果头结点为空则返回null
element 如果头结点为空则抛出异常（getFirst）

检索但不删除链表的尾部的方法
peekLast 如果尾结点为空则返回null

检索并删除此列表的头结点
poll、pollFirst 如果头结点为空则返回null（unlinkFirst）
remove、pop 如果头结点为空则抛出异常（removeFirst）

检索并删除此列表的尾结点
pollLast 如果尾结点为空则返回null（unlinkLast）

添加尾结点
offer、offerLast（linkLast）

添加头节点
offerFirst、push（linkFirst）
*/
//clone方法
public Object clone() {
    //先克隆其他元素，然后将头尾节点置为null
    LinkedList<E> clone = superClone();
    clone.first = clone.last = null;
    clone.size = 0;
    clone.modCount = 0;
    //遍历旧链表，新链表通过尾插法添加元素
    for (Node<E> x = first; x != null; x = x.next)
        clone.add(x.item);
}
```

##### Vector

层数据结构是数组，查询快，增删慢，线程安全

```java
//底层是一个数组
protected Object[] elementData;
//每次扩容的容量大小，如果这个值小于等于0，则将数组容量扩大一倍
protected int capacityIncrement;
//有参构造器
public Vector(int initialCapacity, int capacityIncrement) {
    this.elementData = new Object[initialCapacity];
    this.capacityIncrement = capacityIncrement;
}
//数组大小默认是10，容量增量默认是0
public Vector(int initialCapacity) {
    this(initialCapacity, 0);
}
public Vector() {
    this(10);
}
//对所有需要同步的方法，都加了synchronized修饰
public synchronized void copyInto(Object[] anArray) {}

//最大容量是Integer.MAX_VALUE - 8，和ArrayList一样
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```

### MySQL

```sql
#常用命令
#1.查看当前所有的数据库
show databases;
#2.打开指定的库
use 库名;
#3.查看当前库的所有表
show tables;
#4.查看其它库的所有表
show tables from 库名;
#5.创建表
create table 表名(
	列名 列类型,
	列名 列类型,
	...
);
#6.查看表结构
desc 表名;
```

```sql
#数据库的创建
create database [if not exists] name;
#数据库的删除
drop database [if exists] name;
#数据库的修改
alter database name character set gbk
```

```sql
#表的创建
create table 表名(
	列名 列的类型 【（长度）约束】，
	...
	列名 列的类型 【（长度）约束】
)
#表的修改
#1. 修改列名
alter table 表名 change column 列名 新列名 类型
#2. 修改列的类型或约束
alter table 表名 modify column 列名 类型
#3. 添加新列
alter table 表名 add column 列名 类型 
#4. 删除列
alter table 表名 drop column 列名
#5. 修改表名
alter table 表名 rename to 新表名

#表的删除
drop table 表名
#表的复制
#1. 仅仅复制表的结构
create table 新表名 like 表名

#2.复制表的结构+数据
create table 新表名
select * from 表名

#3. 只复制部分数据
create table 新表名
select 字段
from 表名
where 筛选条件

#4. 仅仅复制某些字段
create table 新表名
select 字段
from 表名
where 0
```

```sql
#添加列级约束：直接进在字段名和类型后面追加约束类型即可，只支持：默认、非空、主键、唯一
#添加表级约束：在各个字段的最下面 constraint 约束名 约束类型（字段）
#六大约束
NOT NULL  #非空，用于保证该字段的值不能为空
DEFAULT #默认，用于保证该字段有默认值
primary key #主键，用于保证该字段的值具有唯一性，并且非空
UNIQUE #唯一：用于保证该字段的值具有唯一性，可以为空
check #检查约束（mysql不支持）
foreign key #外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值，在从表中添加外键约束，用于引用主表中某列的值
```

**主键和唯一的异同**

|      | 保证唯一性 | 是否允许为空 | 一个表可以有几个 | 是否允许组合 |
| :--: | :--------: | :----------: | :--------------: | :----------: |
| 主键 |     √      |      ×       |    至多有一个    | √，但不推荐  |
| 唯一 |     √      |      √       |    可以有多个    | √，但不推荐  |

```sql
#添加非空约束
alter table 表名 modify column 列名 varchar(20) not null

#添加默认约束
alter table 表名 modify column 列名 int default 5

#添加主键
#列级约束
alter table 表名 modify column 列名 类型 primary key
#表级约束
alter table 表名 add primary key(列名)

#添加唯一
#列级约束
alter table 表名 modify column 列名 类型 unique
#表级约束
alter table 表名 add unique(列名)

#添加外键
alter table 表名 add constraint 外键名 foreign key(列名) references 表名(列名)

#删除非空约束
alter table 表名 modify column 列名 类型  null;

#删除默认约束
alter table 表名 modify column 列名 类型

#删除主键
alter table 表名 drop primary key
```

```
标识列，又称自增长列
必须和key搭配
一个表至多只有一个标识列
可以通过set_auto_increment==3设置步长
```

```sql
#级联删除
alter table 表名 add constraint 外键名 foreign key(列名) references major(列名) on delete cascade

#级联置空
alter table 表名 add constraint 外键名 foreign key(列名) references major(列名) on set null
```

```sql
#插入语句
insert into 表名（列名，...） values（值1， ...）
insert into 表名 set 列名=值， 列名=值， 列名=值...
#方式一可以插入多行；
insert into 表名（列名，...） values（值1， ...）， values（值1， ...），values（值1， ...）
#方式一支持子查询
insert into 表名（列名，...） select ...

#修改语句
update 表名 set 列=新值， 列=新值... where 筛选条件
#多表更新
#SQL92语法
update 表1 别名，表2 别名 set 列=值 where 连接条件 and 筛选条件
#SQL99语法
update 表1 别名 inner|left|right join 表2 别名 on 连接条件 set 列=值 where 筛选条件

#删除语句
delete from 表名 where 筛选条件
truncate table 表名
#多表删除
#SQL92语法
delete 别名 from 表1 别名, 表2 别名 where 连接条件 and 筛选条件
#SQL99语法
delete 别名 from 表1 别名 inner|left|right join 表2 别名 on 连接条件 where 筛选条件
/*
truncate和delete的区别
1. delete可以加where条件，truncate不能加
2. truncate删除，效率高一点
3. 假如要删除表中的自增长类
	用delete删除后，再插入数据，自增长列从断点开始
	用truncate删除后，再插入数据，自增长列的值从1开始
4. truncate删除没有返回值，delete删除有返回值
5. truncate删除不能回滚，delete可以回滚
*/
```

```sql
#简单查询语句
select 查询表名 from 表名 where 筛选条件 #where中不允许使用别名

#安全等于<=>
select * from employees where comminssion_pct <=> null;
select * from employees where salary <=> 12000;
#is null 仅仅可以判断null值，可读性较高
#<=> 既可以判断null值，又可以判断普通的数值，可读性较低

select 查询列表 from 表 [where 条件] order by 排序列表 [asc|desc]
/*
asc 是升序
desc 是降序
如果不写，默认是升序
order by字句中可以支持单个字段、多个字段、表达式、函数、别名
order by子句一般是放在查询语句的最后面，limit子句除外
*/
#按表达式排序
select *, salary*12*(1+IFNULL(commission_pct, 0)) 年薪 from employees order by salary*12*(1+IFNULL(commission_pct, 0)) DESC;

#按别名排序
select *, salary*12*(1+IFNULL(commission_pct, 0)) 年薪 from employees order by 年薪 DESC;

#按函数排序
select Length(last_name) 字节长度, last_name, salary from employees order by LENGTH(last_name) desc;

#按多个字段排序，先排的写前面
select * from employees order by salary desc, employee_id desc;
```

```sql
#复杂查询

#联合查询
查询语句1
union
查询语句2
union
...
/*
1. 要求多条查询语句的查询列数是一致的
2. 要求多条查询语句的查询的每一列的类型和顺序最好一致
3. union关键字默认去重，如果使用union all可以留下重复项
*/
```



### hr

#### 自我介绍

  面试官你好，我叫黄雷，就读于江西农业大学软件学院。大学三年来，我刻苦学习，成绩优异，曾多次获得奖学金。在编程方面，我有着浓厚的兴趣。在兴趣的驱使下，我在大学期间参加了许多的编程比赛。虽然结果有好有坏，但是我在一次次的拼搏中，汲取经验，不断的成长

  在生活中，我待人真诚热情、为人随和、有良好的人际关系和很强的适应能力。在校期间参加了校工作室，工作态度认真负责，具有团队合作精神。在指导老师的带领下，我们工作室立项并结题了一个国家级的大创项目。

我的自我介绍结束了。请面试官多多指教，谢谢。

#### 你觉得你个性上最大的优点是什么？

沉着冷静、条理清楚、立场坚定、顽强向上。 乐于助人和关心他人、适应能力和幽默感、乐观和友爱。

#### 说说你最大的缺点？

比较容易担心某件事。比如有个a任务，明天要交给领导，交给领导之前我会核实好多次。上交之后，我可能还会时不时的想想那个任务是不是做完了，还有没有什么缺陷。导致我在做接下来的任务时，没法专注进去。但是我已经意识到了这件事，我会改进的。

#### 你对加班的看法？

如果工作任务紧急，那么加班完成工作任务，这是个人职责。但是，如果我天天都加班，我会反思，是不是我自己的效率太低了。我会通过提高工作效率来避免不必要的加班。

#### 你对薪资的要求

没有具体的薪资要求。因为我觉得公司会根据我的情况和市场标准的水平，给我合理的薪水。

#### 职业规划

我准备在技术领域有所作为，对公司所用的技术，我将不断的深入研究

#### 朋友对你的评价

我觉的我是一个比较随和的人，与不同的人都可以友好相处。在我与人相处时， 我总是能站在别人的角度考虑问题

#### 如果通过这次面试我们单位录用了你，但工作一段时间却发现你根本不适合这个职位， 你怎么办？

不断学习，虚心向领导和同事学习业务知识和处事 经验，了解这个职业的精神内涵和职业要求，力争减少差距

#### 你并非毕业于名牌院校？

是否毕业于名牌院校不重要，重要的是有能力完成公司交给我的工作。大学只是一个地点，再好的大学也不能代替你学习，还得看自己的努力。而且在互联网发达的今天，我们能获得的资源不比他们少。

#### 反问

如果我能进入我们公司，公司对新员工有没有什么培训项目，我可以参加吗

公司工作的日常是怎么样的

如果可以的话，我能提前去公司实习吗
