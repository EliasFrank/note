1. 修改和server有关的配置（ServerProperties）

   ```properties
   server.port = 8081
   server.context-path=/crud
   server.tomcat.uri-encoding = UTF-8
   
   //通用的servlet容器设置
   server.xxx
   //tomcat的设置
   server.tomcat.xxx
   ```

2. 编写一个WebServerFactoryCustomizer：嵌入式的servlet容器的定制器，来修改servelt容器

   ```java
   @Bean//配置嵌入式的servlet容器
   public WebServerFactoryCustomizer<ConfigurableWebServerFactory> webServerFactoryCustomizer(){
           return new WebServerFactoryCustomizer<ConfigurableWebServerFactory>() {
               @Override
               public void customize(ConfigurableWebServerFactory factory) {
                   factory.setPort(8088);
               }
           };
       }
   ```


### 注册web三大组件（servlet、listener、filter）

由于springboot默认是以jar包的方式启动嵌入式的servlet容器来启动springboot的web应用，没有web.xml文件

注册三大组件用以下方式

ServletRegistrationBean

FilterRegistrationBean

ServletListenerRegistrationBean

```java
//注册三大组件
    @Bean
    public ServletRegistrationBean myServlet(){
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(new MyServlet(),"/myServlet");
        return registrationBean;
    }
    @Bean
    public FilterRegistrationBean myFilter(){
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
        filterRegistrationBean.setFilter(new MyFilter());
        filterRegistrationBean.addUrlPatterns("/hello", "/myServlet");
        return filterRegistrationBean;
    }
    @Bean
    public ServletListenerRegistrationBean myListener(){
        ServletListenerRegistrationBean servletContextListener = new ServletListenerRegistrationBean();
        servletContextListener.setListener(new MyListener());
        return servletContextListener;
    }
```

springboot帮我们自动SpringMVC的时候，自动注册SpringMVC的前端控制器，dispatcherServlet

---

### springboot支持切换其他servlet容器（tomcat、jetty、undertow）

默认使用tomcat

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!--如果使用别的容器，则需要要tomcat排除并添加其他容器的依赖-->
```

jetty

![1600848692038](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1600848692038.png)

undertow同上

---

### 嵌入式servlet容器自动配置原理

1. springboot根据导入的依赖情况，给容器中添加相应的EmbeddedServletContainerFactory[TomcatEmbeddedServletContainerFactory]
2. 容器中某个组件要创建对象就会惊动后置处理器EmbeddedServletContainerCustomizerBeanPostProcessor，只要是嵌入式的servlet容器工厂，后置处理器就会工作
3. 后置处理器，从容器中获取所有的EmbeddedServletContainerCustomizer，调用定制器的定制方法

---

### 嵌入式servlet容器启动原理

 获取嵌入式的servlet容器工厂

1. springboot应用启动运行run方法

2. refreshContext(context)；springboot刷新ioc容器[创建ioc容器对象，并初始化容器，创建容器中的每一个组件]，如果是web应用创建AnnotationConfigEmbeddedWebApplicationContext，否则创建AnnotationConfigApplicationContext

3. refresh(context)刷新刚才创建好的ioc容器

4. onRefresh();web的ioc容器重写了onRefresh方法

5. webioc容器会创建嵌入式的servlet容器：createEmbeddedServletContainer();

6. 获取嵌入式的servlet容器工厂

   从ioc容器中获取EmbeddedServletContainerFactory，，TomcatEmbeddedServletContainerFactory创建对象，后置处理器一看是这个对象，就获取所有的定制器来先定制servlet容器相关配置

7. 使用容器工厂获取嵌入式的servlet容器

8. 嵌入式的servlet容器创建对象并启动servlet容器

   先启动嵌入式的servlet容器，再将ioc容器中剩下没有创建出来的对象获取出来，ioc容器启动创建嵌入式的servlet容器

---

### 使用外置的servlet容器

嵌入式servlet容器：应用打成可执行的jar

​	优点：简单、便捷

​	缺点：默认不支持jsp、优化定制比较复杂（使用定制器【ServerProperties、自定义EmeddedServletContainerCustomizer】，自己编写嵌入式servlet容器的创建工厂【EmbeddedServletContainerFactory】）

外置的servlet容器，外面安装tomcat--应用war包的方式打包

步骤：

	1. 必须创建一个war项目（利用idea创建好目录结构）
 	2. 将嵌入式的tomcat指定为provided
 	3. 必须编写一个SpringBootServletInitializer的子类，并调用configure方法
 	4. 启动服务器就可以使用

 