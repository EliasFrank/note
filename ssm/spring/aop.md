AOP ： (Aspect Oriented Programming)

OOP ：(Object Oriented Programming)

面向切面编程：基于OOP基础之上新的编程思想

指在程序运行期间，**将某段代码动态的切入到指定方法的指定位置**进行运行的这种编程方式，面向切面编程



场景：计算器运行计算方式的时候进行日志记录：

加日志记录：

1）、直接编写在方法内部；不推荐，修改维护麻烦

​		日志记录：系统的辅助功能；

​		业务逻辑：核心功能

​		高耦合

2）、希望的是：

​		业务逻辑：核心功能

​		日志模块：在核心功能运行期间，自己动态加上

可以使用动态代理来将日志代码动态的在目标方法执行前后先进行执行

---

**动态代理**：

1. 有了动态代理，日志记录可以做的非常强大，而且与业务逻辑解耦
2. jdk默认的动态代理，如果目标对象没有实现任何接口，是无法为他创建代理对象的。
3. 难写

spring实现了aop功能，底层就是动态代理

1. 可以利用spring一句代码都不写去创建动态代理
2. 实现简单，而且没有强制要求目标对象必须实现接口

**将某段代码（日志）动态的切入（不把日志代码写死在业务逻辑方法中）到指定方法（加减乘除）的指定位置（方法的开始、结束、异常）**进行运行的这种编程方式（spring简化了面向切面编程）

---

 

![1593675307957](C:\Users\hl2333\AppData\Roaming\Typora\typora-user-images\1593675307957.png)

 

---

**aop的使用步骤**

1. 导包
2. 写配置
   1. 将目标类和切面类（封装了通知方法（在目标方法执行前后执行的方法））加入到ioc容器中
   2. 用注解@Aspect告诉spring哪个类是切面类
   3. 告诉spring，切面类里面的每一个方法，都是何时何地运行
3. 测试

---

**切入点表达式的写法**

固定格式：execution(访问权限符 返回值类型 方法全签名(参数表))

通配符：

\* :

   1. 匹配一个或多个字符：

      execution(public int com.jxau.imp.Cacu*.(int, int))

   2. 匹配任意一个参数，第一个是int类型，第二个是参数任意类型

      execution(public int com.jxau.imp.Cacu*.(int, *))

   3. 只能匹配一层路径

   4. 权限位置*不能写，权限位置不写就默认为任何权限；public [可选]

.. :

1. 匹配任意多个参数，任意类型参数

   execution(public int com.jxau.imp.Cacu*.(..))

2. 匹配任意多层路径

   execution(public int com..Cacu*.(int, *))

记住两种切入点表达式：

1. 最精确的

   execution(public int com.jxay.imp.Caculator.add(int, int))

2. 最模糊的

   execution(\* \*.\*(..))

切入点表达式也可以支持逻辑运算符

&&    ||     !

例如：execution(publiv int com.jxay.imp.Caculator.add(..)) && execution(publiv int com..Caculator.add(int, int))

---

**通知方法的执行顺序**

正常执行顺序：@Bfore（前置通知） --> @After（后置通知） -->  @	AfterReturning（正常返回）;

异常执行顺序：@Bfore（前置通知） --> @After（后置通知） -->  @	AfterThrowing（方法异常）;

---

**在通知方法运行的时候，拿到目标方法的详细信息**

1. 只需要为通知方法的参数列表上写一个参数

   JoinPoint 封装了当前目标方法的详细信息

2. 告诉spring哪个参数是用来接收异常或者返回值的

   returning = "参数名"

   throwing = "参数名" Exception 参数名： 指定通知方法可以接受哪些异常

3. 唯一要求就是方法的参数列表一定不能乱写

   通知方法是spring利用反射调用的， 每次方法调用得确定这个方法的参数表的值

   参数表上的每一个参数，spring都得知道是什么

---

告诉spring每个方法在什么时候执行

```
try{
   @Before
   Method.invoke(obj, args);
   @AfterReturing
}catch(Exception e){
   @AfterThrowing
}finally{
   @After
}
```

五个通知注解
 @Before 在目标方法之前运行                         前置通知
@After 在目标方法之后运行                             后置通知
@AfterReturning 在目标方法正常返回之后      返回通知
@AfterThrowing 在目标方法抛出异常之后       异常通知
@Around 环绕                                                   环绕通知

需要在注解里面写上切入点表达式
execution(访问权限符  返回值类型  方法签名)

```java
@AfterReturning(value = "execution(public int com.jxau.impl.CalculatorImpl.*(..))",
returning = "result")
public static void logReturn(int result){
    System.out.println("after returning...run, result is " + result);
}
```

抽取可重用切入点表达式
1. 随便申明一个没有实现的返回void的空方法；

2. 给方法上个标注@Pointcut注解

  ```java
  @Pointcut("execution(public int com.jxau.impl.CalculatorImpl.*(*, *))")
  
  public void testExecution(){}
  ```

```java
/**
     * @Around 环绕 ：是spring最强大的通知，其实和动态代理很像
     */
    @Around("testExecution()")
    public static Object logAround(ProceedingJoinPoint proceedingJoinPoint){
        Object object = null;
        try {
            System.out.println("first");//前置通知
            //相当于method.invoke(obj, args)
            object = proceedingJoinPoint.proceed(proceedingJoinPoint.getArgs());
            System.out.println("end");//返回通知
        }catch (Throwable e){
            System.out.println("throwable");//异常通知
        }finally {
            System.out.println("finally");//后置通知
        }
        return 100;
    }
```

