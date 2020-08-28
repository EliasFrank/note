SpringMVC思想是有一个前端控制器能拦截所有请求，并智能派发

这个前端控制器是一个servlet，应该在web.xml中配置这个servlet来拦截所有请求

一个方法处理一个请求

---

创建SpringMVC步骤

1. 导包

```
aop
beans
context
core
expression
web
webmvc
```

2. 写配置

   1. web.xml

      配置SpringMVC的前端控制器，指定SpringMVC配置文件位置

      ```xml
      <servlet>
          <servlet-name>SpringDispatcherServlet</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
          </init-param>
          <!--servlet启动加载，原来是第一次访问创建对象
          load-on-startup：服务器启动的时候创建对象，值越小，优先级越高，越先创建对象-->
          <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
          <servlet-name>SpringDispatcherServlet</servlet-name>
          <!--
      	/*和/都是拦截所有请求，/会拦截所有请求，但是不会拦截*.jsp,能保证jsp正常访问
          /*的范围更大，还会拦截*.jsp这些请求，一旦拦截jsp界面就不能显示了
      	处理*.jsp是tomcat做的事  所有项目中的web.xml都继承于tomcat的web.xml
      		DefaultServlet是tomcat中处理静态资源的
		除了jsp和servlet，剩下的都是静态资源
      		index.html：静态资源，tomcat就会在服务器下找到这个资源并返回
      		我们前端控制器的/禁用了tomcat服务器中的defaultServlet
      	1. 服务器的web.xml中，有一个defaultservlet是url-pattern=/
      	2. 我们的配置中前端控制器url-pattern=/	
      			静态资源会来到DispatcherServlet（前端控制器）看哪个方法的RequestMapping是index.html
      	3. 我们没有覆盖服务器中的jspservlet的配置
      	4. /* 直接拦截所有请求；我们写/；也是为了迎合后来Rest风格的URL地址
      	-->
          <url-pattern>/</url-pattern>
      </servlet-mapping>
      ```
      
   2. applicationContext.xml
   
      ```xml
      <!--
          视图解析器自动拼串
          <property name="prefix" value="/pages/"/>
          <property name="suffix" value=".jsp"/>
          （前缀）/pages/+return的字符串+.jsp
          -->
          <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
              <property name="prefix" value="/pages/"/>
              <property name="suffix" value=".jsp"/>
          </bean>
      ```
   
3. 测试

---

**helloworld细节**

1. 运行流程：

   1. 客户端点击链接会发送http://localhost:8080/srpingmvc/hello请求
   2. 来到tomcat服务器
   3. SpringMVC的前端控制器收到所有请求
   4. 看请求地址和@RequestMapping标注的哪个匹配，来找使用哪个类的哪个方法处理
   5. 前端控制器找到了目标处理器类和目标方法，直接利用反射执行目标方法
   6. 方法执行完成后会有一个返回值，SpringMVC认为这个返回值就是要去的页面地址
   7. 拿到方法返回值以后；用视图解析器进行拼串得到完整的页面地址
   8. 拿到页面地址，前端控制器帮我们转发到页面

2. RequestMapping("/hello")

   告诉SpringMVC，这个方法用来处理什么请求

   这个/是可以省略的，即使省略了，也是默认从当前项目下开始

   习惯加上比较好 /hello

   注意：如果requestmapping加了/ 那么访问他的链接等不需要加上：\<a href="hello">..\</a>，要么就两个地方都不加/，不然会报错404

   如果连接需要全路径，则需要\<a href="/项目名/hello">..\</a>

3. 

---

**@RequestMapping**

属性：

1. value

2. method

   ```
   限定请求方式：GET、POST
   HRPP中的所有请求方式GET, HEAD, POST,PUT,PATCH,DELETE, OPTIONS,TRACE;
   method=RequestMethod.POST: 只接受这种类型的请求，默认是什么都可以
   ```

   

3. params

   ```
   规定请求参数
   params和headers支持持简单的表达式
   	1. patam1：表示请求必须包含名为param1的请求参数
   		eg;params={"username"}：发送请求的时候必须带上一个名为username的参数，没带会404
   	2. !patam1：表示请求不能包含名为param1的请求参数
   		eg;params!={"username"}：发送请求的时候不能带上一个名为username的参数，带了会404
   	3. patam1!=123：表示请求不能包含名为param1的请求参数
   		eg;params={"username!=123"}：发送请求的时候不能带上一个名为username,值为123的参数，带了会400(注意：可以不带username，但是带了username，值必须不能为123)
   	4. {"param1=value1","param2"}: 请求必须包含名为param1和param2的参数，却param1的值为value
   		eg：params={"username!=123", "pwd", "!age"} 请求的username不能有123，而且必须有pwd，不能有age
   ```

   

4. headers

   ```
   指定能够访问的浏览器
   User-Agent : 浏览器信息
   让火狐能访问，让谷歌不能访问
    @RequestMapping(value = "test04", headers = {"User-Agent=" +
               "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:78.0) Gecko/20100101 Firefox/78.0"})
               
   谷歌：
   User-Agent: Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/83.0.4103.106 Mobile Safari/537.36
   火狐：
   User-Agent
   	Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:78.0) Gecko/20100101 Firefox/78.0
   
   ```

5. consumes：只接受内容类型时哪种的请求，规定请求头中的Content-Type

6. produces：告诉浏览器返回的内容类型是什么，给响应头中加上 Content-Type:text/charset=utf-8;



模糊匹配

URL地址可以写模糊的通配符

​	？：能替代任意一个字符

​	\* ： 能替代任意多个字符和一层路径

​	\\**：能替代多层路径

```
模糊和精确匹配情况下，精确优先
```

路径上可以有占位符，但是只能占一层路径

```

```



---













