# Java基础

## 序列化和反序列化

Java*序列化*是指把Java对象转换为**字节序列**的过程，而Java*反序列化*是指把字节序列恢复为**Java对象**的过程.

**序列化是指将对象转换成一种可存储或者可传输的格式，以便在需要的时候可以还原为对象（反序列化）**。虽然传统意义上，序列化是指将对象转换为字节流，但是在更广泛的概念中，序列化也可以指代将对象转换为其他格式，例如json字符串。JSON序列化将对象的数据以JSON格式进行表示，以便在不同系统之间进行数据交换和传输。

### 字节流
字节流是序列化是将对象转换为字节流形式，适用于底层数据传输和存储。

### JSON序列化
JSON序列化是将对象转换为JSON字符串的形式，适用于数据交换和跨平台传输。


### SerialVersionUID

所有实现了Serializable接口的类都建议生成一个SerialVersionUID，通过IDE或者手写的方式生成，**可用来验证版本的一致性**。

#### 原理
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



#### 应用

##### web或跨平台传输

###### 场景举例

- 前置条件：
  - webapi中的DTO实现Serializebal接口，并显示定义SerialVersionUID。
  - get接口返回DTO的SerialVersionUID
  - update接口需要传入SerialVersionUID，并与当前SerialVersionUID进行对比，相同允许修改，不同不允许修改。允许修改后需要更新新的SerialVersionUID。

- 场景模拟：**仅适用于很少需要修改的数据，例如某种重要全局配置项。**
  - get接口返回体中返回SerialVersionUID，并进行后续操作，例如调用update接口进行数据的修改。在update数据时，将get接口返回的SerialVersionUID一起提交到服务端，服务端将传入的SerialVersionUID与当前DTO对象的SerialVersionUID进行对比，如果相同，则允许继续修改，并更新新的SerialVersionUID；如不同，返回报错信息。（如果在update接口调用之前，已经有其他人对此数据进行了修改，则会导致SerialVersionUID不同。）



## Maven

### POM

在pom中新增dependency后，在IDEA中要先刷新Maven（Reload All Maven Project), 在此过程中Maven会自动下载maven repository中确实的JAR。

- 先Reload All Maven Project，然后再install或者package



## 初始化及执行顺序

执行顺序分先后

- 静态变量和静态代码块：在类加载时候，**静态变量和静态代码块**就已经运行了，且只运行一次。

  可以理解为全局，存放于方法区，随着类而消亡。

  **所有实例皆使用同一个静态变量或者静态代码块。**

  如果有多个静态变量和静态代码块，则按照书写顺序执行。

- 类成员实例变量和实例代码块（构造代码块）：每次类生成实例时候执行，优于构造函数，则按照书写顺序执行，可以将多个构造方法中的相同的代码放到构造代码块中,对对象进行初始化.

- 构造函数：在创建对象时被调用（生成实例时）。



## 构造函数

- 在spring中，如果一个class拥有构造函数，并此类会被注册为bean，构造函数会在bean被注册到上下文之前执行。



## 路径

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





## 关键字

### super

The `super` keyword refers to superclass (parent) objects.

It is used to call superclass methods, and to access the superclass constructor.

The most common use of the `super` keyword is to eliminate the confusion between superclasses and subclasses that have methods with the same name.

To understand the `super` keyword, you should have a basic understanding of [Inheritance](https://www.w3schools.com/java/java_inheritance.asp) and [Polymorphism](https://www.w3schools.com/java/java_polymorphism.asp).

