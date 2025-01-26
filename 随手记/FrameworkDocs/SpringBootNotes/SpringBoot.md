# SpringBoot

The magic you are looking for is in the work you're avoiding.

你所寻找的魔法，就隐藏在你逃避的事情中。

## 重要特性

- 约定优先于配置

  - SpringBoot无需自己配置spring  mvc，spring Ioc ，spring AOP， 以及tomcat，undertow，jetty等服务器，以上这些在Spring 中的繁琐配置，在SpringBoot中均可以通过Maven依赖的starter来快速获取，只需要在Pom.xml中引用对应的starter即可。


**Spring Boot就是一个集成了众多技术模块的启动器，核心的东西包括，spring，spring mvc（负责web模块），security，jpa等。**



## SpringBoot 的依赖和自动配置

- **依赖：**Maven中的spring-boot-start-web包中的Pom文件引用了web常用的Spring依赖，如spring boot，json，tomcat，hibernate-validator，spring web，spring web mvc等

- **配置：** Maven中的spring-boot-autoconfigure中可以找到 spring-boot-autoconfigure-x.x.Release-sources.jar包，解压后在子目录中找到org\springframework\boot\autoconfigure\web\servlet，可以看到许多配置类，例如其中 **DispatcherServletAutoConfiguration** 类就是一个对DispatcherServlet进行自动配置的类。（这些配置类，就是使用上面的依赖进行自动配置的）
- SpringBoot通过Maven依赖找到对应的jar包和嵌入的服务器，然后使用默认自动配置类来创建默认的开发环境，这也是为什么我们如此简单快速的开启编程工作而不是把时间放在引用和配置上。



## 配置

### 使用自定义配置

有些时候，我们需要对默认的环境进行修改以适应个性化的需求，这些在SprintBoot中也非常简单，正如@EnableConfigurationProperties注解那样，它允许读入配置文件的内容来自定义自动初始化所需的内容。

一般情况下使用springBoot提供的默认配置就够了，只在必要的时候去对配置进行自定义，可以使用properties文件对如NoSQL和DataBase等内容进行修改

- NoSQL
- DataBase



## SpringMvc视图解析器

ViewResolver是Spring MVC的视图解析器，作用是定位视图，也就是当控制器只返回一个逻辑名称的时候，无法直接找到视图，需要通过视图解析器来定位解析。

- 机制

  - 增加JSP和JSTL的maven以来配置
  - 在properties文件中自定义视图的前缀后缀
  - 在控制器类上的注解用@Controller，控制方法用@ReuqestMapping来定义控制器映射，在方法中返回逻辑名
  - 再定义一个与controller中返回的逻辑名相符的jsp文件，类中写html和js

  这样就完成了一个简单的控制器，并且让视图解析器找到视图的功能。



## 注解

### @SpringBootApplication

- 此注解标志着此类是一个Spring Boot的入门文件（入口）

### @Bean

`@Bean` is used to *explicitly* **declare a single bean**, rather than letting Spring do it automatically as above. It decouples the declaration of the bean from the class definition, and lets you create and configure beans exactly how you choose.

### @Component

The @Component annotation in Java Spring is used to mark a Java class as a Spring component. It signals to the Spring framework that **the class should be automatically detected during classpath scanning and registered as a bean in the Spring application context**.

**@Component is typically used when you want to define a bean for a class that you create yourself and that has a simple initialization process**. This is because @Component provides a convenient way **to register a class as a bean** without having to define a separate configuration class.



## Spring IoC

Spring IoC，控制反转（IoC），是Spring最成功的理念。

### Spring IoC核心理念

#### 控制反转（Inversion of Control，IoC）

IoC容器是Spring的核心，可以说Spring是一种基于IoC容器编程的框架。区别于常规的生成对象方式，例如new关键字，

**IoC是一种通过描述来生成或者获取对象的技术**（在spring中使用xml，在springBoot中使用注解的形式）。

- 所有IoC容器都要实现BeanFactory接口，BeanFactory是一个顶级容器接口。
  - BeanFactory中的多个getBean方法是IoC中最重要的方法之一，可以通过类型，名称去获取Bean。
  - isSingleton 方法是判断Bean是否在Spring IoC中为单例。
    - 默认情况下Bean都是以单例形式存在的，也就是使用getBean方法返回的都是同一个对象。
    - 与isSingleton方法相反的是isPrototype方法，如果它返回的是true，那么我们使用getBean方法获取Bean时，SpringIoC容器就会创建一个新的Bean返回给调用者，这些与Bean的作用域相关。
  - 在BeanFactory的基础上，还设计了一个更为高级的接口ApplicationContext，它是BeanFactory的子接口之一，和BeanFactory同为最重要的接口设计，在使用过程中，大部分的Spring IoC容器都是ApplicationContext接口的实现类。



Spring IoC容器是一个管理Bean的容器（Bean，就是Spring中每一个需要管理的对象，称为Spring Bean，简称Bean）。

##### 装配Bean的方式

###### 普通方式

通过一个个的注解@Bean注入Spring IoC

- 创建一个POJO类（plain ordinary java object，普通对象，或简单对象）
- 然后定义一个Java配置文件，类注解@Configuration，创建一个init方法，方法注解@Bean(name="")，返回Pojo类
- 使用AnnotationConfigApplicationContext类传递上下文，创建一个类，方法内new AnnotationConfigApplicationContext 的对象ApplicationContext，然后用这个对象的变量名去getBean。

###### 扫描方式

spring允许我们使用扫描Bean装配到IoC容器中

- @Component 标明哪个类被扫描进入Spring IoC容器
- @ComponentScan 标明采用何种策略去扫描装配Bean，只会扫描当前类所在的包和其子包
  - 在需要装配的Bean上增加注解@Component
  - 在java配置类上增加注解@Configruation和@ComponentScan
  - 默认状态下ComponentScan只会扫描配置类当前包及其子包下的Bean，可以通过给ComponentScan写参数的形式来自定义扫描范围。

##### 获取Bean

###### 依赖注入

Dependency injection 简称DI，依赖注入是一种设计模式，用于实现类之间的解耦和依赖关系的管理。它通过将依赖关系的创建和维护责任转移到外部容器中，使得类不需要自己实例化依赖对象，而是由外部容器动态地注入依赖。传统的对象创建方式往往由类自身负责创建和管理依赖对象，这样导致了类之间的紧密耦合。

**Spring will only inject dependencies into beans.** Spring只会对bean进行依赖注入, 也就是说, 某个类需要依赖注入引用其他类, 这些类都必须是bean才可以.

**Filter 需要特别作处理, filters即使加了@Component, 也需要显示的在SecurityConfiguration中进行注册bean, 以及添加到filter chain中. Spring不会自动添加加了注解的filter.**

- @Autowired 最常用的注解，机制最基本的是根据类型（by type）或者根据名称（by name）。

- @Autowired是一个默认必须找到对应Bean的注解，如果不能确定其标注属性一定会存在并且允许这个被标注的属性为null，那么可以配置@Autowired属性的required为false
  - Autowired可以通过类型找到对应的Bean进行注入
  - Autowired可以通过名称找到对应的Bean进行注入，举例：如果接口有多个实现类，直接默认注入，在调用的时候会报错，这时候可以选择by name（用想要注入的Bean的名称作为属性名）注入。
  
- @Primary和@，这两个注解可以用来消除歧义性
  - @Primary 注解可以加在Bean上，意味着此类会被优先注入（例如在一个接口有多个实现类的时候，会优先注入标记此注解的Bean）
  - @Qualifier实现可以通过by name的形式找到想要注入的Bean，一般配合@Autowired一起使用，可实现同时通过类型和名称获取Bean（例如在一个接口有多个实现类的时候，如果有多个标明@Primary注解的，这时候可以在属性上加上@Qualifier，注解内参数为对应Bean的名称。
  
- **带参数的构造方法依赖注入(推荐的方式)**
  
  - 创建需要依赖注入的属性(Injected property)
  
  - 在构造函数方法上加@Autowired, 将需要依赖添加到构造函数方法参数内, 并将其指派给Injected property
  
  - 如果要保持依赖注入的不可变性, 可以在属性声明时候加上final关键字, 这样可以保证在类的生命周期内不会被更改. 
  
  - 依赖注入的生命周期和它的引用类的生命周期是绑定的
  
    ```java
    @Configuration
    public class config{
      @Bean
      @Scope("singleton")
      public CustomBean getCustomBean(){
        return new CustomBean();
      }
    }
    
    public class ClassA{
    	private final CustomBean customBean; //customBean 作为依赖注入的引用，它的引用生命周期由 ClassA 的生命周期决定。
      
      @Autowired
      public ClassA(CustomBean customBean){//CustomBean 本身的生命周期取决于它在 Spring 容器中的声明方式。如果是 singleton，它会在整个应用程序生命周期内存活；如果是 prototype，它的生命周期仅限于注入的那个对象的生命周期（如 ClassA）。
        this.customBean = customBean;
      }
    }
    ```



详细列举不同的作用域（`Scope`）如何影响依赖注入引用的生命周期，以及 `Bean` 本身的生命周期。还是用刚才提供的例子来解释。

**1. Singleton（默认作用域）**

```java
@Bean
@Scope("singleton")
public CustomBean customBean() {
    return new CustomBean();
}
```

- **依赖注入的引用（`customBean` in `ClassA`）**:
  - `customBean` 作为 `ClassA` 中的依赖注入引用，它的引用生命周期由 `ClassA` 的生命周期决定。当 `ClassA` 被销毁或垃圾回收时，`customBean` 的引用也不再存在。
  
- **`CustomBean` 本身的生命周期**:
  - `CustomBean` 是 `singleton` 作用域的 Bean，Spring 容器在启动时会创建该 Bean 的一个实例，并在整个应用程序的生命周期中共享使用。不论有多少个类（如 `ClassA`）注入这个 Bean，它们都会共享这个 `CustomBean` 实例。只有当应用程序关闭或 Spring 容器关闭时，`CustomBean` 才会被销毁。

**2. Prototype**

```java
@Bean
@Scope("prototype")
public CustomBean customBean() {
    return new CustomBean();
}
```

- **依赖注入的引用（`customBean` in `ClassA`）**:
  - `customBean` 的引用生命周期仍然由 `ClassA` 的生命周期决定。当 `ClassA` 被销毁时，这个 `customBean` 引用就会随之消失。
  
- **`CustomBean` 本身的生命周期**:
  - `CustomBean` 是 `prototype` 作用域的 Bean。每次注入时（即每次 `ClassA` 创建时），Spring 容器都会为 `ClassA` 创建一个新的 `CustomBean` 实例。当 `ClassA` 被销毁时，`CustomBean` 实例不会被 Spring 容器自动管理销毁，而是依赖于 Java 的垃圾回收机制。`Prototype` Bean 的生命周期与使用它的对象（如 `ClassA`）的生命周期一致。

**3. Request Scope（仅适用于 Web 应用）**

```java
@Bean
@Scope("request")
public CustomBean customBean() {
    return new CustomBean();
}
```

- **依赖注入的引用（`customBean` in `ClassA`）**:
  - `customBean` 的引用生命周期由 `ClassA` 决定，但这仅在 HTTP 请求范围内有效。一旦请求结束，`ClassA` 和它的依赖（`customBean`）都将被销毁。

- **`CustomBean` 本身的生命周期**:
  - `CustomBean` 是 `request` 作用域的 Bean。每个 HTTP 请求都会生成一个新的 `CustomBean` 实例，这个实例在请求处理结束时被销毁。因此，它的生命周期与 HTTP 请求的生命周期一致，随请求结束而销毁。

**4. Session Scope（仅适用于 Web 应用）**

```java
@Bean
@Scope("session")
public CustomBean customBean() {
    return new CustomBean();
}
```

- **依赖注入的引用（`customBean` in `ClassA`）**:
  - `customBean` 的引用生命周期由 `ClassA` 和 HTTP 会话决定。当 `ClassA` 依赖注入时，会获取当前 HTTP 会话的 `CustomBean` 实例。引用在会话结束时销毁。

- **`CustomBean` 本身的生命周期**:
  - `CustomBean` 是 `session` 作用域的 Bean。每个用户会话会生成一个 `CustomBean` 实例，并在整个会话期间共享使用。会话结束时，`CustomBean` 实例会被销毁。

**5. Application Scope**

```java
@Bean
@Scope("application")
public CustomBean customBean() {
    return new CustomBean();
}
```

- **依赖注入的引用（`customBean` in `ClassA`）**:
  - `customBean` 的引用生命周期由 `ClassA` 的生命周期决定。当 `ClassA` 被销毁时，引用随之消失。
  
- **`CustomBean` 本身的生命周期**:
  - `CustomBean` 是 `application` 作用域的 Bean。整个 Web 应用程序共享一个实例，Bean 的生命周期与整个应用程序的生命周期一致，直到应用程序关闭或重启。

**总结**

| 作用域          | 依赖注入引用的生命周期                               | Bean 本身的生命周期                                          |
| --------------- | ---------------------------------------------------- | ------------------------------------------------------------ |
| **Singleton**   | 由引用它的类（如 `ClassA`）的生命周期决定            | 应用程序的生命周期，所有类共享同一个实例，直到应用程序关闭。 |
| **Prototype**   | 由引用它的类（如 `ClassA`）的生命周期决定            | 每次注入创建一个新实例，实例的生命周期仅由引用它的类决定，随 `ClassA` 销毁。 |
| **Request**     | 由引用它的类的生命周期决定，但在 HTTP 请求结束时销毁 | 每个 HTTP 请求创建一个实例，随请求结束时销毁。               |
| **Session**     | 由引用它的类的生命周期决定，但在 HTTP 会话结束时销毁 | 每个 HTTP 会话创建一个实例，随会话结束时销毁。               |
| **Application** | 由引用它的类的生命周期决定                           | 整个应用程序共享一个实例，Bean 的生命周期与应用程序一致。    |





###### 关于依赖注入需要注意的点

- 1, 一切使用new创建的对象, Spring依赖注入机制都无法捕获, 哪怕此对象加了类似于@Service的注解. 因为new 的对象会绕过Spring Container. 所以,new的对象无法使用依赖注入



#### 面向切面编程（Aspect Oriented Programming，AOP）

#### 生命周期

IoC容器初始化和销毁Bean的过程，就是Bean的生命周期的过程，它大致分为Bean定义，Bean的初始化，Bean的生存期和Bean的销毁4个部分。

##### Bean的定义

- Spring通过我们的配置，如@ComponentScan定义的扫描路径去找带有@Component的类，这个过程就是一个资源定位的过程。
- Bean 未指定Scope时候, 默认为Singleton, 即单例模式的Bean, Bean的生命周期持续整个application context.
  - Scope: Bean可以加Scope注解, 例如 @Scope(“singleton”), @Scope("request"), @Scope("session") 等, 不同的作用域, Bean的生命周期也会不同, 也就意味着, 这些Bean会有不同的 创建, 初始化, 生存, 销毁时机.



The terms **"Bean scope"** and **"Object scope"** can both refer to the lifecycle and visibility of objects, but they apply in different contexts. Let’s break down the differences:

### **Bean Scope (Spring Framework Context)**
In the Spring framework, **Bean scope** refers to how Spring manages the lifecycle of its Beans. It determines **how long** a Bean instance will live, **how many instances** of the Bean are created, and **who can access them**.

Spring provides several scopes that govern the lifecycle of Beans within the **Spring Application Context**:

#### Common Spring Bean Scopes:
- **Singleton** (default): A single instance of the Bean is created, and it is shared across the entire application context. This is the default scope if no `@Scope` annotation is provided.
    - One Bean instance per Spring container.
    - Example: `@Scope("singleton")`
  
- **Prototype**: A new instance of the Bean is created every time it is requested (whether by another Bean or from the application context).
    - Multiple Bean instances per Spring container.
    - Example: `@Scope("prototype")`
  
- **Request** (Web context): A new instance of the Bean is created for every HTTP request in a web application.
    - Example: `@Scope("request")`
  
- **Session** (Web context): A new instance of the Bean is created for every HTTP session in a web application.
    - Example: `@Scope("session")`

- **Application** (Web context): A single Bean instance is created for the lifecycle of a web application.
    - Example: `@Scope("application")`
  
- **WebSocket** (Web context): A Bean instance is created and maintained for the lifecycle of a WebSocket session.
    - Example: `@Scope("websocket")`

**Example in Spring:**

```java
@Bean
@Scope("prototype")
public SomeBean getSomeBean() {
    return new SomeBean();
}
```

**Summary of Bean Scope:**

- **Bean scope** is a Spring concept used to manage object creation and lifecycle within the Spring application context.
- The scope determines how and when Beans are instantiated and destroyed in the Spring container.

---

### **Object Scope (General Programming Context)**
In general **object-oriented programming (OOP)**, **object scope** refers to the lifetime and visibility of an object within the program. It defines **when** the object is created, **where** it can be accessed, and **when** it is destroyed.

In languages like Java, Python, and C++, object scope is determined by how the object is defined and where it is used in the code. There are several types of object scopes:

#### Common Object Scopes in OOP:
- **Local scope**: Objects created within a method or block of code. These objects are only accessible within that block and are destroyed once the method finishes executing.
    - Example: Local variables or objects in a method.
  
- **Instance scope** (Object-level scope): Objects or fields that are tied to an instance of a class. They exist as long as the object instance exists.
    - Example: Instance variables in a class, where each object of the class has its own copy of the variable.
  
- **Class scope** (Static scope): Objects or fields declared with the `static` keyword. These are shared by all instances of the class and exist for the lifetime of the application or class loader.
    - Example: Static variables in Java or class variables in Python.

#### Example of Object Scopes in Java:
```java
public class MyClass {
    private String instanceField;  // Instance scope (exists for each object)

    private static String staticField;  // Class scope (shared across all objects)

    public void someMethod() {
        String localVariable = "local";  // Local scope (exists only in this method)
    }
}
```

**Summary of Object Scope:**

- **Object scope** is a general programming concept that defines the accessibility and lifetime of objects based on where they are declared in the code (method, instance, or class).
- It is language-agnostic and applies to object-oriented programming in general.

#### **Key Differences:**

1. **Context**:
   - **Bean Scope**: Specific to Spring or similar frameworks that manage object lifecycles in a container.
   - **Object Scope**: Applies in general programming, determining object lifecycle based on where they are declared and used in code.

2. **Management**:
   - **Bean Scope**: Spring (or another framework) controls the lifecycle and visibility of Beans (objects) within its container, and the scope can be configured (singleton, prototype, request, etc.).
   - **Object Scope**: Managed by the language's runtime (e.g., Java JVM, Python interpreter), based on when objects are created and garbage-collected.

3. **Lifespan**:
   - **Bean Scope**: Controlled by the Spring container and varies based on the scope (e.g., singleton for the whole application, prototype for each request).
   - **Object Scope**: Depends on where the object is declared (method, class, or static context) and how long references to it exist.

4. **Visibility**:
   - **Bean Scope**: Visibility is within the Spring application context (global within the context or per request/session).
   - **Object Scope**: Visibility is based on the object's declaration (local to methods, instance-level, or static class-level).

#### Example of Bean Scope vs Object Scope:

Let’s say you have a Spring-managed service:

```java
@Service
@Scope("singleton")  // Bean Scope: Singleton (1 instance for the whole app)
public class MyService {
    private int instanceVariable;  // Object Scope: Instance-level variable
    private static int staticVariable;  // Object Scope: Class-level (shared by all instances)
    
    public void someMethod() {
        int localVariable = 0;  // Object Scope: Local (only in this method)
    }
}
```

In this example:
- **Bean Scope**: `MyService` is a singleton, meaning Spring will create only one instance and share it throughout the application.
- **Object Scope**: 
  - `instanceVariable`: Belongs to the `MyService` object and exists as long as that object exists.
  - `staticVariable`: Shared by all instances of `MyService` and exists for the lifetime of the class.
  - `localVariable`: Only exists while `someMethod()` is running.

#### Conclusion:

- **Bean scope** is a framework-specific concept (like in Spring) that defines how many instances of a Bean exist and when they are created or destroyed in the application context.
- **Object scope** is a general concept in OOP that determines the lifetime and visibility of an object based on where it is declared in the program.



## 数据库 

### DataSource配置方式的选择

- 可以在application.properties或者application.yml中配置DataSource，如果未配置，spring boot会寻找默认的内置数据库驱动去应用。
- 在需要动态的配置DataSource时，或者在运行时需要决定使用哪种数据库资源的时候，可以以编程方式配置DataSource：Configure DataSource Programmatically  in Spring Boot.



### 集成Mybatis

在spring-boot中使用mybatis，有两种常用方式

- 原生mybatis [原生mybatis](https://mybatis.org/mybatis-3/getting-started.html)
- mybatis-spring-boot-stater [mybatis-spring](https://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/)



#### 原生mybatis

##### 通过xml全局配置文件

可以通过配置mybatis-config.xml，对datasource，transactionManage，mapper，setting，properties等节点进行配置，详细可参考 

[mybatis-3/configuration](https://mybatis.org/mybatis-3/configuration.html) . 

当使用xml全局配置时，需要我们手动去构建SqlSessionFactory，SqlSessionFactory可以用来获取SqlSession对象，然后可以使用sqlSession来执行数据库的sql命令了。



#### mybatis-spring-boot-starter 

MyBatis-Spring-Boot-Starter是**MyBatis与Spring Boot**集成的启动器，它简化了在Spring Boot应用程序中集成MyBatis的过程，

As you may already know, to use MyBatis with Spring you need at least an `SqlSessionFactory` and at least one mapper interface.

MyBatis-Spring-Boot-Starter will:

- Autodetect an existing `DataSource`
- Will create and register an instance of a `SqlSessionFactory` passing that `DataSource` as an input using the `SqlSessionFactoryBean`
- Will create and register an instance of a `SqlSessionTemplate` got out of the `SqlSessionFactory`
- Auto-scan your mappers, link them to the `SqlSessionTemplate` and register them to Spring context so they can be injected into your beans

In short, mybatis-spring-boot-starter will **automatically** find your dataSource configuration in the properties and generate **sqlSessionFactory** required for executing SQL command. With mybatis-spring, you do not need to manage commits and rollbacks manually, Spring will help you to manage them, you just have to focus on Coding.

**Notice**: If Spring can not find any dataSource configuration in properties or yml, then Spring will not generate any classes (e.g. SqlSessionFactory).

Mybatis-spring relies entirely on the application.properties(yml) file, whitch means that you do not need configure the **datasource** in mybatis-config.xml file, but it doesn't mean you should't create the mybatis-config.xml file, you still need this file to configure the other node(e.g.  properties, settings, typeAliases, mappers etc.)



#### Mybatis Generator Mybatis代码生成器

使用Mybatis代码生成器，可以自动生成基础的数据库访问层代码，省去很多时间和精力可以去做核心的事情。

##### 引用Mybatis生成器依赖

在Pom中添加引用

```xml
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.4.2</version>
</dependency>
```

##### pom配置

``` xml
```



##### 创建Mybatis生成器配置文件

Mybatis生成器核心部分，相关配置都在这个文件中。

命名：mybatis-generator-config.xml

配置文件代码:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
  	<!-- 这是此配置文件依赖的properties源文件配置，即项目配置文件 -->
    <properties resource="application.yml"/>
  	<!-- 核心配置，注意context节点id要唯一，targetRuntime是Mybatis生成器的生成规则，Mybatis3Simple即为简单模式，仅包含基础的增删改查，默认的Mybatis3模式会在基础的增删改查上生成更多的扩展方法，例如ExampleFindByXXX等 -->
    <context id="dbContext" targetRuntime="Mybatis3Simple">
      	<!-- plugin节点待调研 -->
<!--        <plugin type="org.mybatis.generator.plugins.UnmergeableXmlMappersPlugin"/>  -->
        <commentGenerator>
          <!-- suppressAllComments为true，则不生成任何代码注释，建议开启 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <!-- mybatis生成器需要访问数据库，这里是数据库配置 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://8.141.85.27:3306/cbs"
                        userId="admin"
                        password="Nicksui1992.">
            <!--高版本的 mysql-connector-java 需要设置 nullCatalogMeansCurrent=true-->
<!--            <property name="nullCatalogMeansCurrent" value="true"/>-->
        </jdbcConnection>
        <!-- javaType转换配置 -->
        <javaTypeResolver >
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>
      	<!-- 以下三个节点都包含一个targetProject属性，此属性是生成model和mapper的目标根目录，spring boot项目路径一般是，
 src/main/java 和src/main/resources -->
      	<!-- java model类生成配置,targetPackage是目标路径，生成的model类会在此路径下,targetProject为目标项目,设置为项目根目录 -->
        <javaModelGenerator targetPackage="priv.nick.cbs.topgun.model" targetProject="src/main/java">
            <property name="trimStrings" value="true" />
        </javaModelGenerator>
      	<!-- mapper文件配置，targetProject 项目根目录下的resources；targetPackage为resources下的mapper包-->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources">

        </sqlMapGenerator>
      	<!-- 生成dao的配置，dao就是mybatis的数据访问层mapper接口 （Data Access Object (DAO)） -->
        <javaClientGenerator targetPackage="priv.nick.cbs.topgun.dao" targetProject="src/main/java" type="XMLMAPPER">
            <property name="enableSubPackages" value="false"/>
        </javaClientGenerator>
        
      	<!-- 以下table是表配置，要生成几个表，就配置几个table节点。每次生成时候检查一下，这里只配置需要生成的table。
如果table的配置项，在项目中已存在，就会造成代码合并或者覆盖等问题。
不需要的删除掉，以免重复生成或者覆盖，亦或被合并。 -->
        <table tableName="property" domainObjectName="Property">

        </table>
        <table tableName="property_user" domainObjectName="PropertyUser">

        </table>
        <table tableName="role" domainObjectName="Role">

        </table>
        <table tableName="role_permission" domainObjectName="RolePermission">

        </table>
        <table tableName="user_role" domainObjectName="UserRole">

        </table>
    </context>
</generatorConfiguration>
```



## Exception

### ExceptionHandler

You can add extra (`@ExceptionHandler`) methods to any controller to specifically handle exceptions thrown by request handling (`@RequestMapping`) methods in the same controller. Such methods can:

1. Handle exceptions without the `@ResponseStatus` annotation (typically predefined exceptions that you didn't write)
2. Redirect the user to a dedicated error view
3. Build a totally custom error response



### ControllerAdvice

A controller advice allows you to use exactly the same exception handling techniques but apply them across the whole application, not just to an individual controller. You can think of them as an annotation driven interceptor.

Any class annotated with `@ControllerAdvice` becomes a controller-advice and three types of method are supported:

- Exception handling methods annotated with `@ExceptionHandler`.
- Model enhancement methods (for adding additional data to the model) annotated with

`@ModelAttribute`. Note that these attributes are *not* available to the exception handling views.

- Binder initialization methods (used for configuring form-handling) annotated with

`@InitBinder`.

We are only going to look at exception handling - search the [online manual](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-controller) for more on `@ControllerAdvice` methods.



### Handle Exceptions Globally

To handle exceptions globally, you can use the **@ControllerAdvice** annotation class and **@ExceptionHandler** annotation methods.

```java
@ControllerAdvice
public class GlobalExceptionHandlingControllerAdvice {

    protected Logger logger;

    public GlobalExceptionHandlingControllerAdvice() {
        logger = LoggerFactory.getLogger(getClass());
    }

    /**
     * Convert a predefined exception to an HTTP Status code
     */
    @ResponseStatus(value = HttpStatus.CONFLICT, reason = "Data integrity violation")
    // 409
    @ExceptionHandler(DataIntegrityViolationException.class)
    public void conflict() {
        logger.error("Request raised a DataIntegrityViolationException");
        // Nothing to do
    }
}
```

Note that the @ExceptionHandler(DataIntegrityViolationException.class) above, if any controller throws this exception **DataIntegrityViolationException**, this exception will be handled by the conflict() method.

About the custom exception such as **DataIntegrityViolationException** above, we can define custom exception classes by extends RuntimeException or DataAccessException etc. classes as you need, the simple example is as follows:

```java
public class DataIntegrityViolationException extends DataAccessException {

	/**
	 * Unique ID for Serialized object
	 */
	private static final long serialVersionUID = -8146834359701827537L;

	public DataIntegrityViolationException(String msg) {
		super(msg);
	}

	public DataIntegrityViolationException(String msg, Throwable t) {
		super(msg, t);
	}

}
```

For more details of mvc-exceptions, please check the official document: [Exception Handling in Spring MVC](https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc#using-exceptionhandler)



## Spring Boot 加载与自动配置逻辑

### 配置文件加载机制

SpringBoot 加载配置机制，起始于启动类中的run方法，内部有ConfigurableApplicationContext用于发布事件，然后通过注册的监听器EnvironmentPostProcessorApplicationListener监听到事件后马上加载指定搜索路径的配置文件。

### 加载及自动配置

Spring Boot的官方自动配置类全部都由spring-boot-autoconfigure这个jar包提供，自动配置类的注册文件全部都在META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports 中。

- **加载自动配置类** 使用SpringBootApplication注解中的@EnableAutoConfiguration注解，此方法上的@AutoConfigurationPackage 功能是注册需要自动配置的包，如果不指定就是当前注解所在类的包。

- **自动配置原理** 自动配置文件被加载后，就会注册里边提供的自动配置类了，例如 jdbc 下的org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration 类，此类上的一系列注解就是用来自动配置的，核心注解是@AutoConfiguration

Pom中引用的依赖都通过上述逻辑进行加载和自动配置。



Spring官方整合的组件，spring-boot-xxx-stater，一般需要依赖@Configuration和@Bean的配置才会生效。而个人创建的Bean则需要手动调用。





## Spring Boot 配置管理

Spring Boot支持两种配置文件格式，分别问.properties和.yml，推荐使用yml格式，因为yml格式是树状结构，曾测清晰，节省冗余参数前缀配置量，适配多个环境。

### 配置绑定

``` java
@Autowired

private Environment env;

@Value("${user.username}")

private String username;



@Value("${user.age}")

private int age;

....


```

以上方式过于笨拙，冗余代码多，不优雅。是最笨重的方法，不建议使用。

### 参数绑定

yml配置文件：

``` yaml
member:
	name: Tom
	sex: 1
	age: 18
```

新建一个MemberProperties 参数类进行参数绑定：

``` java
@Data
@NoArgsConstructor
@ConfigurationProperties(prefix = "member")
public class MemberProperties{
  private String name;
  private int sex;
  private int age;
}
```

在启动类上添加@EnableConfigurationProperties注解，指定要启用的@ConfigurationProperties配置

``` java
@SpringBootApplication
@RequiredArgsConstructor
@EnableConfigurationProperties(value={MemberProperties.class})
//@ConfigurationPropertiesScan 此注解可以自动扫描所有包目录下的参数类，可以替代@EnableConfigurationProperties，要扫描指定包可以在 basePackages参数中指定。
@Slf4j
public class Application{
  private final MemberProperties memberProperties;
  
  //CommandLineRunner 是Spring Boot内置的命令行运行器，用于在应用启动之后执行一些特定的操作。
  //这里添加CommandLineRunner单纯为了测试参数绑定是否成功，正常使用场景中，直接注入MemberProperties即可。
  @Bean
  public CommandLineRunner commandLineRunner(){
		retrun (args)->{
      log.info("member properties:{}",memberProperties);
    }
  }
}
```

除了参数绑定，还有另外两种方式分别为：

- 构造器绑定
  - 在参数类中添加构造函数，如果是多个构造函数，需要用@ConstructorBinding注解，指定绑定的构造函数。
  - 在启动类中添加@EnableConfigurationProperties注解，指定要启用的@ConfigurationProperties配置，使其生效。
- Bean配置绑定
  - Bean配置最为简单，新建一个参数绑定类。
  - 在一个配置类中（新建一个或者找现有的），添加这个配置类的Bean。如以下代码所示。
  - 使用的时候直接注入即可使用，无需添加@EnableConfigurationProperties注解来使@ConfigurationProperties生效。

``` java
@Data
@NoArgsConstructor
public class MemberProperties{
  private String name;
  private int sex;
  private int age;
}
```

``` java
@SpringBootConfiguration
public class YmlConfig{
  @Bean
  @ConfigurationProperties(prefix = "member")
  public MemberProperties memberProperties(){
    return new MemberProperties;
  }
}
```

### 配置验证

@ConfigurationProperties 注解还支持参数验证，Spring Boot支持 JSR-303 javax.validation规范注解，首先添加一个JSR-303标准实现的依赖

``` xml
<dependency>
  <groupId>org.hibernate.validator</groupId>
  <artifactId>hibernate-validator</artifactId>
</dependency>
```

然后在参数类的字段上增加验证注释，例如

``` java
@Data
@Validated
@ConfigurationProperties(prefix = "member")
public class MemberProperties{
  @NotNull
  private String name;
  private int sex;
  private int age;
}
```

这是如果将yml中的配置项name改为names，启动应用就会报错。

### 加载配置机制

Spring Boot中的配置文件是通过PropertySourceLoader接口实现的，该接口一共有以下两个实现类

- YamlPropertySourceLoader， yml格式配置文件的加载
- PropertiesPropertySourceLoader，properties格式配置文件的加载

### 配置加密

项目中涉及的各种敏感配置信息全都放在配置文件中是不安全的，Spring Boot提供了以下四种加密方式



#### 使用配置中心（支持自动加密/解谜）

比如使用了第三方配置中心，例如SpringCloud微服务中提供的Spring Cloud Config配置中心，就可以把所有动态配置和敏感配置信息存储在配置中心，用其自带的加密解密机制来保护敏感信息。



#### 使用数据库机制

传统做法是把所有配置信息存储在数据库，使用对称加密算法对所有敏感信息进行加密存储，当Spring Boot启动时候，查询数据库配置并加载到内存中，如果是密文，则解密后再加载到内存中。

这是最传统的，简易的，原始的方式，其他第三方配置中心也是将配置持久化到数据库，再提供了一系列的配置管理功能而已。

可以持久化到 关系型数据库（MySql，Oracle），也可以持久化到内存数据库（Redis，Zookeeper）等。

以上是比较常用的加密机制



#### 使用自定义加密解密机制

使用对称加密算法，将敏感信息持久化，自定义一个数据源类实现自定义加密解密机制。

需要注意的是，这种方式适合敏感信息少的应用场景。如果是Spring Boot的自动配置场景，比如数据源，Redis等自动配置，在系统启动的时候就会默认加载配置并自动配置，所以**需要在Spring Boot配置装载之后自动配置之前对配置进行后置的解密处理**。



#### 使用Jasypt Spring Boot（第三方的加密解密方案）

[Jasypt Spring Boot](https://github.com/ulisesbocchio/jasypt-spring-boot)，这是开源地址，SpringBoot应用的加密解密标准。



## Spring Boot 启动过程

### SpringApplication.run 启动类

SpringBoot的启动类Main方法，内部调用的SpringApplication.run，主要作用是返回应用程序上下文，ApplicationContext，

其中包含了prepareEnvironment()初始化环境变量，createApplicationContext()创建应用程序上下文对象，然后对context对象进行初始化设置，最终返回。

![SpringApplication.run](/Users/nicksy/Desktop/截屏2024-07-16 23.27.41.png)

#### 启动类注解

- @SpringBootApplication 标识应用启动的入口类
- @EnableAutoConfiguration 表示开启Spring Boot的**自动配置功能（重点）**
- @ComponentScan 扫描Spring组件并进行注册的注解。



## JWT Token

JWT 是 JSON Web Token 的缩写，是一种安全地在各方之间传输信息的开放标准。它是一种紧凑、自包含的数据传输方法，通常用于客户端和服务器之间的数据传输。

JWT 通常用于认证和授权，服务器通过验证 JWT 中包含的数字签名来验证用户。

**JWT 由三部分组成：Header、Payload 和 Signature（签名）。**

- Header 包含 Token 类型和 Token 签名算法的元数据。
- Payload 包含关于被验证用户或实体的声明（Claim）或陈述。这些声明可包括用户 ID、用户名或电子邮件地址等信息。
- Signature（签名）使用秘钥和 Header 及 Payload 生成，以确保 JWT 的完整性。

使用 JWT 的一个好处是它们是无状态的，这意味着服务器无需跟踪用户的身份认证状态。这可以提高可扩展性和性能。此外，JWT 可以在不同的域和服务中使用，只要它们共享相同的秘钥来验证签名即可。



下图为token验证前后端流程

![身份验证授权流程](/Users/nicksy/note/programNote/Image/身份验证授权流程.jpg)



## Filter

Filter 是Servlet中提供的过滤器，负责在请求进入Controller之前，以及在返回响应（Controller执行完毕）时的一系列自定义的逻辑处理。

### Filter

Filter接口，实现此接口即可实现过滤功能



### OncePerRequestFilter

每次请求仅执行一次的Filter，有的请求可能会涉及服务器内部forward或者拥有多个Servlet容器，这种情况会多次执行Filter功能。

此抽象类常用在 **身份信息认证 Authentication** 这种需求。



### FilterChain



### Filter Order

#### @Order annotation

最简单快捷的方法，将此注解添加在过滤器类上

#### Ordered interface

实现 Ordered这个接口

#### FilterRegistrationBean

最推荐的方法，可自定义，扩展性强

```java
@Configuration
public class FilterConfig {
    @Bean
    public FilterRegistrationBean<AuthenticationFilter> filterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
      	//SetFilter 要设置的Filter类
        filterRegistrationBean.setFilter(new AuthenticationFilter());
      	//设置生效Url
        filterRegistrationBean.addUrlPatterns("/*");
      	//排序，值越小，优先级越高，可以是-负值
        filterRegistrationBean.setOrder(0);
        return filterRegistrationBean;
    }
}
```

### FilterChain

Filter过滤器链路



## Spring Security

### Authentication Filter

Authentication filter will grab username and password and create authentication **object from** them, this object is basically a way to package the credentials, everything within security works in the form of the **authentication object**, so that object is important first.



### AuthenticationManagemer

Once the **authentication object** was been created, the object is handed over to the **authentication manager**, authentication manager is someone who decides what to do with these credentials, authentication manager will delegate task of authenticating the user to someone else, basically, authentication manager is represents 'what should i do with credential i should authenticate'.



### AuthenticationProvider

Authentication provider is someone who is responsible for checking wether the given username and password or wether the given credentials are correct or not, for authentication provider to validate credentials needs two things, one is **PasswordEncoder** and then it needs **UserDetailsService**.

If you are storing the data into memory, not into the database, you can use the In-Memory-Authentication.



### PasswordEncoder

PasswordEncoder is called by **AuthenticationManager**,whenever the password is passed in or the user credentials are passed in, they will be encryped using some of encryption, to decrypt password you need to do some part of like processing over there, that is what **PasswordEncoder** helps you with. it helps you to encode the entered password and it will help **AuthenticationProvider** with the same, so AuthenticationProvider can take care of the rest.



### UserDetailsService

This is called by **AuthenticationProvider** to load the user details, it can call methods like load by username and it will load the details like username, password, and what all roles does this user have in system, if your data is being stored in the database, it will load it from there.



### SecurityContext

This is the Authentication object.

SecurityContext is a sort of context that is available throghtout the duration of the request, so the context wherein the informantion about authentication is stored.

SecurityContext stores the roles, so the application can dictate who can and cannot allowed to do.



security flow

An user enter the username and the password to the request, it gose through a series of Filter Chain, the AuthenticationFilter will be authentication filter and create authentication object, AuthenticationManager will takes this object and asks the AuthenticationProvider to verify it, then the AuthenticationProvider will call the PasswordEncoder and UserDetailsService, use the UserDetailsService to fetch you userdetails, and use the PasswordEncoder to compare the password and everyting, if everything is matches, the AuthenticationProvider will confirm and details are saved in the authentication object, authentication object is updated with the roles and all, then the security context will be populated with the authentication object, and then the you are allowed to access the controller and code excutes, during the request, this context refers to what you are allowed to do and what you are not allowed to do.

![image-20240906183627710](/Users/nicksy/Library/Application Support/typora-user-images/image-20240906183627710.png)



### Spring Security Control The Role&Permission Example

To control the **permissions** of roles in a **Spring Boot** application using **Spring Security**, you can define permissions at different levels such as at the URL level, method level, or within the application logic. Here’s a step-by-step approach:

#### 1. **Define Roles and Permissions in the Database**

You typically start by defining user roles (such as `ROLE_ADMIN`, `ROLE_USER`) and specific permissions. A common approach is to have a `User` entity linked to a `Role` entity and possibly a `Permission` entity.

##### Sample Database Schema:

```java
@Entity
public class Role {
    @Id
    private Long id;
    private String name; // ROLE_ADMIN, ROLE_USER

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
        name = "roles_permissions",
        joinColumns = @JoinColumn(name = "role_id"),
        inverseJoinColumns = @JoinColumn(name = "permission_id")
    )
    private Set<Permission> permissions;
}
```

```java
@Entity
public class Permission {
    @Id
    private Long id;
    private String name; // e.g., "READ_PRIVILEGE", "WRITE_PRIVILEGE"
}
```

```java
@Entity
public class User {
    @Id
    private Long id;
    private String username;
    private String password;

    @ManyToMany(fetch = FetchType.EAGER)
    private Set<Role> roles;
}
```

#### 2. **Implement UserDetailsService to Load User Data**

You'll need to implement `UserDetailsService` to load users and their roles, which will be used by Spring Security for authentication and authorization.

```java
@Service
public class CustomUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        User user = userRepository.findByUsername(username);
        if (user == null) {
            throw new UsernameNotFoundException("User not found");
        }
        return new org.springframework.security.core.userdetails.User(
            user.getUsername(),
            user.getPassword(),
            getAuthorities(user)
        );
    }

    private Collection<? extends GrantedAuthority> getAuthorities(User user) {
        Set<GrantedAuthority> authorities = new HashSet<>();
        for (Role role : user.getRoles()) {
            authorities.add(new SimpleGrantedAuthority(role.getName()));
            role.getPermissions().forEach(permission -> {
                authorities.add(new SimpleGrantedAuthority(permission.getName()));
            });
        }
        return authorities;
    }
}
```

#### 3. **Configure Spring Security**

In the `SecurityConfig` class, configure access to different URLs or methods based on roles or permissions.

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private CustomUserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService).passwordEncoder(passwordEncoder());
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/admin/**").hasRole("ADMIN") // restricts admin URLs to users with "ADMIN" role
            .antMatchers("/user/**").hasAnyRole("USER", "ADMIN")
            .antMatchers("/public/**").permitAll() // public access without any role
            .anyRequest().authenticated()
            .and()
            .formLogin().permitAll()
            .and()
            .logout().permitAll();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

#### 4. **Method-Level Security with `@PreAuthorize` and `@Secured`**

You can control permissions at the method level by using annotations like `@PreAuthorize` or `@Secured`. These allow more granular permission control.

- **`@PreAuthorize`** checks permissions before executing the method:
```java
@Service
public class ProductService {

    @PreAuthorize("hasRole('ADMIN')")
    public void addProduct() {
        // only admins can add products
    }
    
    @PreAuthorize("hasAnyRole('USER', 'ADMIN')")
    public List<Product> getAllProducts() {
        // both users and admins can view products
    }
}
```

- **`@Secured`** allows for simple role checks:
```java
@Secured("ROLE_ADMIN")
public void deleteProduct(Long productId) {
    // only admins can delete products
}
```

To enable method-level security in your configuration, add:
```java
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {
}
```

#### 5. **Role Hierarchy (Optional)**

If you want certain roles to inherit the permissions of others (e.g., `ADMIN` inherits all permissions from `USER`), you can set up a role hierarchy.

```java
@Bean
RoleHierarchy roleHierarchy() {
    RoleHierarchyImpl roleHierarchy = new RoleHierarchyImpl();
    roleHierarchy.setHierarchy("ROLE_ADMIN > ROLE_USER");
    return roleHierarchy;
}
```

#### 6. **Custom Permission Evaluation (Advanced)**

For more fine-grained permission control, you can implement a custom permission evaluator using the `@PreAuthorize` annotation with a SpEL (Spring Expression Language) expression.

```java
@PreAuthorize("hasPermission(#id, 'Product', 'READ_PRIVILEGE')")
public Product getProductById(Long id) {
    // Logic to fetch product
}
```

To implement custom permission evaluation, you’ll need a `PermissionEvaluator` and configure it in your security setup.

#### Summary

- Define roles and permissions in your database or hardcode them.
- Use `UserDetailsService` to load roles and permissions for authentication.
- Configure role-based access to URLs in `SecurityConfig`.
- Use method-level security with `@PreAuthorize` or `@Secured`.
- Optionally, implement role hierarchy or custom permission evaluation for more complex scenarios.

By combining these approaches, you can control user permissions in a highly flexible way using Spring Security.

#### Scenarios to which you can apply

- user login: This is user-level authentication, users send their username and password to the server, which returns the token for subsequent requests.
- server to server: This is server-level authentication where other servers send their client_id and client_secret to get their access_token, the token contains claims (e.g., client_id, scopes, expiry time) , you can verify that the token has been tampered with, you do not need to extract the client_secret from token, because the other server has been authenticate when they send their client_id and client_secret to get access_token, they have proven who they are, the access_token is the proof.
- Third party login: The users provide their credentials(e.g., username, password ) to own server, we use their credentials to request their original platform for obtain their permission,  we only need original platform return the verification result and roles then we can generate token for third party users, why do not need their original platform generate token? because even   we get the token, we also need to verify the token, there are implicate many other factor. so we only need to know that their credentials are permitted and we generate token and authentication for them.

Note: SecurityContextHolder is just a container, whether your authentication is stateful or stateless, SecurityContextHolder is only responsible for storing  authentication info as a container.

- Stateful: The HttpSession usually used for authentication stateful.
- Stateless: Server does not stores any states interact with client, the client sends requests to server with authentication header each time, and then the server will authenticate the token and stores an authentication information into the SecurityContextHolder if the token is successfully authenticated, the information only existing one request duration in the SecurityContextHolder.
- About logout: If we use the stateless authentication, we need the client to send the access_token and refresh_token of users to logout API so that we can invalidate both of them.
- Client Table: The client table data only use for other servers to request our API, we provide a table to store the client_id and client_secret so that they can use these two column to request a token for their authentication such as normal users login that use the username and password.
