---
title: Spring Boot 学习笔记
date: 2026-11-25 18:05:00
tags:
  - Spring Boot
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
---

**技术学习重要性：**

1.市场占有率很高

2.非常重要(https://spring.io)

==Java开发者写程序必备技能，没有之一==

###  01快速上手SpringBoot

#### 1.1SpringBoot入门程序开发

SpringBoot是由Pivotal团队提供的全新框架，其设计目的是用来==简化==Spring应用的==初始搭建==以及==开发过程==

+ idea生成

+ 基于阿里云创建项目，地址：https://start.aliyun.com
+ 基于官网创建项目，地址：https://start.spring.io/

+ 手工制作：

  + 导入坐标

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
  		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  		 xsi:schemaLoaction="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  	<modelVersion>4.0.0</modelVersion>
  	<parent>
  		<groupId>org.apringframework.boot</groupId>
  		<artifactId>spring-boot-starter-parent</artifactId>
  		<version>2.5.4</version>
  	</parent>
  	<groupId>com.itheima</groupId>
  	<artifactId>springboot_01_03_quickstart</artifactId>
  	<version>1.0-SNAPSHOT</version>
  	<dependencies>
  		<dependency>
  			<groupId>org.springframework.boot</groupId>
  			<artifactId>spring-boot-starter-web</artifactId>
  		</dependency>
  	</dependencies>
  </project>
  ```

  + 手工制作引导类

  ```
  package com.itheima;
  
  import org.springframework.boot.SpringApplication;
  import org.springsramework.boot.autoconfigure.SpringBootApplication;
  
  
  @SpringBootApplication
  public class Application{
  	public static void main(String[] args){
  		SpringApplication.run(Application.class,args);
  	}
  }
  ```

  

Spring程序缺点：

+ 依赖设置繁琐
+ 配置繁琐

SpringBoot程序优点

+ 起步依赖(简化依赖配置)
+ 自动配置(简化常用工程相关配置)
+ 辅助功能(内置服务器，.......)

#### 1.2parent

+ 开发SpringBoot程序要继承spring-boot-starter-parent
+ spring-boot-starter-parent中定义了若干个依赖管理
+ 继承parent模块可以避免多个依赖使用相同技术时出现依赖版本冲突
+ 继承parent的形式也可以采用引入以来的形式实现效果

#### 1.3starter

starter是SpringBoot中常见的项目名称，定义了当前项目使用的所有依赖坐标，以达到==减少依赖配置==的目的

parent是所有SpringBoot项目要继承的项目，定义了若干个坐标版本号(依赖管理，而非依赖)，以达到==减少依赖冲突==的目的

**实际开发：**

使用任意坐标时，仅书写GAV中的G和A，V由SpringBoot提供，除非SpringBoot未提供对应版本V

如发生坐标错误，再指定Version(小心版本冲突)

#### 1.4引导类

+ 启动方式

  ```
  @SpringBootApplication
  public class Springboot01QuickstartApplication{
  	public static void main(String[] args){
  		SpringApplication.run(Springboot01QuickApplication.class,args);
  	}
  }
  ```

+ SpringBoot的引导类是Boot工程的执行入口，运行main方法就可以启动项目

+ SpringBoot工程运行后初始化Spring容器，扫描引导类所在包加载bean

#### 1.5内嵌tomcat

+ 内嵌tomcat服务器是SpringBoot辅助功能之一
+ 内嵌tomcat工作原理是将Tomcat服务器多为对象运行，并将对象交给Spring容器管理
+ 变更内嵌服务器思想是去除现有的服务器，添加全新的服务器



### 02REST风格

#### 2.1REST风格简介

+ REST(Representational State Transfer),表现形式状态转换

  + 传统风格资源描述形式

    http://localhost/user/getById?id=1

    http://localhost/user/saveUser

  + REST风格描述形式

    http://localhost/user/1

    http://localhost/user

+ 优点

  + 隐藏资源的访问行为，无法通过地址得知资源是何种操作
  + 书写简化

+ 按照REST风格访问资源时使用行为动作区分对资源进行了何种操作

  + http://loacalhost/users					查询全部用户信息	GET(查询)
  + http://localhost/users/1 			     查询指定用户信息     GET(查询)
  + http://loacalhost/users                    添加用户信息            POST(新增/保存)
  + http://loacalhost/users                    修改用户信息            PUT(修改/更新)
  + http://loacalhost/users/1                 删除用户信息           DELETE(删除)

+ 根据REST风格对资源进行访问称为RESTful

+ 注意：

  上述行为是约定方式，约定不是规范，可以打破，所以称REST风格，而不是REST规范

  描述模块的名称通常使用复数，也就是加s的格式描述，表示此类资源，而非单个资源，例如：users,books,accounts......

#### 2.2RESTful入门案例：

1.设定http请求动作

```java
@RequestMapping(value="/users",method=RequestMethod.POST)
@ResponseBody
public String save(@RequestBody User user){
	System.out.println("user save..."+user);
	return"{'module':'user save'}";
}


@RequestMapping(value="/users",method=RequestMethod.PUT)us
@ResponseBody
public String save(@RequestBody User user){
	System.out.println("user update..."+user);
	return "{'module':'user update'}";
}
```

2.设定请求参数(路径变量)

```
@RequestMapping(value="/users/{id}",method=RequestMethod.DELETE)
@ResponseBody
public String delete(@PathVariable Integer id){
	System.out.println("user delete..."+ id);
	return "{'module':'user delete'}";
}
```



**@PathVariable**

类型：形参注解

位置：SpringMVC控制器方法形参定义前面

作用：绑定路径参数与处理器方法形参间的关系，要求路径参数名与形参名一一对应



==接收参数的三种方式==

@RequestBody			@RequestParam				@PathVariable

+ 区别：
  + @RequestParam用于接收url地址传参或表单传参
  + @RequestBody用于接收json数据
  + @PatnVatiable用于接收路径参数，使用{参数名称}描述路径参数
+ 应用：
  + 后期开发中，发送请求参数超过一个时，以json格式为主，@RequestBody应用较广
  + 如果发送非json格式数据，选用@RequestParam接收请求参数
  + 采用RESTful进行开发，当参数数量较少时，可以采用@PathVariable接收请求路径变量，通常用于传递id值



**@RestController**

类型：类注解

位置：基于SpringMVC的RESTful开发控制器类定义上方

作用：设置当前控制器类为RESTful风格，等同于@Controller与@ResponseBody两个注解的组合功能



**@GetMapping	@PostMapping	@PutMapping	@DeleteMapping**

类型：方法注解

位置：基于SpringMVC的RESTful开发控制器方法定义上方

作用：设置当前控制器方法请求访问路径与请求动作，每种对应一个请求动作，例如@Getmapping对应GET请求

范例：

```
@GetMapping("/{id}")
public String getById(@PathVariable Integer id){
	System.out.println("book getById..."+id);
	return "{'module':'book getById'}";
}
```

属性：value(默认)：请求访问路径

### 03基础配置

**复制工程：**

+ 在工作空间中复制对应工程，并修改工程名称
+ 删除与Idea相关配置文件，仅保留src目录与pom.xml文件
+ ==修改pom.xml文件中的artifactId与新工程/模块名相同==
+ 删除name标签(可选)
+ 保存备份工程共后期使用

#### 3.1属性配置

**SpringBoot默认配置文件application.properties，通过键值对配置对应属性**

**SpringBoot内置属性查询，官方文档中参考文档第一项：Application Properties**

+ 修改服务器端口

  server.port=80

+ 关闭运行日志图标(banner)

  spring.main.banner-mode=off

+ 设置日志相关

  logging.level.root=debug

#### 3.2配置文件分类

**SpringBoot提供了3种配置文件的格式：**

+ properties(传统格式/默认格式)
+ yml(主流格式)
+ yaml

**配置文件间的加载优先级：**

+ properties(最高)
+ yml
+ yaml(最低)

==**不同配置文件中相同的配置加载优先级相互覆盖，不同配置文件中不同的配置全部保留。**==

#### 3.3yaml文件

+ YAML(UAML Ain't Markup Language)，一种数据序列化格式

+ 优点：

  + 容易阅读
  + 容易与脚本语言交互
  + 以数据为中心，重数据轻格式

+ YAML文件扩展名

  + .yml(主流)
  + .yaml

+ 语法规则：

  + 大小写敏感
  + 属性层级关系使用多行描述，每行结尾使用冒号结束
  + 使用缩进表示层级关系，同层级左侧对齐，只允许使用空格(不允许使用Tab键)
  + 属性值前面添加空格(属性名与属性值之间使用冒号+空格作为分隔)
  + \# 表示注释

+ 格式：

  ```
  country: china
  province: beijing
  area: haidian
  
  port: 8080
  
  party: true
  
  birthday: 1999-01-01
  
  user:
  	name: itcast
  	age: 19
  
  likes:
  	- game
  	- music
  	- sleep
  	
  likes2: [game,music,sleep]
  
  users:
  	- name: zhangsan
  	  age: 18
  	- name: list
  	  age: 19
  	  
  users2:
  	- 
  	  name: zhangsan
  	  age: 18
  	- 
  	  name: list
  	  age: 19
  	  
  users3: [{name:zhangsan,age:18},{name:lisi,age:19}]
  ```

==核心规则：数据前面要加空格与冒号分开==

#### 3.4yaml数据读取

+ 使用@Value读单个数据，属性名引用方式：${一级属性名.二级属性名....}

```
baseDir: c:\win10

#使用${属性名}引用数据
tempDir: ${baseDir}\temp
#使用双引号包裹的字符串，其中的转义字符可以生效
tempDir2: "${baseDir}\temp \t1 \t2 \t3"


输出
tempDir=====>c:\win10 emp	1	2	3
```

+ 封装全部数据到Environment对象

```
lesson: SpringBoot

server:
	port: 82

enterprise:
	name: itcast
	age: 18
	tel: 4008230823
	subject:
		- Java
		- 前端
		- 大数据
```

```
@RestController
@RequestMapping("/books")
public class BookController{

	@Autowired
	private Environment env;
	
	@GetMapping("/{id}")
	public String getById(@PathVariable Integer id){
		System.out.println(env.getProperty("lesson"));
		System.out.println(env.getProperty("enterprise.name"));
		System.out.println(env.getProperty("enterprise.subject[0]"));
		return "hello,spring boot!";
	}
}
```

+ 自定义对象封装指定数据

```
@Component
@ConfigurationProperties(prefix = "enterprise")
public class Enterprise{
	private String name;
	private Integer age;
	private String[] subject;
}
```

### 04整合第三方技术

#### 4.1整合JUnit

1.导入测试对应的starter

2.测试类使用@SpringBootTest修饰

3.使用自动装配的形式添加要测试的对象

+ 名称：@SpringBootTest

+ 类型：测试类注解

+ 位置：测试类定义上方

+ 作用：设置JUnit加载的SpringBoot启动类

+ 范例：

  ```
  @SpringBootTest(classes = SpringbootJUnitApplication.class)
  class SpringbootJUnitApplicationTests {}
  ```

+ 相关属性

  classes：设置SpringBoot启动类

+ 注意：如果测试类在SpringBoot启动类的包或子包中，可以省略启动类的设置，也就是省略classes的设定

#### 4.2整合MyBatis



#### 4.3整合MyBatis-Plus



#### 4.4整合Druid



整合任意第三方配置

+ 导入对应的starter
+ 配置对应的设置或采用默认配置

#### 4.5整合SSMP

###### **实体类开发**

+ Lombok，一个Java类库，提供了一组注解，简化POJO实体类开发

  ```
  <dependency>
  	<groupId>org.projrctlombok</groupId>
  	<artifactId>lombok</artifactId>
  </dependency>
  ```

+ lombok版本由SpringBoot提供，无需指定版本

+ 常用注解：@Data

  ```
  @Data
  public class Book{
  	private Integer idj;
  	private String type;
  	private String name;
  	private String description;
  }
  ```

+ 为当前实体类在编译期设置对应的get/set方法，toString方法，hashCode方法，equals方法等

###### **数据层开发**

+ 为了方便调试可以开启MyBatisPlus的日志

  ```
  mybatis-plus:
  	configuration:
  		log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  ```

**分页功能**

+ 分页操作需要设定分页对象IPage

  ```
  @Test
  void testGetPage(){
  	Ipage page = new Page(1,5);
  	bookDao.selectPage(page,null);
  }
  ```

+ IPage对象中封装了分页操作中的所有数据

  + 数据
  + 当前页码值
  + 每页数据总量
  + 最大页码值
  + 数据总量

+ 分页操作是在MyBatisPlus的常规操作基础上增强得到，内部是动态的拼写SQL语句，因此需要增强对应的功能，使用MyBatisPlus拦截器实现

  ```
  @Configuration
  public class MPConfig{
  
  	@Bean
  	public MybatisPlusInterceptor mpInterceptor(){
  		//1.定义Mp拦截器
  		MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();
  		//2.添加具体的拦截器
  		mpInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
  		return mpInterceptor;
  	}
  }
  ```

  

**条件查询功能**

+ 使用QueryWrapper对象封装查询条件，推荐使用LambdaQueryWrapper对象，所有查询操作封装成方法调用

  ```
  @Test
  void testGetByCondition(){
  	IPage page = new Page(1,10);
  	LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
  	lqw.like(Book::getName,"Spring");
  	bookDao.selectList(page,lqw);
  }
  ```

  ```
  @Test
  void testGetByCondition(){
  	IPage page = new Page(1,10);
  	QueryWrapper<Book> qw = new QueryWrapper<Book>();
  	qw.like("name","Spring");
  	bookDao.selectList(qw);
  }
  ```

  

###### **业务层开发**

+ 定义接口

  ```
  public interface BookService{
  	boolean save(Book book);
  	boolean delete(Integer id);
  	boolean update(Book book);
  	Book getById(Integer id);
  	List<Book> getAll();
  	IPage<Book> getByPage(int currentPage,int pageSize);
  }
  ```

+ 实现类

  ```
  @Service
  public class BookServiceImpl implements BookService{
  	@Autowired
  	private BookDao bookDao;
  	
  	public Boolean save(Book book){
  		//将影响多少行的int类型返回值转换位boolean类型
  		return bookDao.insert(book) > 0;
  	}
  }
  ```

  

**业务层快速开发**

+ 使用MyBatisPlus提供有业务层通用接口(IService<T>)与业务层通用实现类(ServiceImpl<M,T>)
+ 在通用类基础上做功能重载或功能追加
+ 重载时不要覆盖原始操作，避免原始提供的功能丢失

+ 接口定义

  ```
  public interface IBookService extends IService<BOok> {
  
  }
  ```

+ 实现类定义

  ```
  @Service
  public class BookServiceImpl extends ServiceImpl<BookDao,Book> implements IBookService{
  
  }
  ```

  

###### **表现层开发**

+ 基于Restful进行表现层接口开发
+ 使用Postman测试表现层接口功能

+ 表现层消息一致性处理

  + 设计表现层返回结果的模型类，用于后端与前端进行数据格式统一，也称为==前后端数据协议==

    ```
    @Data
    public class R{
    	private Boolean flag;
    	private Object data;
    }
    ```

  + 提供一个全参构造方法，提供一个无参的以及一个参数的构造方法

    + 设计统一的返回值结果类型便于前端开发读取数据
    + 返回值结果类型可以根据需求自行设定，没有固定格式
    + 返回值结果模型类用于后端与前端进行数据格式统一，也称为前后端数据协议

###### 前后端协议联调

+ 前后端分离结构设计中页面归属前端服务器

+ 单体工程中页面放置在resources目录下的static目录中(建议执行clean)

+ 前端发送异步请求，调用后端接口

  ```
  //列表
  getAll(){
  	axiox.get("/books").then((res)=>{
  		console.log(res.data);
  	});
  },
  ```

  

