**框架：半成品软件**

​	高度抽取可重用代码的一种设计；高度的通用性

​	书城：WebUtils.java, BaseServlet, Filter

​	打包：bookstore,jar(工具类)

​	框架：抽取成一种高度可重用的、事务控制、强大的servlet、项目中的一些工具。。。

​			多个可重用模块的集合，形成一个某个领域的整体解决方案

---

**spring**

容器（可以管理所有的组件（类））框架

核心：IOC和AOP

![1592636964850](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1592636964850.png)

------

**IOC** :(inversion of control)   控制反转

​	控制：资源的获取方式：

​		主动式：（要什么资源都可以自己创建即可）

```java
BookServlet{
	BookService bs = new BookService();
	AirPlane ap = new AirPlane();//复杂对象的创建是比较庞大的工程；
}
```

​		被动式：资源的获取不是我们自己创建，而是交给容器来创建和设置

```java
BookServlet{
    @Autowired
	BookService bs;
	public void test1(){
		bs.checkout();
	}
}
```

容器：管理所有的组件（有功能的类）；

​	假设，BookServlet受容器管理，BookService也受容器管理；容器可以自动的探查出那些组件（类）需要用到另一些组件（类）；容器帮我们创建BookService对象，并把BookService对象赋值过去；

容器：主动new资源变为被动的接受资源



DI（Dependency Injection）依赖注入

​	容器能知道哪个组件运行的时候，需要另外一个类，容器通过反射的形式，将容器中准备好的BookService对象注入（利用发反射给属性赋值）到BookServlet中

只要容器管理的组件，都能使用容器提供的强大功能

------

**泛型依赖注入原理**

 ![1593586584261](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1593586584261.png)

------

ioc是一个容器，帮我们管理所有的组件

1. 依赖注入：@Autowired：自动赋值
2. 某个组件要使用spring提供的更多（IOC、AOP）必须加入到容器
3. 容器启动，创建所有单实例bean
4. autowired自动装配的时候，是从容器中找这些符合要求的bean
5. 调试spring的源码，容器其实就是一个map，这个map中保存所有创建好的bean，并提供外界获取功能

---

**ioc基本操作**

```java
/**
     * 几个细节
     * ApplicationContext（ioc容器的接口）
     * 1. new ClassPathXmlApplicationContext("text.xml"):ioc容器的配置文件在类路径下。
     *    new FilePathXmlApplicationContext("c:\\test\\test.xml")ioc容器的配置文件在磁盘路径下。
     * 2. 给容器中注册一个组件，我们也从容器中按照id拿到了这个组件的对象。
     *    组件的创建工作，是由容器完成的
     * 3. person对象是什么时候创建好的？
     *     容器中对象的创建在容器创建完成的时候就已经创建好了。
     * 4.同一组件在ioc中是单实例的，容器启动完成前就创建好的。
     * 5.容器中如果没有这个组件，获取这个组件会报错
     *   Exception in thread "main" org.springframework.beans.factory.NoSuchBeanDefinitionException:
     *   No bean named 'person3' available
     * 6.ioc容器在创建这个组件对象的时候，会利用setter方法为Javabean的属性赋值
     * 7.Javabean的属性名是由类中的setter和getter方法决定的
     *    所有getter和setter都由系统自动生成。
     */ 
```

注册组件涉及到的类

```java
public class Person {

    //基本类型直接使用，
    //<property name="name" value="zs"/>自动进行类型转换
    private String name = "张三";
    private int age;
    private String gender;
    private String email;

    private Car car;
    private List<Book> books;
    private Map<String, Object> map;
    private Properties properties;
}
public class Car {
    private String name;
    private double price;
    private String color;
}
public class Book {
    private String name;
    private String author;
}
public class Airplane {
    private String name;
    private String aaa;
    private String bbb;
    private String ccc;
    private int age;
}
```

一个bean标签注册一个组件
class：写要注册的组件的全类名
id:这个对象的唯一标识

```xml
 <bean id="person" class="com.xxxx.bean.Person">
        <!--
            使用property标签为person对象的属性赋值
            name="name" 指定属性名
            value="alice" 为属性赋值
        -->
        <property name="name" value="alice"/>
        <property name="age" value="17"/>
        <property name="gender" value="男"/>
</bean>
```

也可以调用有参构造器为对象赋值时，可以省略name属性，那么会严格按照有参构造器的参数顺序进行赋值

```xml
<bean id="person03" class="com.jxau.bean.Person">
    <!--调用有参构造器为对象赋值-->
    <constructor-arg name="age" value="1"/>
    <constructor-arg name="email" value="emial@hl.com"/>
    <constructor-arg name="name" value="张三"/>
    <constructor-arg name="gender" value="male"/>
</bean>
<bean id="person04" class="com.xxxx.bean.Person">
     <!--调用有参构造器为对象赋值时，可以省略name属性，那么会严格按照有参构造器的参数顺序进行赋值-->
    <constructor-arg value="elias"/>
    <constructor-arg value="15"/>
    <constructor-arg value="female"/>
    <constructor-arg value="elias@hl.com"/>
</bean>
```

调用有参构造器为对象赋值时，如果不指定name，那么会按照有参构造器的参数顺序进行赋值但是可以通过加一个index属性为其指定顺序为参数指定索引，从0开始

```xml
<bean id="person05" class="com.xxxx.bean.Person">
        <constructor-arg value="elias"></constructor-arg>
        <constructor-arg value="10"></constructor-arg>
        <!--指定赋值顺序-->
        <constructor-arg value="elias@hl.com" index="3"></constructor-arg>
        <constructor-arg value="man" index="2"></constructor-arg>
</bean>
```

构造器出现重载的情况，可以用type来指定类型

```xml
<bean id="person06" class="com.xxxx.bean.Person">
        <constructor-arg value="elias06"></constructor-arg>
        <constructor-arg value="06" type="int"></constructor-arg>
        <constructor-arg value="elias@hl.com"></constructor-arg>
</bean>
```

通过p名称空间为bean赋值名称空间

```xml
<!--
通过p名称空间为bean赋值
名称空间：在xml中名称空间是用来防止标签重复的
-->
<bean id="person07" class="com.xxxx.bean.Person" 
      p:age="07" p:name="elias07" p:email="elias@hl.com" p:gender="man"/>
```

----

**正确为各种属性赋值**测试使用null值

1. ```xml
   <bean id="person01" class="com.xxxx.bean.Person">
          <property name="name">
              <null></null>
          </property>
   </bean>
   ```

2. 引用类型赋值（引用其他bean、引用内部bean）

   2.1 引用其他bean

   ```xml
   <bean id="car01" class="com.xxxx.bean.Car">
           <property name="name" value="baoma"/>
           <property name="color" value="blue"/>
           <property name="price" value="100000"/>
   </bean>
   <!--
   ref代表引用外面的一个值
   相当于Car car = ioc.getBean("car01, Car.class)
   -->
   <bean id="person01" class="com.xxxx.bean.Person">
   	<property name="car" ref="car01"/>
   </bean>
   ```

   2.2 引用内部bean

   ```xml
   <bean id="person01" class="com.xxxx.bean.Person">
   	<property name="car">
               <!--引用内部bean，不能被获取到，只能内部使用-->
               <bean id="car02" class="com.xxxx.bean.Car" >
                   <property name="price" value="1000"/>
                   <property name="color" value="green"/>
                   <property name="name" value="benchi"/>
               </bean>
       </property>
   </bean>
   ```

3. 集合类型赋值（list，map，properties）

   list

   ```xml
   <bean id="person02" class="com.xxxx.bean.Person">
       <property name="books">
           <list>
               <bean class="com.xxxx.bean.Book">
                   <property name="name" value="alice in wonderland"/>
               </bean>
               <ref bean="book01"/>
           </list>
       </property>
   </bean>
   ```

   map

   ```xml
   <bean id="person03" class="com.xxxx.bean.Person">
       <property name="map">
           <map>
               <!--一个entry代表一个键值对-->
               <entry key="key01" value="alice"></entry>
               <!--值是一个引用类型-->
               <entry key="key02" value-ref="book01"></entry>
               <!--值又是一个map-->
               <entry key="key03">
                   <map>
                       <entry key="key04" value-ref="car01"></entry>
                   </map>
               </entry>
           </map>
       </property>
   </bean>
   ```

   properties

   ```xml
   <bean id="person04" class="com.xxxx.bean.Person">
       <property name="properties">
            <props>
               <!-- properties；里面k=v，键值对都是String属性，所以可以直接把value写在文本里 -->
               <prop key="username">root</prop>
               <prop key="password">123456</prop>
           </props>
       </property>
   </bean>
   ```

4. util名称空间创建集合类型的bean

   ```xml
   <bean id="person05" class="com.jxau.bean.Person">
       <property name="map" ref="myMap"/>
   </bean>
   <util:map id="myMap" >
       <entry key="key01" value="alice"></entry>
       <entry key="key02" value-ref="book01"></entry>
       <entry key="key03">
           <map>
               <entry key="key04" value-ref="car01"></entry>
           </map>
       </entry>
   </util:map>
   ```

5. 级联属性赋值

   ```xml
   <bean id="person07" class="com.jxau.bean.Person">
       <property name="car">
           <bean id="car02" class="com.jxau.bean.Car" >
               <property name="price" value="1000"/>
               <property name="color" value="green"/>
               <property name="name" value="benchi"/>
           </bean>
       </property>
       <property name="car.price" value="999"/>
   </bean>
   ```

---

当bean中添加了abstract属性，并且赋值为true，则这个bean的配置是抽象的，不能获取他的实例，只能被别人用来被继承

```xml
<bean id="person08" class="com.jxau.bean.Person" abstract="true"></bean>
```

parent : 指定当前bean的配置信息继承哪个

```xml
<bean id="person09" class="com.jxau.bean.Person" parent="person08">
    <property name="name" value="李四"/>
</bean>
```

---

按照配置的顺序创建bean，bean之间的依赖（只是改变创建顺序）

```xml
<bean id="person" class="com.jxau.bean.Person" depends-on="book, car"></bean>
<bean id="car" class="com.jxau.bean.Car"></bean>
<bean id="book" class="com.jxau.bean.Book"></bean>
```

```xml 
<!--bean的作用域：scope="****"
指定bean是否单实例
    prototype：多实例的：
        1. 容器启动默认不会去创建多实例bean
        2. 获取的时候会创建这个bean
        3. 每次获取都会创建一个新的对象
    singleton：单实例的：
        1. 在容器启动完成之前就已经创建好对象，保存在容器中了
        2. 任何获取都是获取之前创建好的对象
    request：在web环境下，同一次请求创建一个bean实例
    session：在web环境下，同一次会话创建一个bean实例
-->

<!-- 
 bean的创建默认就是框架利用反射new出来的bean实例
    工厂模式：工厂帮我们创建对象
    静态工厂：工厂本身不用创建对象，通过静态方法调用，对象 = 工厂类.工厂方法名（）；
    实例工厂：工厂本身需要创建对象
                工厂类 工厂对象 = new 工厂类（）；
                工厂对象.工厂方法名
-->
<!--只要是FacrotyBean的实现类，spring都认为是一个工厂类-->
<bean id="factoryAir" class="com.jxau.factory.MyFactoryBean"></bean>

<!--静态工厂-->
<bean id="AirplaneStaticFactory" class="com.jxau.factory.AirplanStaticFactory"
      factory-method="getAirplane">
    <!--为方法指定参数-->
    <constructor-arg value="张三"></constructor-arg>
</bean>

<!--实例工厂-->
<bean id="AirplaneFactory" class="com.jxau.factory.AirplaneFactory"></bean>
<bean id="plane01" class="com.jxau.bean.Airplane" factory-bean="AirplaneFactory"
      factory-method="getAirplane">
    <constructor-arg value="李四"></constructor-arg>
</bean>
```

```java
/**
 * 实现factorybean接口的类是spring可认识的工厂类
 * spring会自动调用工厂方法创建实例
 *
 * 步骤：
 * 1. 编写一个factorybean的实现类
 * 2. 在spring配置文件中进行注册
 */
public class MyFactoryBean implements FactoryBean<Airplane> {

    /**
     * 返回实现类
     * @return
     * @throws Exception
     */
    public Airplane getObject() throws Exception {
        Airplane air = new Airplane();
        air.setAaa("aaa");
        return air;
    }

    /**
     * 返回实现类的类型
     * @return
     */
    public Class<?> getObjectType() {
        return Airplane.class;
    }

    /**
     * 是单例？
     * @return
     */
    public boolean isSingleton() {
        return false;
    }
}

```

---

```xml
 <!--
    生命周期：bean的创建到销毁
    ioc容器中注册的bean
        1. 单例bean，容器创建的时候就会创建好，容器关闭也会销毁创建的bean
        2. 多实例bean，获取的时候才会创建
        我们可以为bean自定义一些生命周期方法；spring在创建或者销毁的时候就会调用指定的方法
        自定义初始化方法和销毁方法，而且必须是无参的
-->
<bean id="book01" class="com.jxau.bean.Book"
      init-method="myInit" destroy-method="myDestroy"
scope="prototype"/>
```

```java
 /**
 * 单例：bean的生命周期
 *  （容器启动）构造器--->初始化方法---->（容器关闭）销毁方法
*  多实例：
*    获取bean时（构造器---》初始化方法）----》容器关闭不会调用bean的销毁方法
*/
public class MyBeanPostProcession implements BeanPostProcessor {
/**
*初始化之前调用
* @param bean 将要初始化的bean
* @param beanName 将要初始化的bean在配置文件中的id
* @return bean
* @throws BeansException
*/
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + " will init");
        if (bean instanceof Book){
            Book bean1 = (Book) bean;
            bean1.setAuthor("???");
        }
        return bean;
    }

    /**
     * 初始化结束之后调用
     * @param bean 初始化后要返回的bean
     * @param beanName bean在xml配置文件中的id
     * @return
     * @throws BeansException
     */

    /**
     * 后置处理器：
     *  (容器启动）构造器--->后置处理器before--->初始化方法----->后置处理器after-->bean初始化完成
     *  无论bean是否有初始化方法，后置处理器都会默认其有，还会继续工作
     */
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println(beanName + " was inited");
        if (bean instanceof Book) {
            Book bean1 = (Book) bean;
            bean1.setName("use");
        }
        //初始化之后返回的bean，返回的是什么，容器中保存的就是什么
        return bean;
    }
}

```

```xml 
 <!--引用外部属性文件，使用context名称空间
    加载外部配置文件，固定写法classpath:表示引用类路径下的一个资源
-->
<context:property-placeholder location="classpath:dbconfig.properties"/>
<!--username是spring中的key的一个关键字，为了防止配置文件中的key与spring自己的关键字冲突，可以给配置文件中的变量加前缀，例如jdbc_username-->
<!--
数据库连接池作为单实例是最好的，一个项目就一个连接池，连接池里面管理很多链接，链接是直接从连接池中拿
-->
<!--可以让spring帮我们创建连接池对象，管理连接池-->
<bean id="DataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <property name="user" value="${jdbc_username}"/>
    <property name="password" value="${jdbc_password}"/>
    <property name="driverClass" value="${jdbc_driverClass}"/>
    <property name="jdbcUrl" value="${jdbc_jdbcUrl}"/>
</bean>
```

```java
@Controller
public class BookServlet {
    /**
     * 使用Autowired注解实现根据类型实现自动装配
     */
    @Qualifier("bookService222")
    @Autowired(required = false)
    private BookService bookServiceExt;
    
    public void doGet(){
        System.out.println("bookservlet" + bookServiceExt);
        //bookServiceExt.saveBook();
    }
    /**
     * 方法也可以autowired化
     * 这个方法也会在bean创建的时候自动运行
     * 这个方法上的每一个参数都会自动注入值
     * 如果里面的参数，有任何一个装配不上，并且注解上标注了(required=false)，这个方法就不会运行
     * 如果没有标注(required=false)，则会报错
     */
    @Autowired(required=false)
    public void get(BookDao bookDao, @Qualifier("book") BookService bookService, Book book){
        System.out.println("get..." + bookDao + " " + book + " " + bookService);
    }
}
```

```xml 
<!--<bean id="car02" class="com.jxau.bean.Car">
        <property name="name" value="baoma"/>
        <property name="color" value="red"/>
        <property name="price" value="10000000"/>
    </bean>-->
    <!--为person里的自定义类型的属性赋值
        property：手动赋值
        自动赋值（自动装配）：

        自动装配（仅对自定义类型的属性有效）
        autowire="default/no" 默认不自动装配，不自动为car取值

        autowire="byName" 按照名字
                private Car car;
                以属性名（car）作为id去容器中找到这个组件，给他赋值，如果找不到，就赋值为null
                car = ioc.getBean("car");

        autowire="byType" 按照类型
                private Car car;
                以属性的类型作为查找依据去容器中找到这个组件，给他赋值，如果找不到就装配null
                car = ioc.getBean(Car.class");
                如果容器中有多个这个类型的组件，则会按照属性名去查找，如果没有找到，就装配null

        autowire="constructor" 按照构造器进行赋值
        public Person(Car car){}
        先按照有参构造器的类型进行装配（成功就赋值）没有就直接为组件装配null即可
        如果按照类型找到了多个，就用参数的名作为id去容器中查找，如果找到了，就装配，没有就null
    -->
    <bean id="person01" class="com.jxau.bean.Person" autowire="byType"></bean>
    <bean id="car" class="com.jxau.bean.Car">
        <property name="name" value="baoma"/>
        <property name="color" value="red"/>
        <property name="price" value="10000000"/>
    </bean>
    <!--List<Book> books; 容器可以把容器中所有book封装list赋值给这个属性-->
    <bean id="book" class="com.jxau.bean.Book">
        <property name="name" value="to the east"/>
        <property name="author" value="me"/>
    </bean>
    <bean id="map" class="java.util.HashMap"></bean>
    <bean id="book01" class="com.jxau.bean.Book">
        <property name="name" value="to1"/>
    </bean>
    <bean id="book02" class="com.jxau.bean.Book">
        <property name="name" value="to2"/>
    </bean>
    <bean id="book03" class="com.jxau.bean.Book">
        <property name="name" value="to3"/>
    </bean>
    <!--SpEL测试(spring Expression language) spring表达式语言
            在SpEL中使用字面量
            引用其他bean
            引用其他bean的某个属性值
            调用非静态方法
            调用静态方法
            使用运算法：支持所有运算符
    -->
    <bean id="person02" class="com.jxau.bean.Person">
        <!--引用其他bean的属性值-->
        <property name="name" value="#{book.name}"/>
        <!--字面量：${} #{}-->
        <property name="age" value="#{12*12}"/>
        <!--引用其他bean-->
        <property name="car" value="#{car}"/>
        <!--调用静态方法-->
        <property name="email" value="#{T(java.util.UUID).randomUUID().toString().substring(0, 5)}"/>
        <!--调用非静态方法-->
        <property name="gender" value="#{book.getName()}"/>
    </bean>
```

```xml
 <!--通过注解分别创建Dao、Service、Controller(控制器，控制网站跳转逻辑Servlet)-->
    <!--
    通过给bean上添加某些注释，可以快速的将bean加入到ioc容器中
    spring有四个注解
    @Controller：控制器：推荐给控制器层（servlet包下的）的组件加上这个注解
    @Service：业务逻辑：推荐给业务逻辑层的组件添加这个注解：BookService
    @Repository: 给数据库层（持久化层，dao层）的组件添加这个注解
    @Component：给不属于以上几层的组件添加这个注解

   (@Service, @Repository, @Component) 注解是可以随便加的：spring底层不会去验证你这个组件
    推荐各自层加各自注解，给程序员看

    使用注解将组件快速的加入到容器中需要几步
    1、要添加的组件上标四个注解的任何一个
    2、告诉spring，自动扫描加了注解的组件；依赖context名称空间
    3、一定要导入aop包，支持加注解模式的：spring-aop...
    -->
 <!--   <context:component-scan base-package="com.jxau">
        使用context:exclude-filter指定扫描包时不包含的类
        扫描的时候，可以排除一些不要的组件
            type="annotation"：指定排除规则，按照注解进行排除，标注指定注解的组件不要
                expression="" 注解的全类名
            type="assignable"：指定排除某个具体的类，按照类排除
                expression=""：类的全类名

            type="aspectj" aspectj表达式
            type="custom" 自定义一个TypeFilter;自己写代码决定哪些使用
            type="regex"：正则表达式
	<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service"/>
-->

    <!--<context:component-scan base-package="com.jxau" use-default-filters="false">
       		//只扫描进入哪些组件：默认都是全部扫描进来
            //一定要禁用掉默认的过滤规则才行；use-default-filter="false"
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Service"></context:include-filter>
    </context:component-scan>-->
    <context:component-scan base-package="com.jxau"></context:component-scan>
```

---

**@Autowired原理**

@Autowired原理
        @Autowired
        private BookService bookService;

    1. 先按照类型去容器中找到对应的组件：bookService = ioc.getBean（BookService.class）；
    	1.1 找到一个就赋值
    	1.2 找不到就抛异常
    	1.3 找到多个，默认根据@Autowired注解标记的成员变量名作为id查找bean，进行装配
    		1.3.1 找到就进行装配
    		1.3.2 找不到就报错，是因为我们继续用变量名作为id进行匹配的
    可以使用@Qualifier("bookServicec")指定一个新id
    	找到装配
    	找不到报错
    发现Autowired标注的自动装配的属性默认是一定装配上的
    可以@Autowired（require=false）来指定某个属性可以不配置,也就是装配null
    @Autowired, @Resource, @Inject 都是自动装配的注解
    @Autowired：最强大，因为是spring自己的注解
    @Resource：j2ee，是java的标准
    @Inject：EJB
    @Resource拓展性强，因为是java标准，如果切换成另外一个容器框架，@Resource还是可以使用的，而@Autowire就不行

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
/**
 * 使用spring的单元测试
 * 1. 导包spring的单元测试包
 * 2. @ContextConfiguration(locations="")使用它来指定spring配置文件的位置
 * 3. @RunWith指定用哪种驱动进行单元测试，默认就是junit
 *      @RunWith(SpringJunit4ClassRunner.class)
 *      使用spring的单元测试模块来执行标了@Test注解的测试方法
 *      以前@Test注解只是由junit执行
 * 好处：我们不用ioc.getBean（）获取组件了，直接Autowired组件，spring自动为我们装配
 */
@ContextConfiguration(locations = "classpath:applicationContext.xml")
@RunWith(SpringJUnit4ClassRunner.class)
public class test2 {
    @Autowired
    private BookService bookService;
    @Test
    public void test(){
        System.out.println(bookService);
    }
}
```

