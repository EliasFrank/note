### 使用springboot

1. 创建springboot应用，选中我们需要的模块
2. springboot已经默认将这些场景配置好了，只需要在配置文件中指定少量配置就可以运行起来
3. 自己编写业务代码

````
xxxxAutoConfiguration：帮我们给容器中自动配置组件
xxxxProperties：配置类来封装配置文件的内容
````

---

### springboot对静态资源的映射规则

```java
@ConfigurationProperties(prefix = "spring.resources", ignoreUnknowFields = false)
public class ResourceProperties implements ResourceLoaderAware{
    //可以设置和静态资源有关的参数，缓存时间等
```



````java
@Override
		public void addResourceHandlers(ResourceHandlerRegistry registry) {
			if (!this.resourceProperties.isAddMappings()) {
				logger.debug("Default resource handling disabled");
				return;
			}
			Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
			CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
			if (!registry.hasMappingForPattern("/webjars/**")) {
				customizeResourceHandlerRegistration(registry.addResourceHandler("/webjars/**")
						.addResourceLocations("classpath:/META-INF/resources/webjars/")
						.setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
			}
			String staticPathPattern = this.mvcProperties.getStaticPathPattern();
			if (!registry.hasMappingForPattern(staticPathPattern)) {
				customizeResourceHandlerRegistration(registry.addResourceHandler(staticPathPattern)
						.addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations()))
						.setCachePeriod(getSeconds(cachePeriod)).setCacheControl(cacheControl));
			}
		}

//配置欢迎页映射

@Bean
		public WelcomePageHandlerMapping welcomePageHandlerMapping(ApplicationContext applicationContext,
				FormattingConversionService mvcConversionService, ResourceUrlProvider mvcResourceUrlProvider) {
			WelcomePageHandlerMapping welcomePageHandlerMapping = new WelcomePageHandlerMapping(
					new TemplateAvailabilityProviders(applicationContext), applicationContext, getWelcomePage(),
					this.mvcProperties.getStaticPathPattern());
			welcomePageHandlerMapping.setInterceptors(getInterceptors(mvcConversionService, mvcResourceUrlProvider));
			welcomePageHandlerMapping.setCorsConfigurations(getCorsConfigurations());
			return welcomePageHandlerMapping;
		}
````

1. 所有/webjars/**， 都去classpath:/MEAT-INF/resource/webjars找资源；

​			webjars：以jar包的方式引入静态资源

![1599897408209](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1599897408209.png)

2. "/**"访问当前项目的任何资源（静态资源的文件夹）

   ```
   "classpath:/META-INF/resources/"
   "classpath:/resources/"
   "classpath:/static/"
   "classpath:/public/"
   "/":当前项目的根路径
   ```

   localhost:8080/abc === 去静态资源文件夹里面找abc

3. 欢迎页：静态资源文件夹下的所有index.html页面，被"/**"映射（优先级resource > static > public）

   localhost:8080/ 找index页面

4. 所有的**/favicon.ico都是在静态资源文件下找

---

### 模板引擎

jsp、velocity、freemarker、thymeleaf

![1599908845787](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1599908845787.png)

1. 引入thymeleaf

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-thymeleaf</artifactId>
   </dependency>
   切换thymeleaf版本
   
   ```

2. Thymeleaf使用&语法

   我们只需要吧HTML界面放在classpath:/templates/，thymeleaf就能自动渲染

   1. 导入thymeleaf

      ```
      <html lang="en" xmlns:th="http://www.thymeleaf.org">
      ```

   2. 使用thymeleaf

      ```html
       <!--th:text 将div里面的文本内容设置为-->
          <div th:utext="${hello}">there is the place review the welcome info</div>
          <div th:text="${user.get(1)}"></div>
          <hr/>
          <!--<div th:each="one:${user}">
              <div th:text="${one}"></div>
          </div>-->
          <h4 th:each="one:${user}" th:text="${one}"></h4>
          <hr/>
          <h4 th:each="one:${user}">[[${one}]]</h4>
      ```

      

![1599911332820](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1599911332820.png)

---

### SpringMVC自动配置

springboot自动配置好了SpringMVC 

以下是springboot对SpringMVC的默认

* Inclusion of contentNegotiatingViewResolver and BeanNameViewResolver beans
  * 自动配置了ViewResolver（视图解析器：根据方法的返回值得到视图对象（View），视图对象决定如何渲染（转发？重定向？））
  * ContentNefotiatingViewresolver：组合所有的视图解析器
  * 如何定制：我们可以自己给容器中添加一个视图解析器，自动将其组合进来
  
* Support for serving static resources, including support for WebJars 静态资源文件夹路径， webjars

* Static index.html support 静态首页访问

* Custom Favivon support

* 自动注册了of Converter, GenericConverter, Formatter beans

  * Converter：转换器；public String hello(User user); 类型转换使用Converter

  * Dormatter 格式化器：2017.12.17 === Date；

  * ```java
    @Bean
    @ConditionalOnProperty(prefix = "spring.mvc", name = "date-format")//在文件中配置日期格式的规则
    public Formatter<Date> dateFormatter(){
        return new DateFormatter(this.mvcProperties.getDateFormat())//日期格式化组件
    }
    ```

    自己添加的格式器，转换器，我们只需要放在容器中即可

* Support for HttpMessageConverters

  * HttpMessageConverter : SpringMVC用来转换Http请求和响应的；User---Json

  * HttpMessageConverters是从容器中确定；获取所有的HttpMessageConverter

    自己给容器中添加HttpMessageConverter，只需要将自己的组件注册容器中

* Automatic registration of MessageCodesResolver 定义错误代码生成规则

* Automatic use of a ConfigurableWebBindingInitializer来替换默认的（添加到容器）

  ```
  初始化WebDataBinder
  请求数据 ===== JavaBean
  ```

  org.springframework.boot.autoconfigure.web：自动配置web的所有自动配置场景

扩展SpringMVC

```xml
<mvc:view-controller path="/hello" view-name="success"/>
<mvc:interceptors>
	<mvc:interceptor>
    	<mvc:mapping path="/hello"/>
        <bean></bean>
    </mvc:interceptor>
</mvc:interceptors>
```

**编写一个配置类（@Configuration），是WebMvcConfigurerAdapter类型，不能标注@EnableWebMvc**

既保留了所有的自动配置，也能用我们扩展的配置

```java
//使用WebMvcConfigurerAdapter可以来扩展SpringMVC的功能
@Configuration
public class MyConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //浏览器发送  /jxau  请求来到success
        registry.addViewController("/jxau").setViewName("success");
    }
}
```

原理：

1. WebMvcConfigurer是SpringMVC的自动配置类

2. 在做其他自动配置时会导入，@Import(EnableWebMvcConfiguration.class)

3. 容器中所以的WebMvcConfigurer都会一起起作用

4. 我们的配置类也会被调用

   SpringMVC的自动配置和我们的扩展配置都会起作用



**全面接管SpringMVC**

**我们可以在配置类中添加@EnableWebMvc即可**

springboot对SpringMVC的自动配置都不需要了，所有都是我们自己配；所有的SpringMVC的自动配置都失效

---

### 如何修改springboot的默认配置

模式：

1. springboot在自动配置很多组件的时候，先看容器中有没有用户自己配置的(@Bean、@Conponent) 如果有就用用户的配置，如果没有，才自动配置；如果有些组件可以有多个（ViewResolver）将用户配置的和自己默认的组合起来
2. 在springboot中会有非常多的xxxConfigurer帮助我们进行扩展配置

---

### RestfulCRUD

SpringMVC国际化步骤

1. 编写国际化配置文件
2. 使用ResourceBundleMessageSource管理国际化资源文件
3. 在页面使用fmt:message取出国际化内容

springboot国际化步骤：

1. 编写国际化配置文件，抽取页面需要显示的国际化信息

   ![1599994795894](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1599994795894.png)

2. SpringBoot自动配置好了管理国际化资源文件的组件

3. 去页面获取国际化的值

```html
<!DOCTYPE html>
<html lang="en"  xmlns:th="http://www.thymeleaf.org">
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
		<meta name="description" content="">
		<meta name="author" content="">
		<title>Signin Template for Bootstrap</title>
		<!-- Bootstrap core CSS -->
		<link href="asserts/css/bootstrap.min.css" th:href="@{/webjars/bootstrap/4.0.0/css/bootstrap.css}" rel="stylesheet">
		<!-- Custom styles for this template -->
		<link href="asserts/css/signin.css" th:href="@{/asserts/css/signin.css}" rel="stylesheet">
	</head>
	<body class="text-center">
		<form class="form-signin" action="dashboard.html" th:action="@{/user/login}" method="post">
			<img class="mb-4" th:src="@{/asserts/img/bootstrap-solid.svg}" src="asserts/img/bootstrap-solid.svg" alt="" width="72" height="72">
			<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
			<!--判断-->
			<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
			<label class="sr-only" th:text="#{login.username}">Username</label>
			<input type="text"  name="username" class="form-control" placeholder="Username" th:placeholder="#{login.username}" required="" autofocus="">
			<label class="sr-only" th:text="#{login.password}">Password</label>
			<input type="password" name="password" class="form-control" placeholder="Password" th:placeholder="#{login.password}" required="">
			<div class="checkbox mb-3">
				<label>
          			<input type="checkbox" value="remember-me"/> [[#{login.remember}]]
        		</label>
			</div>
			<button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.btn}">Sign in</button>
			<p class="mt-5 mb-3 text-muted">© 2017-2018</p>
			<a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
			<a class="btn btn-sm" th:href="@{/index.html(l='en_US')}">English</a>
		</form>
	</body>
</html>
```

效果：根据浏览器语言设置的信息切换了国际化

原理：国际化Local（区域信息对象）；LocalResolver（获取区域信息对象）

默认就是根据请求头带来的区域信息获取Local进行国际化



**登录：**

开发期间模板引擎页面修改以后，要实时生效

1. 禁用模板引擎的缓存

   ```properties
   #禁用缓存
   spring.thymeleaf.cache=false
   ```

2. 页面修改完成后ctrl+f9重新编译

登录错误消息的显示

```html
<p style="color: red" th:text="${msg}" th:if = "${not #strings.isEmpty(msg)}"></p>
```

CRUD-员工列表

1. RestfulCRUD：CRUD满足rest风格

2. URI：/资源名称/资源标识   HTTP请求方式区分对资源CRUD操作

   |      | 普通CURD（uri来区分操作） | RestfulCRUD      |
   | ---- | ------------------------- | ---------------- |
   | 查询 | getEmp                    | emp--GET         |
   | 添加 | addEmp?xxx                | emp--POST        |
   | 修改 | updateEmp?id=xxx&xx=xx    | emp/{id}--PUT    |
   | 删除 | deleteEmp?id=xxx          | emp/{id}--DELETE |

   实验的请求架构

   |                      | 请求URI  | 请求方式 |
   | -------------------- | -------- | -------- |
   | 查询所有员工         | emps     | GET      |
   | 查询某个员工（修改） | emp/{id} | GET      |
   | 来到添加界面         | emp      | GET      |
   | 添加员工             | emp      | POST     |
   | 修改员工             | emp      | PUT      |
   | 删除员工             | emp/{id} | DELETE   |

   3.thymeleaf公共页面元素抽取

   ```
   1. 抽取公共片段
   <div th:fragment="copy">
   the good
   <div>
   
   2. 引入公共片段
   <div th:insert="~{footer :: copy}"></div>
   ~{templatename::selector} : 模板名：：选择器
   ~{templatename::fragmentname} ：模板名::片段名
   ```

   