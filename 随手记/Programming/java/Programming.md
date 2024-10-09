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
