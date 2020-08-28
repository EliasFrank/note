1. 导包

   ```xml
     <dependencies>
       <!--ioc核心包-->
       <dependency>
         <groupId>commons-logging</groupId>
         <artifactId>commons-logging</artifactId>
         <version>1.2</version>
       </dependency>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-core</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-expression</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-beans</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
   
       <!--AOP核心-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-aop</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-aspects</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
       <dependency>
         <groupId>net.sourceforge.cglib</groupId>
         <artifactId>com.springsource.net.sf.cglib</artifactId>
         <version>2.2.0</version>
       </dependency>
       <dependency>
         <groupId>org.aopalliance</groupId>
         <artifactId>com.springsource.org.aopalliance</artifactId>
         <version>1.0.0</version>
       </dependency>
       <dependency>
         <groupId>aspectj</groupId>
         <artifactId>aspectjweaver</artifactId>
         <version>1.5.4</version>
       </dependency>
   
       <!--jdbc核心-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-jdbc</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-orm</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-tx</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
   
       <!--测试-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-test</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
   
       <!--SpringMVC核心包-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-web</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-webmvc</artifactId>
         <version>5.2.7.RELEASE</version>
       </dependency>
   
       <!--文件上传下载-->
       <dependency>
         <groupId>commons-fileupload</groupId>
         <artifactId>commons-fileupload</artifactId>
         <version>1.4</version>
       </dependency>
       <dependency>
         <groupId>commons-io</groupId>
         <artifactId>commons-io</artifactId>
         <version>2.5</version>
       </dependency>
   
       <!--jstl-->
       <dependency>
         <groupId>...jstl</groupId>
         <artifactId>jstl</artifactId>
         <version>1.2</version>
       </dependency>
   
       <!--JSR303校验-->
       <dependency>
         <groupId>org.hibernate.validator</groupId>
         <artifactId>hibernate-validator</artifactId>
         <version>6.0.5.Final</version>
       </dependency>
       <!-- <dependency>
              <groupId>org.hibernate.validator</groupId>
              <artifactId>hibernate-validator-annotation-processor</artifactId>
              <version>6.1.5.Final</version>
            </dependency>-->
       <dependency>
         <groupId>com.fasterxml</groupId>
         <artifactId>classmate</artifactId>
         <version>1.5.1</version>
       </dependency>
       <dependency>
         <groupId>javax.validation</groupId>
         <artifactId>validation-api</artifactId>
         <version>2.0.1.Final</version>
       </dependency>
       <dependency>
         <groupId>org.jboss.logging</groupId>
         <artifactId>jboss-logging</artifactId>
         <version>3.4.1.Final</version>
       </dependency>
   
       <!--ajax-->
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-databind</artifactId>
         <version>2.9.9.3</version>
       </dependency>
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-core</artifactId>
         <version>2.9.9</version>
       </dependency>
       <dependency>
         <groupId>com.fasterxml.jackson.core</groupId>
         <artifactId>jackson-annotations</artifactId>
         <version>2.9.9</version>
       </dependency>
   
       <!--mybatis-->
       <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis</artifactId>
         <version>3.5.5</version>
       </dependency>
       <!-- <dependency>
            <groupId>apache-log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.8</version>
        </dependency>-->
   
       <!--数据源、驱动-->
       <dependency>
         <groupId>mysql</groupId>
         <artifactId>mysql-connector-java</artifactId>
         <version>8.0.20</version>
       </dependency>
       <dependency>
         <groupId>com.mchange</groupId>
         <artifactId>mchange-commons-java</artifactId>
         <version>0.2.20</version>
       </dependency>
       <dependency>
         <groupId>com.mchange</groupId>
         <artifactId>c3p0</artifactId>
         <version>0.9.5.5</version>
       </dependency>
   
       <!--测试-->
       <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>4.11</version>
         <scope>test</scope>
       </dependency>
         <!--mybatis和spring整合的包-->
       <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis-spring</artifactId>
         <version>2.0.4</version>
       </dependency>
     </dependencies>
   ```

2. 写配置

   1. web.xml

      ```xml
      <!DOCTYPE web-app PUBLIC
       "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
       "http://java.sun.com/dtd/web-app_2_3.dtd" >
      
      <web-app>
        <display-name>Archetype Created Web Application</display-name>
      
        <!--配置spring容器启动-->
        <context-param>
          <param-name>contextConfigLocation</param-name>
          <!--指定spring配置文件位置-->
          <param-value>classpath:spring/applicationContext.xml</param-value>
        </context-param>
      
        <!--字符编码-->
        <filter>
          <filter-name>CharacterEncodingFilter</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
          <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
          </init-param>
          <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
          </init-param>
        </filter>
        <filter-mapping>
          <filter-name>CharacterEncodingFilter</filter-name>
          <url-pattern>/*</url-pattern>
        </filter-mapping>
      
        <!--支持rest风格的过滤器-->
        <filter>
          <filter-name>HiddenHttpMethodFilter</filter-name>
          <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
        </filter>
        <filter-mapping>
          <filter-name>HiddenHttpMethodFilter</filter-name>
          <url-pattern>/*</url-pattern>
        </filter-mapping>
        <listener>
          <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>
      
        <!--配置SpringMVC的前端控制器-->
        <servlet>
          <servlet-name>springDispatcherServlet</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/sprinfmvc.xml</param-value>
          </init-param>
          <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
          <servlet-name>springDispatcherServlet</servlet-name>
          <url-pattern>/</url-pattern>
        </servlet-mapping>
      </web-app>
      ```

   2. spring配置

      ```xml
   <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
      
          <!--spring除了controller不要，其他的dao、service都要-->
          <context:component-scan base-package="com.jxau">
              <!--扫描排除不写use-default-filters="false"-->
              <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
          </context:component-scan>
      
          <!--导入外部配置文件-->
          <context:property-placeholder location="dbprops.properties"/>
          <!--配置数据源-->
          <bean id="ds" class="com.mchange.v2.c3p0.ComboPooledDataSource" >
              <property name="user" value="${jdbc.user}"></property>
              <property name="password" value="${jdbc.password}"></property>
              <property name="driverClass" value="${jdbc.driver}"></property>
              <property name="jdbcUrl" value="${jdbc.url}"></property>
              <property name="maxPoolSize" value="${jdbc.maxPoolSize}"/>
              <property name="minPoolSize" value="${jdbc.minPoolSize}"/>
          </bean>
      
          <!--配置使用mybatis操作数据库-->
          <bean class="org.mybatis.spring.SqlSessionFactoryBean">
              <property name="configLocation" value="classpath:mybatis/mybatis-config.xml"/>
              <property name="dataSource" ref="ds"/>
              <property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
          </bean>
          <!--把每一个dao接口的实现加入到ioc容器中-->
          <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
              <property name="basePackage" value="com.jxau.dao"/>
          </bean>
          <!--配置事务控制：配置事务管理器，让他控制住数据源里面的连接的关闭和提交-->
          <bean id="manager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
              <property name="dataSource" ref="ds"/>
          </bean>
      
          <!--基于xml配置，配置事务，哪些方面切入事务还要写切入点表达式-->
          <aop:config>
              <aop:pointcut id="txPoint" expression="execution(* com.jxau.service.*.*(..))"/>
          </aop:config>
      
          <!--配置事务增强，事务属性，事务建议-->
          <tx:advice transaction-manager="manager">
              <!--配置事务属性-->
              <tx:attributes>
                  <tx:method name="*" rollback-for="java.lang.Exception"/>
                  <tx:method name="get*" read-only="true"/>
                  <tx:method name="insert*" isolation="READ_UNCOMMITTED"/>
              </tx:attributes>
          </tx:advice>
      </beans>
      ```
   
   3. SpringMVC配置
   
      ```xml
      <?xml version="1.0" encoding="UTF-8"?>
      <beans xmlns="http://www.springframework.org/schema/beans"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns:context="http://www.springframework.org/schema/context"
             xmlns:mvc="http://www.springframework.org/schema/mvc"
             xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd">
      
          <!--SpringMVC只扫描控制器，要禁用默认规则-->
          <context:component-scan base-package="com.jxau" use-default-filters="false">
              <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"></context:include-filter>
          </context:component-scan>
      
          <!--配置视图解析器-->
          <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
              <property name="defaultEncoding" value="utf-8"/>
              <property name="maxUploadSize" value="#{1024*1024*20}"/>
          </bean>
          <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
              <property name="suffix" value=".jsp"/>
              <property name="prefix" value="/WEB-INF/pages/"/>
          </bean>
          <!--扫静态资源-->
          <mvc:default-servlet-handler/>
      
          <!--扫动态资源-->
          <mvc:annotation-driven></mvc:annotation-driven>
      </beans>
      ```
   
   4. mybatis配置
   
      ```xml
      <!--spring和mybatis的整合-->
        <!--配置使用mybatis操作数据库-->
          <bean class="org.mybatis.spring.SqlSessionFactoryBean">
              <property name="configLocation" value="classpath:mybatis/mybatis-config.xml"/>
              <property name="dataSource" ref="ds"/>
              <property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
          </bean>
          <!--把每一个dao接口的实现加入到ioc容器中-->
          <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
              <property name="basePackage" value="com.jxau.dao"/>
          </bean>
      ```
   
      