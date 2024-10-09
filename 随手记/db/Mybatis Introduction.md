### Mybatis Introduction


- Building SqlSessionFactory from XML</br>
Every Mybatis application centers around an instance of SqlSessionFactory. A SqlSessionFactory instance can be acquired by using the SqlSessionFactoryBuilder. SqlSessionFactoryBuilder can build a SqlSessionFactory instance from an XML configuration file, or from a customer prepared instance of the Configuration class.

- Building a SqlSessionFactory instance from an XML file is very simple, It is recommended that you use a classpath resource for this configuration, but you could use any inputStream instance, including one created from a literal file path or a file://URL. Mybatis includes a utility class, called Resources, that contains a number of methods that make it simpler to load resources from the classpath and other localtions.</br>
` String resource = "org/mybatis/example/mybatis-config.xml"; `
` InputStream inputStream = Resources.getResourceAsStream(resource); `
` SqlSessionFactory sqlSessionFactory =
  new SqlSessionFactoryBuilder().build(inputStream); `
  
- The configuration XML file contains settings for the core of the MyBatis system, including a DataSource for acquiring database Connection instances, as well as a TransactionManager for determining how transactions should be scoped and controlled. The full details of the XML configuration file can be found later in this document, but here is a simple example:
   
		<?xml version="1.0" encoding="UTF-8" ?>
		<!DOCTYPE configuration
		  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
		  "https://mybatis.org/dtd/mybatis-3-config.dtd">
		<configuration>
		  <environments default="development">
		    <environment id="development">
		      <transactionManager type="JDBC"/>
		      <dataSource type="POOLED">
		        <property name="driver" value="${driver}"/>
		        <property name="url" value="${url}"/>
		        <property name="username" value="${username}"/>
		        <property name="password" value="${password}"/>
		      </dataSource>
		    </environment>
		  </environments>
		  <mappers>
		    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
		  </mappers>
		</configuration> 

- Building SqlSessionFactory without XML</br>
If you prefer to directly build the configuration from Java, rather than XML, or create your own configuration builder, MyBatis provides a complete Configuration class that provides all of the same configuration options as the XML file.

		DataSource dataSource = BlogDataSourceFactory.getBlogDataSource();
		TransactionFactory transactionFactory =
		  new JdbcTransactionFactory();
		Environment environment =
		  new Environment("development", transactionFactory, dataSource);
		Configuration configuration = new Configuration(environment);
		configuration.addMapper(BlogMapper.class);
		SqlSessionFactory sqlSessionFactory =
		  new SqlSessionFactoryBuilder().build(configuration);
		  
	Notice in this case the configuration is adding a mapper class. Mapper classes are Java classes that contain SQL Mapping Annotations that avoid the need for XML mapping. However, due to some limitations of Java Annotations and the complexity of some MyBatis mappings, XML mapping is still required for the most advanced mappings (e.g. Nested Join Mapping). For this reason, MyBatis will automatically look for and load a peer XML file if it exists (in this case, BlogMapper.xml would be loaded based on the classpath and name of BlogMapper.class). More on this later.
	
	