j**ava解析xml有两种技术，分别是dom解析和sax解析**

​	<u>dom方式解析</u>

​		*根据xml的层级结构在内存中分配一个树形结构，把xml的标签，属性和文本都封装成对象

​		*缺点：如果文件过大，造成内存溢出

​		*优点：很方便实现增删改操作

​	<u>sax解析</u>

​		*采用时间驱动，边读边解析

​					-从上到下，一行一行的解析，解析到某一个对象，返回对象名称

​		*缺点：不能实现增删改操作

​		*优点：如果文件过大，不会造成内存溢出，方便实现查询操作

**XML的的两种约束**

​	*1.dtd文件约束*

​		*dtd文件的三种引入方式

```xml
<!DOCTYPE person SYSTEM "dtdTest1.dtd">//本地文件的进入
//xml文件里面定义的约束
<!DOCTYPE person[
	<!ELEMENT person (name, age)>
	<!ELEMENT name (#PCDATA)>
	<!ELEMENT age (#PCDATA)>
]>
<!DOCTYPE person PUBLIC "dtd名称" "dtd文档的URL" > //外部文件的引入
```

​	*dtd文件中，复杂元素<!ELEMENT person (name, age)>的括号中，用“,” 隔开，表示顺序
​			  <!ELEMENT person (name | age)>， 用“|”隔开，表示只能出现一个

​	*dtd复杂元素指示器

​		+, 表示标签可以使用一次或多次

​		?, 表示标签可以使用零次或一次 

​		*, 表示标签可以使用任意次

​	*2.schema文件约束*

​		XMLSchema复杂元素指示器

​			*ALL，元素只能出现一次

​			*Choice， 只能出现其中一个

​			*Sequence,  元素按照顺序出现

​			*maxOccurs="unbounded" 表示出现次数没限制

​			*<any></any>表示可以出现任意元素

​		也可以约束属性

​			*写在复杂元素里面

​			***写在</conplexType>之前

​				----<attribute name="id" type="int" use="required">

​				

​				