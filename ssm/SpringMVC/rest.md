**REST**

* REST: 即Tepreesenttional State Transfer。（资源）表现层状态转化。是目前最流行的一种互联网软件架构。他结构其你清晰，符合标准，易于理解，扩展方便，所以正得到越来越多网站的采用。
* 资源（Resource）：网络上的一个实体，或者说是网络上的一个具体信息。他可以是一段文本、一张蹄片、一首歌曲、一种服务，总之，就是一个具体的存在。可以用一个URI（统一资源定位符）指向他，每种资源对应一个特定的URI。要获取这个资源，访问他的URI就可以，因此URI即为每一个资源的独一无二的识别符
* 表现层（Representation）：把资源具体呈现出来的形式，叫做他的表现层。比如，文本可以用txt格式表现，也可以用HTML格式、XML格式、JSON格式表现，甚至可以采用二进制格式。
* 状态转化（State Transfer）：每发出一个请求，就代表了客户端和服务器的一次交互过程，HTTP协议，是一个无状态协议，即所有的状态都保存在服务器端。因此，如果客户端想要擦操作服务武器，必须通过某种手段，让服务器端发生“状态转化”。而这种转化是建立在表现层之上的，所以就是“表现层状态转化“。具体说：HTTP协议里面，四个表示操作方式的动词：GET, POST, PUT, DELETE。他们分别对应四种基本操作：get用来获取资源，post用来新建资源，put用来更新资源，delete用来删除资源

---

rest：系统希望以非常简洁的URL地址来发请求

​			怎样表示对一个资源的增删改查用请求的方式来区分

/getBook?id=1：查询图书

/deleteBook?id=1：删除图书

/addbook         ： 添加图书



rest推荐：

/book/1   : GET 查询1号图书

/book/1   : DELETE 删除1号图书

/book       : POST    添加图书

系统的URL地址就这么来设计即可：

简洁的URL提交请求，以请求方式区分对资源操作

----

**从页面发起PUT、DELETE形式的请求，SpringMVC提供了对Rest风格的支持**

1. SpringMVC中有一个Filter，他可以吧普通的请求转化为规定形式的请求，首先需要配置这个filter

   ```xml
   <filter>
       <filter-name>HiddenHttpMethodFilter</filter-name>
       <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
     </filter>
     <filter-mapping>
       <filter-name>HiddenHttpMethodFilter</filter-name>
       <url-pattern>/*</url-pattern>
     </filter-mapping>
   ```

   

2. 建立一个post类型的表单， 表单中携带一个\_method参数，这个\_method的值就是DELETE、PUT

   ```
   高版本tomcat，rest支持有点问题，会对jsp的转发有限制，需要
   <%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true"%>
   ```

---

SpringMVC如何获取请求带来的各种信息

* 默认方式请求参数

  直接给方法形参上写一个和请求参数相同的变量。这个变量就来接受请求参数的值

  ```
  public String test02(String username, int age)
  hello?username=hl 获取的值为hl
  hello 获取的值为null
  
  ```

@RequestParam

```huoq
获取请求参数的，参数默认是必须带的
@RequestParam("user")String name
name = request.getParameter("user");

value ，required ， defaultValue 注解有三个参数可以选择

@ReqeustVariable("id")
获取的时路径中的值而不是参数值
```

@RequestHeader

```
@RequestHeader(value = "User-Agent")String user_agent
user_agent = request.getHeader("User-Agent")
如果请求头中没有这个值就会报错

value ，required ， defaultValue 注解有三个参数可以选择

```

@CookieValue

```
@CookieValue(value = "JSESSIONID")String jid
Cookie[] cookies = request.getCookie();
for(Cookie c : cookies){
	if(c.getName.equals("JSESSIONID"))
		sout(c.getValue());
}

如果请求头中没有这个值就会报错

value ，required ， defaultValue 注解有三个参数可以选择
```

---

如果我们的请求参数是一个POJO（自定义参数）

SpringMVC会自动的为这个POJO进行赋值

1. 将POJO中的每一个属性，从request参数中尝试获取出来，并封装即可（如果没找到对应参数，则默认封装null）
2. 还可以级联封装

---

**提交的数据可能有乱码**

请求乱码：

​	GET请求：改server.xml，在8080端口处URIEncoding=“UTF-8”

​	POST请求：

​		在第一次获取请求参数之前设置

```
request.setCharacterrEncoding("UTF-8");
```

​		自己写一个filter：SpringMVC有这个filter













