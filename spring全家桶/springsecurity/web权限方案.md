###  如果要使用数据库查询出来的账号密码进行验证，需要实现**两个重要的接口**

1. 创建类继承UsernamePasswordAuthenticationFilter，重写三个方法

2. 创建类实现UserDetailService，编写查询数据过程，返回User对象，这个User对象是安全框架提供对象

3. PasswordEncoder

    数据加密接口，用于返回User对象里面密码加密

### 设置登录的用户名和密码

1. 通过配置文件

    ```properties
    spring.security.user.name=hl
    spring.security.user.password=hl
    ```

2. 通过配置类

    ```java
    @Configuration
    public class SecurityConfig extends WebSecurityConfigurerAdapter{
        @Override
        protected void configure(AuthenticationManagerBuilder auth) throws Exception {
            BCryptPasswordEncoder passwordEncoder = new BCryptPasswordEncoder();
            String encode = passwordEncoder.encode("123");
            System.out.println(encode);
            auth.inMemoryAuthentication().withUser("hl").password(encode).roles("user");
        }
    
        //需要把PasswordEncoder注册到容器中
        @Bean
        PasswordEncoder bCryptPasswordEncoder(){
            return new BCryptPasswordEncoder();
        }
    }
    ```

3. 自定义编写实现类 

    1. 创建配置类，设置使用哪个userDetailsService实现类

        ```java
        @Configuration
        public class SecurityConfig2 extends WebSecurityConfigurerAdapter {
        
            @Autowired
            UserDetailsService userDetailsService;
        
            @Override
            protected void configure(AuthenticationManagerBuilder auth) throws Exception {
                auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
            }
            @Bean
            public PasswordEncoder passwordEncoder(){
                return new BCryptPasswordEncoder();
            }
        }
        ```

    2. 编写实现类，返回User对象，User对象有用户名密码和操作权限

        ```java
        @Service("userDetailsService")
        public class MyUserDetailsService implements UserDetailsService {
            @Override
            public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
                List<GrantedAuthority> roles = new ArrayList<>(3);
                return new User("hello", new BCryptPasswordEncoder().encode("147"), roles);
            }
        }
        ```

### 查询数据库完成用户认证

* 整合MyBatisPlus完成数据库操作

    1. 引入相关依赖

        ```xml
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--lombok用来简化实体类-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        ```

    2. 创建数据库和数据库表

    3. 编写实体类

        ```java
        @Data
        public class Users {
            private Integer id;
            private String username;
            private String password;
        }
        ```

    4. 整合mp，创建接口，继承mp的接口

        ```java
        //@Repository
        @Mapper
        public interface UsersMapper{
            @Select("select * from users where username = #{username}")
            Users getUser(String username);
        }
        ```

    5. 在MyUserDetailsService调用mapper里面的方法查询数据库进行用户认证

        ```java
        @Service("userDetailsService")
        public class MyUserDetailsService implements UserDetailsService {
            @Autowired
            private UsersMapper usersMapper;
            @Override
            public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
                Users users = usersMapper.getUser(s);
                //判断，如果数据库没有用户名，则认证失败
                if(users == null){
                    throw new UsernameNotFoundException("用户名不存在");
                }
                System.out.println(users);
                List<GrantedAuthority> roles = new ArrayList<>();
                return new User(users.getUsername(),
                        new BCryptPasswordEncoder().encode(users.getPassword()), roles);
            }
        }
        ```

    6. 在启动类添加注解MapperScanner

        ```java
        @SpringBootApplication
        @MapperScan("com.hl.security_demo1.mapper")
        public class SecurityDemo1Application {
            public static void main(String[] args) {
                SpringApplication.run(SecurityDemo1Application.class, args);
            }
        }
        ```

    7. 配置数据源

        ```properties
        #配置数据库连接
        spring.datasource.name=root
        spring.datasource.password=123
        spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
        spring.datasource.url=jdbc:mysql://localhost:3306/jdbc_test?serverTimezone=UTC 
        ```

### 自定义界面

不需要认证可以访问

1. 在配置类实现相关的配置

    ```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin()//自定义自己编写的登录页面
            .loginPage("/login.html") //登录页面设置
            .loginProcessingUrl("/test/login") //登录访问路径
            .defaultSuccessUrl("/test/index") // 登录成功跳转路径
            .and().authorizeRequests()
            .antMatchers("/", "/test/hello", "/test/login","/login.html").permitAll()//设置哪些路径可以直接访问，不需要认证
            .anyRequest().authenticated()
            .and().csrf().disable(); //关闭crsf防护
    }
    ```

2. 编写登录界面和controller

```java
http.exceptionHandling().accessDeniedPage("/unauth.html");//自定义权限提醒界面
```



### 基于角色或权限进行访问控制

#### hasAuthority

如果当前的主体具有指定的权限，则返回true，否则放回false

步骤：

1.  在配置类设置当前访问地址有哪些权限

    ```java
    //当前登录用户，只有具有admin权限才可以访问这个路径
    .antMatchers("/test/index").hasAuthority("admin")
    ```

2. 在UserDetailsService，把返回User对象设置权限

    ```java
    List<GrantedAuthority> roles =
                    AuthorityUtils.commaSeparatedStringToAuthorityList("admin");
    //如果有多个权限，则用,进行分隔，系统对其处理时会将字符串进行切割
    ```

#### hasAnyAuthority

如果当前的主体有任何提供的角色（给定的作为一个逗号分隔的字符串列表）的话，返回true

在配置类设置当前访问地址有哪些权限

```java
//当前登录用户，具有admin或者manager权限都可以访问这个路径
.antMatchers("/test/index").hasAnyAuthority("admin, manager")
```

#### hasRole

如果用户具备给定角色就允许访问，否则出现403

如果当前主体具有指定的角色，则返回true

```java
.antMatchers("/test/index").hasRole("sale")
List<GrantedAuthority> roles = AuthorityUtils.commaSeparatedStringToAuthorityList("admin, ROLE_sale");
```

#### hasAnyRole

表示用户具备任何一个条件都可以访问

```java
.antMatchers("/test/index").hasRole("sale, man")
```

### 注解

==@Secured==

用户具有某个角色，可以访问方法

1. 启动类（配置类）上开启注解

    ```java
    @EnableGlobalMethodSecurity(securedEnabled = true)
    public class SecurityDemo1Application {
    ```

2. 在controller的方法上面使用注解，设置角色

    ```java
    @GetMapping("secured")
    @Secured({"ROLE_sale", "ROLE_manager"})
    public String secured(){
        return "hello, secured";
    }
    ```

3. userDetailsService设置用户角色

    ```java
    List<GrantedAuthority> roles = AuthorityUtils.commaSeparatedStringToAuthorityList("admin,ROLE_sale1");
    ```

==@PreAuthorize==

注解适合进入**方法前**的权限验证

```java
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
```

```java
@GetMapping("secured")
@PreAuthorize("hasAuthority('admin')")
public String secured(){
    return "hello, secured";
}
```

==@PostAuthorize==

注解适合进入**方法后**进行校验

```java
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
```

```java
@PostAuthorize("hasAuthority('admin1')")
public String secured(){
    System.out.println("就这？九折？");
    return "hello, secured";
}
```

==@PostFilter==

对方法的返回数据进行过滤

==@PreFilter==

传入方法数据进行过滤

###  用户注销

在配置类中进行配置

```java
http.logout().logoutUrl("/logout").logoutSuccessUrl("/test/hello").permitAll();
```

### 自动登录

#### 实现原理

![1606613124162](H:\Typora\pics\1606613124162.png)

#### 具体实现

1. 创建数据库表persistent_logins

    ```java
    //在配置数据源的时候加上这一句，可以自动创建数据库表
    jdbcTokenRepository.setCreateTableOnStartup(true);
    //自己创建数据库表，表名必须为persistent_logins
    ```

2. 配置类，注入数据源，配置操作数据库对象

    ```java
    @Bean
    PersistentTokenRepository persistentTokenRepository(){
        JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
        jdbcTokenRepository.setDataSource(dataSource);
        jdbcTokenRepository.setCreateTableOnStartup(true);
        return jdbcTokenRepository;
    }
    ```

3. 配置类配置自动登录

    ```java
     .and().rememberMe().tokenRepository(persistentTokenRepository())              .tokenValiditySeconds(60).userDetailsService(userDetailsService)
    ```

4. 在登录界面添加复选框

### csrf防护

详见https://docs.spring.io/spring-security/site/docs/5.4.1/reference/html5/#servlet-exploits

的第14节