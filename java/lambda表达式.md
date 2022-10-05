### lambda入门

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

### 替代匿名内部类

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

### 对集合进行迭代

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

### 用lambda表达式实现map

```java
List<String> strings = Arrays.asList("one", "two", "three");
//map函数可以说是函数编程里最重要的一个方法了。map的作用是将一个对象变换为另外一个
strings.stream().map(x -> x + "10").forEach(x -> System.out.println(x));
```

---

### 用lambda表达式实现reduce

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

### filter操作

```java
List<Double> cost = Arrays.asList(10.0, 20.0,30.0,40.0);
List<Double> filteredCost = cost.stream().filter(x -> x > 						25.0).collect(Collectors.toList());
filteredCost.forEach(x -> System.out.println(x));
```

---

### 与函数式接口Predicate配合

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

