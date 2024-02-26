## Junit

### junit包中的重要的注解

* @Test 表明这个方法是一个测试方法
  * 此注解标注的方法必须要是public void修饰且方法不能带参数。junit创建新的测试类实例，通过反射来调用被标注了@Test的方法
* @BeforeAll 只能标注在静态方法上，会在**测试类**运行之前执行一次（如果有多个静态方法标注了此注解，执行顺序按照方法名）
* @BeforeEach 只能标注在方法上，在**每个测试方法**执行前会运行一次
* @AfterEach 只能标注在方法上，在**每个测试方法**执行后会运行一次
* @AfterAll 只能标注在静态方法上，在**测试类**运行之后执行一次

这几个注解的执行顺序为：**@BeforeAll**->@BeforeEach->@Test->@AfterEach->@BeforeEach->@Test->@AfterEach->**@AfterAll**

* @Disabled 可以标注在方法或者类上，忽略此测试方法或类，不执行。(控制台会给出提示"xxx() is @Disabled")
* @Nested 内嵌测试类
* @RepeatedTest(value = n) 重复性测试，执行n次
* @Order(n) 该方法第几个运行，需要配合@TestMethodOrder(MethodOrderer.OrderAnnotation.class)使用

```java
public class CalculatorTest {
    private Calculator calculator = new Calculator();
    @Test
    @RepeatedTest(value = 3)
    public void testAdd() {
        Assertions.assertEquals(3, calculator.add(1, 2));
    }
    @Test
    @Disabled
    public void testSub() {
        Assertions.assertEquals(2, calculator.sub(5, 2));
    }
    @Nested
    class test {
        @Test
        public void testNested() {
            System.out.println("test nested");
        }
    }
    public void testIgnore() {
        System.out.println("test ignore");
    }
    @BeforeAll
    public static void testBeforeClass() {
        System.out.println("test before class");
    }
    @BeforeAll
    public static void testBeforeClass2() {
        System.out.println("test before class3");
    }
    @BeforeAll
    public static void testBeforeClass3() {
        System.out.println("test before class2");
    }
    @AfterAll
    public static void testAfterClass() {
        System.out.println("test after class");
    }
    @BeforeEach
    public void testBefore() {
        System.out.println("test before");
    }
    @AfterEach
    public void testAfter() {
        System.out.println("test after");
    }
}
```

### junit包中的重要类

#### Assertions

Assertions类中内置了许多的断言方法，方便开发人员使用。比如比较结果是否相等，结果是否返回true，测试是否超时（assertTimeoutPreemptively），抛出异常是否正确（Assertions#assertThrows(Class<T>, Executable)）等。

#### Assumptions

Assumptions类中也内置了许多断言方法，他和Assertions的不同在于：如果实际结果和预期结果不一致，Assumptions的处理方法是跳过此次测试，而Assertions是判断此次测试为失败

#### ArgumentsAccessor

可以将输入的数据转换成我们需要的类型

```java
@ParameterizedTest
@CsvSource({"Jackey,12,male,1900-02-02","Jan,13,female,1900-10-03"})
public void testArgumentsAccessor(ArgumentsAccessor accessor) {
    Person person = new Person(accessor.getString(0),
                               accessor.getInteger(1),
                               accessor.get(2, String.class),
                               accessor.get(3, LocalDate.class));
    if (person.gender.equals("female")) {
        Assertions.assertEquals(person.name, "Jan");
    } else {
        Assertions.assertEquals(person.name, "Jackey");
    }
    assertEquals(1900, person.birthday.getYear());
}

@ParameterizedTest
@CsvSource({"Jackey,12,male,1900-02-02","Jan,13,female,1900-10-03"})
//@AggregateWith注解可以指定用什么转换器进行类型转换
public void testMyArgumentsAccessor(@AggregateWith(MyArgumentsAccessor.class) Person person) {
    if (person.gender.equals("female")) {
        Assertions.assertEquals(person.name, "Jan");
    } else {
        Assertions.assertEquals(person.name, "Jackey");
    }
    assertEquals(1900, person.birthday.getYear());
}

/**
 * 自定义转换器
 */
class MyArgumentsAccessor implements ArgumentsAggregator {
    @Override
    public Person aggregateArguments(ArgumentsAccessor accessor, ParameterContext context) throws ArgumentsAggregationException {
        Person person = new Person(accessor.getString(0),
                accessor.getInteger(1),
                accessor.get(2, String.class),
                accessor.get(3, LocalDate.class));
        return person;
    
}
class Person {
    String name;
    int age;
    String gender;
    LocalDate birthday;
    public Person(String name, int age, String gender, LocalDate birthday) {
        this.name = name;
        this.age = age;
        this.gender = gender;
        this.birthday = birthday;
    }
}
```

### 参数化测试

@ParameterizedTest 带参数的测试，注释的方法必须有参数。搭配@ValueSource 和 @CsvSource 等进行使用。

@ValueSource参数里面可以带上八大基础类型、字符串和class类型的数组，对数组中的每个值分别执行一次测试

```java
//此方法会执行三次，每次读取数组中的一个数并进行测试
@ParameterizedTest
@ValueSource(ints = {1,2,3})
public void testParameterizedTest(int num) {
    System.out.println(num);
}
```

@CsvSource参数里需要带上的每一组数据用逗号隔开，也可使用@CsvFileSource直接指定需要读取的csv文件进行测试

```java
@ParameterizedTest
@CsvSource({"1,one", "2,two", "3,three"})
public void testCsvSource(int i, String s) {
    System.out.println(i + " = " + s);
}
```

## Mockito

Mock测试：在测试过程中，对不容易构造或者不容易获取的对象，用一个虚拟对象（Mock对象）来创建以便测试运行。

```java
class MockTest {
    //模拟一个random对象
    @Mock
    private Random random;
    
//    @Mock(answer = Answers.RETURNS_SMART_NULLS)
    @Mock(answer = Answers.RETURNS_DEEP_STUBS)
    private AccountDao accountDao;
    @Test
    void testAnswer() {
        System.out.println(accountDao.findAccount("1", "2"));
    }
    
    @BeforeEach
    public void setUp() {
        //使mock对象生效
        MockitoAnnotations.openMocks(this);
    }
    @Test
    void mockTest3() {
        //获取的模拟对象，只会返回方法返回类型的默认值(int返回0，对象返回null，可通过mock注解中的answer配置其他返回值)
        Assertions.assertEquals(0, random.nextInt());
    }
    @Test
    public void mockTest() {
        Random random = Mockito.mock(Random.class);
        int num = random.nextInt();
//        Mockito.verify(random).nextInt();
        //验证random是否执行某个方法，执行了几次，也可以将times函数换为其他函数表示其他意思，比如never()等价于times(0), atLast()最少调用了几次, atMost()最多调用了几次
        Mockito.verify(random, Mockito.times(2)).nextInt();
    }
    /**
     * @Spy 代理对象执行方法的时候，会去调用原来的方法并执行
     * @Mock 不去调用原来的方法，直接返回该类型的默认值。（int返回0，对象返回null）
     */
    @Spy
//    @Mock
    private Calculator calculator;
    @Test
    @DisplayName("add method test")
    void add() {
        //用when去限定方法返回结果的时候，参数不一致（包括顺序不一致）会导致when的返回失败，返回默认值或者走真实方法
//        Mockito.when(calculator.add(1, 2)).thenReturn(4);
        int num = calculator.add(2, 1);
        Assertions.assertEquals(3, num);
    }
    @RepeatedTest(value = 3)
    public void mockTest2() {
        Random random = Mockito.mock(Random.class);
//        Mockito.when(random.nextInt()).thenCallRealMethod();
        Mockito.when(random.nextInt()).thenReturn(100);
        int num = random.nextInt();
        System.out.println(num);
        Assertions.assertEquals(100, num);
//        Mockito.verify(random).nextInt();
//        Mockito.verify(random, Mockito.times(2)).nextInt();
    }
}
```

```java
class LoginControllerTest {
    @Mock
    private AccountDao accountDao;
    @Mock
    private HttpServletRequest request;
    //用于模拟controller
    private LoginController loginController;
    
    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
//        accountDao = Mockito.mock(AccountDao.class);
        loginController = new LoginController(accountDao);
//        loginController = new LoginController(new AccountDao());
    }

    @Test
    public void loginTest() throws UnsupportedOperationException {
        Mockito.when(request.getParameter("username")).thenReturn("tian");
        Mockito.when(request.getParameter("password")).thenReturn("123");
//        Mockito.when(accountDao.findAccount(anyString(), anyString())).thenReturn(new Account());
//        Mockito.when(accountDao.findAccount(anyString(), anyString())).thenReturn(null);
        //anyString()表示参数为任意字符串都会进行后续操作
        Mockito.when(accountDao.findAccount(anyString(), anyString())).thenThrow(new UnsupportedOperationException());

        //模拟登录
        String login = loginController.login(request);
        assertEquals("/index.html", login);
    }
}
```

```java
public class StubbingTest {
    @Mock
    private List<String> list;
//    private ArrayList<String> list;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }
    @AfterEach
    public void destory() {
        reset(this.list);
    }
    @Test
    void testWhen() {
//        when(list.get(1)).thenReturn("one");
        doReturn("one").when(list).get(1);
        assertEquals(list.get(1), "one");
        when(list.get(anyInt())).thenThrow(new RuntimeException());
        assertThrows(RuntimeException.class, ()->{list.get(0);});
    }
    @Test
    void testVoidMethod() {
        //如果函数没有返回值，则函数调用不能写在when中
        doNothing().when(list).clear();
        list.clear();
        verify(list).clear();

        doThrow(RuntimeException.class).when(list).clear();
        assertThrows(RuntimeException.class, ()->{list.clear();});
    }

    @Test
    void testIterator() {
        //每次调用都把return的值往后取一位
        when(list.size()).thenReturn(1,2,3,4);
        assertEquals(list.size(), 1);
        assertEquals(list.size(), 2);
        assertEquals(list.size(), 3);
        assertEquals(list.size(), 4);
        assertEquals(list.size(), 4);
    }

    @Test
    void testWithAnswer() {
        when(list.get(anyInt())).thenAnswer(invocation -> {
//           return invocation.callRealMethod();
            return invocation.getArgument(0, Integer.class);
        });
        assertEquals(1, list.get(1));
        assertEquals(20, list.get(20));
    }
}
```

```java
public class ArgumentMatchersTest1 {
    @Test
    void complexTest() {
        Foo foo = Mockito.mock(Foo.class);
        when(foo.fun(Mockito.isA(Son.class))).thenReturn(100);
        /*assertEquals(foo.fun(new Parent() {
            @Override
            public int work() {
                return 100;
            }
        }), 100);*/
//        assertEquals(foo.fun(new Son()), 100);
//        assertEquals(foo.fun(new Son2()), 100);
        
        //重置这个模拟类
        reset(foo);

        when(foo.fun(Mockito.any(Son.class))).thenReturn(99);
        /*assertEquals(foo.fun(new Parent() {
            @Override
            public int work() {
                return 100;
            }
        }), 100);*/
//        assertEquals(foo.fun(new Son()), 99);
//        assertEquals(foo.fun(new Son2()), 99);
    }

    @Test
    void testAny() {
        Foo mock = Mockito.mock(Foo.class);
        when(mock.fun2(anyInt(), anyString(), anyCollection(), any())).thenReturn(100);
        assertEquals(mock.fun2(1, "jon", Collections.EMPTY_LIST, "123"), 100);
        assertEquals(mock.fun2(1, "lon", Collections.EMPTY_LIST, "23"), 100);
    }

    @Test
    void testSpecify() {
        Foo mock = Mockito.mock(Foo.class);
        //如果参数中用了any，则不允许参数中用固定值，如果需要，要用到eq函数
//        when(mock.fun2(anyInt(), "jon", anyCollection(), any())).thenReturn(100);
        when(mock.fun2(anyInt(), eq("jon"), anyCollection(), any())).thenReturn(100);
        when(mock.fun2(anyInt(), eq("lon"), anyCollection(), any())).thenReturn(200);
        assertEquals(mock.fun2(1, "jon", Collections.EMPTY_LIST, "123"), 100);
        assertEquals(mock.fun2(1, "lon", Collections.EMPTY_LIST, "23"), 200);
    }
    @Test
    void testSpecify2() {
        Foo mock = Mockito.mock(Foo.class);
        when(mock.fun2(anyInt(), eq("jon"), anyCollection(), any())).thenReturn(100);
        mock.fun2(1, "jon", Collections.EMPTY_LIST, "123");
        verify(mock).fun2(anyInt(), eq("jon"), eq(Collections.EMPTY_LIST), eq("123"));
    }

    static class Foo {
        int fun(Parent parent) {
            return parent.work();
        }
        int fun2(int a, String b, Collection c, Serializable d) {
            return 10;
        }
    }
    interface Parent {
        int work();
    }
    class Son implements Parent {
        @Override
        public int work() {
            throw new RuntimeException();
        }
    }
    class Son2 implements Parent {
        @Override
        public int work() {
            throw new RuntimeException();
        }
    }
}
```

```java
public class DocTest {
    @Mock List list1;
    @Spy List list2;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
    }
    @Test
    void testMockingDetails() {
        System.out.println(Mockito.mockingDetails(list1).isSpy());
        System.out.println(Mockito.mockingDetails(list2).isSpy());
        //spy模拟的对象，也是mock对象，下面语句的输出结果为true
        System.out.println(Mockito.mockingDetails(list2).isMock());
    }
    @Test
    void testOrder() {
        List<Integer> list1 = Mockito.mock(List.class);
        List<Integer> list2 = Mockito.mock(List.class);
        InOrder inOrder = inOrder(list1);
        list1.add(1);
        list1.add(3);
        list1.add(2);
        //验证模拟类的函数是否被按顺序调用
        inOrder.verify(list1).add(1);
        inOrder.verify(list1).add(2);

        reset(list1);

        InOrder twoInOrder = inOrder(list1, list2);
        list1.add(1);
        list2.add(1);
        twoInOrder.verify(list1).add(anyInt());
        twoInOrder.verify(list2).add(1);
    }
}
```

