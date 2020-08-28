**spring和javaweb整合使用**

1. spring来控制事务（dao--jdbcTemplate）
2. 所有的组件Atuowired
3. 管理数据库

流程：

1. 导包

2. 写配置

   ```
   1. 将所有组件加入容器中
   	* @Controller: servlet层;目前不能标注在servlet层
   	* @Service ： 业务逻辑层
   	* @Repository：dao层
   	* @Compontent：其他组件
   2. 每个组件之间自动装配
   3.配置出声明式事务
   	事务管理器控制数据库连接池
   
   ioc容器创建和销毁都要在合适的时机完成
   项目启动：{
   	ioc创建完成
   }
   
   项目销毁{
   	ioc销毁
   }
   可以写一个监听器完成这个工作，
   spring已经帮我们做好了这个监听器
   
   <context-param>
   	<param-name>contextConfigLocation</param-name>
   	<param-value>classpath:ioc.xml</param-value>
   </context-param>
   <listener>
   	<listener-class>org.....ContextLoaderListener</listener-class>
   </listener>
   
   ```

3. 测试