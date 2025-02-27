## Java基础

### 序列化和反序列化

Java*序列化*是指把Java对象转换为**字节序列**的过程，而Java*反序列化*是指把字节序列恢复为**Java对象**的过程.

**序列化是指将对象转换成一种可存储或者可传输的格式，以便在需要的时候可以还原为对象（反序列化）**。虽然传统意义上，序列化是指将对象转换为字节流，但是在更广泛的概念中，序列化也可以指代将对象转换为其他格式，例如json字符串。JSON序列化将对象的数据以JSON格式进行表示，以便在不同系统之间进行数据交换和传输。

#### 字节流

字节流是序列化是将对象转换为字节流形式，适用于底层数据传输和存储。

#### JSON序列化

JSON序列化是将对象转换为JSON字符串的形式，适用于数据交换和跨平台传输。

#### SerialVersionUID

所有实现了Serializable接口的类都建议生成一个SerialVersionUID，通过IDE或者手写的方式生成，**可用来验证版本的一致性**。

##### 原理

serialVersionUID 是 Java 为每个序列化类产生的版本标识，可用来保证在反序列时，发送方发送的和接受方接收的是可兼容的对象。如果接收方接收的类的 serialVersionUID 与发送方发送的 serialVersionUID 不一致，进行反序列时会抛出 InvalidClassException。序列化的类可显式声明 serialVersionUID 的值，如下:

```
ANY-ACCESS-MODIFIER static final long serialVersionUID = 1L;
```

当显式定义 serialVersionUID 的值时，Java 根据类的多个方面(具体可参考 Java 序列化规范)动态生成一个默认的 serialVersionUID 。尽管这样，还是建议你在每一个序列化的类中显式指定 serialVersionUID 的值，因为不同的 jdk 编译很可能会生成不同的 serialVersionUID 默认值，进而导致在反序列化时抛出 InvalidClassExceptions 异常。所以，为了保证在不同的 jdk 编译实现中，其 serialVersionUID 的值也一致，可序列化的类必须显式指定 serialVersionUID 的值。另外，serialVersionUID 的修饰符最好是 private，因为 serialVersionUID 不能被继承，所以建议使用 private 修饰 serialVersionUID。



序列化是将对象的状态信息转换为可存储或传输的形式的过程。我们都知道，Java对象是保存在JVM的堆内存中的，也就是说，如果JVM堆不存在了，那么对象也就跟着消失了。

而序列化提供了一种方案，可以让你在即使JVM停机的情况下也能把对象保存下来的方案。就像我们平时用的U盘一样。把Java对象序列化成可存储或传输的形式（如二进制流），比如保存在文件中。这样，当再次需要这个对象的时候，从文件中读取出二进制流，再从二进制流中反序列化出对象。

**虚拟机是否允许反序列化，不仅取决于类路径和功能代码是否一致，一个非常重要的一点是两个类的序列化 ID 是否一致**，这个所谓的序列化ID，就是我们在代码中定义的`serialVersionUID`。



- SerialVersionUID的定义方式：
  - 显示，手动定义或者通过IDE来快捷生成。
  - 隐式，如果未显示定义，则默认隐式生成一个SerialVersionUID，在每次修改类的时候都会自动更新SerialVersionUID。

- **建议显示定义SerialVersionUID**, 如果未显示定义且对类进行了修改操作，反序列化的过程中，会对SerialVersionUID进行比较，如果未定义SerialVersionUID，在反序列化的调用链中initNonProxy类的方法getSerialVersionUID会生成一个新的默认serialVersionUID，会导致反序列化报错InvalidClassException。

- 显示定义SerialVersionUID的好处是可以灵活控制类的版本，做兼容性升级的时候，不改变SerialVersionUID的值。

  做非兼容性升级的时候，改变SerialVersionUID的值，这样可以避免序列化混乱。

##### 应用

###### web或跨平台传输

 场景举例

- 前置条件：
  - webapi中的DTO实现Serializebal接口，并显示定义SerialVersionUID。
  - get接口返回DTO的SerialVersionUID
  - update接口需要传入SerialVersionUID，并与当前SerialVersionUID进行对比，相同允许修改，不同不允许修改。允许修改后需要更新新的SerialVersionUID。

- 场景模拟：**仅适用于很少需要修改的数据，例如某种重要全局配置项。**
  - get接口返回体中返回SerialVersionUID，并进行后续操作，例如调用update接口进行数据的修改。在update数据时，将get接口返回的SerialVersionUID一起提交到服务端，服务端将传入的SerialVersionUID与当前DTO对象的SerialVersionUID进行对比，如果相同，则允许继续修改，并更新新的SerialVersionUID；如不同，返回报错信息。（如果在update接口调用之前，已经有其他人对此数据进行了修改，则会导致SerialVersionUID不同。）



### Maven

#### 下载文档 

- 可以下载引用类库的文档，在编译器控制台 使用命令：

  ```shell
  mvn dependency:sources
  ```

  以上命令即可下载所有引用类库的文档，如果未生效可以重启Invalidate Caches。


#### POM

在pom中新增dependency后，在IDEA中要先刷新Maven（Reload All Maven Project), 在此过程中Maven会自动下载maven repository中确实的JAR。

- 先Reload All Maven Project，然后再install或者package

#### 手动下载Maven依赖

如果通过Pom从Maven库下载依赖失败，可以通过手动下载依赖的方式。

- 进入Maven中央仓库官网，搜索需要的依赖包，比如spring-boot-starter-web，进入后找到需要的版本。

- 在依赖详情页

  1，复制页面的URL（浏览器地址栏的URL），例如https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web/3.3.3 。

  2，复制Pom依赖代码，在Pom中添加依赖

- 编辑cmd命令模板

  模板格式：

  mvn dependency:get
    -DremoteRepositories=url 依赖的URL
    -DgroupId=groupId
    -DartifactId=artifactId
    -Dversion=version

  

  mvn dependency:get -DremoteRepositories=https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web/3.3.3 -DgroupId=groupId -DartifactId=artifactId -Dversion=version

  将以上cmd命令在idea的terminal中执行，即可将以上依赖下载到本地。

- 重启idea，即可加载到刚下载的依赖，检查pom文件，可以看到刚才下载的依赖已经成功引用。

### 初始化及执行顺序

#### 类的加载时机

类的加载是通过类加载器（Classloader）完成的，它既可以是饿汉式[eagerly load]（只要有其它类引用了它就加载）加载类，也可以是懒加载[lazy load]（等到类初始化发生的时候才加载）。不过我相信这跟不同的JVM实现有关，然而他又是受JLS保证的（当有静态初始化需求的时候才被加载）

#### 类的初始化时机

**加载完类后**，类的初始化就会发生，意味着它会初始化所有类静态成员，以下情况一个类被初始化： 实例通过使用new()关键字创建或者使用class.forName()反射，但它有可能导致ClassNotFoundException。



note：类要先加载，然后才会发生初始化。

#### 类初始化和实例化的异同

类的初始化过程与类的实例化过程的异同？ 

- 类的初始化是指，**类加载过程中的初始化阶段对类变量按照程序猿的意图进行赋值的过程**；
- 而类的实例化是指，在类完全加载到内存中后**创建对象的过程**。



执行顺序分先后

- 静态变量和静态代码块：在类加载时候，**静态变量和静态代码块**就已经运行了，且只运行一次。

  可以理解为全局，存放于方法区，随着类而消亡。

  **所有实例皆使用同一个静态变量或者静态代码块。**

  如果有多个静态变量和静态代码块，则按照书写顺序执行。

- 类成员实例变量和实例代码块（构造代码块）：每次类生成实例时候执行，优于构造函数，则按照书写顺序执行，可以将多个构造方法中的相同的代码放到构造代码块中,对对象进行初始化.

- 构造函数：在创建对象时被调用（生成实例时）。

[详细参考](https://developer.aliyun.com/article/232338)

### 构造函数

- 在spring中，如果一个class拥有构造函数，并此类会被注册为bean，构造函数会在bean被注册到上下文之前执行。
- 创建子类时，其构造函数必须首先调用其父类中的构造函数。Java继承中构造函数的执行顺序是:首先执行超类的构造函数，然后执行子类的构造函数

### 路径

1. File对象变量的`getCanonicalPath`返回全路径，但不会包含`../.`等符号
2. File对象变量的`getAbsolutePath`返回全路径，会包含`../.`等符号
3. 使用`/`开头，会从磁盘根目录开始索引
4. 不使用`/`开头，会从eclipse或idea的项目根目录开始索引，例如：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration>
<configuration>

    <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} %5level [%thread] - %msg%n</pattern>
        </encoder>
    </appender>
    <appender name="file_error" class="ch.qos.logback.core.FileAppender">
        <file>log/logback.txt</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>log/logback.%d{yyyy-MM-dd}.%i.txt</fileNamePattern>
            <timeBasedFileNamingAndTriggeringPolicy
                    class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>10MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
            <maxHistory>7</maxHistory>
        </rollingPolicy>
        <filter class="ch.qos.logback.classic.filter.LevelFilter">
            <level>ERROR</level>
            <onMatch>ACCEPT</onMatch>
            <onMismatch>DENY</onMismatch>
        </filter>
        <append>true</append>
        <immediateFlush>true</immediateFlush>
        <encoder>
            <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level - %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="priv.nick.cbs.topgun.dao">
        <level value="console"/>
    </logger>
    <root level="ERROR">
        <appender-ref ref="file_error"/>
    </root>
    <root level="DEBUG">
        <appender-ref ref="stdout" />
    </root>

</configuration>
```

上面的xml是一个logback日志配置，其中节点<file>log/logback.txt</file>，意味着从项目根目录开始检索log文件夹。

如果在<file>log/logback.txt</file>改成 /log/logback.txt，那么意味着将从磁盘根目录检索，这个写法不适用于此场景，将会导致报错。

### Class中代码的执行顺序

The order of code execution in a Java class is as follows.

- Static variables are initialized. Static blocks are executed. 按照代码顺序执行
- Instance variables are initialized. Instance blocks are executed. 按照代码顺序执行
- Constructor is executed.
- 普通代码块
- Static methods can be called without creating an instance.

### 关键字

#### super

The `super` keyword refers to superclass (parent) objects.

It is used to call superclass methods, and to access the superclass constructor.

The most common use of the `super` keyword is to eliminate the confusion between superclasses and subclasses that have methods with the same name.

To understand the `super` keyword, you should have a basic understanding of [Inheritance](https://www.w3schools.com/java/java_inheritance.asp) and [Polymorphism](https://www.w3schools.com/java/java_polymorphism.asp).

#### static

If you had static variable, block, method, it is all of them execution together in textual order.

静态变量，方法，块的执行优先于非静态，非静态的执行顺序也按照代码文本顺序执行。

静态的总是优先执行。

静态的执行顺序优先于构造函数。

```java
static int x = method("x");

static {
    System.out.println("init 1");
}

static int y = method("y");

static {
    System.out.println("init 2");
}

static int method(String name) {
    System.out.println(name);
    return 0;
}
```

#### variable

Static variables are shared among all instances of a class. Non static variables are specific to that instance of a class. Static variable is like a global variable and is available to all methods. 

**Non static variable is like a local variable and they can be accessed through only instance of a class**.

The `static` keyword is a non-access modifier used for methods and attributes. Static methods/attributes can be accessed without creating an object of a class.



基本数据类型的局部变量保存在Java虚拟机栈的局部变量表中。

基本数据类型的成员变量保存在Java虚拟机堆中。

包装类型属于对象类型，几乎所有对象类型都保存在Java虚拟机堆中。

While static variables use memory very efficiently, a static variable is not constant. **It can be changed at any time**. Imagine that you want a constant that is the same for every instance of a class. To create **a global constant** shared by every instance of a class, you combine Java's static and final keywords.

#### final

The `final` keyword is a **non-access** modifier used for classes, attributes and methods, which makes them non-changeable (impossible to inherit or override).

The `final` keyword is useful when you **want a variable to always store the same value**, like PI (3.14159...).

如果类成员变量添加了final修饰符, 那么此变量只能在构造函数中初始化.

##### 构造后不可变原则

在类成员变量上增加final关键字，使用构造方法的方式注入service，符合构造后不可变原则，更安全，易于维护，加上final字段，确保service只能通过构造方法注入。

``` java
public class AuditorService implements AuditorAware<String>{
    //加上final字段，可以保证auditorAwareService只能通过构造方法注入，并且注入后不可更改
    //使之线程安全，不会造成nullPointerExcetion等错误
    private final AuditorAwareService auditorAwareService;
    public AuditorService(AuditorAwareService auditorAwareService){
        this.auditorAwareService = auditorAwareService;
    }
}
```



#### static+final=constant

When you combine static final keywords in Java you **create a variable that is global to the class and impossible to change**. This creates what developers from other platforms would consider the equivalent to a global, constant variable.

### IDEA快捷键

#### 选中多行并编辑

option+鼠标左键选中光标并拖动，连续行可用

command+shift+option+鼠标左键选中，可选中非连续行

### Servlet

Java Servlet 是运行在 Web 服务器或应用服务器上的程序，它是作为来自 Web 浏览器或其他 HTTP 客户端的请求和 HTTP 服务器上的数据库或应用程序之间的中间层。

使用 Servlet，您可以收集来自网页表单的用户输入，呈现来自数据库或者其他源的记录，还可以动态创建网页。

Java Servlet 通常情况下与使用 CGI（Common Gateway Interface，公共网关接口）实现的程序可以达到异曲同工的效果。但是相比于 CGI，Servlet 有以下几点优势：

- 性能明显更好。
- Servlet 在 Web 服务器的地址空间内执行。这样它就没有必要再创建一个单独的进程来处理每个客户端请求。
- Servlet 是独立于平台的，因为它们是用 Java 编写的。
- 服务器上的 Java 安全管理器执行了一系列限制，以保护服务器计算机上的资源。因此，Servlet 是可信的。
- Java 类库的全部功能对 Servlet 来说都是可用的。它可以通过 sockets 和 RMI 机制与 applets、数据库或其他软件进行交互。

#### Servlet 任务

Servlet 执行以下主要任务：

- 读取客户端（浏览器）发送的显式的数据。这包括网页上的 HTML 表单，或者也可以是来自 applet 或自定义的 HTTP 客户端程序的表单。
- 读取客户端（浏览器）发送的隐式的 HTTP 请求数据。这包括 cookies、媒体类型和浏览器能理解的压缩格式等等。
- 处理数据并生成结果。这个过程可能需要访问数据库，执行 RMI 或 CORBA 调用，调用 Web 服务，或者直接计算得出对应的响应。
- 发送显式的数据（即文档）到客户端（浏览器）。该文档的格式可以是多种多样的，包括文本文件（HTML 或 XML）、二进制文件（GIF 图像）、Excel 等。
- 发送隐式的 HTTP 响应到客户端（浏览器）。这包括告诉浏览器或其他客户端被返回的文档类型（例如 HTML），设置 cookies 和缓存参数，以及其他类似的任务。

#### Servlet 包

Java Servlet 是运行在带有支持 Java Servlet 规范的解释器的 web 服务器上的 Java 类。

Servlet 可以使用 **javax.servlet** 和 **javax.servlet.http** 包创建，它是 Java 企业版的标准组成部分，Java 企业版是支持大型开发项目的 Java 类库的扩展版本。

这些类实现 Java Servlet 和 JSP 规范。在写本教程的时候，二者相应的版本分别是 Java Servlet 2.5 和 JSP 2.1。

Java Servlet 就像任何其他的 Java 类一样已经被创建和编译。在您安装 Servlet 包并把它们添加到您的计算机上的 Classpath 类路径中之后，您就可以通过 JDK 的 Java 编译器或任何其他编译器来编译 Servlet。

#### Servlet示例

```java
//使用注解方式标注Servlet映射
@WebServlet(name = "FormServlet", urlPatterns = "/calculateServlet")
public class FormServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest request, 
      HttpServletResponse response)
      throws ServletException, IOException {

        String height = request.getParameter("height");
        String weight = request.getParameter("weight");

        try {
            double bmi = calculateBMI(
              Double.parseDouble(weight), 
              Double.parseDouble(height));
            
            request.setAttribute("bmi", bmi);
            response.setHeader("Test", "Success");
            response.setHeader("BMI", String.valueOf(bmi));

            request.getRequestDispatcher("/WEB-INF/jsp/index.jsp").forward(request, response);
        } catch (Exception e) {
           request.getRequestDispatcher("/WEB-INF/jsp/index.jsp").forward(request, response);
        }
    }

    private Double calculateBMI(Double weight, Double height) {
        return weight / (height * height);
    }
}
```



使用XML的方式配置Servlet映射

```xml
<web-app ...>

    <servlet>
       <servlet-name>FormServlet</servlet-name>
       <servlet-class>com.root.FormServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>FormServlet</servlet-name>
        <url-pattern>/calculateServlet</url-pattern>
    </servlet-mapping>

</web-app>
```

HTML页面

```html
<form name="bmiForm" action="calculateServlet" method="POST">
    <table>
        <tr>
            <td>Your Weight (kg) :</td>
            <td><input type="text" name="weight"/></td>
        </tr>
        <tr>
            <td>Your Height (m) :</td>
            <td><input type="text" name="height"/></td>
        </tr>
        <th><input type="submit" value="Submit" name="find"/></th>
        <th><input type="reset" value="Reset" name="reset" /></th>
    </table>
    <h2>${bmi}</h2>
</form>
```

测试调用

```java
public class FormServletLiveTest {

    @Test
    public void whenPostRequestUsingHttpClient_thenCorrect() 
      throws Exception {

        HttpClient client = new DefaultHttpClient();
        HttpPost method = new HttpPost(
          "http://localhost:8080/calculateServlet");

        List<BasicNameValuePair> nvps = new ArrayList<>();
        nvps.add(new BasicNameValuePair("height", String.valueOf(2)));
        nvps.add(new BasicNameValuePair("weight", String.valueOf(80)));

        method.setEntity(new UrlEncodedFormEntity(nvps));
        HttpResponse httpResponse = client.execute(method);

        assertEquals("Success", httpResponse
          .getHeaders("Test")[0].getValue());
        assertEquals("20.0", httpResponse
          .getHeaders("BMI")[0].getValue());
    }
}
```

#### Servlet 生命周期

For a servlet not hosted in a distributed environment (the default), the servlet container must use only one instance per servlet declaration. 对于不在分布式环境中托管的servlet(默认情况)，servlet容器必须每个servlet声明只使用一个实例。

常规来讲，应用程序启动后，第一个Request请求进来后，Tomcat会检查是否存在Servlet实例，如果没有，则调用HttpServlet的实现init()方法来初始化一个Servlet实例。并在应用程序或服务关闭之前，进来的其他Request都将使用这个实例，每个请求会创建自己的thread来处理请求，是并行的，非线程安全的。在应用程序或服务关闭时，将调用destroy()来销毁Servlet实例。

以上就是Servlet的生命周期。

### Character 字符

1 byte = 8bits

1 Utf-8 character = 4bytes = 32bits 

HS256 (HMAC with SHA-256) HS256就是带有SHA-256的HMAC

```
Algorithm 	Key Length
HS256	256 = bits (32 bytes)
HS384	384 = bits (48 bytes)
HS512	512 = bits (64 bytes)
```

UTF-8 is based on 8-bit code units. Each character is encoded as 1 to 4 bytes. **The first 128 Unicode code points** are encoded as 1 byte in UTF-8.  前128个Unicode码在UTF-8中被编码为1字节。

#### 生成SHA-256的字符串

To generate a random JWT secret key, you can use a tool like Node.js to create a random string. Here's a simple example:

1. Open your terminal or command prompt.
2. Run the following Node.js script to generate a random string:

```
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### Maven相关

#### 引用问题

新增dependency后，先刷新下maven库，然后在install或者package，再在代码中引用即可。如果还找不到引用，那就查看pom引用是否正确，如果引用正确，那就在maven本地库目录中，根据路径找到对应目录，删除当前版本文件后，重新刷新maven库再package或install。

#### 使用Iterator迭代器

概念：

1.迭代器是一种模式、详细可见其[设计模式](https://blog.csdn.net/Jae_Wang/article/details/80524295)，可以使得序列类型的数据结构的遍历行为与被遍历的对象分离，即我们无需关心该序列的底层结构是什么样子的。只要拿到这个对象,使用迭代器就可以遍历这个对象的内部。此外，迭代器通常被称为“轻量级”对象，因为创建它的代价小。ex:Java中的Iterator功能比较简单，并且只能单向移动：
　　(1)使用方法iterator()要求容器返回一个Iterator。第一次调用Iterator的next()方法时，它返回序列的第一个元素。注意：iterator()方法是java.lang.Iterable接口,被Collection继承。
　　(2) 使用next()获得序列中的下一个元素。
　　(3) 使用hasNext()检查序列中是否还有元素。
　　(4) 使用remove()将迭代器新返回的元素删除。

Iterator的扩展接口是ListIterator



场景：在遍历集合的时候，在循环内对集合内元素进行删除操作的时候，普通for循环抑或是增强for循环，直接remove都会引起报错。

普通for循环在删除相邻重复元素时候会漏删。

增强for循环在删除元素的时候直接报错。

解决方案：使用Iterator迭代器，可在遍历时正常删除元素。

```java
public class Test {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>() {
            {
                add("a");
                add("b");
                add("b");
                add("c");
            }
        };
        Iterator<String> iterator = list.iterator();
        while(iterator.hasNext()){
            String next = iterator.next();
            if(next == "b"){
                iterator.remove();
            }
        }
        System.out.println(list);
    }
}
//输出：[a, c]
```

### 集合

**Java集合中保存的元素实质是对象的引用，而非对象本身。**

#### ArrayList

基于索引的，有序的

#### HashSet

基于对象的，无序的

### 规则和约定

#### 命名

- DAO：The Data Access Object (or DAO) pattern: **separates a data resource's client interface from its data access mechanisms**. adapts a specific data resource's access API to a generic client interface.
- DTO：The Data Transfer Object (DTO) pattern is used **to transfer data between software application subsystems or layers, particularly in the context of network calls or database retrieval in Java applications**. It reduces the number of method calls by aggregating the data in a single transfer.

### 加密规则

#### 密码加密

##### 用户密码

用户密码加密为单向不可逆，加密后存入数据库，每次登陆输入密码后，都将密码密码使用相同的方式加密再比对。

举例：

- 使用java的BcryptUtil类下的bcrypt方法对用户密码进行加密，存入数据库。
- 用户每次登陆输入密码，都会对密码进行相同方式的 bcrypt 加密，并将加密后的密码与数据库存储的加密密码比对，相同则代表密码正确。

#### Hash

Hashing is the process of transforming any given key or a string of characters into another value by some algorithm

#### bcrypt

bcrypt is an algorithm for hashing something that is uses SALT.

For example, we want to hasing an key like 'programing' with bcrypt, it will be combine 'programing' and a random value generated by SALT like 'XwZ78', the generated end result like `$2y$10$yhG7l.ymHOBL4NpjL7hnjO41vMwQUR8N0GKF6skrcpiTVXsqlMvry`

### Date

when you use `new Date()`, it creates a `Date` object that represents the current date and time at the moment of instantiation.

Here's an example:

```
java


复制代码
import java.util.Date;

public class Main {
    public static void main(String[] args) {
        Date currentDate = new Date();
        System.out.println("Current Date and Time: " + currentDate);
    }
}
```

The `Date` object will be initialized with the current system time (in milliseconds since January 1, 1970, 00:00:00 GMT, also known as the Unix epoch).

new Date会创建一个日期, 此日期是从1970-01-01 00:00:00 到当前时间的毫秒数.

时间原点(1970-01-01 00:00:00)+毫秒数 = 当前时间

## 我眼中牛逼的代码该是什么样子的

层级明确

逻辑清晰

简洁易懂

易于维护

高度封装

极少冗余

业务层专注实现业务逻辑, 将描述业务以外的代码放到 工具包下,数据访问层里, 避免大量代码堆积. 

做到, 高可用(复用性强), 不要只做一个将增删改以及一大堆业务逻辑堆砌到一起, 写出的丑陋代码自己过一阵子都看不懂的简易工具人.



下面写给自己.

写代码,就是艺术创作, 要对自己的代码有高标准, 高追求.

 不要只站在门前不往里边看一眼, 只有深入代码世界, 才能体会其中乐趣.

前进的路上, 肯定是要忍受孤独, 怀疑, 疲惫, 厌倦, 懒惰, 等等所有的负面情绪, 但请你一定要坚持下去, 峰回路转, 终见坦途.

路漫漫其修远兮，吾将上下而求索

### 对象

Java中对象是通过引用传递的. 所以需要注意的是, 声明一个新对象的时候, 没有为这个引用去new一个新的对象, 并使用此引用去接收其他对象,那么相当于此引用直接指向其他对象, 这时候修改新对象变量, 会对原对象也一并修改.

所以要注意: 为避免修改原对象, 要使用 new的方式去声明新对象(在堆栈中创建新的对象和引用)

``` java
//person变量存储在栈中, new Person()产生的新对象存储在堆中.
Person person =new Person();
//未使用new的方式去声明Object o,相当于将o引用直接指向堆中的person对象.
Object o = person;
//这时候去修改o对象,也会对person对象一并修改. 
//因为o和person这两个变量(存在栈中的两个引用地址)皆指向堆中的person对象.
o.id = ‘13’;

//正确的做法是new一个新的对象
Object o = new Object();
o = person;
o.id = “123”;
```



举例:

``` java
//错误版本
public List<String> convertMapToList(Map<String,List<String>> map){
        List<String> list = new ArrayList<>();
        for (Map.Entry<String,List<String>> entry:map.entrySet()){
            String key = entry.getKey();
          	//这里是将entry.getValue()拿到的List对象集合的引用指向新声明的List集合对象,所以这里的value指向的依然是entry.getvalue()的引用.
            List<String> value = entry.getValue();
          	//如果直接用value.add,会导致map的原list被更改
            value.add(0,key);
            list.addAll(value);
        }
        return list;
    }
//正确版本
public List<String> convertMapToList(Map<String, List<String>> map) {
    List<String> resultList = new ArrayList<>();
    
    for (Map.Entry<String, List<String>> entry : map.entrySet()) {
        String key = entry.getKey();
        List<String> value = entry.getValue();
        
        // Create a new list to avoid modifying the original list in the map
        List<String> tempList = new ArrayList<>(value);
        tempList.add(0, key);  // Add the key as the first element
        
        resultList.addAll(tempList);  // Add the modified list to the result list
    }
    
    return resultList;
}
```

### 多线程

在现代web开发框架下，基本上默认都是多线程模式的，比如SpringBoot，SpringMVC等，它们都使用Servlet容器来管理线程池，例如Tomcat。每个请求进来，都会分配一个空闲的线程出来。

但是在非web环境下，例如普通的一个后台运行的服务程序，它默认状态下就是单线程的，这时候如果我们想开启多线程，就要继承Thread或实现Runnable，或者线程池ExecutorService等。

如果我们要对多线程情况下的共享资源进行管理，为了避免并发读写导致的数据不准确，需要使用同步线程的方式处理共享资源。

原理是，当一个线程的Synchronized(lock)获取锁的时候，其他线程在获取这个lock对象的时候，就无法拿到lock的锁了，因为已经有获取并占用这个锁了，其他线程会排队，直到占用锁的线程释放了锁。

例如

- Synchronized 粗粒度的简单同步线程处理方式，自动获取锁和释放锁
- ReentrantLock 细粒度的，手动获取锁和释放锁，`ReentrantLock` 的 **锁机制** 和 `synchronized` 类似，**都是保证线程安全、控制共享资源访问**，但 `ReentrantLock` 是 **基于 AQS（AbstractQueuedSynchronizer）** 实现的 **显式锁**，提供了更灵活的锁管理。

任何对象都具有锁属性，因为JVM为所有对象分配了监视器锁，也就意味着，我们可以使用任何对象类型去声明一个锁。例如：

``` java
class AccountReentrantLock{
    private BigDecimal balance = BigDecimal.ZERO;
    private final Lock lock = new ReentrantLock();

    public void deposit(BigDecimal amount) {
        lock.lock();//获取锁
        try{
            balance = balance.add(amount);
            Thread.sleep(1000);//模拟任务
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();//释放锁
        }
    }

    public void withdraw(BigDecimal amount) {
        lock.lock();
        try{
            if(balance.compareTo(amount) >= 0) {
                balance = balance.subtract(amount);
            }else {
                System.out.println("You can't withdraw more than your amount");
            }
        }finally {
            lock.unlock();
        }
    }

    public BigDecimal getBalance() {
        lock.lock();
        try{
            return balance;
        }finally {
            lock.unlock();
        }
    }
}
```





`ReentrantLock` **可以是公平锁**，但默认情况下**是非公平锁**。而 `synchronized` **只能是非公平锁**，没有公平锁的选项。

**1️⃣ 公平锁 vs. 非公平锁**

**公平锁（Fair Lock）：**

- 线程按照 **请求锁的顺序** 依次获取锁，避免线程饥饿。
- **优点**：保证所有线程都有公平竞争的机会。
- **缺点**：性能稍低，开销较大，因为需要维护等待队列。

**非公平锁（Unfair Lock）：**

- 线程**可以尝试直接抢占锁**，即使有其他线程在等待。
- **优点**：吞吐量高，性能好。
- **缺点**：可能导致某些线程长时间拿不到锁（线程饥饿）。

**2️⃣ `synchronized` 的锁机制**

📌 `synchronized` **只能是非公平锁**，它的锁依赖于 **JVM 内部的对象监视器（Monitor）**：

```
java复制编辑public synchronized void method() {
    // 线程执行代码
}
```

- 底层原理

  ：

  1. **进入同步代码块**时，线程尝试**获取对象的监视器锁（Monitor）**。
  2. **如果锁已被占用**，线程进入**阻塞状态（Blocked）**。
  3. **锁释放后**，等待的线程**唤醒后竞争锁**，谁抢到谁执行。
  4. **线程执行完毕后**，自动释放锁。

**⚠️ 为什么 `synchronized` 只能是非公平锁？**

- `synchronized` 依赖 `Monitor` 机制，而 **唤醒线程是由 JVM 负责的，JVM 并不会按照线程等待顺序来保证公平性**。
- **线程一旦被唤醒，就会直接去竞争锁**，并不会严格按照 FIFO（先进先出）顺序执行。

**3️⃣ `ReentrantLock` 的公平锁 vs. 非公平锁**

📌 `ReentrantLock` **默认是非公平锁**，但你可以手动设置成公平锁：

```
java复制编辑// 默认是非公平锁
ReentrantLock lock = new ReentrantLock(); 

// 显式指定公平锁
ReentrantLock fairLock = new ReentrantLock(true);
```

**🔹 `ReentrantLock.lock()` 获取锁的过程**

- 非公平锁（默认）
  - **尝试直接获取锁**，如果失败才会进入等待队列。
  - **可能发生插队**，提高并发性能，但降低公平性。
- 公平锁
  - **线程按顺序排队**，不能插队，保证公平性，但吞吐量稍低。

### **代码示例**

```
java复制编辑public class FairLockExample {
    private static final ReentrantLock fairLock = new ReentrantLock(true); // 公平锁

    public static void main(String[] args) {
        Runnable task = () -> {
            for (int i = 0; i < 3; i++) {
                fairLock.lock();
                try {
                    System.out.println(Thread.currentThread().getName() + " 获取到锁");
                } finally {
                    fairLock.unlock();
                }
            }
        };

        Thread t1 = new Thread(task, "线程1");
        Thread t2 = new Thread(task, "线程2");
        Thread t3 = new Thread(task, "线程3");

        t1.start();
        t2.start();
        t3.start();
    }
}
```

📌 **运行结果（公平锁）**

```
复制编辑线程1 获取到锁
线程2 获取到锁
线程3 获取到锁
线程1 获取到锁
线程2 获取到锁
线程3 获取到锁
```

- 公平锁情况下，线程按照请求顺序执行。

📌 **如果使用默认的非公平锁**

```
java


复制编辑
private static final ReentrantLock fairLock = new ReentrantLock(false); // 非公平锁
```

- **可能的结果**：

```
复制编辑线程1 获取到锁
线程1 获取到锁
线程3 获取到锁
线程2 获取到锁
线程1 获取到锁
线程2 获取到锁
```

- 线程可能会**插队抢占锁**，不严格按照顺序执行。

**4️⃣ `synchronized` vs. `ReentrantLock`（公平性）**

| 特性               | `synchronized` | `ReentrantLock(false)`（默认） | `ReentrantLock(true)`（公平锁） |
| ------------------ | -------------- | ------------------------------ | ------------------------------- |
| **公平性**         | 非公平         | 非公平                         | **公平**                        |
| **线程等待顺序**   | 可能发生插队   | 可能发生插队                   | 按请求顺序执行                  |
| **吞吐量**         | 高             | 高                             | **低**                          |
| **是否能手动设置** | ❌ 不能         | ✅ 可选                         | ✅ 可选                          |

**5️⃣ `synchronized` 是基于对象监视器，而 `ReentrantLock` 是基于 `state`**

你问的这个问题很关键！💡

📌 **`synchronized` 是依赖于 JVM 赋予对象的 \**“监视器锁（Monitor）”\**，本质上是**对象头的 MarkWord 记录锁状态**。**

📌 **`ReentrantLock` 则是自己管理锁状态**，底层依赖 **AQS（AbstractQueuedSynchronizer）**，通过 `state` 变量控制锁的获取和释放。

**🔹 结论**

- `synchronized` 只能是 **非公平锁**，且 **基于对象监视器（Monitor）**。
- `ReentrantLock` **默认是非公平锁**，但**可以手动设置为公平锁**，**底层基于 `AQS` 维护 `state` 变量**。
- **如果你需要严格控制线程的公平性，应该选择 `ReentrantLock(true)`**。
- **非公平锁一般能提高并发性能，因为它允许直接竞争锁，而不需要排队。**



### Servlet 线程池

Servlet容器本身管理者一个线程池，这个线程池是处理Http请求的，线程池可以并行处理Http请求，但单个线程内对请求的处理是同步的，意思是，假设有200个线程，那么可以同时并行处理200个http请求，但每个线程，在请求返回响应之前，这个线程是阻塞的，只有这个请求返回了响应，线程资源才会释放，这时候才可以继续处理等待中的其他请求。

所以，Servlet线程池可以并行处理请求，但线程内的处理是同步的。

我们可以通过以下方式来管理Servlet线程池，Spring内嵌默认使用的Tomcat。

``` properties
# 设置Tomcat的最大线程数
server.tomcat.max-threads=200
# 设置Tomcat的最小线程数
server.tomcat.min-spare-threads=10
# 设置请求队列的大小，默认是 10
server.tomcat.accept-count=100
```

或者

``` java
import org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory;
import org.springframework.boot.web.servlet.server.ServletWebServerFactory;
import org.springframework.boot.web.servlet.server.WebServerFactoryCustomizer;
import org.apache.catalina.connector.Connector;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class TomcatConfig {

    @Bean
    public ServletWebServerFactory servletContainer() {
        TomcatServletWebServerFactory factory = new TomcatServletWebServerFactory();
        
        // 配置 Tomcat 的线程池
        factory.addConnectorCustomizers((connector) -> {
            connector.setAttribute("maxThreads", 200); // 最大线程数
            connector.setAttribute("minSpareThreads", 10); // 最小空闲线程数
            connector.setAttribute("acceptCount", 100); // 队列大小
        });
        
        return factory;
    }
}
```



### Spring 线程池

Spring 提供了对线程池的良好支持，通常用于处理异步任务和并发请求。Spring 线程池管理机制基于 `TaskExecutor` 接口，常见的实现包括 `ThreadPoolTaskExecutor` 和 `SimpleAsyncTaskExecutor` 等。Spring 线程池为多线程应用提供了一种高效、可配置的方式来管理任务并控制并发。

#### ThreadPoolTaskExecutor 

`ThreadPoolTaskExecutor` 是 Spring 提供的一个功能强大的线程池实现(最常用的)。它实现了 `TaskExecutor` 接口，并且支持配置线程池的核心线程数、最大线程数、队列容量等参数。

**配置示例：**

可以通过 `@Configuration` 和 `@Bean` 注解来配置 Spring 线程池。

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

@Configuration
public class ThreadPoolConfig {

    @Bean
    public ThreadPoolTaskExecutor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);  // 核心线程数
        executor.setMaxPoolSize(50);   // 最大线程数
        executor.setQueueCapacity(100); // 队列容量
        executor.setThreadNamePrefix("Async-"); // 线程名称前缀
        executor.initialize();
        return executor;
    }
}
```

- **`corePoolSize`**：核心线程数，线程池中始终存在的线程数。
- **`maxPoolSize`**：最大线程数，线程池中最大允许的线程数。
- **`queueCapacity`**：任务队列的容量，用于存储待处理的任务。如果线程池中的线程数已满，任务将被放入队列中，直到有线程空闲出来。
- **`threadNamePrefix`**：为线程命名的前缀，可以帮助调试。

通过以上配置，Spring 会自动使用这个线程池来执行异步任务或其他需要线程池支持的任务。

#### **使用 Spring 线程池执行异步任务**

Spring 的线程池可以与 `@Async` 注解一起使用来异步执行方法。默认情况下，`@Async` 会使用 Spring 配置的线程池来执行异步任务。

**启用异步支持**

在 Spring 配置类上添加 `@EnableAsync` 注解来启用异步支持。

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;

@Configuration
@EnableAsync
public class AsyncConfig {
}
```

**使用 `@Async` 注解**

使用 `@Async` 注解标记的方法将在另一个线程中异步执行，返回一个 `Future` 或 `CompletableFuture` 对象。

```java
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;
import java.util.concurrent.CompletableFuture;

@Service
public class MyAsyncService {

    @Async
    public CompletableFuture<String> asyncTask() throws InterruptedException {
        Thread.sleep(2000); // 模拟耗时操作
        return CompletableFuture.completedFuture("异步任务完成");
    }
}
```



**Async**可以让服务层方法异步执行，达到Api不必等Async内的方法执行完毕，直接返回响应的目的，这样可以让Servlet容器的线程池立即释放资源，这样api会先得到响应结果，而异步任务交给Spring线程池处理。

**场景：无需关心异步任务的执行结果，且不需要通知调用方任务完成**

- **适用场景**：当你希望执行一个异步操作，但不需要等待它完成或返回任何结果，只关心任务是否能异步执行，且调用方不需要关心结果。
- 应用场景：
  - **发送通知**：例如用户提交订单后，后台系统发送一封确认邮件或短信通知，这些操作可以在后台异步进行，调用方不需要等待邮件是否成功发送。
  - **日志记录**：在请求处理中，异步记录日志或发送数据到监控系统，不需要返回任何结果。
  - **触发任务**：例如将某些任务推送到消息队列，不需要知道消息是否成功发送，只需异步完成该操作。

示例：

``` java
@Service
public class NotificationService {

    @Async
    public void sendEmail(String email) {
        // 模拟发送邮件
        try {
            Thread.sleep(2000);
            System.out.println("邮件已发送到：" + email);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

``` java
@RestController
@RequestMapping("/api")
public class NotificationController {

    @Autowired
    private NotificationService notificationService;

    @PostMapping("/notify")
    public String notifyUser(@RequestParam String email) {
        // 异步发送邮件，调用返回后立即响应
        notificationService.sendEmail(email);
        return "操作成功，通知将在后台发送";
    }
}
```

**解释：**

- 调用 `sendEmail()` 后，控制权会立即返回给用户，无需等待邮件是否成功发送。

- 使用 `void` 表示不需要关心任务执行的结果，调用方不需要等待任务完成。

  

**CompletableFuture对象**，配合Async使用，可以让Servlet容器线程池的资源立即释放，同时将Async内的代码任务交给Spring线程池来处理，CompletableFuture会强制等Spring线程池处理完毕后，才会将结果返回响应。(这让api变成了同步行为，但servlet又因为async可以立即释放资源，**导致了一种，servlet容器层面的异步，api实际行为同步的结果。**)

**场景：需要处理异步任务，且需要通知调用方任务完成或获取结果**

- **适用场景**：当异步操作需要返回一个结果，或者调用方需要在某个时刻等待任务完成的结果。
- 应用场景：
  - **长时间计算任务**：例如执行一个耗时的计算任务，调用方希望获得计算结果，但又不想阻塞主线程。
  - **外部服务调用**：调用外部的 API 或数据库时，调用方希望在后台执行该操作，且能够在未来某个时间点获取响应结果。
  - **多个异步任务的汇总**：多个异步任务并发执行，最终需要汇总结果或处理后续逻辑。

示例：

``` java
@Service
public class NotificationService {

    @Async
    public void sendEmail(String email) {
        // 模拟发送邮件
        try {
            Thread.sleep(2000);
            System.out.println("邮件已发送到：" + email);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

``` java
@RestController
@RequestMapping("/api")
public class NotificationController {

    @Autowired
    private NotificationService notificationService;

    @PostMapping("/notify")
    public String notifyUser(@RequestParam String email) {
        // 异步发送邮件，调用返回后立即响应
        notificationService.sendEmail(email);
        return "操作成功，通知将在后台发送";
    }
}
```

**解释：**

- `processData` 方法返回 `CompletableFuture<String>`，表示异步任务将返回一个 `String` 结果。
- 调用方可以通过 `CompletableFuture` 来获取异步任务的结果，并可以在需要时等待结果。



### Servlet 线程池+ Spring 线程池

这是一种，**线程池资源隔离**

这两者结合使用，web架构本身就是一种多线程，由servlet的线程池来实现的。那么Spring线程池+@async+completableFuture，意味着，独立于servlet容器线程池之外，在服务层又开了一个多线程（即ThreadPoolTaskExecutor的线程池管理的多线程）。这就相当于有了两个多线程，一个servlet，一个ThreadPoolTaskExecutor的Spring线程池。servlet之于api请求，可以立即释放线程池资源，Spring线程池又是它自己的管理逻辑。



----

通过结合 **Servlet 线程池 + Spring 线程池 + @Async**，提升了 **请求并发处理能力、资源利用率、响应时间和系统吞吐量**。

----



使用 **Servlet 线程池 + Spring 线程池 + @Async + void 或 CompletableFuture** 组合，除了**提高 HTTP 请求的并行处理吞吐量**外，还带来了一些其他的好处，特别是在响应性能、资源管理和系统可扩展性方面。我们来深入探讨它们的优势和作用。

**1.Servlet 线程池 + Spring 线程池 的协同作用**

- **Servlet 线程池**：负责接收和分配 HTTP 请求的处理线程（默认由 Servlet 容器管理）。它为每个请求分配一个线程，在处理请求时该线程会阻塞，直到处理完成后释放。
- **Spring 线程池**：负责处理业务逻辑中的异步任务（例如耗时的数据库查询、外部 API 调用、文件处理等）。它独立于 Servlet 线程池，通常用于业务层的长时间任务处理，不会直接影响 Servlet 容器的工作。

这两者协同工作的主要目标是：

- **解耦请求处理和业务逻辑处理**：HTTP 请求的处理交给 Servlet 线程池，而复杂的业务逻辑交给 Spring 线程池，这样可以避免服务端的主线程被阻塞，保持高并发能力。
- **释放主线程资源**：Servlet 容器线程池中的线程可以更快地释放出来，不需要等到长时间任务完成（如慢查询、文件上传等）。通过使用 Spring 线程池处理慢任务，Spring 容器可以提供更高效的请求响应时间。

**2.通过 @Async + void 或 CompletableFuture 提高的优势**

- **异步任务不阻塞 Servlet 线程池**：
  - 当你使用 `@Async + void` 时，长时间运行的任务会被推迟到 Spring 的线程池中处理，而 Servlet 线程池可以很快地将控制权交还给请求方，响应可以尽早返回（即使后台任务还未完成）。这样一来，主线程不会被长时间任务占用，系统的吞吐量和响应速度都会大幅提升。
  - **例如**：发送通知邮件或短信、上传文件、外部 API 调用等，这些任务不需要立即给用户响应，异步执行即可。
- **资源管理优化**：
  - **线程池管理**：通过 `ThreadPoolTaskExecutor` 或自定义线程池配置，可以精细管理后台异步任务的执行。例如，可以控制最大线程数、核心线程数以及队列大小，以防止任务过载影响系统性能。
  - 通过将异步任务从 Servlet 线程池中分离，避免了线程池的过度占用，提高了系统的整体资源利用率。Servlet 线程池的线程被优化用于处理 HTTP 请求，而 Spring 线程池则优化用于业务逻辑的处理。
- **性能提升**：
  - 由于 `@Async` 方法异步执行，它将任务从同步（阻塞）的执行模式转为并发的非阻塞模式，能够更高效地使用多核 CPU。比如，系统的多个任务可以在同一时间并行执行，而不是串行执行。这样一来，系统整体吞吐量就能大幅度提高，特别是在大量请求的情况下。
  - 例如，**数据库查询**：通过 `@Async` 让查询任务异步执行，在查询期间不会阻塞主线程，从而保持其他请求的高并发。
- **不需要等待异步任务的结果**：
  - 使用 `@Async + void`，调用方无需等待任务的完成，任务执行完全独立。这非常适合一些无需关心执行结果的任务（例如邮件、短信等通知）。使用 `CompletableFuture` 可以让调用方在需要时根据任务执行结果采取进一步行动，但也不会影响主线程的工作。

**3.提升的几个方面**

1. **响应时间降低**：
   - 使用异步任务处理慢任务（如慢查询、文件上传等），可以使得请求的响应时间更短，避免了在请求等待长时间任务执行的过程中，用户体验受影响。
2. **请求并发量提升**：
   - 由于 Servlet 线程池中的线程可以迅速返回并释放，Spring 线程池负责慢任务的处理，能够有效提高系统的并发处理能力，减小请求响应的延迟。
3. **系统可扩展性增强**：
   - 当系统负载增大时，Spring 线程池提供了灵活的配置（如线程池大小、队列大小等），让系统能够高效地处理更多的并发请求。同时，你可以根据业务需求灵活调整 Spring 线程池，处理更多的慢任务。
4. **资源隔离**：
   - 通过 Spring 线程池处理异步任务，确保 Servlet 线程池的线程资源不会被占满，避免了因为慢任务处理过多而导致请求处理阻塞的情况，保持了系统的高效运行。

**4.总结**

通过结合 **Servlet 线程池 + Spring 线程池 + @Async (void 或 CompletableFuture)**，可以实现：

- **高并发处理**：Servlet 线程池负责 HTTP 请求的分发和接收，Spring 线程池处理业务逻辑中的耗时任务，避免主线程被阻塞。
- **优化资源管理**：Servlet 线程池和 Spring 线程池可以独立配置，避免了线程资源的浪费和过度占用。
- **响应时间的优化**：通过异步执行任务，HTTP 请求的响应时间降低，提升了用户体验。
- **系统吞吐量提升**：异步任务的引入使得系统能同时处理更多请求，提高了系统的整体吞吐量。

这种方式不仅能够提高并发量，还能够保证系统资源的高效利用和更流畅的用户体验。在高并发、高负载的场景中，这种方式尤其有效。



### I/O

I/O（Input/Output，输入/输出）指的是计算机系统中 **数据的输入和输出** 操作，主要涉及 **CPU 与外部设备（如磁盘、网络、键盘、屏幕等）之间的数据传输**。I/O 是所有计算机系统的核心组成部分，它决定了数据如何在不同的组件之间流动。

**I/O 的分类**

I/O 主要分为 **阻塞 I/O（Blocking I/O）** 和 **非阻塞 I/O（Non-blocking I/O）**，以及同步和异步 I/O，不同的方式决定了数据读写时 CPU 的工作模式。

**1. 阻塞 I/O（Blocking I/O）**

- **特点**：I/O 操作完成之前，调用线程会一直 **等待（阻塞）**，无法执行其他任务。

- 例子

  ：

  - 你用 Java 的 `InputStream.read()` 读取文件内容时，调用 `read()` 的线程会阻塞，直到数据读取完成。
  - 在传统的 Servlet 处理 HTTP 请求时，线程会阻塞等待数据库查询返回结果，导致吞吐量降低。

**2. 非阻塞 I/O（Non-blocking I/O）**

- **特点**：I/O 操作不会阻塞调用线程，而是立即返回，如果数据还未准备好，调用方可以选择**轮询**或者**回调**的方式获取数据。

- 例子

  ：

  - Java 的 `NIO（New I/O）` 提供 `Selector` 机制，允许多个 I/O 操作共享一个线程，并且不会阻塞线程。
  - **应用场景**：高并发 Web 服务器（如 Nginx），不会为每个请求创建一个线程，而是使用事件驱动的非阻塞 I/O 处理多个请求。

**3. 同步 I/O（Synchronous I/O）**

- **特点**：调用方需要主动等待 I/O 操作完成，即使是非阻塞 I/O，线程仍然需要轮询检查 I/O 任务的状态。

- 例子

  ：

  - `while (!dataAvailable()) { /* 轮询等待数据 */ }` 这种方式就是同步的，即使线程不会阻塞，但它仍然需要不断检查数据状态，浪费 CPU 资源。

**4. 异步 I/O（Asynchronous I/O）**

- **特点**：调用方不需要等待 I/O 任务完成，而是直接注册一个**回调函数**，等 I/O 任务完成后，操作系统会通知应用程序执行回调函数。

- 例子

  ：

  - Java 的 `CompletableFuture` 和 `@Async`，可以在 I/O 操作完成后自动回调，不会阻塞主线程。

  - Spring WebFlux（基于 Reactor），使用 **事件驱动（Event-driven）** 方式来处理 HTTP 请求，而不是传统的阻塞式线程池模型。

    

**实际应用：I/O 在 Web 开发中的作用**

| I/O 类型       | 适用场景                                       | 例子                                                      |
| -------------- | ---------------------------------------------- | --------------------------------------------------------- |
| **阻塞 I/O**   | 传统 Web 服务器（Tomcat 默认模式）、数据库查询 | Servlet API（`doGet()`、`doPost()`）                      |
| **非阻塞 I/O** | 高并发 Web 服务器（如 Nginx）、消息队列        | Java NIO（`Selector`）、Netty                             |
| **同步 I/O**   | 传统数据库查询、文件读取                       | JDBC 连接 MySQL                                           |
| **异步 I/O**   | 高并发 API、微服务                             | `@Async` + `CompletableFuture`、Spring WebFlux（Reactor） |



**I/O 本质上就是** **数据的读写**，只是它涉及的不仅仅是文件，还有 **网络、数据库、磁盘等各种输入/输出操作**。

简单来说：

- **输入（Input）**：从外部数据源（磁盘、网络、键盘等）读取数据到程序里。
- **输出（Output）**：把数据从程序写入外部数据源（文件、网络、数据库等）。

**举几个常见的 I/O 例子：**

| I/O 操作         | 具体例子                                   | 属于哪种 I/O                             |
| ---------------- | ------------------------------------------ | ---------------------------------------- |
| **文件 I/O**     | 读取文件、写入日志文件                     | 阻塞 I/O                                 |
| **网络 I/O**     | HTTP 请求、WebSocket、RPC 调用             | 非阻塞 I/O（如 Netty）                   |
| **数据库 I/O**   | 查询 MySQL、写入 Redis                     | 阻塞 I/O（传统 JDBC）或异步 I/O（R2DBC） |
| **消息队列 I/O** | 生产/消费 Kafka、RabbitMQ 消息             | 异步 I/O                                 |
| **控制台 I/O**   | Scanner 读取键盘输入、System.out.println() | 阻塞 I/O                                 |

### I/O 流

I/O 流可以理解为 **数据的流动通道**，它用于在 **程序和外部设备（文件、网络、内存等）之间传输数据**。

在 Java 里，I/O 流是 **面向数据流** 设计的，主要分为 **输入流（InputStream）** 和 **输出流（OutputStream）**：

- **输入流（Input Stream）**：从外部读取数据到程序里（数据“流入”）。
- **输出流（Output Stream）**：将数据从程序写到外部设备（数据“流出”）。

**按数据类型，I/O 流可以分为：**

| 类型                           | 说明                                                         | 主要类                                                       |
| ------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **字节流（Binary Stream）**    | 以 **字节（8-bit）** 为单位传输数据，适用于图片、音频、视频等 | `InputStream`、`OutputStream`、`FileInputStream`、`FileOutputStream` |
| **字符流（Character Stream）** | 以 **字符（16-bit Unicode）** 为单位，适用于文本处理         | `Reader`、`Writer`、`FileReader`、`FileWriter`               |

**I/O 流的常见用法：**

📌 **读取文件（字节流）**

```java
FileInputStream fis = new FileInputStream("test.txt");
int data;
while ((data = fis.read()) != -1) {
    System.out.print((char) data); // 逐字节读取并转换成字符
}
fis.close();
```

📌 **写入文件（字符流）**

```java
FileWriter writer = new FileWriter("output.txt");
writer.write("Hello, I/O 流！");
writer.close();
```

**流的特点**

1. **单向流动**：输入流 **只能读**，输出流 **只能写**。
2. **顺序处理**：数据 **按顺序** 读取或写入，不能随机访问（但可以借助 `RandomAccessFile`）。
3. **可能阻塞**：读取数据时，可能会等待数据到达（例如网络流）。

**流 VS I/O**

- **I/O** 是 **概念**，指的是所有 **输入和输出操作**，包括 **磁盘、网络、数据库**。
- **I/O 流** 是 **具体的实现**，用于在 **程序和外部设备** 之间 **传输数据**（流动）。

💡 **通俗理解**：

- **I/O 就是数据的读写**，是 **行为**（比如读文件、写数据库）。
- **I/O 流是数据流动的管道**，是 **技术**（比如 `FileInputStream` 读取文件）。

**总结**：I/O 流让程序可以以 **流的方式** 读写数据，使数据像水流一样流动，而不需要一次性加载整个数据块，提高了 **效率和灵活性**。



