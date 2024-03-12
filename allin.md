### java

**jdk动态代理和cglib的区别**

Java动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。

而cglib动态代理是利用asm开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。

1. 如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP
2. 如果目标对象实现了接口，可以强制使用CGLIB实现AOP
3. 如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换.

#### 线程

**java线程的实现方法，几种状态，状态之间的转换关系**

1. 继承Thread类，重写run方法
2. 实现Runnable接口，重写run方法，并用其初始化Thread
3. 实现Callable接口，重写call方法，并用其初始化FutureTask
4. 使用线程池创建

线程状态：

```java
NEW,//新生
RUNNABLE,//运行
BLOCKED,//阻塞
WAITING,//等待（一直等）
TIMED_WAITING,//有时间限制的等待（超时等待）
TERMINATED;//终止
```

<img src="upload/image-20210924100247326.png" alt="image-20210924100247326" style="zoom: 67%;" />

#### **lambd**

**lambda入门**

```java
// 1. 不需要参数,返回值为 5
() -> 5

// 2. 接收一个参数(数字类型),返回其2倍的值
x -> 2 * x

// 3. 接受2个参数(数字),并返回他们的差值
(x, y) -> x – y

// 4. 接收2个int型整数,返回他们的和
(int x, int y) -> x + y

// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)
(String s) -> System.out.print(s)
```

替代匿名内部类

使用lambda表达式

```java
public class TestLambda {
	public static void main(String[] args) {
		new Thread(() -> System.out.println("hello")).start();
	}
}
```

不使用lambda表达式

```java
new Thread() {
	@Override
	public void run() {
	System.out.println("hello");
	}
}.start();
```

---

对集合进行迭代

```java
List<String> strings = Arrays.asList("one", "two", "three");
//不使用lambda表达式
for (String string : strings) {
	System.out.println(string);
}
//使用lambda表达式,两种方法
strings.forEach(x-> System.out.println(x));
strings.forEach(System.out::println);
```

---

用lambda表达式实现map

```java
List<String> strings = Arrays.asList("one", "two", "three");
//map函数可以说是函数编程里最重要的一个方法了。map的作用是将一个对象变换为另外一个
strings.stream().map(x -> x + "10").forEach(x -> System.out.println(x));
```

---

用lambda表达式实现reduce

```java
List<String> strings = Arrays.asList("one", "two", "three");
//reduce函数是将所有值合并为一个
String s = strings.stream().map(x -> x.replace('o', '0')).reduce((sum, x) -> sum + x).get();
System.out.println(s);
```

如果用for循环来做

```java
List<Double> cost = Arrays.asList(10.0, 20.0,30.0);
double sum = 0;
for(double each:cost) {
	each += each * 0.05;
	sum += each;
}
System.out.println(sum);
```

---

filter操作

```java
List<Double> cost = Arrays.asList(10.0, 20.0,30.0,40.0);
List<Double> filteredCost = cost.stream().filter(x -> x > 						25.0).collect(Collectors.toList());
filteredCost.forEach(x -> System.out.println(x));
```

---

与函数式接口Predicate配合

```java
public static void filterTest(List<String> languages, Predicate<String> condition) {
	languages.stream().filter(x -> condition.test(x)).forEach(x -> 				System.out.println(x + " "));
}
public static void main(String[] args) {
	List<String> languages = Arrays.asList("Java","Python","scala","Shell","R");
	System.out.println("Language starts with J: ");
	filterTest(languages,x -> x.startsWith("J"));
	System.out.println("\nLanguage ends with a: ");
	filterTest(languages,x -> x.endsWith("a"));
	System.out.println("\nAll languages: ");
	filterTest(languages,x -> true);
	System.out.println("\nNo languages: ");
	filterTest(languages,x -> false);
	System.out.println("\nLanguage length bigger three: ");
	filterTest(languages,x -> x.length() > 4);
}
```

#### **集合**

List、Set、Map都是接口，前两个继承至Collection接口，Map为独立接口

List子类有ArrayList、LinkedList、Vector

Set子类有HashSet、LinkedHashSet、TreeSet

Map子类有Hashmap、TreeMap、LinkedHashMap、HashTable

Collection接口下还有个Queue接口，子类有优先级队列，双端队列和阻塞队列等。

**同步的集合**

ArrayList、Linkedlist、HashMap、HashSet都是不同步的，Vector、ConcurrentHashMap是同步的。Collections里面提供了使集合同步的方法Collections.synchronizedXXX()，可以返回当前集合的同步对象。

其实返回的集合是Collections里面自己实现的集合类

```java
 static class SynchronizedCollection<E> implements Collection<E>, Serializable {
        final Collection<E> c;  // Backing Collection
        final Object mutex;     // Object on which to synchronize
     //...
 }
//SynchronizedXXX静态内部类大部分都继承至SynchronizedCollection，调用c的方法会放在同步代码块之中，用mutex做互斥量
public void add(int index, E element) {
    synchronized (mutex) {list.add(index, element);}
}
```

**ArrayList**

底层数据结构是数组，查询快，增删慢，线程不安全

```java
transient Object[] elementData; 
//默认容量是10
private static final int DEFAULT_CAPACITY = 10;
//使用有参构造器实例化的时候，会直接创建一个object数组
if (initialCapacity > 0) 
    this.elementData = new Object[initialCapacity];
//使用空参构造器实例化时，直接初始化为一个空数组
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
public ArrayList() {
    this.e，lementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}
//最大容量是整形的最大值-8，因为还要保存一些其他的信息
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
//扩容时，默认扩容为当前数组长度的1.5倍
int newCapacity = oldCapacity + (oldCapacity >> 1);
//查找数组中是否包含某个值，或者获取某个值的下标时，最后执行的都是下面这个函数，从前往后查找
int indexOfRange(Object o, int start, int end) {
	//...
    for (int i = start; i < end; i++) {
        //默认是使用object的equals比较对象是否相等
        if (o.equals(es[i])) {
            return i;
        }
    }
}
//数组的克隆
ArrayList<?> v = (ArrayList<?>) super.clone();
v.elementData = Arrays.copyOf(elementData, size);
v.modCount = 0;
return v;
//使用get方法时，会先判断要获取的下标是否越界
public E get(int index) {
    Objects.checkIndex(index, size);
    return elementData(index);
}
//使用set方法时，也会先判断是否越界。用的是size（数组元素的个数）而不是数组的长度（length）来判断是否越界
public E set(int index, E element) {
    Objects.checkIndex(index, size);
    //...	
}
//添加元素，最终调用的是
private void add(E e, Object[] elementData, int s) {
    //如果s超过数组长度，则进行扩容
    if (s == elementData.length)
        elementData = grow();
}
//在指定位置插入元素
System.arraycopy(elementData, index,
                 elementData, index + 1,
                 s - index);
elementData[index] = element;
//删除元素
private void fastRemove(Object[] es, int i) {
    //...
    if ((newSize = size - 1) > i)
    	System.arraycopy(es, i + 1, es, i, newSize - i);
    es[size = newSize] = null;
}
//判断两个集合是否相等，调用的是对方的迭代器遍历（传入的对象必须实现了List接口）
for (; from < to; from++) {
    if (!oit.hasNext() || !Objects.equals(es[from], oit.next())) {
        return false;
    }
}
//判断两个ArrayList是否相等
for (int i = 0; i < s; i++) {
    if (!Objects.equals(es[i], otherEs[i])) {
        equal = false;
        break;
    }
}
//计算hashCode，用的int值，所以最后算出来的结果不会很大
int hashCode = 1;
for (int i = from; i < to; i++) {
    Object e = es[i];
    hashCode = 31 * hashCode + (e == null ? 0 : e.hashCode());
}
//清空整个数组，是将整个数组中的元素置为null，size置为0，但是数组的length不变
for (int to = size, i = size = 0; i < to; i++)
    es[i] = null;
//添加整个集合，会先判断数组中剩余的空间大小是否能放下要添加集合的大小，不能放下则扩容
public boolean addAll(Collection<? extends E> c) {
    Object[] a = c.toArray();
    int numNew = a.length;
    if (numNew > (elementData = this.elementData).length - (s = size))
        elementData = grow(s + numNew);
    System.arraycopy(a, 0, elementData, s, numNew);
}
//移除某个集合中的所有元素
// 找到第一个包含的元素，然后判断是否被需要删除的集合包含，不包含就挨个往前挪
boolean batchRemove(Collection<?> c, boolean complement,
                    final int from, final int end) {
    final Object[] es = elementData;
    int w = r++;
    try {
        for (Object e; r < end; r++)
            if (c.contains(e = es[r]) == complement)
                es[w++] = e;
    } catch (Throwable ex) {
    } finally {
        shiftTailOverGap(es, w, end);
    }
}
```

**LinkedList**

底层数据结构是双向链表，查询慢，增删快，线程不安全

```java
//底层数据结构
private static class Node<E> {
    E item;
    Node<E> next;
    Node<E> prev;
    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
//维护了头结点和尾节点
transient Node<E> first;
transient Node<E> last;
//清空当前链表
public void clear() {
    for (Node<E> x = first; x != null; ) {
        Node<E> next = x.next;
        x.item = null;
        x.next = null;
        x.prev = null;
        x = next;
    }
    first = last = null;
}
//get和set方法都会先调用这个方法判断是否越界
private void checkElementIndex(int index) {
    if (!isElementIndex(index))
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
private boolean isElementIndex(int index) {
    return index >= 0 && index < size;
}
//添加方法调用的是
private boolean isPositionIndex(int index) {
    return index >= 0 && index <= size;
}
//查找某个位置的元素时，会根据元素位置在前半段或者后半段，选择从头开始遍历还是从尾结点开始
Node<E> node(int index) {
    if (index < (size >> 1)) {
        Node<E> x = first;
    } else {
        Node<E> x = last;
    }
}
/**
以下方法继承至Deque（双端队列）
当定义变量为List<Integer> list = new LinkedList<>();时不能使用以下方法

检索但不删除链表的头部的方法
peek、peekFirst 如果头结点为空则返回null
element 如果头结点为空则抛出异常（getFirst）

检索但不删除链表的尾部的方法
peekLast 如果尾结点为空则返回null

检索并删除此列表的头结点
poll、pollFirst 如果头结点为空则返回null（unlinkFirst）
remove、pop 如果头结点为空则抛出异常（removeFirst）

检索并删除此列表的尾结点
pollLast 如果尾结点为空则返回null（unlinkLast）

添加尾结点
offer、offerLast（linkLast）

添加头节点
offerFirst、push（linkFirst）
*/
//clone方法
public Object clone() {
    //先克隆其他元素，然后将头尾节点置为null
    LinkedList<E> clone = superClone();
    clone.first = clone.last = null;
    clone.size = 0;
    clone.modCount = 0;
    //遍历旧链表，新链表通过尾插法添加元素
    for (Node<E> x = first; x != null; x = x.next)
        clone.add(x.item);
}
```

**Vector**

层数据结构是数组，查询快，增删慢，线程安全

```java
//底层是一个数组
protected Object[] elementData;
//每次扩容的容量大小，如果这个值小于等于0，则将数组容量扩大一倍
protected int capacityIncrement;
//有参构造器
public Vector(int initialCapacity, int capacityIncrement) {
    this.elementData = new Object[initialCapacity];
    this.capacityIncrement = capacityIncrement;
}
//数组大小默认是10，容量增量默认是0
public Vector(int initialCapacity) {
    this(initialCapacity, 0);
}
public Vector() {
    this(10);
}
//对所有需要同步的方法，都加了synchronized修饰
public synchronized void copyInto(Object[] anArray) {}

//最大容量是Integer.MAX_VALUE - 8，和ArrayList一样
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```

#### 文件

BIO

同步阻塞I/O模式，数据的读取写入必须阻塞在一个线程内等待其完成。

NIO

非阻塞式I/O模式，线程在读取或者写入数据时可以进行别的操作

* buffer （缓冲区），bio是面向流（stream）nio面向缓冲区（buffer）
* channel（通道）通道是双向的，可读可写
* selcetors（选择器）单个线程处理多个通道

AIO

异步非阻塞型 IO，是根据事件和回调机制实现的

#### 反射

### WEB

**cookie和session的区别**

会话（session）跟踪是web程序中常用的技术，用来**跟踪用户的整个会话**。常用的会话跟踪技术是Session和Cookies。**Cookies通过在客户端记录信息确认用户身份，Session通过在服务器端记录信息确认用户身份。**

在Web程序中，会话跟踪是很重要的技术。理论上，一个用户的所有请求都应该属于同一个会话，而另一个用户的所有请求操作则应该属于另一个会话，二者不能混淆。例如，用户A在超市买任何东西都应该放在A的购物车内，而不应该放在B或C的购物车内。

Web程序使用的HTTP协议传输数据，**而我们知道HTTP协议是无状态的。一旦数据交换完毕，客户端与服务端的连接就会关闭，再次交换数据需要建立新的连接。这意味着服务器无法从连接上跟踪会话。**即用户A购买了一件商品放入购物车内，当再次购买商品时服务器已经无法判断该购买行为是属于用户A的会话还是用户B的会话了。要跟踪该会话，必须引入一种机制。

而Cookies和Session就是这种机制。可以弥补HTTP无状态协议的不足，在Session出现之前，所有网站都用Cookies来跟踪会话

**Cookies**

1.什么是Cookies

由于HTTP是一种无状态协议，服务器单从网络连接上无法知道客户身份，怎么办呢？**那么就给每个客户发一个通行证吧！每人一个，无论谁访问都必须携带自己通行证。这样服务器就能从通行证上确认客户身份了。这就是Cookie的工作原理。**

2.Cookies工作原理

Cookie实际上是一小段的文本信息。客户端请求服务器，如果服务器需要记录该用户状态，就使用response向客户端浏览器颁发一个Cookie。客户端浏览器会把Cookie保存起来。当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。服务器检查该Cookie，以此来辨认用户状态。服务器还可以根据需要修改Cookie的内容。

3.Java中的Cookies

Java把Cookies封装成了javax.servlet.http.Cookies类。每个Cookies都是该Cookies类的对象。服务器通过操作该Cookies类的对象操作Cookies。**通过request.getCookie()获取客户端提交的所有Cookies**（以Cookie[]数组形式返回），**通过response.addCookie(Cookiecookie)向客户端设置Cookie。**

Cookie对象使用key-value属性对的形式保存用户状态，一个Cookie对象保存一个属性对，一个request或者response同时使用多个Cookie。因为Cookie类位于包javax.servlet.http.*下面，所以JSP中不需要import该类。

4.设置Cookies的所有属性

除了name与value之外，Cookie还具有其他几个常用的属性。每个属性对应一个getter方法与一个setter方法。Cookie类的所有属性如表1.1所示。

| 属 性 名       | 描  述                                                       |
| -------------- | ------------------------------------------------------------ |
| String name    | 该Cookie的名称。Cookie一旦创建，名称便不可更改               |
| Object value   | 该Cookie的值。如果值为Unicode字符，需要为字符编码。如果值为二进制数据，则需要使用BASE64编码 |
| **int maxAge** | **该Cookie失效的时间，单位秒。如果为正数，则该Cookie在maxAge秒之后失效。如果为负数，该Cookie为临时Cookie，关闭浏览器即失效，浏览器也不会以任何形式保存该Cookie。如果为0，表示删除该Cookie。默认为–1** |
| boolean secure | 该Cookie是否仅被使用安全协议传输。安全协议。安全协议有HTTPS，SSL等，在网络上传输数据之前先将数据加密。默认为false |
| String path    | 该Cookie的使用路径。如果设置为“/sessionWeb/”，则只有contextPath为“/sessionWeb”的程序可以访问该Cookie。如果设置为“/”，则本域名下contextPath都可以访问该Cookie。注意最后一个字符必须为“/” |
| String domain  | 可以访问该Cookie的域名。如果设置为“.google.com”，则所有以“google.com”结尾的域名都可以访问该Cookie。注意第一个字符必须为“.” |
| String comment | 该Cookie的用处说明。浏览器显示Cookie信息的时候显示该说明     |
| int version    | 该Cookie使用的版本号。0表示遵循Netscape的Cookie规范，1表示遵循W3C的RFC 2109规范 |

5.Cookies的有效期

Cookies的maxAge代表Cookies的有效期，单位为**秒**，Cookie中通过getMaxAge()方法与setMaxAge(int maxAge)方法来读写maxAge属性。

如果maxAge属性为正数，则表示该Cookie会在maxAge秒之后自动失效。浏览器会将maxAge为正数的Cookie持久化，即写到对应的Cookie文件中。无论客户关闭了浏览器还是电脑，只要还在maxAge秒之前，登录网站时该Cookie仍然有效。下面代码中的Cookie信息将永远有效。

```java
Cookie cookie = new Cookie("username","helloweenvsfei");  // 新建Cookie**

cookie.setMaxAge(Integer.MAX_VALUE);      // 设置生命周期为MAX_VALUE

response.addCookie(cookie);           // 输出到客户端
```

　　如果maxAge为负数，则表示该Cookie仅在本浏览器窗口以及本窗口打开的子窗口内有效，关闭窗口后该Cookie即失效。maxAge为负数的Cookie，为临时性Cookie，不会被持久化，不会被写到Cookie文件中。Cookie信息保存在浏览器内存中，因此关闭浏览器该Cookie就消失了。Cookie默认的maxAge值为–1。

　　如果maxAge为0，则表示删除该Cookie。**Cookie机制没有提供删除Cookie的方法，因此通过设置该Cookie即时失效实现删除Cookie的效果。失效的Cookie会被浏览器从Cookie文件或者内存中删除**

```java
Cookie cookie = new Cookie("username","helloweenvsfei");  // 新建Cookie
cookie.setMaxAge(0);              // 设置生命周期为0，不能为负数
response.addCookie(cookie);           // 必须执行这一句
```

6.Cookies的修改、删除

Cookies不提供修改和删除的对应方法，只提供一个addCookie(Cookie cookie)方法

如果要修改Cookies，只需要新建一个同名的Cookies，添加到response中覆盖掉原先的Cookies

如果要删除Cookies，只需要新建一个同名的Cookies，然后将maxAge设置为0，并添加到response中覆盖掉原来的Cookies

**注意：修改、删除Cookie时，新建的Cookie除value、maxAge之外的所有属性，例如name、path、domain等，都要与原Cookie完全一样。否则，浏览器将视为两个不同的Cookie不予覆盖，导致修改、删除失败。**

**Session**

除了Cookies，Web程序还可以使用Session来记录客户端状态。**Session是服务器端使用的一种记录客户端状态的机制**，使用比Cookies简单，但是**会增大服务端的压力**

1.什么是Session

Session是一种记录客户状态的机制，和Cookies不同的是，Cookies是保存在客户端上，而Session是保存在服务端上的。客户端访问服务器的时候，服务器把客户端的信息以某种方式保存在服务器上，这就是Session。客户端再次访问服务器时只要从Session中找到该客户就行了。

如果说**Cookie机制是通过检查客户身上的“通行证”来确定客户身份的话，那么Session机制就是通过检查服务器上的“客户明细表”来确认客户身份。Session相当于程序在服务器上建立的一份客户档案，客户来访的时候只需要查询客户档案表就可以了。**

2.Session工作原理

Session在Java中对应的类是javax.servlet.http.HttpSession类。每个来访者对应一个session对象，所有该客户的Session都保存在这个Session对象里。**Session对象是客户端在第一次请求服务器时服务器创建的**Session也是一种key-value的属性对，通过getAttribute(Stringkey)和setAttribute(String key，Objectvalue)方法读写客户状态信息。Servlet里通过request.getSession()方法获取该客户的Session

例如：

```java
HttpSession session = request.getSession();       // 获取Session对象

session.setAttribute("loginTime", new Date());     // 设置Session中的属性

out.println("登录时间为：" +(Date)session.getAttribute("loginTime"));      // 获取Session属性
```

request还可以使用getSession(boolean create)来获取Session。区别是如果该客户的Session不存在，request.getSession()方法会返回null，而getSession(true)会先创建Session再将Session返回。

3.Session的生命周期

Session保存在服务端。**为了获得更高的存取速度，服务器一般把Session保存在内存中。每个用户拥有一个独立的Session，如果Session内容过于复杂，当大量用户访问时有可能会导致内存溢出，因此Session应该尽量保持精简。**

**Session在用户第一次访问时自动创建。**只有在访问Jsp和Servlet时才会创建，如果是访问image，html等静态文件，是不会创建Session的。如果尚未生成Session，也可以使用request.getSession(true)强制生成Session。

**Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session。**用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的Session“活跃（active）”了一次。

4.Session的有效期

由于会有越来越多的用户访问服务器，因此Session也会越来越多。**为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了。**

Session的超时时间为maxInactiveInterval属性，可以通过对应的getMaxInactiveInterval()获取，通过setMaxInactiveInterval(longinterval)修改。

Session的超时时间也可以在web.xml中修改。另外，通过调用Session的invalidate()方法可以使Session失效。

5.Session的常用方法

Session中包括各种方法，使用起来要比Cookie方便得多。Session的常用方法如表1.2所示。

表1.2 HttpSession的常用方法

| 方 法 名                                          | 描  述                                                       |
| ------------------------------------------------- | ------------------------------------------------------------ |
| void setAttribute(String attribute, Object value) | 设置Session属性。value参数可以为任何Java Object。通常为Java Bean。value信息不宜过大 |
| String getAttribute(String attribute)             | 返回Session属性                                              |
| Enumeration getAttributeNames()                   | 返回Session中存在的属性名                                    |
| void removeAttribute(String attribute)            | 移除Session属性                                              |
| String getId()                                    | 返回Session的ID。该ID由服务器自动创建，不会重复              |
| long getCreationTime()                            | 返回Session的创建日期。返回类型为long，常被转化为Date类型，例如：Date createTime = new Date(session.get CreationTime()) |
| long getLastAccessedTime()                        | 返回Session的最后活跃时间。返回类型为long                    |
| int getMaxInactiveInterval()                      | 返回Session的超时时间。单位为秒。超过该时间没有访问，服务器认为该Session失效 |
| void setMaxInactiveInterval(int second)           | 设置Session的超时时间。单位为秒                              |
| void putValue(String attribute, Object value)     | 不推荐的方法。已经被setAttribute(String attribute, Object Value)替代 |
| Object getValue(String attribute)                 | 不被推荐的方法。已经被getAttribute(String attr)替代          |
| boolean isNew()                                   | 返回该Session是否是新创建的                                  |
| void invalidate()                                 | 使该Session失效                                              |

Tomcat中Session的默认超时时间为20分钟。通过setMaxInactiveInterval(int seconds)修改超时时间。可以修改web.xml改变Session的默认超时时间。例如修改为60分钟：

```xml
<session-config>
   <session-timeout>60</session-timeout>      <!-- 单位：分钟 -->
</session-config>
```

注意：\<session-timeout>参数的单位为分钟，而setMaxInactiveInterval(int s)单位为秒。

**Cookies和Session的区别**

1. Cookies的数据保存在客户端上，Session的数据保存在服务器上
2. Cookies不是很安全，攻击人可以分析存在本地的Cookies文件并进行Cookies欺骗
3. 设置cookie时间可以使cookie过期。但是使用session-destory（），我们将会销毁会话。
4. Session的数据保存在服务器上，会对服务器造成压力。如果想减轻服务器压力，应当使用Cookies
5. 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。(Session对象没有对存储的数据量的限制，其中可以保存更为复杂的数据类型)
6. Session的生命周期是从浏览器启动到浏览器关闭，即浏览页面一关，Session就消失了。
    Cookies是预先设置存活时间，或永久的保存到本地文件中

### MySQL

```sql
#常用命令
#1.查看当前所有的数据库
show databases;
#2.打开指定的库
use 库名;
#3.查看当前库的所有表
show tables;
#4.查看其它库的所有表
show tables from 库名;
#5.创建表
create table 表名(
	列名 列类型,
	列名 列类型,
	...
);
#6.查看表结构
desc 表名;
```

```sql
#数据库的创建
create database [if not exists] name;
#数据库的删除
drop database [if exists] name;
#数据库的修改
alter database name character set gbk
```

```sql
#表的创建
create table 表名(
	列名 列的类型 【（长度）约束】，
	...
	列名 列的类型 【（长度）约束】
)
#表的修改
#1. 修改列名
alter table 表名 change column 列名 新列名 类型
#2. 修改列的类型或约束
alter table 表名 modify column 列名 类型
#3. 添加新列
alter table 表名 add column 列名 类型 
#4. 删除列
alter table 表名 drop column 列名
#5. 修改表名
alter table 表名 rename to 新表名

#表的删除
drop table 表名
#表的复制
#1. 仅仅复制表的结构
create table 新表名 like 表名

#2.复制表的结构+数据
create table 新表名
select * from 表名

#3. 只复制部分数据
create table 新表名
select 字段
from 表名
where 筛选条件

#4. 仅仅复制某些字段
create table 新表名
select 字段
from 表名
where 0
```

```sql
#添加列级约束：直接进在字段名和类型后面追加约束类型即可，只支持：默认、非空、主键、唯一
#添加表级约束：在各个字段的最下面 constraint 约束名 约束类型（字段）
#六大约束
NOT NULL  #非空，用于保证该字段的值不能为空
DEFAULT #默认，用于保证该字段有默认值
primary key #主键，用于保证该字段的值具有唯一性，并且非空
UNIQUE #唯一：用于保证该字段的值具有唯一性，可以为空
check #检查约束（mysql不支持）
foreign key #外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值，在从表中添加外键约束，用于引用主表中某列的值
```

**主键和唯一的异同**

|      | 保证唯一性 | 是否允许为空 | 一个表可以有几个 | 是否允许组合 |
| :--: | :--------: | :----------: | :--------------: | :----------: |
| 主键 |     √      |      ×       |    至多有一个    | √，但不推荐  |
| 唯一 |     √      |      √       |    可以有多个    | √，但不推荐  |

```sql
#添加非空约束
alter table 表名 modify column 列名 varchar(20) not null

#添加默认约束
alter table 表名 modify column 列名 int default 5

#添加主键
#列级约束
alter table 表名 modify column 列名 类型 primary key
#表级约束
alter table 表名 add primary key(列名)

#添加唯一
#列级约束
alter table 表名 modify column 列名 类型 unique
#表级约束
alter table 表名 add unique(列名)

#添加外键
alter table 表名 add constraint 外键名 foreign key(列名) references 表名(列名)

#删除非空约束
alter table 表名 modify column 列名 类型  null;

#删除默认约束
alter table 表名 modify column 列名 类型

#删除主键
alter table 表名 drop primary key
```

```
标识列，又称自增长列
必须和key搭配
一个表至多只有一个标识列
可以通过set_auto_increment==3设置步长
```

```sql
#级联删除
alter table 表名 add constraint 外键名 foreign key(列名) references major(列名) on delete cascade

#级联置空
alter table 表名 add constraint 外键名 foreign key(列名) references major(列名) on set null
```

```sql
#插入语句
insert into 表名（列名，...） values（值1， ...）
insert into 表名 set 列名=值， 列名=值， 列名=值...
#方式一可以插入多行；
insert into 表名（列名，...） values（值1， ...）， values（值1， ...），values（值1， ...）
#方式一支持子查询
insert into 表名（列名，...） select ...

#修改语句
update 表名 set 列=新值， 列=新值... where 筛选条件
#多表更新
#SQL92语法
update 表1 别名，表2 别名 set 列=值 where 连接条件 and 筛选条件
#SQL99语法
update 表1 别名 inner|left|right join 表2 别名 on 连接条件 set 列=值 where 筛选条件

#删除语句
delete from 表名 where 筛选条件
truncate table 表名
#多表删除
#SQL92语法
delete 别名 from 表1 别名, 表2 别名 where 连接条件 and 筛选条件
#SQL99语法
delete 别名 from 表1 别名 inner|left|right join 表2 别名 on 连接条件 where 筛选条件
/*
truncate和delete的区别
1. delete可以加where条件，truncate不能加
2. truncate删除，效率高一点
3. 假如要删除表中的自增长类
	用delete删除后，再插入数据，自增长列从断点开始
	用truncate删除后，再插入数据，自增长列的值从1开始
4. truncate删除没有返回值，delete删除有返回值
5. truncate删除不能回滚，delete可以回滚
*/
```

```sql
#简单查询语句
select 查询表名 from 表名 where 筛选条件 #where中不允许使用别名

#安全等于<=>
select * from employees where comminssion_pct <=> null;
select * from employees where salary <=> 12000;
#is null 仅仅可以判断null值，可读性较高
#<=> 既可以判断null值，又可以判断普通的数值，可读性较低

select 查询列表 from 表 [where 条件] order by 排序列表 [asc|desc]
/*
asc 是升序
desc 是降序
如果不写，默认是升序
order by字句中可以支持单个字段、多个字段、表达式、函数、别名
order by子句一般是放在查询语句的最后面，limit子句除外
*/
#按表达式排序
select *, salary*12*(1+IFNULL(commission_pct, 0)) 年薪 from employees order by salary*12*(1+IFNULL(commission_pct, 0)) DESC;

#按别名排序
select *, salary*12*(1+IFNULL(commission_pct, 0)) 年薪 from employees order by 年薪 DESC;

#按函数排序
select Length(last_name) 字节长度, last_name, salary from employees order by LENGTH(last_name) desc;

#按多个字段排序，先排的写前面
select * from employees order by salary desc, employee_id desc;
```

```sql
#复杂查询

#联合查询
查询语句1
union
查询语句2
union
...
/*
1. 要求多条查询语句的查询列数是一致的
2. 要求多条查询语句的查询的每一列的类型和顺序最好一致
3. union关键字默认去重，如果使用union all可以留下重复项
*/
```

MySQL调优

1. 选取最适合的字段属性

    * 在创建表的时候，我们可以将表中字段的宽度设得尽可能小，在它上面执行的查询也就会越快

    * 应该尽量把字段设置为NOTNULL，这样在将来执行查询的时候，数据库不用去比较NULL值

    * 对于某些文本字段，例如“省份”或者“性别”，我们可以将它们定义为ENUM类型。因为在MySQL中，ENUM类型被当作数值型数据来处理，而数值型数据被处理起来的速度要比文本类型快得多
2. 使用连接查询来代替子查询
3. 使用联合来代替手动创建临时表
4. 事务
5. 锁定表
6. 使用外键
7. 使用索引
8. 优化的查询语句
9. 应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描
10. 应尽量避免在 where 子句中使用 != 或 <> 操作符，否则将引擎放弃使用索引而进行全表扫描。
11. 如果在 where 子句中使用参数，也会导致全表扫描
12. 对于多张大数据量（这里几百条就算大了）的表JOIN，要先分页再JOIN，否则逻辑读会很高，性能很差
13. 任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段。

**索引**



**范式**

1NF 每个属性都不可再分

2NF 消除了非主属性对码的部分函数依赖

3NF 消除了非主属性对码的传递依赖

BCNF 在3NF的基础上消除主属性对于码的部分依赖与传递函数依赖

### ES

**为什么用es**

es是一个实时分布式搜索和分析引擎，基于 Lucene 的全文检索引擎，它会对数据进行分词后保存索引，擅长管理大量的索引数据，相对于 MySQL 来说不擅长经常更新数据及关联查询。

对已有的数据进行搜索的时候，Solr更快

当实时建立索引的时候solr会产生io阻塞，查询性能较差

随着数据量的增加，Solr的搜索效率会比较低，而es没有明显的变化

es采用倒排索引搜索数据

**什么是倒排索引**

采用lucene倒排索引作为底层，倒排索引结构是根据内容（词语）找文档

### Spring

spring的基本特性：ioc和aop

#### ioc

ioc：控制反转。将需要的组件交给容器去管理。在容器初始化的时候，会加载所有的非抽象的非懒加载的单实例bean，存放在map中。在需要的时候，直接从容器中获取就可以了。多实例的bean在每次获取的时候创建一个对象。获取的时候，会先按照类型去找，然后按照变量名作id去找

流程：

1. spring容器在启动的时候，会先保存所有注册进来的Bean的定义信息

    1. xml注册bean；<bean/>
    2. 注解注册bean；@Service、@Component、@Bean

2. spring容器会在合适的时机创建这些bean

    1. 用到这个bean的时候，利用getBean创建bean，创建好以后保存在容器中
    2. 统一创建剩下所有bean的时候，finishBeanFactoryInitialization

3. 后置处理器：BeanPostProcessor

    1. 每一个bean创建完成，都会使用各种后置处理器进行处理，来增强bean功能

        AutowiredAnnotationBeanPostProcessor；处理自动注入

        AnnotationAwareAspectJAutoProxyCreator；用来做AOP功能

4. 事件驱动模型

    ApplicationListener；事件监听

    ApplicationEventMulticaster；事件派发

#### aop

aop：面向切面编程。在程序运行期间，**将某段代码动态的切入到指定方法的指定位置**进行运行的这种编程方式。

1. 先创建所有的BeanPostProcessor，然后创建所有的单实例bean。先从缓存中获取当前bean，如果能获取到，说明bean是之前创建过的，直接使用，否则再创建（只要创建好的bean都会被缓存起来）

2. AnnotationAwareAspectJAutoProxyCreator会在任何bean创建之前先尝试返回bean的实例

    1. 每一个bean创建之前，调用postProcessBeforeInstantiation()
        1. 判断当前bean是否在advisedBeans中（保存了所有需要增强的bean）
        2. 判断当前bean是否是基础类型的Advice、Pointcut、Advisor、AopInfrastructureBean
        3. 是否需要跳过
    2. 创建对象
        1. 获取当前bean的所有增强器（通知方法）Object[] specificInterceptors
            1. 找到候选的所有增强器（找哪些通知方法是需要切入当前bean方法的）
            2. 获取到能在bean使用的增强器
            3. 给增强器排序eligibleAdvisors = sortAdvisors(eligibleAdvisors);
        2. 保存当前bean在advisedBean中
        3. 如果当前bean需要增强，创建当前bean的代理对象
            1.  获取所有增强器（通知方法）
            2. 保存到proxyFactory
            3. 创建代理对象，spring自动决定
        4. 给容器中返回当前组件使用cglib增强了的代理对象
        5. 以后容器中获取到的就是这个组件的代理对象，执行目标方法的时候，代理对象就会执行通知方法的流程

3. 目标方法的执行

    容器中保存了组件的代理对象（cglib增强后的对象），这个对象里面保存了详细信息（比如增强器，目标对象，xxx）

    1. CglibAopProxy.intercept()拦截目标方法的执行
    2. 根据ProxyFactory对象获取要执行的目标方法的拦截器链
        1. 遍历所有的增强器，将其转换为Interceptor
    3. 如果没有拦截器链，直接执行目标方法
    4. 如果有拦截器链，把需要执行的目标对象，目标方法，拦截器链等信息传入，创建一个CglibMethodInvocation对象
    5. 拦截器的触发过程
        1. 如果没有拦截器，就执行目标方法，或者拦截器的索引和拦截器数组-1大小一样（指定到了最后一个拦截器）执行目标方法
        2. 链式获取每一个拦截器，拦截器执行invoke方法，每一个拦截器等待下一个拦截器执行完成返回以后再来执行。拦截器链的机制，保证通知方法与目标方法的执行顺序

### Redis

**redis过期时间**

键的默认过期时间的永不过期

expire和pexpire可以将键的生存时间设置多少秒或者毫秒(p)

expireat和pexpireat可以将键的过期时间设置为指定的毫秒数时间戳

但是这四个命令内部都是调用同一个函数实现的

```c
expireGenericCommand(redisClient *c, long long basetime, int unit)
```

也可以通过ttl和pttl查看键的生存时间还有多久

```c
ttlGenericCommand(redisClient *c, int output_ms) //output_ms为1则返回毫秒，为0返回秒
```

**数据类型**

String，list，set，zset，hash

string可以存一些简单的字符串，比如房源信息的浏览量

hash可以存对象信息，比如把id作为key，filed-value则放入房源名称，描述等信息

list可以存储用户收藏的房屋信息，key是用户的id，value则是房屋id

set可以用来放共同关注

zset可以根据房屋搜索的相关度进行排序

**redis为什么快**



### HR

**自我介绍**

面试官你好，我叫黄雷，毕业于江西农业大学软件学院。在用友工作了两年，这两年来我刻苦学习，努力工作，因表现优异被多次评为优秀员工。在编程方面，我有着浓厚的兴趣。在兴趣的驱使下，我在工作期间研读了公司框架的源码，对公司的技术有了进一步的了解，极大的提高了自己的工作效率。在生活中，我待人真诚热情、为人随和、有良好的人际关系和很强的适应能力。我的自我介绍结束了。请面试官多多指教，谢谢。

**你觉得你个性上最大的优点是什么？**

沉着冷静、条理清楚、立场坚定、顽强向上。 乐于助人和关心他人、适应能力和幽默感、乐观和友爱。

**说说你最大的缺点？**

比较容易担心某件事。比如有个a任务，明天要交给领导，交给领导之前我会核实好多次。上交之后，我可能还会时不时的想想那个任务是不是做完了，还有没有什么缺陷。导致我在做接下来的任务时，没法专注进去。但是我已经意识到了这件事，我会改进的。

**你对加班的看法？**

如果工作任务紧急，那么加班完成工作任务，这是个人职责。但是，如果我天天都加班，我会反思，是不是我自己的效率太低了。我会通过提高工作效率来避免不必要的加班。

**你对薪资的要求**

没有具体的薪资要求。因为我觉得公司会根据我的情况和市场标准的水平，给我合理的薪水。

**职业规划**

我准备在技术领域有所作为，对公司所用的技术，我将不断的深入研究

**朋友对你的评价**

我觉的我是一个比较随和的人，与不同的人都可以友好相处。在我与人相处时， 我总是能站在别人的角度考虑问题

**如果通过这次面试我们单位录用了你，但工作一段时间却发现你根本不适合这个职位， 你怎么办？**

不断学习，虚心向领导和同事学习业务知识和处事 经验，了解这个职业的精神内涵和职业要求，力争减少差距

**你并非毕业于名牌院校？**

是否毕业于名牌院校不重要，重要的是有能力完成公司交给我的工作。大学只是一个地点，再好的大学也不能代替你学习，还得看自己的努力。而且在互联网发达的今天，我们能获得的资源不比他们少。

**反问**

如果我能进入贵公司，公司对新员工有没有什么培训项目？

公司的日常工作时间是怎么安排的？

公司的晋升机制

