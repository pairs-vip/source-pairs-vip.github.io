---
title: SSM 框架学习笔记
date: 2026-11-25 18:06:00
tags:
  - Spring
  - MyBatis
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
---

# 1.Spring简介

### 1.1Spring是什么

Spring是分层的Java SE/EE应用full-stack轻量级开源框架，以==IoC==(Inverse Of Control:反转控制)和==AOP==(Aspect Oriented Programming：面向切面编程)为内核。

提供了==展现层SpringMVC==和==持久层Spring JDBCTemplate==以及==业务层事务管理==等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，逐渐成为使用最多的Java EE企业应用开源框架。

### 1.2Spring发展历程

1997年，IBM提出了EJB的思想

1998年，SUN制定开发标准EJB1.0

1999年，EJB1.1发布

2001年，EJB2.0发布

2003年，EJB2.1发布

2006年，EJB3.0发布

==Rod Johnson(Spring之父)==

Expert One-to-One J2EE Design and Development(2002)阐述了 J2EE使用EJB开发设计的有点及解决方案

Expert One-to-One J2EE Development without EJB(2004)阐述了J2EE开发不使用EJB的解决方式(Spring雏形)

==2017年9月发布了Spring的最新版本Spring5.0通用版(GA)==

### 1.3Spring的优势

+ 方便解耦，简化开发

  通过Spring提供的IoC容器，可以将对象间的依赖关系交由Spring进行控制，避免硬编码所造成的过度耦合，用户不必再为单例模式类，属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用。

+ AOP编程的支持

  通过Spring的AOP功能，方便进行面向切面的编程，许多不容易用传统OOP实现的功能可以通过AOP轻松实现。

+ 声明式事务的支持

  可以将我们从单调烦闷的事务管理代码中解脱出来，通过声明的方式灵活的进行事务管理，提高开发效率和质量。

+ 方便程序的测试

  可以用非容器依赖的编程方式进行几乎所有的测试工作，测试不再是昂贵的操作，而是随手可做的事情。

+ 方便集成各种优秀框架

  Spring对各种优秀框架(Struts,Hibemate,Hessian,Quqrtz等)的支持。

+ 降低JavaEE  API的使用难度

  Spring对JavaEE API(如JDBC，JavaMail，远程调用等)进行了薄薄的封装层，使这些API的使用难度大为降低。

+ Java 源码时经典学习范例

  Spring的源代码设计精妙，结构清晰，匠心独用，处处体现着大师对Java设计模式灵活运用继以对Java技术的高深造诣，他的源码无疑时Java技术的最佳实践范例。

### 1.4Spring的体系结构

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220106155452213.png" alt="image-20220106155452213" style="zoom:67%;" />

# 2.Spring快速入门

### 2.1Spring程序开发步骤

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220106160155319.png" alt="image-20220106160155319" style="zoom:67%;" />

1.导入Spring开发的基本包坐标

2.编写Dao接口和实现类

3.创建Spring核心配置文件

4.在Spring配置文件中配置UserDaoImpl

5.使用Spring的API获得Bean实例

# 3.Spring配置文件

### 3.1Bean标签基本配置

用于配置对象由Spring来创建

默认情况下它调用的是类中的无参构造函数，如果没有无参构造函数则不能创建成功。

基本属性：

+ id：Bean实例在Spring容器中的唯一标识
+ class：Bean的全限定名称

### 3.2Bean标签范围配置

scope：指对象的作用范围，取值如下：

| 取值范围       | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| singleton      | 默认的，单例的                                               |
| prototype      | 多例的                                                       |
| request        | WEB项目中，Spring创建一个Bean的对象，将对象存入到request域中 |
| session        | WEB项目中，Spring创建一个Bean的对象，将对象存入到session域中 |
| global session | WEB项目中，应用在Portlet环境，如果没有Portlet环境那么globalSession相当于session |

+ 当scope的取值为==singleton==时

  Bean的实例化个数：1个

  Bean的实例化时机：当Spring核心文件被加载时，实例化配置的Bean实例

  Bean的生命周期：

  + 对象创建：当应用加载，创建容器时，对象就被创建了
  + 对象运行：只要容器在，对象一直活着
  + 对象销毁：当应用卸载，销毁容器时，对象就被销毁了

+ 当scope的取值为==prototype==时

  Bean的实例化个数：多个

  Bean的实例化时机：当调用getBean()方法时实例化Bean

  Bean的生命周期：

  + 对象创建：当使用对象时，创建新的对象实例
  + 对象运行：只要在对象的使用中，就一直活着
  + 对象销毁：当对象长时间不用时，被Java的垃圾回收器回收了

### 3.3Bean的生命周期配置

+ init-method：指定类中的初始化方法名称
+ destory-method：指定类中销毁方法名称

### 3.4Bean实例化三种方式

+ 无参==构造==方法实例化
+ 工厂==静态==方法实例化
+ 工厂==实例==方法实例化

### 3.5Bean的依赖注入

因为UserService和UserDao都在Spring容器中，而最终程序直接使用的是UserService，所以可以在Spring容器中，将UserDao设置到UserSrvice内部。

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220106191709131.png" alt="image-20220106191709131" style="zoom:67%;" />

==依赖注入==(Dependency Injection)：他是Spring框架核心IOC的具体实现。

在编写程序时，通过控制反转，把对象的创建交给了Spring，但是代码中不可能出现没有依赖的情况。

IOC解耦只是降低他们的依赖关系，但不会消除。例如：业务层仍会调用持久层的方法。

这种业务层和持久层的依赖关系，在使用Spring之后，就让Spring来维护了。

简单说就是坐等框架把持久层对象传入业务层，而不用我们自己去获取。

### 3.6Bean的依赖注入方式

+ 构造方法注入(有参)

  1.在类中创建带参构造方法，此外还需要补一个无参的构造方法

  2.在配置文件中修改注入方式：

  ```xml
  <bean id="userDao" class="com.itheima.impl.UserDaoImpl"></bean>
  <bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
  	<constructor-arg name="userDao" ref="userDao"></constructor-arg>
  </bean>
  ```

  

+ set方法注入

  + P名命控件注入本质也是set方法注入，但比起上述set方法注入更加方便，主要体现在配置文件中，如下：

    首先需要引入P命名空间：

    ```xml
    xmlns:p="http://www.springframework.org/schema/p"
    ```

    其次，需要修改注入方式

    ```xml
    <bean id="userDao" class="com.itheima.impl.UserDaoImpl"></bean>
    <bean id="userService" class="com.itheima.service.impl.UserServiceImpl" p:userDao-ref="userDao"/>
    ```

  + 子标签方式注入

    ```xml
    <bean id="userDao" class="com.itheima.impl.UserDaoImpl"></bean>
    <bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
    	<property name="userDao" ref="userDao"></property>
    </bean>
    ```

### 3.7Bean的依赖注入的数据类型

之前的操作，都是注入的引用Bean，出了对象的引用可以注入，普通的数据类型，集合等都可以在容器中进行注入。

注入数据的三种数据类型：

+ 普通数据类型

  ```xml
  <bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
  	<property name="username" value="zhangsan"></property>
  </bean>
  ```

+ 引用数据类型

+ 集合数据类型

  ```java
  private List<String> strlist;
  private Map<String,User> userMap;
  private Properties properties;
  ```

  

  ```xml
  <bean> id="user1" class="com.itheima.domain.User"
  	<property name="name" value="tom"/>
  	<property name="addr" value="beijing"/>
  </bean>
  <bean> id="user2" class="com.itheima.domain.User"
  	<property name="name" value="lucy"/>
  	<property name="addr" value="tianjin"/>
  </bean>
  
  <bean id="userService" class="com.itheima.service.impl.UserServiceImpl">
  	<property name="strlist">
  		<list>
  			<value>aaa</value>
  			<value>bbb</value>
  			<value>ccc</value>
  		</list>
  	</property>
  	<property name="userMap">
  		<map>
  			<entry key="user1" value-ref="user1"></entry>
  			<entry key="user2" value-ref="user2"></entry>
  		</map>
  	</property>	
  	<property name="properties">
  		<props>
  			<prop key="p1">ppp1</prop>
  			<prop key="p2">ppp2</prop>
  			<prop key="p3">ppp3</prop>
  		</props>
  	</property>
  </bean>
  ```



### 3.8引入其他配置文件(分模块开发)

实际开发中，Spring的配置内容非常多，这就导致Spring配置很复杂且体积很大，所以，可以将部分配置拆解到其他配置文件中，而在Spring主配置文件通过import标签进行加载

```xml
<import resource="applicationContext-xxx.xml"/>
```

### 3.9知识要点

Spring的重点配置

```xml
<bean>标签
	id属性：容器中Bean实例的唯一标识，不允许重复
	class属性：要实例化的Bean的全限定名
	scope属性：Bean的作用范围，常用是Singleton(默认)和prototype
	<property>标签：属性注入
		name属性：属性名称
		value属性：注入的普通属性值
		ref属性：注入的对象引用值
		<list>标签
		<map>标签
		<properties>标签
	<constructor-arg>标签
<import>标签：导入其他的Spring的分文件
```

# 4.Spring相关API

### 4.1ApplicationContext的继承体系

==applicationContext==：接口类型，代表应用上下文，可以通过其实例获得Spring容器中的Bean对象。

### 4.2ApplicationContext的实现类

+ ClassPathXmlApplicationContext

  它是从类的根路径下加载配置文件，推荐使用这种

+ FileSystemXmlApplicationContext

  它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置

+ AnnotationConfigApplicationContext

  当使用注解配置容器对象时，需要使用此类来才能创建spring容器，它用来读取注解

### 4.3getBean()方法使用

```java
public Object getBean(String name) throws BeansException{
	assertBeanFactoryActive();
	return getBeanFactory().getBean(name);
}

public <T>T getBean(Class<T> requireType) throws BeansException{
	assertBeanFactoryActive();
	return getBeanFactory().getBean(requireType);
}
```

其中，当参数的数据类型是字符串时，标识根据Bean的id从容器中获得Bean实例，返回是Object，需要强转。当参数的数据类型是Class类型时，标识根据类型从容其中匹配Bean实例，当容器中相同类型Bean由多个时，则此方法会报错。

### 4.4知识要点

Spring的重点API

```java
ApplicationContext app = new ClasspathXmlApplicationContext("xml文件")
app.getBean("id")
app.getBean(Class)
```

# 5.Spring配置数据源

### 5.1数据源(连接池)的作用

+ 数据源(连接池)是提高程序性能出现的
+ 事先实例化数据源，初始化部分连接资源
+ 使用连接资源时从数据源中获取
+ 使用完毕后将连接资源归还给数据源

常见的数据源(连接池)：DBCP,C3P0,BoneCP,Druid等

### 5.2数据源的开发步骤

1.导入数据源的坐标和数据库驱动坐标

2.创建数据源对象

3.设置数据源的基本连接数据

4.使用数据源获取连接资源和归还连接资源

### 5.3知识要点

==Spring容器加载properties文件==

先添加context命名空间，将上面的<bean>的命名空间复制，将其中的bean全部改成context

```xml
<context:property-placeholder location="xx.properties"/>
<property name="" value="${key}"/>
```

# 6.Spring注解开发

## 6.1Spring原始注解

Spring是轻代码而重配置的框架，配置比较繁重，影响开发效率，所以注解开发是一种趋势，注解代替配置文件可以简化配置，提高开发效率。

Spring原始注解主要是替代<Bean>的配置

| 注解           | 说明                                            |
| -------------- | ----------------------------------------------- |
| @Component     | 实用类上用于实例化Bean                          |
| @Controller    | 使用在web层类上用于实例化Bean                   |
| @Service       | 使用在service层类上用于实例化Bean               |
| @Repository    | 使用在dao层类上用于实例化Bean                   |
| @Autowired     | 使用在字段上用于根据类型依赖注入                |
| @Qualifier     | 结合@Autowired一起使用 用于根据名称进行依赖注入 |
| @Resource      | 相当于@Autowired+@Qualifier，按照名称进行注入   |
| @Value         | 注入普通属性                                    |
| @Scope         | 标注Bean的作用范围                              |
| @PostConstruct | 使用在方法上标注该方法是Bean的初始化方法        |
| @PreDestory    | 使用在方法上标注该方法是Bean的销毁方法          |

注意：使用注解进行开发时，需要在applicationContext.xml中配置组件扫描，作用是指定哪个包及其子包下的Bean需要进行扫描以便识别使用注解配置的类，字段和方法。

```xml
<!--注解的组件扫描-->
<context:component-scan base-package="com.itheima"></context:component-scan>
```

### 6.2Spring新注解

使用上面的注解还不能全部替代xml配置文件，还需要使用注解替代的配置如下：

+ 非自定义的Bean的配置：<Bean>
+ 加载properties文件的配置：<context:property-placeholder>
+ 组件扫描配置：<context:component-scan>
+ 引入其他文件：<import>

| 注解            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| @Configuration  | 用于指定当前类是一个Spring配置类，当创建容器时会从该类上加载注解 |
| @ComponentScan  | 用于指定Spring在初始化容器时需要扫描的包。作用和在Spring的xml配置文件中的<context:component-scan base-package="com.itheima"/>一样 |
| @Bean           | 使用在方法上，标注该方法的返回值存储到Spring容器中           |
| @PropertySource | 用于加载.properties文件中的配置                              |
| @Import         | 用于导入其他配置类                                           |

# 7.Spring集成Junit

### 7.1原始Junit测试Spring的问题

在测试类中，每个测试方法都有以下两行代码：

```java
ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
IAccountSrvice as = ac.getBean("accountService",IAccountService.class);
```

这两行代码的作用是获取容器，如果不写会提示空指针异常，所以不能轻易删除掉。

### 7.2解决思路

+ 让SpringJunit负责创建Spring容易，但是需要将配置文件的名称告诉它
+ 将需要进行测试Bean直接测试类中进行注入

### 7.3Spring集成Junit步骤

1.导入Spring集成Junit的坐标

```xml
<dependency>
	<groupId>junit</groupId>
	<artifactId>junit</artifactId>
	<version>4.12</version>
	<scope>test</test>
</dependency>
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-context</artifactId>
	<version>5.0.5RELEASE</version>
</dependency>
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-test</artifactId>
	<version>5.0.5RELEASE</version>
</dependency>
```

2.使用@Runwith注解替换原来的运行期

3.使用@ContextConfiguration指定配置文件或配置类

4.使用@Autowired注入需要测试的对象

5.创建测试方法进行测试

```java
@Runwith(SpringJUnit4ClassRunner.class)
//@ContextConfiguration("classpath:applicationContext.xml")
@ContextConfiguration(classes = {SpringConfiguration.class})
public class SpringJunitTest{
	@Autowired
	private UserService userService;
	
	@Autowired
	private DataSource dataSource;
	
	@Test
	public void test1(){
		userService.save();
		System.out.println(dataSouce.getConnection());
	}
}
```

# 8.Spring与Web环境集成

servlet和jsp坐标

```xml
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>javax.servlet-api</artifactId>
	<version>3.0.1</version>
	<scope>provided</scope>
</dependency>
<dependency>
	<groupId>javax.servlet.jsp</groupId>
	<artifactId>javax.servlet.jsp-api</artifactId>
	<version>2.2.1</version>
	<scope>provided</scope>
</dependency>
```

### 8.1ApplicationContext应用上下文获取方式

应用上下文对象是通过==new ClasspathXmlApplicationContext(spring配置文件)==方式获取的，但是每次从容器中获得Bean时都要编写==new ClasspathXmlApplicationContext(spring配置文件)==,这样的弊端是配置文件加载多次，应用上下文对象创建多次。

在Web项目中，可以使用==servletContextListener==监听Web应用的启动，我们可以在Web应用启动时，就如加载Spring的配置文件，创建应用上下文对象==ApplicationContext==，在将其存储到最大的域==servletContext==域中，这样就可以在任意位置从域中获得应用上下文==ApplicationContext==对象了。

注意：

==**servlet和listener都要在web.xml中配置**==

```java
public class ContextLoaderListener implements ServletContextListener{
	public void contextInitialized(ServletContextEvent servletContextEvent){
		ServletContext servletContext = servletContextEvent.getServletContext();
		//读取web.xml中的全局参数
		String contextConfigLocation = servletContext.getInitParameter("contextConfigLocation");
		ApplicationContext app = new ClassPathXmlApplicationContext(contextConfiguration);
		//将Spring的应用上下文对象存储到ServletContext域中
		servletContext.setAttribute("app",app);
		Sysetm.out.println("Spring容器创建完毕");
	}
	public void contextDestory(ServletContextEvent servletContextEvent){

	}
}
```

### 8.2Spring提供获取应用上下文的工具

Spring提供了一个监听器ContextLoaderListener就是对上述功能的封装，该监听器内部加载Spring配置文件，创建应用上下文对象，并存储到ServletContext域中，提供了一个客户端工具WebApplicationContextUtils供使用者获得应用上下文对象。

所以我们需要做的只有两件事：

1.在web.xml中配置ContextLoaderListener监听器(导入sping-web坐标)

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-web</artifactId>
	<version>5.0.5RELEASE</version>
</dependency>
```

2.使用WebApplicationContextUtils获得应用上下文对象ApplicationContext

### 8.3通过工具获得应用上下文对象

```java
ApplicationContext app = WebApplicationContextUtils.getWebApplicationContext(servletContext);
Object obj = applicationContext.getBean("id");
```

### 8.4知识要点

1.配置ContextLoaderListener监听器(导包)

2.使用WebApplicationContextUtils获得应用上下文

# 9.SpringMVC简介

### 9.1SpringMVC概述

SpringMVC是一种基于Java的实现MVC设计模型的请求驱动类型的轻量级Web框架，属于SpringFrameWork的后续产品，已经融合在Spring Web Flow中。

SpringMVC已经成为目前最主流的MVC框架之一，并且随着Sping3.0的发布，全面超越Struts2，成为最优秀的MVC框架，它通过一套注解，让一个简单的Java类成为处理请求的控制器，而无需实现任何接口，同时，他还支持RESTgul编程风格的请求。

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220107210454426.png" alt="image-20220107210454426" style="zoom:67%;" />

1.导入SpringMVC包

2.配置Servlet

3.编写Controller

4.将Controller使用注解配置到Spring容器中(@Controller)

5.配置Spring-mvc.xml文件(配置组件扫描)

6.执行访问测试

### 9.2SpringMVC快速入门

需求：客户端发起请求，服务器端接受请求，执行逻辑并进行视图跳转。

开发步骤：

1.导入SpringMVC相关坐标

```xml
<dependency>
	<groupId>org.springframework</groupId>
	<artifactId>spring-webmvc</artifactId>
	<version>5.0.5.RELEASW</version>
</dependency>
```

2.配置SpringMVC核心控制器DispathcerServlet

在web.xml中配置springmvc的前端控制器

```xml
<servlet>
	<servlet-name>DispatcherServlet</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	<init-param>
		<param-name>contextConfigLoaction</param-name>
		<param.value>classpath:spring-mvc.xml</param-value>
	</init-param>
	<load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
	<servlet-name>DispatcherServlet</servlet-name>
	<url-pattern>/</url-pattern>
</servlet-mapping>
```

3.创建Controller类和视图页面

创建一个controller包，并在包中创建controller类，创建success.jsp页面

4.使用注解配置Controller类中业务方法的映射地址

```java
@Controller
public class UserController{
	@RequestMapping("/quick")
	public String sava(){
		System.out.println("Controller save running....");
		return "success.jsp";
	}
}
```

5.配置SpringMVC核心文件spring-mvc.xml

在resource文件夹中创建spring-mvc.xml

```xml
<!--补一个context命名空间-->
xmlns:context="http://www.springframework.org/schema/context"
xsi:schemaLocation="http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"


<!--Controller的组件扫描-->
<context:component-scan base-package="com.itheima.controller"/>
```

6.客户端发起请求测试

# 10.SpringMVC的组件解析

###  10.1SpringMVC的执行流程

1.用户发送请求到前端控制器DispatcherServlet

2.DispatcherServlet收到请求调用HandlerMapping处理器映射器

3.处理器映射器找到具体的处理器(可以根据xml配置，注解进行查找)，生成处理器对象以及处理拦截器(如果有则生成)一并返回给DispatcherServlet

4.DispatcherServlet调用HandlerAdapter处理器适配器

5.HandlerAdapter经过适配调用具体的处理器(Controller,也叫后端控制器)

6.Controller执行完成返回ModelAndView

7.HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServler

8.DispatcherServlet将ModelAndView传给ViewReslover视图解析器

9.ViewReslover解析后返回具体View

10.DispatcherServlet根据View进行渲染视图(即将模型数据填充至视图中)。DispatcherServlet响应客户。

### 10.2SpringMVC注解解析

**@RequestMapping**

作用：用于建立请求URL和处理请求方法之间的对应关系。

位置：

​	类上：请求URL的第一级访问目录，此处不写的话，就相当于英语的根目录

​	方法上：请求URL的第二级访问目录，与类上的使用@RequestMapping标注的一级目录一起组成访问虚拟路径属性。

​	value：用于指定请求的URL。他和path属性的作用是一样的。

​	method：用于指定请求的方式。

​	params：用于指定限制请求参数的条件。它支持简单的表达式。要求请求参数的key和value必须和配置的一摸一样，例如：

​	params = {"accountName"}，表示请求参数必须有accountName

​	params = {"money!100"}，表示请求参数中的money不能是100

==**SpringMVC注解组件扫描**==

1.mvc命名空间引入

```xml
命名空间：xmlns:context="http://www.springframework/org/schema/context"
		xmlns:mvc="http://www.springframework.org/schema/mvc"
约束地址：http://www.springframework.org/schema/context
		http://www.springframework.org/schema/contextspring-context.xsd
		http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc.xsd
```

2.组件扫描

SpringMVC基于Spring容器，所以在进行SpringMVC操作时，需要将Controller存储到Spring容器中，如果使用@Controller注解标注的话，就需要使用<context:component-scan base-package="com.itheima.controller"/>进行扫描。

### 10.3SpringMVC的XML配置解析

1.视图解析器

SpringMVC有默认组件配置，默认组件都是DispatcherServlet.properties配置文件中配置的，该配置文件地址org/springframework/web/servlet/DispathcherServlet.properties，该文件中配置了默认的视图解析器，如下：

```
org.springframework.web.servlet.ViewResolver=org.springframework.web.servlet.view.InternalResourceViewResolver
```

翻看该解析器源码，可以看到该解析器的默认设置，如下

```
REDIRECT_URL_PREFIX = "redirect:"  --重定向前缀
FORWARD_URL_PREFIX = "forward:"   --转发前缀(默认值)
prefix = "";    --视图名称前缀
suffix = "";    --视图名称后缀
```

### 10.4知识要点

**SpringMVC的相关组件**

+ 前端控制器：DispatcherServlet
+ 处理器映射器：HandlerMapping
+ 处理器适配器：HandlerAdapter
+ 处理器：Handler
+ 视图解析器：View Resolver
+ 视图：View

**SpringMVC的注解和配置**

+ 请求映射注解：@RequestMapping

+ 视图解析器配置：

  REDIRECT_URL_PREFIX = "redirect:"  --重定向前缀
  FORWARD_URL_PREFIX = "forward:"   --转发前缀(默认值)
  prefix = "";    --视图名称前缀
  suffix = "";    --视图名称后缀

# 11.SpringMVC的数据响应

### 11.1SpringMVC的数据相响应方式

1.页面跳转

+ 直接返回字符串
+ 通过ModelAndView对象返回

2.回写数据

+ 直接返回字符串
+ 返回对象或集合

### 11.2页面跳转

**1.返回字符串形式**

直接返回字符串：此方式会将返回的字符串与视图解析器的前后缀拼接后跳转。

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220109144208586.png" alt="image-20220109144208586" style="zoom:67%;" />

返回带有前缀的字符串：

转发：forward:/WEB-INF/views/index.jsp

重定向：redirect:/index.jsp

**2.通过ModelAndView对象返回**

```java
@RequestMapping("quick11")
public ModelAndView save2(){
	/*
	Model：模型，作用封装数据
	View：视图，作用展示数据
	*/
	ModelAndView modelAndview = new ModelAndView();
	//设置模型数据
	modelAndView.addObject("username","zhangsan");//  ("user",usre)
	//设置视图名称
	modelAndView.setViewName("index");
	return modelAndView;
}
```

也可以让SpringMVC框架帮我创建ModeAndView对象，只需要在形参位置写上此对象

```java
@RequestMapping("quick11")
public ModelAndView save2(ModelAndView modelAndView){
	modelAndView.addObject("username","zhangsan");//  ("user",usre)
	modelAndView.setViewName("index");
	return modelAndView;
}
```

```java
@RequestMapping("quick11")
public ModelAndView save2(Model model){
	model.addAttribute("username","itcast");
	return "success";
}
```

还可以用Request域的方式进行返回

```java
@RequestMapping("quick11")
public ModelAndView save2(HttpServletRequest request){
	request.addAttribute("username","itcast");
	return "success";
}
```

###  11.3回写数据

**1.直接返回字符串**

+ Web基础阶段，客户端访问服务器端，如果想直接回写字符串作为响应体返回的话，只需要使用response.getWriter().print("hello world")即可。==在Controller中方法如下：==

  通过SpringMVC框架注入的response对象，使用response.getWriter().print("hello world")回写数据，此时，不需要视图跳转，业务方法返回值为void。

```java
@RequestMapping("/quick4")
public void quickMethod4(HttpServletResponse response) throws IOException{
	response.getWriter().print("hello world");
}
```

+ 将需要回写的字符串直接返回，但此时需要通过@ResponseBody注解告知SpringMVC框架，方法返回的字符串不是跳转是直接在http响应体中返回。

```java
@RequestMapping("/quick4")
@ResponseBody   //告知SpringMVC框架，不进行视图跳转，直接进行数据响应
public String quickMethod4(){
	return "hello itheima";
}
```

### ==**返回对象**====**（使用json的转换工具将对象转换成json格式字符串再返回）**==

​	**(1.在pom.xml中导入json依赖坐标(三个)**

```xml
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-core</artifactId>
	<version>2.9.0</version>
</dependency>
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.9.0</version>
</dependency>
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-annotations</artifactId>
	<version>2.9.0</version>
</dependency>
```

​	**(2.在方法中进行对象到json转换以及以字符串形式回写**

```java
@RequestMapping("/quick9")
@Responsebody
public String save() {
	User user = new User();//一般此处user应该是数据层返回的
	user.setUsername("lisi");
	user.setAge(19);
	//使用json的转换工具将对象转换成json格式字符串再返回
	ObjectMapper objectmapper = new ObjectMapper();
	String json = objectmapper.writeValueAsString(user);
	
	return json;
}
```

**2.返回对象或集合**

通过SpringMVC帮助我们对对象或集合进行json字符串的转换并回写，为处理器适配器配置消息转换参数，指定使用jackson进行对象或集合的转换，因此需要再spring-mvc.xml中进行配置。

```xml
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
	<property name="messageConverters">
		<list>
			<bean class="org.springframework.http.converter.json.MappingJackson2HttpMassageConverter">
			</bean>
		</list>
	</property>
</bean>
```

方法中：

```java
@RequstMapping("/quick10")
@ResponseBody
//期望SpringMVC自动将User转换成json格式的字符串
public User save10(){
	User user = new User();
	user.setUsername("lisi1");
	user.setAge(33);
	
	return user;
}
```

 在方法上添加==@ResponseBody==就可以返回json格式的字符串，但是这样配置比较麻烦，配置的代码比较多，因此，我们可以使用mvc的注解驱动代替上述配置。

```
<!--mvc的注解驱动-->
<mvc:annotation-driven/>
```

其中用到了mvc命名空间，要先导入mvc命名空间。

```
命名空间：xmlns:mvc="http://www.springframework.org/schema/mvc"
约束地址：http://www.springframework.org/schema/mvc
		http://www.springframework.org/schema/mvc/spring-mvc.xsd
```



在SpringMVC的各个组件中，==处理器映射器，处理器适配器，视图解析器==称为SpringMVC的三大组件。

使用<<mvc:annotation-driven>>自动加载RequestMappingHandlerMapping(处理映射器)和RequestMappingHandlerAdapter(处理适配器),可用在Spring-mvc.xml配置文件中使用<<mvc:annotation-driven>>代替注解处理器和适配器的配置。

同时使用<<mvc:annotation-driven>>默认底层就会集成jackson进行对象或集合json格式字符串的转换。



# 12.SpringMVC获得请求数据

### 12.1获得请求参数

客户端请求参数的格式：name=value&name=value...

服务端想要获得请求的参数，有时还需要进行数据的封装，SpringMVC可以接收如下类型的参数：

+ 基本类型参数
+ POJO类型参数
+ 数组类型参数
+ 集合类型参数

### 12.2获得基本类型参数

Controller中业务方法的参数名称要与请求参数的name一致，参数值会自动映射匹配。

例如：

```java
http://localhost:8080/itheima_springmvc/quick?username=zhangsan&age=12

@RequestMapping("/quick")
@ResponeBody
public void quickMethod(String username,int age)throws IOException{
	System.out.println(username);
	System.out.println(age);
}
```

### 12.3获得POJO类型参数

Controller中的业务方法的POJO参数的属性名与请求参数的name一致，参数会自动映射匹配。

```
http://localhost:8080/itheima_springmvc/quick?username=zhangsan&age=12
```

```java
public class User{
	private String username;
	private int age;
	getter/setter...
}

@RequestMapping("/quick")
@ResponseBody
public void quickMethod(User user)throws IOException{
	System.out.println(user);
}
```

### 12.4获得数组类型参数

Controller中业务方法数组名称与请求参数的name一致，参数值会自动映射匹配

```
http://localhost:8080/itheima_springmvc/quick11?strs=111&strs=222&strs=333
```

```java
@RequestMapping("/quick")
@ResponseBody
public void quickMethod(String[] strs)throws IOException{
	System.out.println(Arrays.asList(strs));
}
```

数组转换为集合   ==Arrays.asList(strs)==

### 12.5获得集合类型参数

获得集合参数时，要将集合参数包装到一个POJO中才可以。

当使用ajax提交时，可以指定contentType为json形式，那么在方法参数位置使用==@RequestBody==可以直接接收集合数据而无需使用POJO进行包装。

导入Jquery

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220109205723771.png" alt="image-20220109205723771" style="zoom:100%;" />

服务端：

```java
@RequestMapping("/quick15")
@ResponseBody
public void save15(@RequestBody List<User> userList) throws IOException{
	System.out.println(userList);
}
```

==**注意：**==

要在spring-mvc.xml中加一条配置，否则jquery可能不能正常加载

```
<!--开放资源的访问，一般是静态资源-->
<mvc:resources mapping="/js/**" location="/js/"/>
```

mapping：访问服务端找资源时的地址

location：具体资源所在的目录



**或者配置以下这句话，代表访问资源时，SpringMVC找匹配地址，如果找不到交由原始容器，即tomcat，tomcat可以找到静态资源。**

```
<mvc:default-servlet-handler/>
```

### 12.6请求数据乱码问题

当post请求时，数据会出现乱码，我们可以设置一个过滤器来进行编码的过滤。

```xml
<!--配置全局过滤的filter-->
<filter>
	<filter-name>CharacterEncodingFilter</filter-name>
	<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	<init-prarm>
		<param-name>encoding</param-name>
		<param-value>UTF-8</param-value>
	<init-param>
</filter>
<filter-mapping>
	<filter-name>CharacterEncodingFilter</filter-name>
	<url-pattern>/*<url-pattern>
</filter-mapping>
```

### 12.7参数绑定注解@RequestParam

当请求的参数名称与Controller的业务方法参数名称不一致时，就需要通过@RequestParam注解显示绑定。

```html
<from action="${pageCOntext.request.contextPath}/quick14" method="post">
	<input type="text" name="name">
	<input type="submit" value="提交">
</form>
```

```java
@RequestMapping("/quikc14")
@ResponseBody
public void quikcMethod14(@RequestParam("name") String username) throws IOException{
	System.out.println(username);
}
```

注解@RequestParam还有如下的参数可以使用

+ value：与请求参数名称绑定
+ required：指定的请求参数是否必须被包括，默认是true，提交时如果没有此参数会报错
+ defaultValue：当没有指定请求参数时，则使用指定的默认值赋值

```java
@RequestMapping("/quick14")
@ResponseBody
public void quickMethod14(@RequestParam(value="name",required=false,defaultValue="itcast") String username) throws IOException{
	System.out.println(username);
}
```

###  12.8获得Restful风格的参数

==Restful==是一种软件==架构风格，设计风格==，而不是标准，只是提供了一组设计原则和约束条件，主要用于客户端和服务器交互类的软件，基于这个风格设计的软件可以更简介，更有层次，更易于实现缓存机制。

==Restful==风格的请求是使用=="url+请求方式"==表示一次请求目的的，HTTP协议里面四个表示操作方式的动词如下：

+ GET：用于获取资源
+ POST：用于新建资源
+ PUT：用于更新资源
+ DELETE：用于删除资源

例如：

+ /user/1	GET：得到id=1 的user
+ /user/1	DELETE：删除id=1 的user
+ /user/1	PUT：更新id=1 的user
+ /user	POST：新增user

 上述url地址/user/1中的1就是要获得的请求参数，在SpringMVC中可以使用占位符进行参数绑定，地址/user/1可以写成/user/{id},占位符{id}对应的就是1的值，在业务方法中我们可以使用==@PathVariable==注解进行占位符的匹配获取工作。

```
http://localhost:8080/itheima_springmvc/quick19/zhangsan
```

```java
@RequestMapping("/quick19/{name}")
@ResponseBody
public void quickMethod19(@PathVariable(value="name" require=true) String name1){
	Sysetm.out.println(name1);
}
```

### 12.9自定义类型转换器

+ SpringMVC默认已经提供了一些常用的类型转换器，例如客户端提交的字符串转换成int进行参数设置。
+ 但是不是所有的数据类型都提供了转换器，没有提供的就需要自定义转化器，例如，日期类型的数据就需要自定义转化器。

自定义类型转化器的开发步骤：

1.定义转换器实现Converter接口

```java
public class DateConverter implements Converter<String,Date>{
	public Date convert(String dateStr){
		//将日期的字符串转换成真正的日期对象，返回
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		Date date = sdf.parse(dateStr);
		return date;
	}
}
```

2.在配置文件中声明转换器

```xml
<bean id="conversionService" class = "org.springframework.context.support.ConversionServiceFactoryBean">
	<property name="converters">
		<list>
			<bean class="com.itheima.converter.DateConverter"></bean>
		</list>
	</property>
</bean>
```

3.在<annotation-driven>中引用转换器

```
<mvc:annotation-driven conversion-service="conversionService"/>
```

### 12.10获得Servlet相关API

SpringMVC支持使用原始ServletAPI对象作为控制器方法的参数进行注入，常用的对象如下：

+ HttpServletRequest
+ HttpServletResponse
+ HttpSession

```java
@RequestMapping("/quick16")
@ResponseBody
public void quickmethod(HttpServletRequest request,HttpServletResponse response,HttpSession session){
	System.out.println(request);
	System.out.println(response);
	System.out.println(session);
}
```

### 12.11获得请求头

使用@RequestHeader可以获得请求头的信息，相当于web阶段学习的request.getHeader(name)

**1.@RequestHeader**

@RequestHeader注解属性如下

+ value：请求头的名称
+ required：是否必须携带此请求头

```java
@RequestMapping("/quick17")
@ResponseBody
public void quickMethod(@RequestHeader(value="User-Agent",required=false)String headerValue){
	System.out.println(headerValue);
}
```

**2.@CookieValue**

使用@CookieValue可以获得指定Cookie的值

@CookieValue注解的属性如下：

+ value：指定cookie的名称
+ required：是否必须携带此cookie

```java
@RequestMapping("/quick18")
@ResponseBody
public void quickMethod(@CookieValue(value="JSESSIONID",required=false))String jsessionId){
	System.out.println(jsessionId);
}
```

### 12.12文件上传

**1.文件上传客户端的三要素**

+ 表单项type="file"
+ 表单的提交方式为post
+ 表单的enctype属性是多部分表单形式，及enctype="multipart/form-data"

```html
<form action="${pageContext.request.contextPath}/quick20" method="post" enctype="mutipart/form-data">
	名称：<input type="text" name="name"><br>
	文件：<input type="file" name="file"><br>
	<input type="submit" value="提交"><br>
</form>
```

**2.文件上传原理**

+ 当form表单修改为多部分表单时，request.getParameter()将失效。
+ enctype="application/x-www-form-urlencoded"时，form表单的正文内容格式是：key=value&key=value
+ 当form表单的enctype取值为Mutilpart/form-data时，请求正文内容就变成多部分形式：

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220110163138358.png" alt="image-20220110163138358" style="zoom:100%;" />

### 12.13单文件上传步骤

1.导入fileupload和io坐标

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220110163355625.png" alt="image-20220110163355625" style="zoom:100%;" />

2.配置文件上传解析器

```xml
<bean id = "multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<!--上传文件总大小-->
	<property name="maxUploadSize" value="5242800"/>
	<!--上传单个文件的大小-->
	<property name="maxUploadSizePerFile" value="5242800"/>
	<!--上传文件的编码类型-->
	<property name="defaultEncoding" value="UTF-8"/>
</bean>
```

3.编写文件上传代码

```java
//注意，此处形参中的MultipartFile file 名称要与表单中file那项名称一致
@RequestMapping("/quick22")
@ResponseBody
public void save22(Stirng name,MultipartFile file) throws IOException{
	System.out.println(name);
	//获得上传文件的名称
	String originalFilename = uploadFile.getOriginalFilename();
	//上传文件
	uploadFile.transferTo(new File("C:\\upload\\"+originalFilename));
}
```

### 12.14多文件上传实现

多文件上传，只需要将页面修改为多个文件上传项，将方法参数MultipartFile类型修改为MutipartFile[]即可

```html
<h1>多文件上传</h1>
<form action="${pageContext.request.contextPath}/quick22" method="post" enctype="multipart/form-data">
	名称：<input type="text" name="name"><br>
	文件1：<input type="file" name="uploadfiles"><br>
	文件2：<input type="file" name="uploadfiles"><br>
	文件3：<input type="file" name="uploadfiles"><br>
	<input type="submit" value="提交"><br>
</form>
```

服务端：

```java
@RequestMapping("/quick22")
@ResponseBody
public void save22(Stirng name,MultipartFile[] uploadfiles) throws IOException{
	for(Mutipartfile uploadfile:uploadfiles)
	//获得上传文件的名称
	String originalFilename = uploadFile.getOriginalFilename();
	//上传文件
	uploadFile.transferTo(new File("C:\\upload\\"+originalFilename));
}
```

# 13.Spring JdbcTemplate基本使用

### 13.1JdbcTemplate概述

它是Spring框架中提供的一个对象，是对原始繁琐的Jdbc API对象的简单封装，spring框架为我们提供了很多的操作模板类。例如：操作关系型数据的Jdbc Template和Hibername Template，操作mosql数据库的Redis Template，操作消息队列的JmsTemplate等等。

### ==13.2JdbcTemplate开发步骤==

1.导入spring-jdbc和spring-tx坐标

2.创建数据库表和实体

3.创建JdbcTemplate对象

4.执行数据库操作

```java
public void test(){
	//创建数据源对象
	ComoPlloedDataSource dataSource = new ComboPooledDataSource();
	dataSource.setDrivreClass("com.mysql.jdbc.Driver");
	dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
	dataSource.setUSer("root");
	dataSource.setPassword("root");
	
	JdbcTemplate jdbcTemplate = new JdbcTemplate();
	//设置数据源对象，知道数据库在哪里
	jdbcTemplate.setDataSource(dataSource);
	//执行操作
	int row = jdbcTemplate.update("insert into account values(?,?)","tom",5000);
	System.out.println(row);
}
```

执行操作：

更新操作：jdbcTemplate.update(sql,params)

查询操作：jdbcTemplate.query(sql,Mapper,params)  返回list集合

​					jdbcTemplate.queryForObject(sql,Mapper,patams)   返回单个对象

​					jdbcTemplate.queryForObject(sql,Integer.class)  返回基本数据类型

### 13.3Spring产生JdbcTemplate对象

可以将JdbcTemplate的创建权交给Spring，将数据源DataSource的创建权也交给Spring，在Spring容器内部将数据源DataSource注入到JdbcTemplate模板对象中，如下：

```xml
<!--数据源DataSource-->
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
	<property name="jdbcUrl" value="jdbc:mysql:///test"></property>
	<property name="user" value="root"></property>
	<property name="password" value="root"></property>
</bean>
<!--JdbcTemplate-->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
	<property name="dataSource" ref="dataSource"></property>
</bean>
```

# 14.Spring练习环境搭建

### 14.1Spring环境搭建步骤

1.创建工程(Project&Module)

2.导入静态页面

3.导入需要的坐标

4.创建包结构(controller,service,dao,domain,utils)

5.导入数据库脚本

6.创建POJO类(实体类)(User.java,Role.java)

7.创建配置文件(applicationContext.xml,spring-mvc.xml,jdbc.properties,log4j.properties)以及配置web.xml

# 15.SpringMVC拦截器

### 15.1拦截器(interceptor)的作用

SpringMVC的==拦截器==类似于Servlet开发中的过滤器Filter，用于对处理器进行==预处理和后处理==。

将拦截器按一定的顺序连接成一条链，这条链称为拦截器链(Interceptor Chain)。在访问被拦截的方法或字段时，拦截器链中的拦截器就会按其之前定义的顺序被调用。拦截器也是AOP思想的具体实现。

==对访问目标资源时做相应的干预==

### 15.2拦截器和过滤器区别

| 区别     | 过滤器(Filter)                                          | 拦截器(Interceptor)                                          |
| -------- | ------------------------------------------------------- | ------------------------------------------------------------ |
| 使用范围 | 是servlet规范中的一部分，任何Java Web工程都可以使用     | 是SpringMVC框架自己的，只有使用了SpringMVC框架的工程才能用   |
| 拦截范围 | 在url-pattern中配置了/*之后，可以对所有要访问的资源拦截 | 在<mvc:mapping path=" "/>中配置了/**之后，也可以对所有资源进行拦截，但是可以通过<mvc:exclude-mapping path=" " />标签排除不需要拦截的资源 |

### 15.3SpringMVC拦截器的快速入门

自定义拦截器只需要三步：

1.创建拦截器类实现HandlerInterceptor接口，覆盖三个方法(preHandle,postHandle,afterCompletion)

​	preHandle在目标方法执行之前执行

​	postHandle在目标方法执行之后 视图对象返回之前执行

​	afterCompletion在流程都执行完毕后执行

```java
public class MyInterceptor1 implements HandlerInterceptor{
	//在目标方法执行之前执行
	public boolean preHandle(HttpServletRequest request,HttpServletResponse response,Object handler){
		return true;//返回true代表放行，返回false代表不放行
	}
	//在目标方法执行之后，视图对象返回之前执行
	public void postHandle(HttpServletRequest request,HttpServletResponse response,ModelAndView modeAndView){
		//一般对modelAndView进行相应的修改操作
	}
	public void afterCompletion(HttpServletRequest request,HttpServletResponse response,Object handler,Exception ex){
	
	}
}
```

2.配置拦截器

```xml
<!--配置拦截器-->
<mvc:interceptors>
	<mvc:interceptor>
		<!--对那些资源执行拦截操作-->
		<mvc:mapping path="/**"/>
		<bean class="com.itheima.interceptor.MyInterceptor1"/>
	</mvc:interceptor>
</mvc:interceptors>
```

3.测试拦截器的拦截效果

多个拦截器的执行顺序和过滤器一样

```
preHandle11111...
preHandle22222...
目标资源执行...
postHandle22222...
postHandle11111...
afterCompletion22222...
afterCompletion11111...
```

==注意：==先1后2的原因是因为配置的先后顺序

### 15.4拦截器方法说明

| 方法名            | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| preHandle()       | 方法将在请求处理之前进行调用，该方法的返回值是布尔类型的，当返回false时，表示请求结束，后续的Interceptor和Controller都不会再执行；当返回值为true时就会继续调用下一个Interceptor的preHandle方法 |
| postHandle()      | 该方法实在当前请求进行处理之后被调用，前提是preHandle方法的返回值为true时才能被调用，且他会在DispatcherServlet进行视图返回渲染之前被调用，所有我们可以再这个方法中对Controller处理之后的ModelAndView对象进行操作 |
| afterCompletion() | 该方法将在整个请求结束后，也就是再DispatcherServlet渲染了对应的视图之后执行，前提是preHandle方法的返回值为true时才能被调用 |

# 16.SpringMVC异常处理

### 16.1异常处理的思路

系统中的异常包括两类：预期异常和运行时异常RuntimeException，前者通过捕获异常从而获取异常信息，后者主要通过规范diamagnetic开发，测试等手段减少运行时异常的发生

系统的Dao，Service，Controller出现都通过throws Exception向上抛出，最后由SpringMVC前端控制器交由异常处理器进行异常处理：如下图：

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220112140521164.png" alt="image-20220112140521164" style="zoom:100%;" />

### 16.2异常处理两种方式 

+ 使用SpringMVC提供的简单异常处理器SimpleMappingExceptionResolver
+ 实现Spring的异常处理接口HandlerExceptionResolver自定义自己的异常处理器

### 16.3简单异常处理器SimpleMappingExceptionResolver

SpringMVC已经定义好了该类型转换器，在使用时可以根据项目情况进行相应异常与视图的映射配置

```xml
<!--配置简单映射异常处理器-->
<bean class="org.springframework.web.servlet.handler,SimpleMappingExceptionResolver">
	<property name="defaultErrorView" value="error"/>   //默认错误视图
	<property name="exceptionMappings">
		<map>
			<entry key="com.itheima.exception.MyException" value="error"/>
			<entry key="java.lang.ClassCastException" value="error"/>
		</map>
	</property>
</bean> 
```

### 16.4自定义异常处理步骤

1.创建异常处理器类实现HandlerExceptionResolver

2.配置异常处理器

3.编写异常页面

4.测试异常处理

==判断异常是否属于某种异常的方法==

instanceof

Exception e

if(e instanceof ClassCastException)

# 17.Spring 的AOP简介

### 171.AOP

AOP为Aspect Orinented Programming的缩写，意思为面向切面编程，是通过预编译方法和运行期动态代理实现程序功能的唯一维护的一种技术

AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各3部分之间得耦合度降低，提高程序得可重用性，同时提高了开发的效率。

### 17.2AOP的作用及其优势

+ 作用：在程序运行期间，在不修改源码的情况下对方法进行功能增强
+ 优势：减少重复代码，提高开发效率，并且便于维护

### 17.3AOP的底层实现

实际上，AOP的底层是通过Spring提供的动态代理技术实现的。在运行期间，Spring通过动态代理技术动态的生成代理对象，代理对象方法执行时进行增强功能的介入，在去调用目标对象的方法，从而完成功能的增强。

### 17.4AOP的动态代理技术

**常见的动态代理技术**

+ JDK代理：基于接口的动态代理技术
+ cglib代理：基于父类的动态代理技术

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220112155509711.png" alt="image-20220112155509711" style="zoom:67%;" />![image-20220112155532865](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220112155532865.png)

![image-20220112155532865](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220112155532865.png)

### ==17.5JDK的动态代理(要懂)==

### 17.6cglib的动态代理

1.创建增强器

2.设置父类(目标)

3.设置回调

4.创建代理对象

### 17.7AOP相关概念

Spring的AOP实现底层就是对上面的动态代理的代码进行了封装，封装之后我们只需要对需要关注的部分进行代码编写，并通过配置的方式完成指定目标的方法增强。

在正式讲解AOP的操作之前，必须了解AOP的相关术语：

+ Target(目标对象)：代理的目标对象
+ Proxy(代理)：一个类被AOP织入增强后，就产生了一个结果代理类
+ Joinpoint(连接点)：指被拦截到的点，在spring中，这些点指的是方法，因为spring只支持方法类型的连接点。==即被增强的方法==
+ Pointcut(切入点)：指我们要对那些Joinpoint进行拦截的定义。==真正被增强的方法==
+ Advice(通知/增强)：指拦截到Joinpoint之后所要做的事情就是通知
+ Aspect(切面)：指切入点和通知的结合。==目标方法+增强==
+ Weaving(织入)：指把增强应用到目标对象来创建新的代理对象的过程。spring采用动态代理织入，而Aspect采用编译期织入和类装载期织入

### 17.8AOP开发明确的事项

**1.需要编写的内容**

+ 编写核心业务代码(目标类的目标方法)    谁是切点
+ 编写切面类，切面类中有通知(增强功能方法)    谁是通知
+ 在配置文件中，配置织入关系，即将哪些通知与哪些连接点进行结合

**2.AOP技术实现的内容**

Spring框架监控切入点方法的执行，一旦监控到切入点方法被运行，使用代理机制，动态创建目标对象的的代理对象，根据通知类别，在代理对象的相应位置，将通知对应的功能织入，完成完整的代码逻辑运行。

**3.AOP底层使用哪种代理方式**

在spring中，框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式。

# 18.基于XML的AOP开发

### 18.1快速入门

1.导入AOP相关坐标

2.创建目标接口和目标类(内部有切点)

3.创建切面类(内部有增强方法)

4.将目标类和切面类的对象创建权交给Spring

5.在applicationContext.xml中配置织入关系

6.测试代码

### 18.2XML配置AOP详解

**1.切点表达式的写法**

表达式语法：

```
execution([修饰符] 返回值类型 报名.类名.方法名(参数))
```

+ 访问修饰符可以省略
+ 返回值类型，包名，类名，方法名可以使用星号*代表任意
+ 包名与类名之间一个点.代表当前包下的类，两个点..代表当前包及其子包下的类
+ 参数列表可以使用两个点..代表任意个数，任意类型的参数列表

**2.通知的类型**

通知的配置语法：

```
<aop:通知类型 method="切面类中方法名" pointcut="切点表达式"></aop:通知类型>
```

| 名称         | 标签                   | 说明                                                     |
| ------------ | ---------------------- | -------------------------------------------------------- |
| 前置通知     | \<aop:before>          | 用于配置前置通知，指定增强的方法在切入点方法之前执行     |
| 后置通知     | \<aop:after-returning> | 用于配置后置通知，指定增强的方法在切入点方法之后执行     |
| 环绕通知     | \<aop:around>          | 用于配置环绕通知，指定增强的方法在切入点之前和之后都执行 |
| 异常抛出通知 | \<aop:throwing>        | 用于配置异常抛出通知，指定增强的方法在出现异常时执行     |
| 最终通知     | \<aop:after>           | 用于配置最终通知，无论增强方式执行是否有异常都会执行     |

**3.切点表达式的抽取**

当多个增强的切点表达式相同时，可以将切点表达式进行抽取，在增强中使用pointcut-ref属性代替pointcut属性来引用抽取后的切点表达式。

```
<aop:config>
	<!--引用MyAspect的Bean为切面对象-->
	<aop:aspect ref="myAspect">
		<aop:pointcut id="myPointcut" expression="execution(* com.itheima.aop.*.*(..))"/>
		<aop:before method="before" pointcut-ref="mtPointcut"></aop:before>
	</aop:aspect>
</aop:config>
```

# 19.基于注解的AOP开发

### 19.1快速入门

基于注解的aop开发步骤：

1.创建目标接口和目标类(内部有切点)

2.创建切面类(内部有增强方法)

3.将目标类和切面类的对象创建权交给spring

4.在切面类中使用注解配置织入关系    

```
标注MyAspect是一个切面类    @Aspect
配置前置通知    @Before("execution(* com.itheima.anno.*.*(..))")
```

5.在配置文件中开启组件扫描和AOP自动代理

```
<!--组件扫描-->
<context:component-scan base-package="com.itheima.anno"/>
<!--aop自动代理-->
<aop:aspect-autoproxy/>
```

6.测试

### 19.2注解配置AOP详解

**1.注解通知的类型**

通知的配置语法：@通知注解("切点表达式")

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220112223630134.png" alt="image-20220112223630134" style="zoom:67%;" />

**2.切点表达式的抽取**

同xml配置aop一样，我们可以将切点表达式抽取，抽取方式是在切面内定义方法，在该方法上使用==@Pointcut==注解定义切点表达式，然后再在增强注解中进行引用，具体如下：

```
@Component("myAspect")
@Aspect
public class MyAspect{
	@Before("MyAspect.myPoint()")
	public void before(){
		System.out.println("前置代码增强....");
	}
	@Pointcut("execution(* com.itheima.aop.*.*(..))")
	public void myPoint(){} 
}
```

# 20.编程式事务控制相关对象

### 20.1PlatformTransactionManager(控制行为)

**平台事务管理器**

PlatformTransactionManager==接口==是spring的事务管理器，它里面提供了我们常用的操作事务的方法。

| 方法                                                         | 说明               |
| ------------------------------------------------------------ | ------------------ |
| TransactionStatus getTransaction(TransactionDefination defination) | 获取事务的状态信息 |
| void commit(TransactionStatus status)                        | 提交事务           |
| void rollback(TransactionStatus status)                      | 回滚事务           |

==注意：PlatformTransactionManager是接口类型，不同的Dao层技术则有不用的实现类。==

### 20.2TransactionDefinition(维护事务的属性信息，参数)

**事务的定义对象**

TransactionDefinition是事务的定义信息对象，里面有如下方法：

| 方法                         | 说明               |
| ---------------------------- | ------------------ |
| int getIsolationLevel()      | 获得事务的隔离级别 |
| int getPropogationBehavior() | 获得事务的传播行为 |
| int getTimeout()             | 获得超时时间       |
| boolean isReadOnly()         | 是否只读           |

**1.事务隔离级别**

设置隔离级别，可以解决事务并发产生的问题，如脏读，不可重复读和虚读。

+ ISOLATION_DEFAULT
+ ISOLATION_READ_UNCOMMITTED
+ ISOLATION_READ_COMMITTED
+ ISOLATION_REPEATABLE_READ
+ ISOLATION_SERIALIZABLE

**2.事务的传播行为**

REQUIRED：如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。一般的选择（默认值)

SUPPORTS：支持当前事务，如果当前没有事务，就以非事务方式执行(没有事务)

MANDATORY：使用当前的事务，如果当前没有事务，就抛出异常

REQUERS_NEW：新建事务，如果当前在事务中，把当前事务挂起。

NOT_SUPPORTED：以非事务方式执行操作，如果当前存在事务，就把当前事务挂起

NEVER：以非事务方式运行，如果当前存在事务，抛出异常

NESTED：如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行REQUIRED类似的操作

超时时间：默认值是-1，没有超时限制。如果有，以秒为单位进行设置

是否只读：建议查询时设置为只读

### 20.3TransactionStatus(事务的运行过程中封装一些状态信息)

**事务的状态对象**

TransactionStatus接口提供的的是事务具体的运行状态，方法介绍如下。

| 方法                       | 说明           |
| -------------------------- | -------------- |
| boolean hasSavepoint()     | 是否存储回滚点 |
| boolean isCompleted()      | 事务是否完成   |
| boolean isNewTransaction() | 是否是新事物   |
| boolean isRollbackOnly()   | 事务是否回滚   |

==平台事务管理器+事务的定义对象=事务的状态对象==

# 21.基于XML的声明式事务控制

### 21.1什么是声明式事务控制

Spring的声明式事务顾名思义就是采用声明的方式来处理事务，声明，就是指在配置文件中声明，用在Sprng配置文件中声明式的处理事务来代替代码式的处理事务

**声明式事务处理的作用**

+ 事务管理不侵入开发的组件，具体的说，业务逻辑对象不会意识到正在事务管理之中，事实上也应该如此，因为事务管理是属于系统层面的服务，而不是业务逻辑的一部分，如果想要改变事务管理策划的话，也只需要在定义的文件中重新配置即可
+ 在不需要要事务管理的时候，只需要在设定文件上修改一下，即可移去事务管理服务，无需改变代码重新编译，这样维护起来比较方便

==注意：Spring声明式事务控制底层就是AOP==

### 21.2快速入门

### 21.3切点方法的事务参数的配置

```
<!--事务增强配置-->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
	<tx:attributes>
		<tx:method name="*"/>
	</tx:attributes>
</tx:advice>
```

其中，\<tx:method>代表切入点方法的事务参数的配置：

\<tx:method name="ransfer" isolation="REPEATABLE_READ" propagation="REQUIRED" timeout="-1" read-only="false"/>

+ name：切点方法名称
+ isolation：事务的隔离级别
+ propogation：事务的传播行为
+ timeout：超时时间
+ read-only：是否只读

### 21.4知识要点

==**声明式事务控制的配置要点**==

+ 平台事务管理器的配置

  ```
  <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  	<property name="dataSource" ref="dataSource"/>
  </bean>
  ```

+ 事务通知的配置

+ 事务aop织入的配置

  ```
  <aop:config>
  	<aop:advisor advice-ref="txAdvice" pointcut="execution(* com.itheima.service.impl.*.*(..))"/>
  </aop:config>
  ```

# 22.基于注解的声明式控制的配置要点

### 22.1注解声明式事务控制的配置要点

+ 平台事务管理器配置(xml方式)
+ 事务通知的配置(@Transactional注解配置)
+ 事务注解驱动的配置\<tx:annotation-driven/>

### 22.2注解配置声明式事务控制解析

1.使用@Transactional在需要进行事务控制的类或是方法上下修饰，注解可用的属性同xml配置方式，例如：隔离级别，传播行为。

2.注解使用在类上，那么该类下的所有方法都使用同一套注解参数配置。

3.使用在方法上，不同的方法可以采用不同的事务参数配置。

4.Xml配置文件中要开启事务的注解驱动\<tx:annotation-driven/>

# 23.Mybatis简介

### 23.1原始jdbc操作的分析

**原始jdbc开发存在的问题**

1.数据库连接创建，释放频繁造成系统资源浪费从而影响系统性能

2.sql语句在代码中硬编码，造成代码不易维护，实际应用sql变化比较大，sql变动需要改变java代码

3.查询操作时，需要手动将结果集中的数据手动封装到实体中，插入操作中，需要手动将实体的数据设置到sql的占位符位置。

**上述问题的解决方案**

1.使用数据库连接池初始化连接资源

2.将sql语句抽取到xml配置文件中

3.使用反射，内省等底层技术，自动将实体与表进行属性字段的自动映射

### 23.2什么是Mybatis

+ ==mybatis是一个优秀的基于java的持久层框架==，它内部封装了jdbc，使开发者==只需要关注sql语句==本身，而不需要花费精力去处理加载驱动，创建链接，创建statement等繁杂的过程。
+ mybatis通过xml或者注解的方式将要执行的各种statement配置起来，并通过java对象和statement中的sql的动态参数进行映射生成最终执行的sql语句
+ 最后mybatis框架执行sql并将结果映射为java对象并返回。采用ORM思想解决了==实体和数据库映射的问题==，对jdbc进行了封装，==屏蔽了jdbc api底层的访问细节==，使我们不用与jdbc api打交道，就可以完成对数据库的持久化操作。

# 24.MyBatis的快速入门

### 24.1MyBatis开发步骤

MyBatis开发步骤：

1.添加MyBatis坐标

​	mysql驱动

​	mybatis框架 

2.创建user数据表

3.编写User实体类

4.编写映射文件UserMapper.xml

写入mapping文件约束头：

```
<!--mapping文件约束头-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--编写映射文件-->
<mapper namespace="userMapper">
	<select id="findAll" resultType="com.itheima.domain.User">
		select * from suer
	</select>
</mapper>
```

5.编写核心文件SqlMapConfig.xml

写入核心文件约束头

```
<!--核心文件约束头-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--编写核心文件-->
<configuration>
	<!--数据源环境-->
	<environments default="developement">
		<environment id="developement">
			<transactionManager type="JDBC"></transactionManager>
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver"/>
				<property name="url" value="jdbc:mysql://locatlhost:3306/test"/>
				<property name="username" value="root"/>
				<property name="password" value="root/>
			</dateSource>
		</environment>
	</environments>
	
	<!--加载映射文件-->
	<mappers>
		<mapper resource="com/itheima/mapper/UserMapper.xml"></mapper>
	</mappers>
</configuration>
```

6.编写测试类

```
public class MyBatisTest{
	@Test
	public void test(){
		//获得核心配置文件
		InputStream resourceAsStream = Resources.getResourceAsStream("sqlMapConfig.xml");
		//获得session工厂对象
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
		//获得Session会话对象
		SqlSession sqlSession = sqlSessionFactory.openSession();
		//执行操作  参数：namespace+id
		List<User> userlist = sqlSession selectList("userMapper.findAll");
		//测试数据
		System.out.println(userlist);
		//释放资源
		sqlSession.close();
	}
}
```

# 25.MyBatis的映射文件概述

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220113190435873.png" alt="image-20220113190435873" style="zoom:67%;" />

# 26.MyBatis的增删改查操作

### 26.1MyBatis的查询数据操作

```
<!--查询操作-->
<mapper namespace="userMapper">
<select id="findAll" resultType="com.itheima.domain.User">
	select * from user
</select>
```



### 26.2MyBatis的插入数据操作

```
<!--插入操作-->
<mapper namespace="userMapper">
<insert id="save" parameterType="com.itheima.domain.User">
	insert into user values(#{id},#{username},#{password})
</insert>
```

**插入操作注意的问题**

+ 插入语句使用insert标签
+ 在映射文件中使用parameterType属性指定要插入的数据类型
+ Sql语句中使用#{实体属性名}方法引用试题中的属性名
+ 插入操作使用的API是sqlSession.insert("命名空间.id",实体对象);
+ 插入操作涉及数据库的数据变化，所以要使用sqlSession对象显示的提交事务，即sqlSession.commit()

### 26.3MyBatis的修改数据操作

```
<mapper namespace="userMapper">
	<update id="update" parameterType="com.itheima.domain.User">
		update user set username=#{username},password=#{password} where id=#{id}
	</update>
</mapper>
```

**修改操作注意的问题**

+ 修改语句使用update标签
+ 修改操作使用的API是sqlSession.update("命名空间.id",实体对象);

### 26.4MyBatis的删除数据操作

```
<mapper name="userMapper">
	<delete id="delete" parameterType="java.lang.Integer">
		delete from user where id=#{id}
	</delete>
</mapper>
```

**删除操作注意的问题**

+ 删除语句使用delete标签
+ ==Sql语句中使用#{任意字符串}方式引用传递的单个参数==
+ 删除操作使用API是sqlSession.delete("命名空间.id",Object);

# 27.MyBatis核心配置文件概述

### 27.1MyBatis核心配置文件层级关系

+ configuration配置
  + properties属性
  + settings设置
  + typeAliases类型别名
  + typeHandlers类型处理器
  + objectFactory对象工厂
  + plugins插件
  + environments环境
    + environment环境变量
      + transactionManager事务管理器
      + dataSource数据源
  + databaseIdProvider数据库厂商标识
  + mappers映射器

### 27.2MyBatis常用配置解析

**1.environments标签**

数据库环境的配置，支持多环境配置

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220113195133129.png" alt="image-20220113195133129" style="zoom:100%;" />

其中，事务管理器(transactionManager)类型有两种：

+ JDBC：这个配置就是直接使用了JDBC的提交和回滚设置，它依赖于从数据源得到的连接来管理事务作用域。
+ MANAGED：这个配置几乎没有做什么，它从来不提交或回滚一个连接，而是让容器来管理事务的整个生命周期(比如JEE应用服务器的上下文)。默认情况下他会关闭连接，然而一些容器并不希望这样，因此我们需要将closeConnection属性设置为false来阻止它默认的关闭行为。

其中数据源(dateSource)类型有三种：

+ UNPOOLED：这个数据源的实现只是每次被请求时打开和关系连接。
+ POOLED：这种数据源的实现利用"池"的概念将JDBC连接对象组织起来。
+ JNDI：这个数据源的实现是为了能在如EJB或应用服务器这类容器中使用，容器中可以集中或在外部配置数据源，然后放置一个JNDI上下文的引用。

**2.mapper标签**

该标签的作用时加载映射的，加载方式有以下几种：

+ 使用相对于类路径下的资源引用：例如：\<mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
+ 使用完全限定资源定位符(URL)，例如：\<mapper url="file://var/mappers/AuthorMapper.xml"/>
+ 使用映射器接口实现类的完全限定类名，例如：\<mapper class="org.mybatis.builder.AuthorMapper"/>
+ 将包内的映射器接口实现全部注册为映射器，例如：\<package name="org.mybatis.builder"/>

**3.Properties标签**

实际开发中，习惯将数据源的配置信息单独抽取成一个properties文件，该标签可以加载额外配置的properties文件

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220113231252525.png" alt="image-20220113231252525" style="zoom:67%;" />

**4.typeAliases标签**

类型别名是为Java类型设置一个短的名字，原来的类型名称配置如下

```
<select id="findAll" resultType="com.itheima.domain.User">
	select * from User
</select>
```

配置typeAliases，为com.itheima.domain.User定义别名为user

```
<typeAliases>
	<typeAlias type="com.itheima.domain.User" alias="user"></typeAlias>
</typeAliases>
```

```
<select id="findAll" resultType="user"
	select * from User
</select>
```

上面为自定义，mybatis框架以将为我们设置好一些常用的类型的别名

| 别名    | 数据类型 |
| ------- | -------- |
| string  | String   |
| long    | Long     |
| int     | Integer  |
| double  | Double   |
| boolean | Boolean  |

# 28.MyBatis的相应API

### 28.1SqlSession工厂构建器SqlSessionFactoryBuilder

常用API：SqlSessionFactory build(InputStream inputStream)

通过加载mybatis的核心文件的输入流的形式构建一个SqlSessionFactory对象

```
String resource = "org/mybatis/builder/mybatis-config.xml";
InoutStream inputStream = Resource.getResourceAsStream(resource);
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
SqlSessionFactory factory = builder.build(inputStream);
```

### 28.2SqlSession工厂对象SqlSessionFactory

SqSessionFactory有多个方法创建SqlSession实例，常用的有以下两个方法：

| 方法                            | 解释                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| openSession()                   | 会默认开启一个事务，但事务不会自动提交，也就意味着需要手动提交该事务，更新操作数据才会持久化到数据库中 |
| openSession(boolean autoCommit) | 参数为是否自动提交，如果设置为true，那么不需要手动提交事务   |

### 28.3SqlSession会话对象

SqlSession实例在MyBatis中是非常强大的一个类，在这里可以看到所有的执行语句，提交或回滚事务和获取映射器实例的方法。

执行语句的方法主要有：

```
<T> T selectOne(String statement,Object parameter)
<E> List<E> selectList(String statement,Object parameter)
int insert(String statement,Object parameter)
int update(String statement,Object parameter)
int delete(String statement,Object parameter)
```

操作事务的主要方法有：

```
void commit()
void rellback()
```

# 29.MyBatis的Dao层实现

### 29.1传统开发方式

**1.编写UserDao接口**

```
public interface UserDao{
	List<User> findAll() throws IOException;
}
```

### 29.2代理开发方式

**1.代理开发方式介绍**

采用Mybatis的代理开发方式实现DAO层的开发，这种方式是我们后面进入企业的主流。

Mapper接口开发方法只需要程序员编写Mapper接口(相当于Dao接口)，由Mybatis框架根据接口定义创建接口的动态代理对象，代理对象的方法体同上边Dao接口实现类方法。

Mapper接口开发需要遵循以下规范：

1.Mapper.xml文件中的namespace与mapper接口的全限定名相同

2.Mapper接口方法名和Mapper.xml中定义的每个statement的id相同

3.Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql的parameterType的类型相同

4 .Mapper接口方法中的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同

**2.编写UserMapper接口**

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220114163543325.png" alt="image-20220114163543325" style="zoom:67%;" />

**3.测试代理方法**

```
@Test
public void testProxyDao()throwsIOException{
	InputStream resourceAsStream = Resources.getResourceAsStream("SqlMapConfig.xml");
	SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
	SqlSession sqlSession = sqlSessionFactory.openSession();
	//获得Mybatis框架生成的UserMapper接口的实现类
	UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
	User user = userMapper.findById(1);
	System.out.println(user);
	sqlSession.close();
}
```

# 30.Mybatis映射文件深入

### 30.1动态sql语句

**1.动态sql语句概述**

Mybatis的映射文件中，前面我们的SQL都是比较简单的，有些时候业务逻辑复杂时，我们的SQL是动态变化的，此时在前面学习的SQL就不能满足要求了。

**2.动态SQL之\<if>**

我们根据实体类的不同取值，使用不同的SQL语句来进行查询，比如在di如果不为空时可以根据id查询，如果username不为空时还要加入用户名作为条件，

```
<select id="findByCondition" parameterTypej="user" resultType="user"
	select * from User
	<where>
		<if text="id!=0">
			and id=#{id}
		</if>
		<if text="username!=null">
			and username=#{username}
		</if>
		<if text="password!=null">
			and password=#{password}
		</if>
	</where>
</select>
```

**3.动态SQL之\<foreach>**

循环执行sql的拼接操作，例如：select * from user where id in(1,2,5);

```
<select id="findByIds" parameterType="list" resultType="user">
	select * from User
	<where>
		<foreach collection="array" open="id in("  close=")" item="id" separator=",">
			#{id}
		</foreach>
	</where>
</select>
```

### 30.2SQL片段抽取

Sql中可将重复的sql提取出来，使用时用include引用即可，最终达到sql重用的目的

```
<!--抽取sql片段简化编写>
<sql id="selectUser"> select * from User </sql>
<select id="findById" parameterType="int" resultType="user">
	<include refid="selectUser"></include> whrere id=#{id}
</select>

<select id="findByIds" parameterType="listj" resultType="user">
	<include refid="selectUser"></include>
	<where>
		<foreach collection="array" open="id in(" close=")" item="id" separator=",">
			#{id}
		</foreach>
	</where>
</select>
```

# 31.MyBatis核心配置文件深入

### 31.1typeHandlers标签

无论是MyBatis在预处理语句(PreparedStatement)中设置一个参数时，还是从结果集中取出一个值时，都会用配型处理器将获取的值以合适的方式转换成Java类型。下表描述了一些默认的类型处理器(截取部分)。

| 类型处理器         | Java类型                  | JDBC类型                           |
| ------------------ | ------------------------- | ---------------------------------- |
| BooleanTypeHandler | java.lang.Boolean,boolean | 数据库兼容的BOOLEAN                |
| ByteTypeHandler    | java.lang.Byte,byte       | 数据库兼容的NUMERIC或BYTE          |
| ShortTypeHandler   | java.lang.Short,short     | 数据库兼容的NUMERIC或SHORT INTEGER |
| IntegerTypeHandler | java.lang.Integer,int     | 数据库兼容的NUMERIC或INTEGER       |
| LongTypeHandler    | java.lang.Long,long       | 数据库兼容的NUMERIC或LONG INTEGER  |

可以重写类型处理器或创建自己的类型处理器来出来不支持的非标准的类型。具体做法为：实现org.apache.ibatis.type.TypeHandler接口，或继承一个便利的类org.apache.ibatis.type.BaseTypeHandler，然后可以选择性的将它映射到一个JDBC类型。

开发步骤：

1.定义转换类继承类BaseTypeHandler\<T>

2.覆盖4个未实现的方法，其中setNonNullParameter为java程序设置数据到数据库的回调方法，getNullableResult为查询时mysql的字符串类型转换成java的Type类型的方法

3.在MyBatis核心配置文件中进行注册

4.测试转换是否正确

### 31.2plugins标签

MyBatis可以使用第三方的插件来对功能进行扩展，分页助手PageHelper是将分页的复杂操作进行封装，使用简单的方式即可获得分页的数据

开发步骤：

1.导入通用PageHelper的坐标

```
<!--分页助手-->
<dependency>
	<groupId>com.github.pagehelper</groupId>
	<artifactId>pagehelper</artifactId>
	<version>3.7.5</version>
</dependency>
<dependency>
	<groupId>com.github.jsqlparser</groupId>
	<artifactId>jsqlparser</artifactId>
	<version>0.9.1</version>
</dependency>
```

2.在mybaits核心配置文件配置PageHelper插件

```
<!--配置分页助手-->
<plugins>
	<plugin interceptor="com.github.pagehelper.PageHelper">
		<property name="dialect" value="mysql"></property>     //配置方言
	</plugin>
</plugins>
```

3.测试分页数据获取

```
@Test
public void test() throws IOException{
	InputStream resourceAsStream = Resource.getResourceAsStream("sqlMapConfig.xml");
	SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
	SqlSession sqlSession = sqlSessionFactory.openSession();
	UserMapper mapper = sqlSession.getMapper(UserMapper.class);
	//设置分页相关参数  当前页+每页显示的条数
	PageHelper.startPage(1,3);
	
	List<User> userList = mapper.findAll();
	for(User user:userList){
		System.out.println(user);
	}
	
	//获得与分页相关的参数
	PageInfo<User> pageInfo = new PageInfo<User>(userList);
	System.out.println("当前页："+pageInfo.getPageNum());
	System.out.println("总条数："+pageInfo.getTotal());
	System.out.println("上一页："+pageInfo.getPrePage());
	System.out.println("是否是第一页："+pageInfo.isIsFirstPage());
	
	sqlSession.close;
}
```

# 32.MyBatis的多表操作 

###  32.1一对一查询

**1.一对一查询模型**

用户表和订单的关系为：一个用户有多个订单，一个订单只从属于一个用户

一对一查询需求：查询一个订单，与此同时查询出该订单所属的用户

==在order类中，加一个属性private User user;==

![image-20220115153250174](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220115153250174.png)

```
<mapper namespace="com.itheima.mapper.OrderMapper"
	<resultMap id="orderMap" type="com.itheima.domain.Order"
		<!--手动指定字段与实体属性的映射关系
			column:数据表的字段名称
			property:实体的属性名称
		-->
		<id column="oid" property="id"></id>
		<result column="ordertime" property="ordertime"></result>
		<result column="total" property="ototal"></result>
		
		<!--
			property：当前实体(order)中的属性名称(private User user)
			javaType：当前实体(order)中的属性的类型(User)
		-->
		
		<association property="user" javaType="com.itheima.domain.User">
			<id column="uid" property="id"></id>
            <result column="username" property="username"></result>
            <result column="password" property="password"></result>
            <result column="birthday" property="birthday"></result>
		</association>
		
	</resultMap>
	
	<select id="findAll" resultMap="orderMap">
		select *,o.id oid from orders o,user u where o.uid=u.id
	</select>
</mapper>
```

### 32.2一对多查询

**1.一对多查询的模型**

用户表和订单表的关系为：一个用户有多个订单，一个订单只从属于一个用户

一对多查询的需求：查询一个用户，于此同时查出该用户具有的订单

==在User类中，加一个属性private List\<Order> orderList;==

![image-20220115154759069](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220115154759069.png)

```
<mapper namespace="com.itheima.mapper.UserMapper"
	<resultMap id="userMap" type="com.itheima.domain.User"
		<!--手动指定字段与实体属性的映射关系
			column:数据表的字段名称
			property:实体的属性名称
		-->
		<id column="uid" property="id"></id>
		<result column="username" property="username"></result>
        <result column="password" property="password"></result>
        <result column="birthday" property="birthday"></result>
		
		<!--
			配置集合信息
			property:集合名称
			ofType:当前集合中的数据类型
		-->
		<collection property="orderList" ofType="com.itheima.domain.Order">
			<!--封装order的数据-->
			<id column="oid" property="id"></id>
			<result column="ordretime" property="ordertime"></result>
			<result column="total" property="total"></result>
		</collection>
	</resultMap>
	
	<select id="findAll" resultMap="userMap">
		select *,o.id oid from user u,orders o where u.id=o.uid
	</select>
</mapper>
```

### 32.3多对多查询

**1.多对多查询的模型**

用户表和角色表的关系为：一个用户可以有多个角色，一个角色也可以被多个用户拥有

多对多查询的需求：查询用户同时查出该用户的所有角色；

==在User类中加入一条属性private List\<Role> roleList;==

![image-20220115160138326](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220115160138326.png)

```
<mapper namespace="com.itheima.mapper.UserMapper"
	<resultMap id="userRoleMap" type="com.itheima.domain.User"
		<!--User的信息-->
		<id column="userId" property="id"></id>
		<result column="username" property="username"></result>
        <result column="password" property="password"></result>
        <result column="birthday" property="birthday"></result>
		
		<!--user内部的roleList信息-->
		
		<collection property="roleList" ofType="com.itheima.domain.Role">
			<id column="roleId" property="id"></id>
			<result column="roleName" property="roleName"></result>
			<result column="roleDesc" property="roleDesc"></result>
		</collection>
	</resultMap>
	
	<select id="findAll" resultMap="userRoleMap">
		select * from user u,sys_user_role ur,sys_role r where u.id=ur.userId and ur.roleId=r.id
	</select>
</mapper>
```

# 33.MyBatis的注解开发

### 33.1Mybatis的常用注解

@Insert：实现新增

@Update：实现更新

@Delete：实现删除

@Select：实现查询

@Result：实现结果集封装

@Results：可以与@Result一起使用，封装多个结果集

@One：实现一对一结果集封装

@Many：实现一对多结果集封装

### 33.2注解完成基本增删改查

```
public interface UserMapper{
	@Insert("insert into user values(#{id},#{username},#{password},#{birthday})")
	public void save(User user);
	
	@Update("update user set username=#{username},password=#{password} where id=#{id}")
	public void update(User user);
	
	@Delete("delete from user where id=#{id}")
	public void delete(int id);
	
	@Select("select * from user where id=#{id}")
	public User findById(int id);
	
	@Select("select * from user")
	public List<User> findAdd();
}
```

==在核心配置文件中，不需要加载映射文件，直接加载映射关系==

```
<!--加载映射关系-->
<mappers>
	<!--指定接口所在的包-->
	<package name="com.itheima.mapper"></package>
</mappers>	
```

### 33.3MyBatis的注解实现复杂映射开发

实现复杂映射之前我们可以在映射文件中通过配置\<resultMap>来实现，使用注解开发后，我们可以使用@Reuslts注解，@Result注解，@One注解，@Many注解组合完成复杂关系的配置

| 注解          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| @Results      | 代替的是标签\<resultMap>该注解中可以使用单个@Result注解，也可以使用@Result集合。<br />使用格式：@Results({@Result( ),@Result( ) }) 或 @Results(@Result( ) ) |
| @Result       | 代替了\<id>标签和\<result>标签<br />@Result中属性介绍：<br />column：数据库的列名<br />property：需要装配的属性名<br />one：需要使用的@One注解(@Result (one=@one) () ) )<br />many：需要使用的@Many注解(@Result (many=@many) () ) ) |
| @One(一对一)  | 代替了\<assocation>标签，是多表查询的关键，在注解中用来指定子查询返回单一对象<br />@one注解属性介绍：<br />select：指定用来多表查询的sqlmapper<br />使用格式：@Result(column="",property="",one=@One(select="")) |
| @Many(多对一) | 代替了\<collection>标签，是多表查询的关键。在注解中用来指定子查询返回对象集合。<br />使用格式：@Result(property="",column="",many=@Many(select="")) |

### 33.4一对一查询

查询sql：

select * from orders

select * from user where id = ?  			//?即为第一条中查询出来的结果

```
public interface OrderMapper{

	@Select("select * from orders")
	@Results({
		@Result(column="id",property="id"),
		@Result(column="ordertime",property="ordertime"),
		@Result(column="total",property="total"),
		@Result(
			property="user",		//要封装的属性名称
			column="uid",			//根据哪个字段去查询user表的数据
			javaType=User.class,	//要封装的实体类型
			//select属性，代表查询那个接口的方法获得数据
			one = @One(select = "com.itheima.mapper.UserMapper.findById")
		)
	})
	public List<Order> findAll();
}
```

### 33.5一对多查询

查询sql：

select * from user

select * from orders where uid=?       //？即为第一条查询结果

```
public interface UserMapper{

	@Select("select * from user")
	@Results({
		@Result(id=true,column="id",property="id"),
		@Result(column="username,property="username"),
		@Result(column="password",property="password"),
		@Result(
			property="orderList",	//要封装的属性名称
			column="id",			//根据哪个字段去查询user表的数据
			javaType=List.class,	//要封装的实体类型
			//select属性，代表查询那个接口的方法获得数据
			many = @Many(select = "com.itheima.mapper.OrderMapper.findById")
		)
	})
	public List<User> findAll();
}
```

```
package com.itheima.mapper;

public interface OrderMapper{
	
	@Select("select * from orders where uid=#{id}")
	public List<Role> findById(int id);
}
```



### 33.6多对多查询

查询sql：

select * from user

select * from sys_user_role ur,sys_role where ur.roleId=r.id and ur.userId=?     //?即为第一条查询结果

```
public interface UserMapper{

	@Select("select * from user")
	@Results({
		@Result(id=true,column="id",property="id"),
		@Result(column="username,property="username"),
		@Result(column="password",property="password"),
		@Result(
			property="roleList",	//要封装的属性名称
			column="id",			//根据哪个字段去查询user表的数据
			javaType=List.class,	//要封装的实体类型
			//select属性，代表查询那个接口的方法获得数据
			many = @Many(select = "com.itheima.mapper.RoleMapper.findByUid")
		)
	})
	public List<User> findUserAndRoleAll();
}
```

```
package com.itheima.mapper;

public interface RoleMapper{
	
	@Select("select * from sys_user_role ur,sys_role r where ur.roleId"=r.id and ur.userId=#{uid}")
	public List<Role> findByUid(int uid);
}
```

# 34.SSM框架整合

### 34.1准备工作

**1.原始方式整合**

 <img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220115220108717.png" alt="image-20220115220108717" style="zoom:67%;" />

**2.创建Maven工程**

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/ssm/image-20220115220231586.png" alt="image-20220115220231586" style="zoom:67%;" />

**3.导入Maven坐标**

Spring相关：spring-context,aspectjweaver,spring-jdbc,spring-tx,spring-test,spring-webmvc

servlet和jsp相关：servlet-api,jsp-api

mybatis相关：mybatis,mybatis-spring(mybatis整合spring需要的),mysql-connector-java,c3p0,junit,jstl

**4.编写实体类**

```
package com.itheima.domain;

public class Account{
	private Integer id;
	private String name;
	private Double money;

	//生成getter和setter
}
```

**5.编写Mapper接口**

```
package com.itheima.mapper;

public interface AccountMapper{

	public void save(Account account);
	
	public List<Account> findAll();
}
```

**6.编写Service接口**

```
package com.itheima.service;

public interface AccountService{
	
	public void save(Account account);
	
	public List<Account> findAll();
}
```

**7.编写Service接口实现**

```
package com.itheima.service.impl;

@Service("accountService")
public class AccountServiceImpl implements AccountService{

	@Override
	public void save(Account account){
		InputStream resourceAsStream = Rersource.getResourceAsStream("sqlMapConfig.xml");
		sqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
		sqlSession sqlSession = sqlSessionFactory.openSession();
		AccountMapper mapper = sqlSession.getMapper(AccountMapper.class);
		mapper.save(account);
		sqlSession.commit();
		sqlSession.close();
	}
	
	@Override
	public List<Accoutn> findAdd(){
		return null;
	}
}
```

**8.编写Controller**

```
package com.itheima.controller;

@Controller
@RequestMapping("/account")
public class AccountController{
	
	@AutoWired
	private AccountService accountService;

	//保存
	@RequestMapping("/save")
	@ResponseBody
	public String save(Account account){
		accountService.save(account);
		return "保存成功";
	}
	
	//查询
	@ResquestMapping("/findAll")
	public ModelAndView findAll(){
		List<Account> accountList = accountService.findAll();
		ModelAndView modelAndView = new ModeAndView();
		modelAndView.addObject("accountList",accountList);
		modelAndView.setViewName("accountList");
		return modelAndView;
	}
}
```

**9.编写添加页面**

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
	<title>Title</title>
</head>
<body>
	<h1>保存账户信息表单</h1>
	<form name="accountFrom" action="${pageContext.request.contextPath}/account/save" method="post">
		用户名称<input type="text" name="name"><br/>
		账户金额<input type="text" name="money"<br/>
		<input type="submit" value="保存"><br/>
	</form>
</body>
</html>
```

**10.编写列表页面**

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
	<title>Title</title>
</head>
<body>
	<h1>展示账户数据列表</h1>
	<table>
		<tr>
			<th>账户id</th>
			<th>账户名称</th>
			<th>账户金额</th>
		</tr>
		<tr>
			<th>1</td>
			<td>zhangsan</td>
			<td>5000</td>
		</tr>
	</table>
</body>
</html>
```

**11.编写相应配置文件**

+ Spring配置文件：applicationContext.xml

  ```
  <?xml version="1.0" encoding="UTF-8" ?>
  <beans 	xmlns="http://www.springframework.org/schema/beans"
  		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  		xmlns:aop="http://www.springframework.org/schema/aop"
  		xmlns:tx="http://www.springframework.org/schema/tx"
  		xmlns:context="http://www.springframework.org/schema/context"
  		xsi:schemaLocation="http://www.springframework.org/schema/beans
  http://www.springframework.org/schema/beans/spring-beans.xsd
  http://www.springframework.org/schema/tx
  http://www.springframework.org/schema/tx/spring-tx.xsd
  http://www.springframework.org/schema/aop
  http://www.springframework.org/schema/aop/spring-aop.xsd
  http://www.springframework.org/schema/context
  http://www.springframework.org/schema/context/spring-context.xsd">
  
  	<!--组件扫描 扫描service和mapper-->
  	<context:component-scan base-package="com.itheima">
  		<!--排除controller的扫描-->
  		<context:exclude-filter type="annotation" expression="org.springframe.stereotype.Controller">
  	</context:cmponent-scan>
  
  
  </beans>
  ```

+ SpringMVC配置文件：spring-mvc.xml

  ```
  <?xml version="1.0" encoding="UTF-8" ?>
  <beans 	xmlns="http://www.springframework.org/schema/beans"
  		xmlns:mvc="http://www.springframework.org/schema/mvc"
  		xmlns:context="http://www.springframework.org/schema/context"
  		xmlns:xsi=http://www.w3.org/2001/XMLSchema-instance”
  		xsi:schemaLocation="http://www.springframework.org/schema/beans
  http://www.springframework.org/schema/beans/spring-beans.xsd
  http://www.springframework.org/schema/mvc
  http://www.springframewozk.org/schema/mvc/spring-mvc.xsd
  http://www.springframework.org/sehema/context
  http://www.springframework.org/schema/context/spring-context.xsd">
  
  	<!--组件扫描，主要扫描controller-->
  	<context:component-scan base-package="com.itheima.controller"></context:component-scan>
  	<!--配置mvc注解驱动-->
  	<mvc:annotation-drivern></mvc:annotation-drivern>
  	<!--内部资源视图解析器-->
  	<bean id="resourceViewResolver" class="org.springframework.web.servlet.view.InternlResourceViewResolver">
  		<property name="prefix" value="/WEB-INF/pages/"></property>
  		<property name="suffix" value=".jsp"></property>
  	</bean>
  	<!--开放静态资源访问权限-->
  	<mvc:default-servlet-handeler></mvc:default-servlet-handler>
  </beans>
  ```

+ MyBatis映射文件：AccountMapper.xml

  ```
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
  	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <mapper>
  
  
  </mapper>
  ```

+ MyBatis核心文件：sqlMapConfig.xml

  ```
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
  	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  	"http://mybatis.org/dtd/mybatis-3-config.dtd">
  <configuratioin>
  	<!--加载properties文件-->
  	<properties resource="jdbc.properties"></properties>
  	<!--定义别名-->
  	<typeAliases>
  		<!--<typeAlias type="com.itheima.domain.Account" alias="account"></typeAlias>-->
  		<package name="com.itheima.domain"></package>
  	</typeAliases>
  	
  	<!--环境-->
  	<environments default="development">
  		<environment id="development">
  			<transactionManager type="JDBC"></transactionManager>
  			<dataSource type="POOLED">
  				<property name="driverClassName" value="${jdbc.driver}"></property>
  				<property name="url" value="${jdbc.url}"></property>
  				<property name="username" value="${jdbc.username}"></property>
  				<property name="password" value="${jdbc.password}"></property>
  				</dataSource>
  			</environment>
  		</environments>
  		
  		
  		<!--加载映射-->
  		<mappers>
  			<!--<mapper resource="com/itheima/mapper/AccountMapper.xml"></mapper>-->
  			<package name="com.ithiema.mapper"></package>
  			
  </configuration>
  ```

+ 数据库连接信息文件：jdbc.properties

  在resource文件夹下

  ```
  jdbc.driver=com.mysql.jdbc.Driver
  jdbc.url=jdbc:mysql://localhost:3306/ssm
  jdbc.username=root
  jdbc.password=root
  ```

+ Web.xml文件：web.xml

  ```
  <web-app>
  	<!--Spring监听器-->
  	<context-param>
  		<param-name>contextConfigLocatjion></param-name>
  		<param-value>classpath:applicationContext.xml</param-value>
  	</context-param>
  	<listener>
  		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-calss>
  	</listener>
  	
  	<!--springmvc的前端控制器-->
  	<servlet>
  		<servlet-name>DispatcherServlet</servlet-name>
  		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-calss>
  		<init-param>
  			<param-name>contextConfigLocation</param-name>
  			<param-value>classpath:spring-mvc.xml</param-value>
  		<init-param>
  		<load-on-startup>1</load-on-startup>
  	</servlet>
  	<servlet-mapping>
  		<servlet-name>DispatcherServlet</servlet-name>
  		<url-pattern>/</url-pattern>
  	</servlet-mapping>
  	
  	<!--乱码过滤器-->
  	<filter>
  		<filter-name>CharacterEncodingFilter</filter-name>
  		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  		<init-param>
  			<param-name>encoding</param-name>
  			<param-value>UTF-8</param-value>
  		</init-param>
  	</filter>
  	<filter-mapping>
  		<filter-name>CharacterEncodingFilter</filter-name>
  		<url-pattern>/*</url-pattern>
  	</filter-mapping>
  </web-app>
  ```

+ 日志文件：log4j.properties

  在resource文件夹下

  ```
  log4j.appender.stdout=org.apache.log4j.ConsoleAppender
  log4j.appender.stdout.Targer=System.err
  log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
  log4j.appender.stdout.layout.ConversionPattern=%d{ABSOLUTE} %5p %c{1}:%L - %m%n
  
  log4j.rootLogger=all, stdout
  ```

  

