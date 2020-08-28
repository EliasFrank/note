通过SpringMVC的表单标签可以实现将模型数据中的属性和HTML表单元素相绑定，以实现表单数据更便捷编译和表单值的回显

```html
<%@taglib prefix="form" uri=""/>
<form:form action="">
    <!--
		path就是原来html-input的name项，需要写
		path：
			1. 当做原生的name项
			2. 自动回显隐含模型中某个对象对应的这个属性的值
		
		SpringMVC认为，表单数据中的每一项最终都是要回显的
		path指定的是一个属性：这个属是从隐含模型（请求域中取出的某个对象中的属性）
							请求域中必须有一个对象，拥有这个属性
		modelAttribute=""
		1. 以前我们表单标签会从请求域中获取一个command对象，吧这个对象中的没有给属性对应的显示出来
		2. 可以告诉SpringMVC不要去取command的值，放了一个modelAttribute指定的值,取对象用的key就用modelAttribute指定
	-->
	last name : <form:input path="lastName"/><br/>
    ......
    <!--
	items="" : 指定要遍历的集合；自动遍历，遍历出的每一个元素是一个book对象
	itemLabel="属性名"：指定遍历出的这个对象的哪个属性时作为option标签体的值
	itemValue="属性名" 指定刚才遍历出来的这个对象的哪个属性时作为要提交的value的值
	-->
    <from:select path="" items="${authors}" itemLabel="name" itemValue="id">			</from:select><br/>
</form:form>
```

























