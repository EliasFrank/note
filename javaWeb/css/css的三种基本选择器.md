1）css有三种基本选择器

```shell
i. 标签选择器
	div{
		background-color:red;
	}
ii. class选择题
	xxx.class{
		background-color:blue;
	}
	.class{
		color:blue;
	}
ii.id选择器
	xxx.id{
		background-color:blue;
	}
	.id{
		color:red;
	}
```

2）选择器的优先级：

​	xxx标签里面的style属性值  > id选择器 > class选择器 > 标签选择器

3）扩展选择器

​	

```shell
i.关联选择器：
	<div><p>jfsafsafsd</p></div>
	<p>15616551</p>
	设置div里面的p标签的样式而不改变第二句p标签里面的样式，可以使用关联选择器：
		div p{
			background-color:red;
		}
ii.组合选择器
	<div>gdfgdsaferf</div>
	<p>418618685</p>
	div,p{
		background-color:red;
	}
iii.伪元素选择器

```

