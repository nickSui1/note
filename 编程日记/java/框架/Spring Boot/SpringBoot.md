# SpringBoot

The magic you are looking for is in the work you're avoiding.

你所寻找的魔法，就隐藏在你逃避的事情中。

## 重要特性

- 约定优先于配置

  - SpringBoot无需自己配置spring  mvc，spring Ioc ，spring AOP， 以及tomcat，undertow，jetty等服务器，以上这些在Spring 中的繁琐配置，在SpringBoot中均可以通过Maven依赖的starter来快速获取，只需要在Pom.xml中引用对应的starter即可。

  

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

- @Autowired 最常用的注解，机制最基本的是根据类型（by type）或者根据名称（by name）。
- @Autowired是一个默认必须找到对应Bean的注解，如果不能确定其标注属性一定会存在并且允许这个被标注的属性为null，那么可以配置@Autowired属性的required为false
  - Autowired可以通过类型找到对应的Bean进行注入
  - Autowired可以通过名称找到对应的Bean进行注入，举例：如果接口有多个实现类，直接默认注入，在调用的时候会报错，这时候可以选择by name（用想要注入的Bean的名称作为属性名）注入。
- @Primary和@，这两个注解可以用来消除歧义性
  - @Primary 注解可以加在Bean上，意味着此类会被优先注入（例如在一个接口有多个实现类的时候，会优先注入标记此注解的Bean）
  - @Qualifier实现可以通过by name的形式找到想要注入的Bean，一般配合@Autowired一起使用，可实现同时通过类型和名称获取Bean（例如在一个接口有多个实现类的时候，如果有多个标明@Primary注解的，这时候可以在属性上加上@Qualifier，注解内参数为对应Bean的名称。
- 带参数的构造方法依赖注入
  - 在构造方法的参数内，使用@Autowired，如果需要，可以加上@Qualifier

#### 面向切面编程（Aspect Oriented Programming，AOP）



#### 生命周期

IoC容器初始化和销毁Bean的过程，就是Bean的生命周期的过程，它大致分为Bean定义，Bean的初始化，Bean的生存期和Bean的销毁4个部分。

##### Bean的定义

- Spring通过我们的配置，如@ComponentScan定义的扫描路径去找带有@Component的类，这个过程就是一个资源定位的过程。



## 数据库 

### DataSource配置方式的选择

- 可以在application.properties或者application.yml中配置DataSource，如果未配置，spring boot会寻找默认的内置数据库驱动去应用。

- 在需要动态的配置DataSource时，或者在运行时需要决定使用哪种数据库资源的时候，可以以编程方式配置DataSource：Configure DataSource Programmatically  in Spring Boot.

  

