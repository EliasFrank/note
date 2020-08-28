使用dom4j解析xml

​	*dom4j是一个组织，针对xml提供解析器dom4j

​	*dom4j不是javase的一部分，想要使用需要：

​			***导入dom4j提供的jar包

​			----创建一个文件夹lib

​			----复制jar包到lib下面

​			----右键点击jar包，build path --- add to buildpath

​			----看到jar包，变成奶瓶样子，表示导入成功

​	*得到document

​			SAXReader reader = new SAXReader();

​			Document document = reader.read(url);

使用dom4j查询xml

​	*查询所有name元素里面的值

​		*

​		1.创建解析器

​		2.得到document

​		3.得到根节点  getRootElement()

​		4.得到所有的p1标签

​				*element(qname)

​						**表示获取标签下面的第一个该名称的子标签

​						**qname:标签的名称

​				*elements(qname):

​						**获取标签下面是这个名称的所有子标签（一层）

​				*elements() 

​						**获取标签下面的所有一层子标签

​		5.得到name

​		6.得到name里面的值



使用dom4j实现添加操作

​	*在第一个p1标签的末尾添加一个元素<sex>male</sex>

​	*步骤

​		*1.创建解析器

​		*2.得到document

​		*3.得到根节点

​		*4.获取第一个p1

​				*使用element方法

​		*5.在p1下面添加袁术

​				*在p1上面直接使用addElement（“标签名称”）方法。放回一个Element

​		*6.在添加完成之后的元素下面添加文本

​				*在sex上直接使用setText(“文本内容”)方法



​		*7.回写xml

​				*格式化OutputFormat， 使用createPrettyPrint方法，表示一个漂亮的格式

​				*使用类XMLWriter直接new这个类，传递两个参数

​							***第一个参数是xml文件路径，new FileOutputStream("路径")

​							***第二个参数是格式化类的引用

使用dom4j实现在特定位置添加元素

​	*在第一个p1下面的age标签之前添加<school>jxau</school>

​	*步骤：

​		*1.创建解析器

​		*2.得到document

​		*3.得到根节点

​		*4.获取到第一个p1

​		*5.获取p1下面的所有的元素

​				**elements()方法，返回list集合

​			**使用list里面的方法，在特定位置添加元素

​						--首先创建元素，在元素下面创建文本

​								--使用DocumentHelper类方法createElement()方法创建标签

​								--把文本添加到标签下面，使用setText("文本内容")

​			*6.回写xml

使用dom4j实现删除节点的操作

​	*删除第一个p1下面的<school>jxau</school>

​	*步骤

​		*1.得到document

​		*2.得到根节点

​		*3.得到p1节点

​		*4.得到school元素

​		*5.通过p1节点的remove方法删除元素

​				*得到school的父节点

​					--第一种直接得到p1

​					--使用方法getParent得到

​				*删除操作

​					--在p1上通过remove方法删除节点

​		*6.回写xml

使用dom4j获取属性操作

​	*1.得到document对象

​	*2.得到根节点

​	*3.得到第一个p1元素

​	*4.得到p1里面的属性值

​			-p1.attributeValue("id1");

​			-在p1上面执行这个方法，里面的参数是属性名称

使用dom4j支持xpath的操作

​	*可以直接获取到某个元素

​	*第一种形式

​			/AAA/DDD/BBB  表示一层一层的，取到aaa下面的ddd下面的bbb

​	*第二种形式

​			//BBB   取到所有的名称为bbb的元素

​	*第三种形式

​			/*   取所有元素

​	*第四种形式

​			** BBB[1]  取第一个找到的bbb

​			** BBB[last()]  取最后一个bbb元素

​	*第五种形式

​			**  //BBB[@id]   找bbb元素里有id属性的

​	*第六种形式

​			**  //BBB[@id = 'b1'] 表示元素名称是bbb,在bbb上面有属性id,并且属性id的值为b1

使用dom4j支持xpath具体操作

​	**默认情况下，dom4j不支持xpath

​	**如果想要在dom4j里面使用xpath

​			** 第一步  ：需要引入支持xpath的jar包， 使用jaxen-1.1-beta-6.jar

​			** 需要把jar包导入到项目中

​	** 在dom4j里面提供里两个方法，用来支持xpath

​			**  selectNodes("xpath表达式")

​							-获取多个节点

​			**  selectSingleNode("xpath表达式")；

​							-获取一个节点

​			** 使用xpath实现： 查询xml中所有那么元素的值

​					** 所有那么元素的xpath表示： //name

​					**  使用selectNodes("//name")；

​					** 代码和步骤

​		

```java
		//1.得到document
		//2.直接使用selectNodes("//name")方法得到所有的元素值
		Document document = Dom4jUtils.getDocument();
		
		List<Node> list = document.selectNames("//name");
		
		for(Node node : list){
			String s = node.getText();
			System.out.println(s);
		}

```

使用xpath实现：获取第一个p1下面的name的值

​	*//p1[@id='aa'/name]

​	*使用到selectSingleNode("//p1[@p='aaa']/name")

​	*步骤和代码

​	

```java
		String path = "./src/xpath/person.xml";
		
		Document document = Dom4jUtils.getDocument(path);
		
		Node name = document.selectSingleNode("//p1[@id='aaa']/name");
		
		System.out.println(name.getText());
```

