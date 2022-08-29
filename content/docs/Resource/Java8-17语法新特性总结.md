---
weight: 301
math: true
---

# Java8-17语法新特性总结

> 班级：202115
>
> 学号：19377054
>
> 姓名：周洪熙

[toc]

## Java 8

### Interface

interface 的设计初衷是面向抽象，提高扩展性。这也留有一点遗憾，Interface 修改的时候，实现它的类也必须跟着改。

为了解决接口的修改与现有的实现不兼容的问题。新 interface 的方法可以用 `default` 或 `static` 修饰，这样就可以有方法体，实现类也不必重写此方法。

一个 interface 中可以有多个方法被它们修饰，这 2 个修饰符的区别主要也是普通方法和静态方法的区别。

`default` 修饰的方法，是普通实例方法，可以用this调用，可以被子类继承、重写。
`static` 修饰的方法，使用上和一般类静态方法一样。但它不能被子类继承，只能用Interface调用。
我们来看一个实际的例子。

```java
public interface InterfaceNew {
    static void sm() {
        System.out.println("interface提供的方式实现");
    }
    static void sm2() {
        System.out.println("interface提供的方式实现");
    }

    default void def() {
        System.out.println("interface default方法");
    }
    default void def2() {
        System.out.println("interface default2方法");
    }
    //须要实现类重写
    void f();
}

public interface InterfaceNew1 {
    default void def() {
        System.out.println("InterfaceNew1 default方法");
    }
}
```

如果有一个类既实现了 `InterfaceNew` 接口又实现了 `InterfaceNew1` 接口，它们都有 `def()` ，并且 `InterfaceNew` 接口和 `InterfaceNew1` 接口没有继承关系的话，这时就必须重写 `def()` 。不然的话，编译的时候就会报错。

```java
public class InterfaceNewImpl implements InterfaceNew , InterfaceNew1{
    public static void main(String[] args) {
        InterfaceNewImpl interfaceNew = new InterfaceNewImpl();
        interfaceNew.def();
    }

    @Override
    public void def() {
        InterfaceNew1.super.def();
    }

    @Override
    public void f() {
    }
}
```

### Lambda 表达式

```java
(parameters) -> expression 或
(parameters) ->{ statements; }
```

Example: 替代匿名内部类

```java
List<Integer> strings = Arrays.asList(1, 2, 3);

//以前
Collections.sort(strings, new Comparator<Integer>() {
@Override
public int compare(Integer o1, Integer o2) {
    return o1 - o2;}
});

//Lambda
Collections.sort(strings, (Integer o1, Integer o2) -> o1 - o2);
//分解开
Comparator<Integer> comperator = (Integer o1, Integer o2) -> o1 - o2;
Collections.sort(strings, comperator);
```

Example: 集合迭代

```java
void lamndaFor() {
        List<String> strings = Arrays.asList("1", "2", "3");
        //传统foreach
        for (String s : strings) {
            System.out.println(s);
        }
        //Lambda foreach
        strings.forEach((s) -> System.out.println(s));
        //or
        strings.forEach(System.out::println);
        //map
        Map<Integer, String> map = new HashMap<>();
        map.forEach((k,v) -> System.out.println(v));
}
```

Example: 访问变量

```java
int i = 0;
Collections.sort(strings, (Integer o1, Integer o2) -> o1 - i);
```

lambda 表达式可以引用外部变量，但是该变量默认拥有 final 属性，不能被修改，如果修改，编译时就报错。

### 方法的引用

Java 8 允许使用 `::` 关键字来传递方法或者构造函数引用，无论如何，表达式返回的类型必须是 functional-interface。

```java
public class LambdaClassSuper {
    LambdaInterface sf(){
        return null;
    }
}

public class LambdaClass extends LambdaClassSuper {
    public static LambdaInterface staticF() {
        return null;
    }

    public LambdaInterface f() {
        return null;
    }

    void show() {
        //1.调用静态函数，返回类型必须是functional-interface
        LambdaInterface t = LambdaClass::staticF;

        //2.实例方法调用
        LambdaClass lambdaClass = new LambdaClass();
        LambdaInterface lambdaInterface = lambdaClass::f;

        //3.超类上的方法调用
        LambdaInterface superf = super::sf;

        //4. 构造方法调用
        LambdaInterface tt = LambdaClassSuper::new;
    }
}
```

### Optional

> 防止 NPE，是程序员的基本修养，注意 NPE 产生的场景：
> 1） 返回类型为基本数据类型，return 包装数据类型的对象时，自动拆箱有可能产生 NPE。
> 反例：public int f() { return Integer 对象}， 如果为 null，自动解箱抛 NPE。
> 2） 数据库的查询结果可能为 null。
> 3） 集合里的元素即使 isNotEmpty，取出的数据元素也可能为 null。
> 4） 远程调用返回对象时，一律要求进行空指针判断，防止 NPE。
> 5） 对于 Session 中获取的数据，建议进行 NPE 检查，避免空指针。
> 6） 级联调用 obj.getA().getB().getC()；一连串调用，易产生 NPE。
> 正例：使用 JDK8 的 Optional 类来防止 NPE 问题。

#### 如何创建一个 Optional

```java
/**
* Common instance for {@code empty()}. 全局EMPTY对象
*/
private static final Optional<?> EMPTY = new Optional<>();

/**
* Optional维护的值
*/
private final T value;

/**
* 如果value是null就返回EMPTY，否则就返回of(T)
*/
public static <T> Optional<T> ofNullable(T value) {
   return value == null ? empty() : of(value);
}
/**
* 返回 EMPTY 对象
*/
public static<T> Optional<T> empty() {
   Optional<T> t = (Optional<T>) EMPTY;
   return t;
}
/**
* 返回Optional对象
*/
public static <T> Optional<T> of(T value) {
    return new Optional<>(value);
}
/**
* 私有构造方法，给value赋值
*/
private Optional(T value) {
  this.value = Objects.requireNonNull(value);
}
/**
* 所以如果of(T value) 的value是null，会抛出NullPointerException异常，这样貌似就没处理NPE问题
*/
public static <T> T requireNonNull(T obj) {
  if (obj == null)
         throw new NullPointerException();
  return obj;
}
```

`ofNullable` 方法和 `of` 方法唯一区别就是当 value 为 null 时， `ofNullable` 返回的是EMPTY， `of` 会抛出 `NullPointerException` 异常。如果需要把 `NullPointerException` 暴漏出来就用 `of` ，否则就用 `ofNullable` 。

#### map()相关方法

```java
/**
* 如果value为null，返回EMPTY，否则返回Optional封装的参数值
*/
public<U> Optional<U> map(Function<? super T, ? extends U> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Optional.ofNullable(mapper.apply(value));
        }
}
/**
* 如果value为null，返回EMPTY，否则返回Optional封装的参数值，如果参数值返回null会抛 NullPointerException
*/
public<U> Optional<U> flatMap(Function<? super T, Optional<U>> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Objects.requireNonNull(mapper.apply(value));
        }
}
```

`flatMap()` 参数返回值如果是 null 会抛 `NullPointerException` ，而 `map()` 返回EMPTY。

#### 判断 value 是否为 null

```java
/**
* value是否为null
*/
public boolean isPresent() {
    return value != null;
}
/**
* 如果value不为null执行consumer.accept
*/
public void ifPresent(Consumer<? super T> consumer) {
   if (value != null)
    consumer.accept(value);
}
```

#### 获取 value

```java
/**
* Return the value if present, otherwise invoke {@code other} and return
* the result of that invocation.
* 如果value != null 返回value，否则返回other的执行结果
*/
public T orElseGet(Supplier<? extends T> other) {
    return value != null ? value : other.get();
}

/**
* 如果value != null 返回value，否则返回T
*/
public T orElse(T other) {
    return value != null ? value : other;
}

/**
* 如果value != null 返回value，否则抛出参数返回的异常
*/
public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X {
        if (value != null) {
            return value;
        } else {
            throw exceptionSupplier.get();
        }
}
/**
* value为null抛出NoSuchElementException，不为空返回value。
*/
public T get() {
  if (value == null) {
      throw new NoSuchElementException("No value present");
  }
  return value;
}
```

#### 过滤值

```java
/**
* 1. 如果是empty返回empty
* 2. predicate.test(value)==true 返回this，否则返回empty
*/
public Optional<T> filter(Predicate<? super T> predicate) {
        Objects.requireNonNull(predicate);
        if (!isPresent())
            return this;
        else
            return predicate.test(value) ? this : empty();
}
```

### Date-Time API

这是对 `java.util.Date` 强有力的补充，解决了 Date 类的大部分痛点：

1. 非线程安全
2. 时区处理麻烦
3. 各种格式化、和时间计算繁琐
4. 设计有缺陷，Date 类同时包含日期和时间；还有一个 java.sql.Date，容易混淆。

我们从常用的时间实例来对比 java.util.Date 和新 Date 有什么区别。用 `java.util.Date` 的代码该改改了。

#### java.time 主要类

```java
LocalDateTime.class //日期+时间 format: yyyy-MM-ddTHH:mm:ss.SSS
LocalDate.class //日期 format: yyyy-MM-dd
LocalTime.class //时间 format: HH:mm:ss
```

#### 格式化

```java
public void newFormat() {
    //format yyyy-MM-dd
    LocalDate date = LocalDate.now();
    System.out.println(String.format("date format : %s", date));

    //format HH:mm:ss
    LocalTime time = LocalTime.now().withNano(0);
    System.out.println(String.format("time format : %s", time));

    //format yyyy-MM-dd HH:mm:ss
    LocalDateTime dateTime = LocalDateTime.now();
    DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    String dateTimeStr = dateTime.format(dateTimeFormatter);
    System.out.println(String.format("dateTime format : %s", dateTimeStr));
}
```

#### 字符串转日期格式

```java
LocalDate date = LocalDate.of(2021, 1, 26);
LocalDate.parse("2021-01-26");

LocalDateTime dateTime = LocalDateTime.of(2021, 1, 26, 12, 12, 22);
LocalDateTime.parse("2021-01-26 12:12:22");

LocalTime time = LocalTime.of(12, 12, 22);
LocalTime.parse("12:12:22");
```

#### 日期计算

```java
public void pushWeek() {
    //一周后的日期
    LocalDate localDate = LocalDate.now();
    //方法1
    LocalDate after = localDate.plus(1, ChronoUnit.WEEKS);
    //方法2
    LocalDate after2 = localDate.plusWeeks(1);
    System.out.println("一周后日期：" + after);

    //算两个日期间隔多少天，计算间隔多少年，多少月
    LocalDate date1 = LocalDate.parse("2021-02-26");
    LocalDate date2 = LocalDate.parse("2021-12-23");
    Period period = Period.between(date1, date2);
    System.out.println("date1 到 date2 相隔："
               + period.getYears() + "年"
               + period.getMonths() + "月"
               + period.getDays() + "天");
    //打印结果是 “date1 到 date2 相隔：0年9月27天”
    //这里period.getDays()得到的天是抛去年月以外的天数，并不是总天数
    //如果要获取纯粹的总天数应该用下面的方法
    long day = date2.toEpochDay() - date1.toEpochDay();
    System.out.println(date2 + "和" + date2 + "相差" + day + "天");
    //打印结果：2021-12-23和2021-12-23相差300天
}
```

#### 获取指定日期

```java
public void getDayNew() {
    LocalDate today = LocalDate.now();
    //获取当前月第一天：
    LocalDate firstDayOfThisMonth = today.with(TemporalAdjusters.firstDayOfMonth());
    // 取本月最后一天
    LocalDate lastDayOfThisMonth = today.with(TemporalAdjusters.lastDayOfMonth());
    //取下一天：
    LocalDate nextDay = lastDayOfThisMonth.plusDays(1);
    //当年最后一天
    LocalDate lastday = today.with(TemporalAdjusters.lastDayOfYear());
    //2021年最后一个周日，如果用Calendar是不得烦死。
    LocalDate lastMondayOf2021 = LocalDate.parse("2021-12-31").with(TemporalAdjusters.lastInMonth(DayOfWeek.SUNDAY));
}
```

#### 时区

```java
//当前时区时间
ZonedDateTime zonedDateTime = ZonedDateTime.now();
System.out.println("当前时区时间: " + zonedDateTime);

//东京时间
ZoneId zoneId = ZoneId.of(ZoneId.SHORT_IDS.get("JST"));
ZonedDateTime tokyoTime = zonedDateTime.withZoneSameInstant(zoneId);
System.out.println("东京时间: " + tokyoTime);

// ZonedDateTime 转 LocalDateTime
LocalDateTime localDateTime = tokyoTime.toLocalDateTime();
System.out.println("东京时间转当地时间: " + localDateTime);

//LocalDateTime 转 ZonedDateTime
ZonedDateTime localZoned = localDateTime.atZone(ZoneId.systemDefault());
System.out.println("本地时区时间: " + localZoned);

//打印结果
当前时区时间: 2021-01-27T14:43:58.735+08:00[Asia/Shanghai]
东京时间: 2021-01-27T15:43:58.735+09:00[Asia/Tokyo]
东京时间转当地时间: 2021-01-27T15:43:58.735
当地时区时间: 2021-01-27T15:53:35.618+08:00[Asia/Shanghai]
```

## Java 9

### JShell

JShell 是 Java 9 新增的一个实用工具。为 Java 提供了类似于 Python 的实时命令行交互工具。
可以在终端使用 `jshell` 命令进入。

### 模块化系统

什么是模块系统？
简单来说，你可以将一个模块看作是一组唯一命名、可重用的包、资源和模块描述文件（module-info.java）。
任意一个 jar 文件，只要加上一个模块描述文件（module-info.java），就可以升级为一个模块。

在引入了模块系统之后，JDK 被重新组织成 94 个模块。Java 应用可以通过新增的 jlinkopen in new window 工具 (Jlink 是随 Java 9 一起发布的新命令行工具。它允许开发人员为基于模块的 Java 应用程序创建自己的轻量级、定制的 JRE)，创建出只包含所依赖的 JDK 模块的自定义运行时镜像。这样可以极大的减少 Java 运行时环境的大小。

在JDK 9中，您可以如下声明这两个模块：

```java
module policy {
    exports pkg1;
}
module claim {
    requires policy;
}
```

我们可以通过 `exports` 关键词精准控制哪些类可以对外开放使用，哪些类只能内部使用。

```java
module my.module {
    //exports 公开指定包的所有公共成员
    exports com.my.package.name;
}

module my.module {
     //exports…to 限制访问的成员范围
    export com.my.package.name to com.specific.package;
}
```

具体地，参考：
[Java 9 揭秘（2. 模块化系统）](https://www.cnblogs.com/IcanFixIt/p/6947763.html)

### 快速创建*不可变集合*

```java
List.of("Java", "C++");
Set.of("Java", "C++");
Map.of("Java", 1, "C++", 2);
```

### 接口私有方法

```java
public interface MyInterface {
    private void methodPrivate(){
    }
}
```

### try-with-resources 增强

在 Java 9 之前，我们只能在 `try-with-resources` 块中声明变量：

```java
try (Scanner scanner = new Scanner(new File("testRead.txt"));
    PrintWriter writer = new PrintWriter(new File("testWrite.txt"))) {
    // omitted
}
```

在 Java 9 之后，在 `try-with-resources` 语句中可以使用 effectively-final 变量。

```java
final Scanner scanner = new Scanner(new File("testRead.txt"));
PrintWriter writer = new PrintWriter(new File("testWrite.txt"))
try (scanner;writer) {
    // omitted
}
```

什么是 effectively-final 变量？ 简单来说就是没有被 `final` 修饰但是值在初始化后从未更改的变量。

正如上面的代码所演示的那样，即使 `writer` 变量没有被显示声明为 `final` ，但它在第一次被赋值后就不会改变了，因此，它就是 effectively-final 变量。

### Stream & Optional 增强

```java
// Stream

// ofNullable()
Stream<String> stringStream = Stream.ofNullable("Java");
System.out.println(stringStream.count());// 1
Stream<String> nullStream = Stream.ofNullable(null);
System.out.println(nullStream.count());//0

// takeWhile()
List<Integer> integerList = List.of(11, 33, 66, 8, 9, 13);
integerList.stream().takeWhile(x -> x < 50).forEach(System.out::println);// 11 33

// dropWhile()
List<Integer> integerList2 = List.of(11, 33, 66, 8, 9, 13);
integerList2.stream().dropWhile(x -> x < 50).forEach(System.out::println);// 66 8 9 13

// iterate()
public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f) {
}
// 新增加的重载方法
public static<T> Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> next) {
}
// 使用原始 iterate() 方法输出数字 1~10
Stream.iterate(1, i -> i + 1).limit(10).forEach(System.out::println);
// 使用新的 iterate() 重载方法输出数字 1~10
Stream.iterate(1, i -> i <= 10, i -> i + 1).forEach(System.out::println);

// Optional

// ifPresentOrElse()
public void ifPresentOrElse(Consumer<? super T> action, Runnable emptyAction)

Optional<Object> objectOptional = Optional.empty();
objectOptional.ifPresentOrElse(System.out::println, () -> System.out.println("Empty!!!"));// Empty!!!

// or()
public Optional<T> or(Supplier<? extends Optional<? extends T>> supplier)

Optional<Object> objectOptional = Optional.empty();
objectOptional.or(() -> Optional.of("java")).ifPresent(System.out::println);//java
```

### 进程 API

```java
// java.lang.ProcessHandle
// 获取当前正在运行的 JVM 的进程
ProcessHandle currentProcess = ProcessHandle.current();
// 输出进程的 id
System.out.println(currentProcess.pid());
// 输出进程的信息
System.out.println(currentProcess.info());
```

## Java 10

### 局部变量类型推断(var)

```java
var id = 0;
var codefx = new URL("https://mp.weixin.qq.com/");
var list = new ArrayList<>();
var list = List.of(1, 2, 3);
var map = new HashMap<String, String>();
var p = Paths.of("src/test/java/Java9FeaturesTest.java");
var numbers = List.of("a", "b", "c");
for (var n : list)
    System.out.print(n+ " ");

var count=null; //❌编译不通过，不能声明为 null
var r = () -> Math.random();//❌编译不通过,不能声明为 Lambda表达式
var array = {1,2,3};//❌编译不通过,不能声明数组
```

var 并不会改变 Java 是一门静态类型语言的事实，编译器负责推断出类型。

### 集合增强

```java
// List，Set，Map 提供了静态方法copyOf()返回入参集合的一个不可变拷贝
static <E> List<E> copyOf(Collection<? extends E> coll) {
    return ImmutableCollections.listCopy(coll);
}

// java.util.stream.Collectors 中新增了静态方法，用于将流中的元素收集为不可变的集合
var list = new ArrayList<>();
list.stream().collect(Collectors.toUnmodifiableList());
list.stream().collect(Collectors.toUnmodifiableSet());
```

### Optional 增强

```java
// Optional 新增了orElseThrow()方法来在没有值时抛出指定的异常
Optional.ofNullable(cache.getIfPresent(key))
        .orElseThrow(() -> new PrestoException(NOT_FOUND, "Missing entry found for key: " + key));
```

## Java 11

### HTTP Client 标准化

这里建议使用第三方库 `com.squareup.okhttp3` ，这里就不再过多介绍原生API。

### String 增强

```java
//判断字符串是否为空
" ".isBlank();//true
//去除字符串首尾空格
" Java ".strip();// "Java"
//去除字符串首部空格
" Java ".stripLeading();   // "Java "
//去除字符串尾部空格
" Java ".stripTrailing();  // " Java"
//重复字符串多少次
"Java".repeat(3);             // "JavaJavaJava"
//返回由行终止符分隔的字符串集合。
"A\nB\nC".lines().count();    // 3
"A\nB\nC".lines().collect(Collectors.toList());
```

### Optional 增强

```java
// 新增了empty()方法来判断指定的 Optional 对象是否为空
var op = Optional.empty();
System.out.println(op.isEmpty());//判断指定的 Optional 对象是否为空
```

### Lambda 参数的局部变量语法

```java
// Java11 开始允许开发者在 Lambda 表达式中使用 var 进行参数声明
// 下面两者是等价的
Consumer<String> consumer = (var i) -> System.out.println(i);
Consumer<String> consumer = (String i) -> System.out.println(i);
```

## Java 12

### String 增强

```java
// indent() 方法可以实现字符串缩进
String text = "Java";
// 缩进 4 格
text = text.indent(4);
System.out.println(text);
text = text.indent(-10);
System.out.println(text);
// output
    Java
Java


// transform() 方法可以用来转变指定字符串
String result = "foo".transform(input -> input + " bar");
System.out.println(result); // foo bar
```

### Files 增强（文件比较）

```java
// mismatch() 方法用于比较两个文件，并返回第一个不匹配字符的位置，如果文件相同则返回 -1L
Path filePath1 = Files.createTempFile("file1", ".txt");
Path filePath2 = Files.createTempFile("file2", ".txt");
Files.writeString(filePath1, "Java 12 Article");
Files.writeString(filePath2, "Java 12 Article");

long mismatch = Files.mismatch(filePath1, filePath2);
assertEquals(-1, mismatch);

// 不相同
Path filePath3 = Files.createTempFile("file3", ".txt");
Path filePath4 = Files.createTempFile("file4", ".txt");
Files.writeString(filePath3, "Java 12 Article");
Files.writeString(filePath4, "Java 12 Tutorial");

long mismatch = Files.mismatch(filePath3, filePath4);
assertEquals(8, mismatch);
```

### 数字格式化工具类

```java
// NumberFormat 新增了对复杂的数字进行格式化的支持
NumberFormat fmt = NumberFormat.getCompactNumberInstance(Locale.US, NumberFormat.Style.SHORT);
String result = fmt.format(1000);
System.out.println(result);

// output
1K
```

## Java 13

### SocketAPI 重构

Java 13 将 Socket API 的底层进行了重写， `NioSocketImpl` 是对 `PlainSocketImpl` 的直接替代，它使用 `java.util.concurrent` 包下的锁而不是同步方法。如果要使用旧实现，请使用 `-Djdk.net.usePlainSocketImpl=true` 。

并且，在 Java 13 中是默认使用新的 Socket 实现。

```java
public final class NioSocketImpl extends SocketImpl implements PlatformSocketImpl {
}
```

### FileSystems

`FileSystems` 类中添加了以下三种新方法，以便更容易地使用将文件内容视为文件系统的文件系统提供程序：

- `newFileSystem(Path)`
- `newFileSystem(Path, Map<String, ?>)`
- `newFileSystem(Path, Map<String, ?>, ClassLoader)`

## Java 14

### 空指针异常精准提示

通过 JVM 参数中添加 `-XX:+ShowCodeDetailsInExceptionMessages` ，可以在空指针异常中获取更为详细的调用信息，更快的定位和解决问题。

```java
a.b.c.i = 99; // 假设这段代码会发生空指针

// 增加参数后提示的异常中很明确的告知了哪里为空导致
Exception in thread "main" java.lang.NullPointerException:
        Cannot read field 'c' because 'a.b' is null.
    at Prog.main(Prog.java:5)
```

### switch 的增强

```java
// 传统的 switch 语法存在容易漏写 break 的问题，而且从代码整洁性层面来看，多个 break 本质也是一种重复
// Java12 增强了 swtich 表达式，使用类似 lambda 语法条件匹配成功后的执行块，不需要多写 break
switch (day) {
    case MONDAY, FRIDAY, SUNDAY -> System.out.println(6);
    case TUESDAY                -> System.out.println(7);
    case THURSDAY, SATURDAY     -> System.out.println(8);
    case WEDNESDAY              -> System.out.println(9);
}

// Java 13 中 Switch 表达式中多了一个关键字用于跳出 Switch 块的关键字 yield，主要用于返回一个值
// yield和 return 的区别在于：return 会直接跳出当前循环或者方法，而 yield 只会跳出当前 Switch 块，同时在使用 yield 时，需要有 default 条件

// ex1
String result = switch (day) {
            case "M", "W", "F" -> "MWF";
            case "T", "TH", "S" -> "TTS";
            default -> {
                if (day.isEmpty())
                    yield "Please insert a valid day.";
                else
                    yield "Looks like a Sunday.";
            }

        };
System.out.println(result);

// ex2
private static String descLanguage(String name) {
        return switch (name) {
            case "Java": yield "object-oriented, platform independent and secured";
            case "Ruby": yield "a programmer's best friend";
            default: yield name +" is a good language";
        };
}
```

## Java 15

### CharSequence

```java
// CharSequence 接口添加了一个默认方法 isEmpty() 来判断字符序列为空，如果是则返回 true
public interface CharSequence {
    default boolean isEmpty() {
        return this.length() == 0;
    }
}
```

### TreeMap

`TreeMap` 新引入了下面这些方法：

- `putIfAbsent()`
- `computeIfAbsent()`
- `computeIfPresent()`
- `compute()`
- `merge()`

### EdDSA(数字签名算法)

```java
// 新加入了一个安全性和性能都更强的基于 Edwards-Curve Digital Signature Algorithm （EdDSA）实现的数字签名算法。
// 虽然其性能优于现有的 ECDSA 实现，不过，它并不会完全取代 JDK 中现有的椭圆曲线数字签名算法( ECDSA)。
KeyPairGenerator kpg = KeyPairGenerator.getInstance("Ed25519");
KeyPair kp = kpg.generateKeyPair();

byte[] msg = "test_string".getBytes(StandardCharsets.UTF_8);

Signature sig = Signature.getInstance("Ed25519");
sig.initSign(kp.getPrivate());
sig.update(msg);
byte[] s = sig.sign();

String encodedString = Base64.getEncoder().encodeToString(s);
System.out.println(encodedString);
```

### 文本块

解决 Java 定义多行字符串时只能通过换行转义或者换行连接符来变通支持的问题，引入三重双引号来定义多行文本。
Java 13 支持两个 `"""` 符号中间的任何内容都会被解释为字符串的一部分，包括换行符。

```java
String json = """
                {
                    "name":"mkyong",
                    "age":38
                }
                """;

String query = """
               SELECT `EMP_ID`, `LAST_NAME` FROM `EMPLOYEE_TB`
               WHERE `CITY` = 'INDIANAPOLIS'
               ORDER BY `EMP_ID`, `LAST_NAME`;
               """;
```

另外， `String` 类新增加了 3 个新的方法来操作文本块：

- `formatted(Object... args)` ：它类似于 `String` 的 `format()` 方法。添加它是为了支持文本块的格式设置。
- `stripIndent()` ：用于去除文本块中每一行开头和结尾的空格。
- `translateEscapes()` ：转义序列如 “\\t” 转换为 “\t”

Java 14 中，为其引入了两个新的转义字符：

- `\` : 表示行尾，不引入换行符
- `\s` ：表示单个空格

```java
String str = "凡心所向，素履所往，生如逆旅，一苇以航。";

String str2 = """
        凡心所向，素履所往， \
        生如逆旅，一苇以航。""";
System.out.println(str2);// 凡心所向，素履所往， 生如逆旅，一苇以航。
String text = """
        java
        c++\sphp
        """;
System.out.println(text);

// 输出：
java
c++ php
```

## Java 16

### 记录类型

Java 14 引入 `record` 关键字， `record` 提供一种紧凑的语法来定义类中的不可变数据。
`record` 关键字可以简化 数据类（一个 Java 类一旦实例化就不能再修改）的定义方式，使用 `record` 代替 `class` 定义的类，只需要声明属性，就可以在获得属性的访问方法，以及 `toString()` ， `hashCode()`， `equals()` 方法
类似于使用 `class` 定义类，同时使用了 lombok 插件，并打上了 `@Getter` , `@ToString` ， `@EqualsAndHashCode` 注解

```java
/**
 * 这个类具有两个特征
 * 1. 所有成员属性都是final
 * 2. 全部方法由构造方法，和两个成员属性访问器组成（共三个）
 * 那么这种类就很适合使用record来声明
 */
final class Rectangle implements Shape {
    final double length;
    final double width;

    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    double length() { return length; }
    double width() { return width; }
}
/**
 * 1. 使用record声明的类会自动拥有上面类中的三个方法
 * 2. 在这基础上还附赠了equals()，hashCode()方法以及toString()方法
 * 3. toString方法中包括所有成员属性的字符串表示形式及其名称
 */
record Rectangle(float length, float width) { }
```

Java 15 支持在局部方法和接口中使用 `record` 。
Java 16 中非静态内部类可以定义非常量的静态成员。

```java
public class Outer {
    class Inner {
        static int age;
    }
}
```

### instanceof 模式匹配

Java 12 首次引入 instanceof 模式匹配。
Java 13, 14, 15 相比较上个版本无变化，继续收集更多反馈。
Java 16 模式变量不再隐式为 final。

新版的 instanceof 可以在判断是否属于具体的类型同时完成转换。

```java
Object obj = "我是字符串";
if(obj instanceof String str){
    System.out.println(str);
}

// 从 Java SE 16 开始，你可以对 instanceof 中的变量值进行修改。
public void learn(Object obj) {
    if (obj instanceof String s) {
        System.out.println(s);
        s = "JavaGuide";
        System.out.println(s);
    }
}
```

## Java 17

### 密封类

密封类可以对继承或者实现它们的类进行限制。

比如抽象类 `Person` 只允许 `Employee` 和 `Manager` 继承。

```java
public abstract sealed class Person
    permits Employee, Manager {
    //...
}
```

另外，任何扩展密封类的类本身都必须声明为 `sealed` 、 `non-sealed` 或 `final` 。

```java
public final class Employee extends Person {
}

public non-sealed class Manager extends Person {
}
```

如果允许扩展的子类和封闭类在同一个源代码文件里，封闭类可以不使用 permits 语句，Java 编译器将检索源文件，在编译期为封闭类添加上许可的子类。
在 JDK 15 引入的 sealed class（密封类）在 JDK 16 得到了改进：更加严格的引用检查和密封类的继承关系。

### Stream.toList()

如果需要将 `Stream` 转换成 `List` ,需要通过调用 `collect` 方法使用 `Collectors.toList()` ，代码非常冗长。
在Java 17中将会变得简单，可以直接调用 `toList()` 。

```java
private static void streamToList() {
    Stream<String> stringStream = Stream.of("a", "b", "c");
    List<String> stringList =  stringStream.toList();
    for(String s : stringList) {
        System.out.println(s);
    }
}
```
