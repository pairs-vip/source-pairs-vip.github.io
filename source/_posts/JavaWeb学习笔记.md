---
title: JavaWeb 学习笔记
date: 2026-11-25 18:02:00
tags:
  - Java
  - JavaWeb
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
---

# JavaWeb

JavaWeb：使用Java语言完成服务器端程序开发

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20210928152233862.png" alt="image-20210928152233862" style="zoom:50%;" />

### 基础加强

 1.Junit单元测试

 2.反射

 3.注解

#### Junit单元测试

测试分类：

+ 黑盒测试：不需要写代码，给输入值，看程序是否能够输出期望的值
+ 白盒测试：需要写代码，关注程序具体的执行流程

Junit使用：白盒测试

​	步骤：

​		1.定义一个测试类(测试用例)  

​		2.定义测试方法：可以独立运行

​		3.给方法加@Test

​		4.导入Junit依赖环境

​	判定结果：

+ 红色：失败
+ 绿色：成功
+ 一般我们会使用断言操作来处理结果
  + Assert.assertEquals(期望的结果,运算的结果);

注解：

+ @Before：修饰的方法会在测试方法之前被自动执行
+ @After：修饰的方法会在测试方法执行之后自动被执行

  

#### 反射

反射：框架设计的灵魂

框架：半成品软件。可以在框架的基础上进行软件开发，简化编码

概念：将类的各个组成部分封装为其他对象，这既是反射机制

**反射的好处：**

+ 可以在程序的运行过程中，操作这些对象
+ 可以解耦，提高程序的可扩展性

**获取Class类对象的方式：**

+ Class.forName("全类名")：将字节码文件加载进内存，返回Class对象

  多用于配置文件，将类名定义在配置文件中，读取文件，加载类

+ 类名.class：通过类名的属性class获取

  多用于参数的传递

+ 对象.getClass()：getClass()方法在object中定义的

  多用于对象获取字节码的方式

**结论：**

+ 同一个字节码文件(*.class) 在一次程序运行过程中，只会被加载一次，不论通过哪一种方式获得的class对象都是同一个。

**使用Class对象：**

​	获取功能：

+ 获取成员变量们
  + Field[] getFields()   获取所有public修饰的成员变量
  + Field getField(String name)  获取指定名称的 public 修饰的成员变量
  + FIeld[] getDeclaredFields()  获取所有的成员变量，不考虑修饰符
  + Field getDeclaredField(String name)   获取指定名称的成员变量，不考虑修饰符
+ 获取构造方法们
  + Constructor<?> [] getConstructors()
  + Constructor<T> getConstructor(类<?> ... parameterTypes)
  + Constructor<?> [] getDeclaredConstructors()
  + Constructor<T> getDeclaredConstructor(类<?> ... parameterTypes)
+ 获取成员方法们
  + Method [] getMethods()
  + Method getMethod(String name,类<?>  ... prarmeterTypes)
  + Method [] getDeclaredMethods()
  + Method getDeclaredMethod(String name,类<?>  ... prarmeterTypes)
+ 获取类名
  + String getName()

**Field：成员变量**

+ 操作：
  + 设置值：void set(Object obj,Object value)
  + 获取值：get(Object obj)
  + 忽略访问权限修饰符的安全检查：setAccessible(true)  暴力反射

**Constructor：构造方法**

+ 创建对象：T  newInstance(object... initargs)

  如果使用空参数构造方法创建对象，操作可以简化：Class对象的newInstance方法

**Method：方法对象**

+ 执行方法：Object invoke(object obj,object...args)
+ 获取方法的名称：String getName：获取方法名



#### 注解

**概念：**说明程序的。给计算机看的

**注释：**用文字描述程序，给程序员看的

定义：注解(Annotation),也叫元数据，一种代码级别的说明，他是JDK1.5之后版本引入一个特性，与类，接口，枚举是在同一个层次，他可以声明在包，类，字段，方法，局部变量，方法参数的前面，用来对这些元素进行说明，注释。

**概念描述：**

+ JDK1.5之后的新特性
+ 说明程序的

**作用分类：**

+ 编写文档：通过代码里标识的注解生成文档【生成文档doc文档】
+ 代码分析：通过代码里标识的注解对代码进行分析【使用反射】
+ 编译检查：通过代码里标识的注解让编译器能够实现基本的编译检查【Override】

**JDK中于定义的一些注解**

+ @Override：检测被该注解标注的方法是否是继承自父类（接口）的
+ @Deprecated：该注解标注的内容，标识已过时
+ @SuppresWarnings：压制警告   一般传递参数all     @SuppresWarnings（"all"）

**自定义注解**

+ 格式：

  元注解

  public   @interface 注解名称{

  ​			属性列表；

  }

+ 本质：注解本质上就是一个接口，该接口默认继承Annotation接口

  + public interface MyAnno extends java.lang.annotation.Annotation { }

+ 属性：接口中的抽象方法

  + 属性的返回值类型 有下列取值

    1.基本数据类型  

    2.String  

    3.枚举  

    4.注解  

    5.以上类型的数组

  + 定义了属性，在使用时需要给属性赋值

    + 如果定义属性时，使用default关键字给属性默认初始化值，则使用注解时，可以不进行属性的赋值

    + 如果只用一个属性需要赋值，并且属性的名称是value，则value可以省略，直接定义值即可

    + 数组赋值时，值使用{}包裹，如果数组中只有一个值，则{}可以省略

**元注解：**用于描述注解的注解

+ @Target：描述注解能够作用的位置

  + ElementType取值
    + TYPE：可以作用在类上
    + METHOD：可以作用在方法上
    + FIELD：可以作用在成员变量上

+ @Retention：描述注解被保留的阶段

  + + @Retention(RetentionPolicy.RUNTIME)：当前被描述的注解，会保留到class字节码文件中，并被JVM读取到

+ @Documented：描述注解是否呗抽取到api文档中

+ @Inherited：描述注解是否被子类继承



**在程序使用(解析)注解**：获取注解中定义的属性值

+ 获取注解定义位置的对象   （Class，Method，Field）

+ 获取指定的注解

  + getAnnotation(Class)

    // 其实就是在内存中生成了一个该注解接口的子类实现对象

    ```
    public class ProImpl implements Pro{
        public String className(){
            return "cn.itcast.annotation.Demo1";
        }
        public String nethodName(){
            return "show";
        }
    }
    ```

+ 调用注解中的抽象方法获取配置的属性值



**小结：**

+ 以后大多数时候，我们会使用注解，而不是自定义注解
+ 注解给谁用？
  + 编译器
  + 解析程序 TestCheck就是解析程序
+ 注解不是程序的一部分，可以理解为注解就是一个标签



### 01数据库

#### JavaWeb概念

​	使用Java语言开发互联网项目，简单理解：使用Java语言开发网站

1.数据库的基本概念

​	数据库的因为单词：DataBase   简称：DB

​	什么是数据库：用于存储和管理数据的仓库。、

​	数据库的特点：

+ 持久化存储数据的，其实数据库就是一个文件系统
+ 方便存储和管理系统
+ 使用了统一的方式操作数据库--SQL

2.MySQL数据库软件

+ 去mysql的安装目录下找到my.ini文件

+ 卸载mysql

+ **删除C:/ProgramData目录下的MySQL文件夹。**

**配置：**

+ MySQL服务启动
  + 手动
  + cmd  --> services.msc   打开服务的窗口
  + 使用管理员打开cmd
    + net start mysql  :  启动mysql的服务
    + net stop mysql   :  关闭mysql服务
+ MySQL登录
  + mysql -uroot -p密码
  + mysql -hip -uroot -p连接目标的密码
  + mysql --host=ip --user=root --password=连接目标的密码
+ MySQL退出
  + exit
  + quit



3.SQL

1.什么是SQL？

Structured Query Language(结构化查询语言)

其实就是定义了操作所有关系型数据库的规则。每一种数据库操作的方式存在不一样的地方，称为“方言”



#### ==**SQL分类**==

+ DDL（操作数据库，表）

+ DQL（查询表中的数据）

+ DML（增删改表中的数据）

+ DCL（授权）

  

***DDL：操作数据库，表***



**操作数据库**：CRUD

+ C（Create）：创建

  + 创建数据库：  create database  数据库名称；

  + 创建数据库，不存在再创键    create database if not exists  数据库名称；

  + 创建数据库，并指定字符集    create database 数据库名称 character set 字符集名；

  + 创建数据库，判断存在，并指定字符集为gbk

    create database if not exixts db character set gbk;

+ R（Retrieve）：查询    

  + 查询所有数据库的名称     show databases;
  + 查询某个数据库的字符集：查询某个数据库的创建语句   show create database 数据库名称;

+ U（Update）：修改

  + 修改数据库的字符集   alter database 数据库名称 character set 字符集名称；

+ D（Delete）：删除

  + 删除数据库   drop database 数据库名称；
  + 判断存在，删除   drop database if exists 数据库名称；

+ 使用数据库

  + 查询当前正在使用的数据库名称   select database();
  + 使用数据库   use 数据库名称；

**操作表：**

+ C（Create）：创建

  + 创建表语法： 

    create table 表名(

    ​		列名1 数据类型1，

    ​		列名2 数据类型2，

    ​		...

    ​		列名n 数据类型n

    );

    ==注意：最后一列，不需要加逗号(,)==

    数据库类型：

    + int:整数类型

      age int,

    + double:小数类型

      score double(5,2),   //一共有5位，小数点后有2位

    + date:日期， 只包含年月日， yyyy-MM-dd

    + datetime:日期，包含年月日时分秒，yyyy-MM-dd HH:mm:ss

    + timestamp:时间错类型，  包含年月日时分秒，yyyy-MM-dd HH:mm:ss

      ==如果将来不给这个字段赋值，或赋值为null，则默认使用当前的系统时间，来自动赋值==

    + varchar:字符串

      name varchar(20)  姓名最大20个字符

      zhangsan  8个字符   张三   2个字符

  + 复制表：

    create table 表名 like 被复制的表名；

+ R（Retrieve）：查询    
  + 查询某个数据库中所有的表的名称  show tables；
  + 查询表结构  desc 表名；
  + 查询表的字符集  show create table 表名；

+ U（Update）：修改
  + 修改表名   alter table 表名  rename to 新的表名；
  + 修改表的字符集   alter table 表名  character set 字符集名称；
  + 添加列   alter table 表名 add 列名 数据类型；
  + 修改列名称  类型
    + alter table 表名 change 列名 新列名 新数据类型；
    + alter table 表名 modify 列名 新数据类型
  + 删除列  alter table 表名 drop 列名；

+ D（Delete）：删除

  + 删除表  drop table if exixts 表名；       drop table 表名；

    

***DML：增删改表中的数据***

+ 添加数据：

  insert into 表名（类名1，列名2，...列名n）values（值1，值2，...值n）;

  注意：

  ​	列名要和值一一对应

  ​	如果表名后，不定义列名，则默认给所有的列添加值

  ​	insert into 表名 values(值1，值2，...值n);

  ​	除了数字类型，其他类型都需要使用引号(单双都可以)引起来

+ 删除数据

  delete from 表名  where 条件

  注意：

  ​	如果不加条件，则删除表中所有纪录

  ​	如果要删除所有记录，delete from 表名；--- 不推荐使用，有多少纪录就会执行多少次删除操作      ，TRUNCATE TABLE 表名；  ------推荐使用，效率更高，先删除表，然后再创建一张一摸一样的表。

+ 修改数据

  update 表名 set 列名1=1，列名2=2，... 列名n =n  where 条件；

  注意：

  ​	如果不加条件，则会将表中所有纪录全部修改。

  

***DQL：查询表中的纪录***

​		select * from 表名；

+ 语法：

  select

  ​	字段列表

  from

  ​	表名列表

  where

  ​	条件列表

  group by

  ​	分组字段

  having

  ​	分组之后的条件

  order by

  ​	排序

  limit

  ​	分页限定

+ 基础查询

  + 多个字段的查询  select 字段名1，字段名2... from 表名；  查询所有可以用*代替字段列表；

  + 去除重复   dinstinct    ,select dinstinct address from student;

  + 计算列   一般可以使用四则运算计算一些列的值（一般只会进行数值型的计算）

    + ifnull(表达式1，表达式2):null参与的运算，计算结果都为null
    + 表达式1：哪个字段需要判断是否为null
    + 表达式2：如果该字段为null后的替换之

  + 起别名

    as:as也可以省略

  ==例如：select name,math,english,math + ifnull(english,0) as 总分 from student;==

+ 条件查询

  + where字句后跟条件

  + 运算符

    ```
    >,<,<=,>=,=,<>
    BETWEEN...AND
    IN(集合)
    LIKE：模糊查询
    	占位符：_   单个占位符    % 多个占位符
    IS NULL
    and 或 &&
    or 或 ||
    not 或 ！
    ```

**DQL查询语句：**

+ 排序查询  order by 子句

  order by 排序字段1 排序方式1 ， 排序字段2 怕排序方式2，...

  排序方式：   ASC：升序，默认的；    DESC：降序

  如果有多个排序条件，则当前面的条件值一样时，才会判断第二条件。

+ 聚合函数：将一列数据作为一个整体，进行纵向计算。  例如：计算全班数学总分

  count：计算个数  select count (id) from student;

  max：计算最大值  select max (math) from student;

  min：计算最小值   select min (math) from student;

  sum：计算和         select sum (math) from student;

  avg：计算平均值    select avg (math) from student;

  + 注意：聚合函数的计算，排除null值

    解决方案：1.选择不包含非空的列进行计算

    ​					2.IFNULL函数

+ 分组查询

  语法：group by 分组字段

  注意：

  + 分组之后查询的字段：分组字段，聚合函数

  + where和having的区别：

    1.where在分组之前进行限定，如果不满足条件，则不参与分组，having在分组之后进行限定，如果不满足条件，则不会被查询出来

    2.where后不可以跟聚合函数，having可以进行聚合函数的判断

  ==例如：select sex,avg(math),count(id) 人数 from student where math>70 group by sex 人数>2;==

+ 分页查询

  语法：limit  开始的索引，每页查询的条数

  select * from studnet limit 0,3;   ----第一页

  ==公式：开始的索引= （当前的夜码-1） *每页显示条数==

  limit是一个”MySQL方言“  ，用来完成分页操作。

**DCL：管理用户，授权**

+ 管理用户

  + 添加用户

    ​	craete user'用户名'@'主机名' identified by '密码';

  + 删除用户

    ​	drop user'用户名'@'主机名';

  + 修改用户

    ​	update user set password = password('新密码') where user = '用户名';

    ​	set password for '用户名'@'主机名' = password('新密码');

  + 查询用户 

    ​	use mysql;

    ​	select * from user;

  ==mysql中忘记了root的密码？==

  1.cmd --->net stop mysql    停止mysql服务  （需要管理员运行该cmd）

  2.使用无验证方式启动mysql服务：mysqld --skip-grant-tables

  3.打开新的cmd窗口，直接输入mysql命令，回车，就可以登录

  4.use mysql；

  5.update user set password('你的新密码')  where user = 'root';

  6.关闭两个窗口

  7.打开任务管理器，手动结束mysqld.exe进程

  8.启动mysql服务

  9.使用新密码登录

  

+ 权限管理

  + 查询权限：

    show grants for '用户名'@'主机名';

    show grants for 'lisi'@'%';

  + 授予权限：

    grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';

    ---给张三用户授予所有权限，在任意数据库任意表上

    grant all on *****.***** to 'zhangsan'@'localhost';

  + 撤销权限:

    revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';

    revoke update on db3.'account' from 'lisi'@'%';



**约束：**

概念：对表中的数据进行限定，保证数据的正确性，有效性和完整性。

分类：

+ 主键约束：primary key
+ 非空约束：not null
+ 唯一约束：unique
+ 外键约束：foreign key

***非空约束：not null ,某一列的值不能为null***

​	1.创建表时添加约束

 		create table stu(

​				id INT,

​				NAME VARCHAR(20) NOT NULL    --- name为非空

​		  );

​	2.创建完表之后，添加非空约束

​			alter table stu modify name varchar(20) not null;

​	3.删除非空约束

​			alter table stu modify name varchar(20);

***唯一约束：unique  ，某一列的值不能重复,可以为null ，但是只能有一条记录为null***

​		1.创建表时添加唯一约束

​					create table stu(

​							id INT,

​							phone_number varchar(20) unique	

​					);

​		==2.删除唯一约束==

​			alter table stu drop index phone_number;

​		3.在创建完表之后添加唯一约束

​			alter table stu  modify phone_number varchar(20) unique;

***主键约束：primary key ，非空且唯一，一张表只能有一个字段为主键，主键就是表中记录的唯一标识***

​	1.创建表时添加主键约束

​			create table stu(

​					id INT primary key,        ----id为主键

​					name varchar(20)

​			);

​	2.删除主键

​			alter table stu primary key;

​	3.创建完表后，添加主键

​			alter table stu modify id INT primary key;

​	==4.自动增长：==

​			概念：如果某一列是数值类型的，使用auto_increment 可以来完成值的自动增长

​			》在创建表时，添加主键约束，并且完成主键自动增长

​				create table stu(

​						id int primary key auto_increment,

​						name varchar(20)

​				);

​			》删除自动增长

​				alter table stu modify id int;

​			》添加自动增长

​				alter table stu modify id int auto_increment;

***外键约束：foreign key  ,让表与表产生关系，从而保证数据的正确性***

​	1.创建表时，可以添加外键

​			create table 表名(

​				...

​				外键列

​				constraint 外键名称  foreign key (外键列名称)   references 主表名称(主表列名称)

​			);

​	2.删除外键
​			alter table 表名 drop foreign key 外键名称；

​	3.创建表之后，添加外键

​			alter table 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称)  REFRENCES 主表名称(主表列名称);

​	==4.级联操作 ,修改或删除一个表中的纪录，与之相关都修改或删除==

​          添加级联操作：

​				alter table 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY (外键字段名称)  REFRENCES 主表名称(主表列名称)  ON UPDATE CASCASW ON DELETE CASCADE  ;



#### 数据库的设计

+ 多表之间的关系：

  + 一对一(了解):如人和身份证

    实现方式：可以在任意一方添加唯一外键指向另一方的主键，

    （可以直接将另一张表合到第一张中，没有必要两张表）

    <img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20211001113602403.png" alt="image-20211001113602403" style="zoom:50%;" />

  + 一对多(多对一):如部门和员工

    实现方式：在多的一方建立外键，指向一定的一方的主键

    <img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20211001113053552.png" alt="image-20211001113053552" style="zoom:50%;" />

  + 多对多：如学生和课程

    实现方式：需要借助第三张中间表，中间表至少包含两个字段，这两个字段作为第三张表的外键，分别指向两张表的主键。

    <img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20211001113140460.png" alt="image-20211001113140460" style="zoom:50%;" />

+ 数据库设计范式：

  概念：设计数据库时，需要遵循的一些规范。要遵循后面的范式要求，必须先遵循前面所有范式要求。 

  分类：

  + 第一范式（1NF）   每一列都是不可分割的原子数据项
  + 第二范式 （2NF） 在1NF的基础上，非码属性必须完全依赖于码（在1NF基础上消除并非主属性对码的部分函数依赖）
  + 第三范式 （3NF） 在2NF的基础上，任何非主属性不依赖于其他非主属性（在2NF基础上消除传递依赖）

+ 数据库的备份和还原

  1.命令行 

  + 备份：mysqldump -u用户名 -p密码 数据库名称 > 保存的路径
  + 还原：登录数据库，创建数据库，使用数据库，执行文件， souece 文件路径

  2.图像化工具

#### 多表查询&&事务

**多表查询**

多表查询的分类：

+ 内连接查询：

  + 隐式内连接：使用where条件消除无用数据

    select * from emp,dept where emp.'dept_id'=dept.'id';

  + 显示内连接：select 字段列表 from 表名1 [inner] join 表名2 on条件

    例如：select * from emp inner join dept on emp.'dept_id'=dept.'id';

    ​			select * from emp  join dept on emp.'dept_id'=dept.'id';

  + 内连接查询：

    + 从哪些表中查询数据
    + 条件是什么
    + 查询那些字段

+ 外连接查询：

  + 左外连接：select 字段列表 from 表1 left [outer] join 表2 on 条件；

    左外连接查询的是左表所有的数据及其交集部分

  + 右外连接：select 字段列表 from 表1 right [outer] join 表2 on 条件；

    右外连接查询的是右表所有的数据及其交集部分

+ 子查询：

  概念：查询中嵌套查询，称嵌套的查询为子查询

  例子：要查询工资最高的员工信息 ： 分为两步

  ​			1  查询最高工资是多少  9000    select max(salary) from emp;

  ​			2  查询员工信息，并且工资等于900    slect * from emp where salary = 9000;

  ​			一条语句完成：select * from emp where emp.salary = (select max(salary) from emp);

  ***子查询不同情况***

  + 子查询的结果是单行单列的：子查询可以作为条件，使用运算符去判断。运算符：>     >=    <    <=     =

    select * from emp where emp.salary = (select max(salary) from emp);

  + 子查询的结果是多行单列的：子查询可以作为条件，使用运算符in来判断

    select * from emp where dept_id in (select id from dept where name = '财务部' or name = '市场部');

  + 子查询的结果是多行多列的：子查询可以作为一张虚拟表参与查询

    子查询：select * from dept t1,(select * from emp where emp.'join.date' >'2011-11-11') t2  where t1.id = t2.dept_id;

    普通内连接：select * from emp t1,dept t2 where t1.'dept_id' = t2.'id' and t1.'join_date'>'2011-11-11';

**事务：**

概念：如果一个包含多个步骤的业务操作，被事务管理，那么这些操作要么同时成功，要么同时失败。

操作：1，开启事务：start  transaction;

​			2，回滚：rollback;

​			3，提交：commit;

​			4，MySQL数据库中的事务默认自动提交	

​				事务提交的两种方式：

​					1.自动提交：mysql就是自动提交的，一条DML（增删改）语句会自动提交一次事务。

​					2.手动提交：Oracle数据库默认手动提交事务  需要先开启事务在提交

​				修改事务的默认提交方式：

​					查看事务的默认提交方式：select  @@autocommit; ---1  代表自动提交  0 代表手动提交

​					修改默认提交方式：set @@autocommit = 0;		

**事务的四大特征：**

+ 原子性：是不可分割的最小操作单位，要么同时成功，要么同时失败
+ 持久性：当事务提交或回滚后，数据库会持久化的保存数据
+ 隔离性：多个事物之间，相互独立
+ 一致性：事务操作前后，数据总量不变

事务的隔离级别：（了解）

概念：多个事务之间是隔离的，相互独立的，但是如果多个事务操作同一批数据，则会引发一些问题，设置不同的隔离级别就可以解决这些问题。

存在问题：

1.脏读：一个事务，读取到另一个事务中没有提交的数据

2.不可重复读（虚读）：在同一个事务中，两次读取到的数据不一样

3.幻读：一个事务操作（DML）数据表中的所有记录，另一个事务添加了一条数据，则第一个事务查询不到自己的修改。

隔离级别：

1.read uncommitted：读未提交     产生问题：脏读，虚读，幻读

2.read committed：读已提交   产生问题： 虚读，幻读

3.repeatable read： 可重复读    产生问题：幻读

4.serializable：串行化(上锁)     产生问题：可以解决所有问题

==注意：隔离级别从小到大安全性越来越高，但是效率越来越低==

数据库查询隔离级别：select @@tx_isolation;

数据库设置隔离级别：set global transaction isolation level 级别字符串;



#### JDBC

jdbc概念：Java DataBase Connectivity     Java数据库连接， Java语言操作数据库

jdbc本质：其实就是官方（sun公司）定义的一套操作所有关系型数据库的规则，即接口，各个数据库厂商去实现这套接口，提供数据库驱动jar包，我们可以使用这套接口（JDBC）编程，真正执行的代码时驱动jar包中的实现类

快速入门：

​	1.导入驱动jar包

​			复制mysql-connector-java-5.1.37-bin.jar到项目的libs目录下

​			右键libs目录---->Add As Library

​	2.注册驱动

​	3.获取数据库连接对象 Connection

​	4.定义sql

​	5.获取执行sql语句的对象 Statement

​	6.执行sql，接受返回结果

​	7.处理结果

​	8.释放资源

***JDBC中的各个对象：***

1.DiverManager：驱动管理对象

2.Connection：数据库连接对象

3.Statement：执行sql的对象

4.ResultSet：结果集对象

5.PrepareStatement：执行sql对象

+ DriverManager：驱动管理对象

  功能：

  1.注册驱动：告诉程序该使用哪一个数据库驱动jar

  static void registerDriver(Driver driver):注册与给定的驱动程序 DriverManager

  写代码使用：Class.forName("com.mysql.jdbc.Driver");

  通过查看源码发现：在com.mysql.jdbc.Driver类中存在静态代码块

  ```
  static{
  	try{
  			java.sql.DriverManager.registerDriver(new Driver());	
  	}catch(SQLException E){
  			throw new RuntimeException("can't register driver!");
      }
  }
  ```

  2.获取数据库连接：

  ​	方法：static Connection getConnection(String url,String user,String password)

  ​	参数：

  ​			url:指定连接的路径    语法：jdbc:mysql://ip地址(域名):端口号/数据库名称

  ​					如果连接的时本机mysql服务器，并且mysql服务器默认端口为3306，则url可简写：

  ​					jdbc:mysql:///数据库名称

  ​			user:用户名

  ​			password:密码

+ Connection:数据库连接对象：

  功能：1，获取执行sql的对象

  ​					Statement createStatement()

  ​					PreparedStatement prepareStatement(String sql)

  ​			2，管理事务

  ​					开启事务：setAutoCommit(boolean auto Commit):调用该方法设置参数为false，即开启事务

  ​					提交事务：commit()

  ​					回滚事务：rollback()

+ Statement:执行sql对象

  功能：执行sql：

  ​		1.boolean execute(String sql)：可以执行任意sql

  ​		2.int executeUpdate(String sql)：执行DML(insert,delete,update)语句，DDL(create,alter,drop)语句    

  ​			返回值：影响的行数，可以通过这个影响的行数来判断DML语句是否执行成功  返回值>0的则执行成功，反之则失败。

  ​		3.ResultSet exectueQuery(String sql)：执行DQL(select)语句

+ ResultSet:结果集对象

  + next() ：游标向下移动一行，判断当前行是否是最后一行末尾（是否有数据），如果是返回false，如果不是则返回true

  + getXxx(参数)：获取数据     Xxx代表数据类型 如：int  getInt()  ,String   getString()

    参数：1，int：代表列的编号，从1开始，  如：getString(1)

    ​			2，String：代表列名称。  如：getDouble("balance")

  + 使用步骤：1，游标向下移动一行，2，判断是否有数据，3.获取数据

    ```
    //循环判断游标是否是最后一行末尾
    while(re.next()){
        //获取数据
        int id = re.getInt(1);
        String name = rs.getString(2);
        double balance = rs.getDouble(3);
    
        System.out.println(id + "----" + name + "----" + balance);
    }
    ```

+ PrepareStatement:执行sql语句

  ==1.sql注入问题：在拼接sql时，有一些sql的特殊关键字参与字符串的拼接，会造成安全性问题==

  ​	输入用户名随便，输入密码：a' or 'a' = 'a

  ​	sql:select * from user where username = 'fhjskjg' and password = 'a' or 'a' = 'a'

  2.解决sql注入问题：使用PreparedStatement对象来解决

  3.预编译的SQL：参数使用？作为占位符

  4.步骤

  ​	1.导入驱动jar包

  ​			复制mysql-connector-java-5.1.37-bin.jar到项目的libs目录下

  ​			右键libs目录---->Add As Library

  ​	2.注册驱动

  ​	3.获取数据库连接对象 Connection

  ​	4.定义sql

  ​				sql的参数是哦那个？作为占位符。如:select * from user where username = ? and password = ?;

  ​	5.获取执行sql语句的对象 PrapareStatement     Connection.perpareStatement(String sql);

  ​	6.给？赋值

  ​			setXxx(参数1，参数2)   参数1：？位置编号，从1开始    参数2：？的值

  ​	7.执行sql，接受返回结果，不需要传递sql语句

  ​	8.处理结果

  ​	9.释放资源

  ​	

+ JDBC控制事务

  事务：一个包含多个步骤的业务操作，如果这个业务操作被事务管理，则这多个步骤要么同时成功要么同时失败。

  操作：：

  ​	1，开启事务

  ​	2，提交事务

  ​	3，回滚事务

  使用Connection对象来管理事务：

  + 开启事务：setAutoCommit（boolean autoCommit）：调用该方法设置参数为false，即开启事务  conn.setAutoCommit(false);

    在执行sql前开启事务

  + 提交事务：commit（）

    当所有的sql都执行完提交事务

  + 回滚事务：rollback（）

    在catch中回滚事务

    

+ 数据库连接池

  1.概念：其实就是一个容器（集合），存放数据库连接的容器。

  ​			当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器。

  2.好处：

  ​	1.节约资源

  ​	2.用户访问高效

  3.实现：

  ​	1.标准接口：DataSource    javax.sql包下的

  ​			1.方法：

  ​					获取连接：getConnection()

  ​					归还连接：如果连接对象Connection是从连接池中获取的，那么调用Connection.close()方法，则不会在关闭连接，而是归还链接。

  ​	2.一般我们不去实现，有数据库厂商来实现

  ​			1.C3P0：数据库连接池技术

  ​			2.Druid：数据库连接池实现技术，由阿里巴巴提供
  
  4.C3P0：数据库连接池技术
  
  ​	步骤
  
  ​		1.导入jar包（两个）   
  
  ​				不要忘记导入数据库驱动jar包
  
  ​		2.定义配置文件
  
  ​				名称：c3p0.properties 或者 c3p0-config.xml
  
  ​				路径：直接将文件放在src目录下即可
  
  ​		3.创建核心对象  数据库连接池对象  ComboPooledDataSource
  
  ​		4.获取连接：getConnection
  
  **5.Druid:数据库连接池技术，由阿里巴巴提供**
  
  ​	步骤：
  
  ​		1.打入jar包： druid-1.0.9.jar
  
  ​		2.定义配置文件：	
  
  ​				是properties形式的
  
  ​				可以叫任意名称，可以放在任意目录下
  
  ​		3.加载配置文件。Properties
  
  ​		4.获取数据库连接池对象：通过工厂来获取   DruidDataSourceFactory
  
  ​		5.获取连接：getConnection
  
  ***定义工具类：***
  
  ​	1.定义一个类  JDBCUtils
  
  ​	2.提供静态代码块加载配置文件，初始化连接池对象
  
  ​	3.提供方法
  
  ​			1.获取连接方法：通过数据库连接池获取连接
  
  ​			2.释放资源
  
  ​			3.获取连接池的方法

+ ***Spring JDBC***

  Spring框架对JDBC的简单封装。提供了一个JDBCTemplate对象简化JDBC的开发

  步骤

  ​		1.导入jar包（5个）

  ​		2.创建JdbcTemplate对象，依赖于数据源DataSource

  ​					JdbcTemplate template = new JdbcTempleate（ds）；

  ​		3.调用JdbcTemplate的方法来完成CRHD的操作

  ​				update():执行DML语句。增删改语句

  ​				queryForMap():查询结果将结果集封装为map集合，将列名作为key，将值作为value，将这条纪录封装为一个map集合    	

  ​		        **注意：这个方法查询的结果集长度只能是1**

  ​				queryForList():查询结果将结果集封装为List集合

  ​				**注意：将每一条纪录封装为一个Map集合，再将Map集合床在到List集合中**

  ​				query():查询结果，将结果封装为JavaBean对象

  ​				**注意：query的参数：RowMapper**

  ​							**一般我们使用BeanPropertyRowMapper实现类。可以完成数据到JavaBean自动封装**

  ​							**new BeanPropertyRowMapper<类型>(类型.class)**

  ​				queryForObject:查询结果，将结果封装为对象

  ​				**注意：一般用于聚合函数的查询**

演示：

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20211002174705145.png" alt="image-20211002174705145" style="zoom:67%;" />

query（）：查询直接封装为JavaBean对象

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20211002181116856.png" alt="image-20211002181116856" style="zoom:67%;" />





### 02Web

JavaWeb：使用Java语言开发基于互联网的项目

#### 软件架构

+ C/S架构：Client/Server    客户端/服务器端

  在用户本地有一个客户端程序，在远程有一个服务器端程序  如：qq，迅雷

  优点：用户体验好

  缺点：开发，安装，部署，维护  麻烦

+ B/S架构：Browser/Server   浏览器/服务器端

  只需要一个浏览器，用户通过不同的网址（URL），客户访问不同的服务器端程序

  优点：开发，安装，部署，维护  简单

  缺点：如果应用过大，用户的体验可能会受到影响

  ​			对硬件要求过高

***B/S架构详解：**

​		资源分配：

​			**静态资源：*使用静态网页开发技术发布的资源

​					特点：			

​							所有用户访问，得到的结果是一样的    如：文本，图片，音频，视频，HTML，CSS，JavaScript

​							如果用户请求的是静态资源，那么服务器会直接将静态资源发送给浏览器。浏览器中内置了静态资源的解析引擎，可以展示静态资源。

​			**动态资源：*使用动态网页及时发布的资源

​					特点：

​							所有用户访问，得到的结果可能不一样 如：jsp/servlet，php，asp...

​							如果用过户请求的是动态资源，那么服务器会执行动态资源，转换为静态资源，在发送给浏览器

*要学习动态资源，必须先学习静态资源

*静态资源：

​		*HTML：用于搭建基础网页，展示页面的内容

​		*CSS：用于美化页面，布局页面

​		*JavaScript：控制页面的元素，让页面有一些动态对的效果



#### HTML

**概念**：是最基础的网页开发语言

​			Hyper Text Markup Language  超文本标记语言

​			超文本：超文本是用超链接的方法，将各种不同空间的文字信息组织在一起的网状文本

​			标记语言：由标签构成的语言。<标签名称> 如 html，xml

​								标记语言不是编程语言



**语法**：

​		1.html文档后缀   .html或 .htm

​		2.标签分为：

​				围堵标签：有开始标签和结束标签。如<html> </html>

​				自闭和标签：开始标签和结束标签在一起。如 <br/>

​		3.标签可以嵌套：

​				需要正确嵌套，不能你中有我，我中有你

```
		<a> <b></b> </a>
```

​		4.在开始标签中可以定义属性，属性是由键值对构成，值需要用引号(单双都可以)引起来

​		5.html的标签不区分大小写，但是建议使用小写

```
<html>
		<head>
				<title>title</title>
				<body>
					<font color = 'red' > hello world </font> <br/>
					<font color = 'green'> hello world </font>
				</body>
		</head>
</html>
```

**标签**：

+ 文件标签：构成html最基本的标签

  + html：html文档的根标签
  + head：头标签。用于指定html文档的一些属性，引入外部资源
  + title：标题标签
  + body：体标签
  + < !DOCTYPE html>:html5中定义该文档为html文档

+ 文本标签：和文本相关的标签

  + 注释：<!-- 注释  -- >

  + ```
    <h1> to <h6>：标题标签  <h1>标题</h1>   h1到h6字体大小逐渐递减
    ```

  + ```
    <p>:段落标签
    ```

  + ```
    <br>:换行
    ```

  + ```
    <hr>:显示一条水平线 
    属性：
    	color:颜色
    	width:宽度
    	size:高度
    	align:对齐方式   center：居中  left：左对齐   right：右对齐
    ```

  + ```
    <b>:加粗
    ```

  + ```
    <i>:斜体
    ```

  + ```
    <font>:字体标签
    属性：
    	color:颜色
    	size:大小
    	face:字体
    ```

  + ```
    <center>:文本居中
    ```

    **属性定义：**
    	color:
    		1.英文单词：red,green,blue
    		2.rgb(值1，值2，值3):值的范围：0~255  如 rgb(0,0,255)
    		3.#值1值2值3:值的范围：00~FF 之间  如：#FF00FF
    	width:
    		1.数值：width = '20',数值的单位：默认是px(像素)
    		2.数值%：占比相对于父元素的比例

+ 图片标签：

  ```
  代码：
   <!-- 展示一张图片 img -->
   <img src = "image/jingsuan_2.jpg" align"right" alt = "古镇" width = "200" htght = "500"/>
   <! -- 
   		相对路径
   			以.开头的路径
   			./：代表当前目录   ./image/1.jpg
   			../:代表上一级目录
   -->
   <img src = "./image/jingwai_1.jpg">
   <img src = "../image/jingwai_1.jpg">
  ```

+ 列表标签：

  有序列表：

  ​		ol：

  ​		li：

  无序列表：

  ​		ul：

  ​		li：

+ 链接标签：

  a：定义一个超链接

  ​	属性：

  ​			href：指定访问资源的URL（统一资源定位符）

  ​			targer：指定打开资源的方式

  ​					_self：默认值，在当前页面打开

  ​					_blank：在空白页面打开

+ div和span：

  这两个标签没有任何效果，只是起一个包裹作用，是用来和CSS一起控制文本

  div：每一个div占满一整行，块级标签

  span：文本信息在一行展示，行内标签，内联标签

+ 语义化标签：html5中为了提高程序的可读性，提供了一些标签

  ```
  <header>
  <footer>
  ```

  这两个标签也没有任何效果，起一个包裹作用，是用来和CSS一起控制文本

+ 表格标签：

  + table：定义表格

    + width：宽度
    + border：边框
    + cellpadding：定义内容和单元格的距离
    + cellspacing：定义单元格之间的距离，如果指定为0，则单元格的线会合为一条
    + bgcolor：背景色
    + align：对齐方式

  + tr：定义行

    + bgcolor：背景色
    + align：对齐方式

  + td：定义单元格

    + colspan：合并列
    + rowspan：合并行

  + th：定义表头单元格

  + ```
    <caption>:表格标题
    ```

  + ```
    <thead>:表示表格头部分
    ```

  + ```
    <tbody>:表示表格的体部分
    ```

  + ```
    <tfoot>:表示表格的脚部分
    ```

    **<thead><tbody><tfoot>也是没有任何效果的，用来和CSS一起使用**

+ ==表单标签：==

**表单：**用于采集用户输入的数据的。用于和服务器进行交互

​		form:用于定义表单的。可以定义一个范围，范围代表采集用户的范围

​		属性：

​			action：指定提交数据的URL

​			method：指定提交方式

   			分类：一共有7种，2种比较常用

​					get：

​						1.请求参数会在地址栏种显示，会封装到请求行中。

​						2.请求参数大小是有限制的

​						3.不太安全

​					post：

​						1.请求参数不会在地址栏中显示，会封装在请求体中。

​						2.请求参数的大小没有限制

​						3.较为安全

​			***==表单项中的数据要想被提交：必须指定其name属性。==***

*==**表单项标签：**==*

+ input：可以通过type属性值，改变元素展示的样式

  type属性：

  + text：文本输入框，默认值

    placeholder：指定输入框的提示信息，当输入框的内容发生变化，会自动请空提示信息

  + password：密码输入框，输入显示密文

  + radio：单选框

    注意：1.要想让多个单选框实现单选的效果，则多个单选框的name属性值必须一样

    ​			2.一般会给每一个单选框提供value属性，指定其被选中后提交的值

    ​			3.checked属性，可以指定默认值checkbox：复选框

  + checkbox：复选框

    注意：1.一般会给每一个单选框提供value属性，指定其被选中后提交的值

    ​			2.checked属性，可以指定默认值

  + file：文件选择框

  + hidden：隐藏域

  + 按钮：

    + submit：提交按钮。可以提交表单
    + button：普通按钮
    + image：图片提交按钮    src属性指定图片的路径

  + label：只当输入项的文字描述信息

    ​			注意：label的for属性一般会和input的id属性值对应，如果对应了，则点击label区域，会让input输入框获取焦点。

+ select：下拉列表

​			子元素：option    指定列表项

+ textarea：文本域

​			cols：指定列数，每一行有多少个字符

​			rows：默认多少行



#### CSS

+ CSS：页面美化和布局控制

+ 概念：Cascading Style Sheets 层叠样式表

  ​	层叠：多个样式可以作用在同一个html的元素上，同时生效

+ 好处：

  ​	1.功能强大

  ​	2.将内容展示和样式控制分离

  ​		*降低耦合度，解耦

  ​		*让分工写作更容易

  ​		*提高开发效率

+ CSS的使用：CSS与html结合方式

  + 内联样式：

    在标签内使用style属性指定css代码

    如：`<div style="color:red;">hello css</div>`

  + 内部样式：

    在head标签内，定义style标签，style标签的标签体内容就是css代码

    如：

    ```html
    <style>
        div{
            color:blue:
        }
    </style>
    <div> hello css </div>
    ```

  + 外部样式：

    1.定义css资源文件

    2.在head标签内，定义link标签，引入外部的资源文件

    如：

    ```
    a.css文件：
    	div{
    		color:green;
    	}
    	
    <link rel="stylesheet" href="css/a.css">
    <div> hello css</div>
    ```

  注意：

  ​		1，2，3种方式，css作用范围越来越大

  ​		1方式不常用，后期常用2，3

  ​		第3种格式也可以写为：

  ```html
  <style>
      @import "css/a.css";
  </style>
  ```

+ css语法

  格式：

  ```
  选择器{
  	属性名1：属性值1;
  	属性名2：属性值2;
  	...
  }
  选择器：筛选具有相似特征的元素
  注意：每一对属性需要使用;隔开，最后一对属性可以不加;
  ```

+ 选择器：筛选具有相似特征的元素

  分类：

  + 基础选择器:

    + id选择器：选择器具体的id属性值的元素，建议在同一个html页面中id值唯一

      语法： #id属性值{ }

    + 元素选择器：选择具有相同标签名称的元素

      语法：标签名称{  }

      注意：id选择器优先级高于元素选择器

    + 类选择器：选择具有相同的class属性值的元素

      语法：  .class属性值{  }

      类选择器优先级高于元素选择器

      id选择器>类选择器>元素选择器

  + 扩展选择器：

    + 选择所有元素：

      语法： *{ }

    + 并集选择器：

      语法：选择器1，选择器2{ }

    + 子选择器：筛选选择器1元素下的选择器2元素

      语法：选择器1 选择器2{ }

    + 父选择器：筛选选择器2的父元素选择器

      语法：选择器1>选择2{ }

    + 属性选择器：选择元素名称，属性名=属性值的元素

      语法：元素名称[属性名="属性值"]{ }

    + 伪类选择器：选择一些元素具有的状态

      语法：元素：状态{ }  

      如：<a>

      ​	状态：

      ​		link：初始化状态

      ​		visited：北方问过的状态

      ​		active：正在访问状态

      ​		hover：鼠标悬浮状态

      ```
      <a href = "#"> 黑马程序员 </a>
      
      a:link{
      	color:pink;
      }
      a:hover{
      	color:red;
      }
      a:actice{
      	color:blue;
      }
      a:visited{
      	color:balck;
      }
      ```

      

+ 属性

  + 字体文本
  
    + font-size:字体大小
    + color:文本颜色
    + text-align:对齐方式
    + line-height:行高
  
  + 背景
  
    + background:
  
  + 边框
  
    + border:设置边框，复合属性
  
  + 尺寸
  
    + width:宽度
    + height:高度
  
  + 盒子模型：控制布局
  
    + margin:外边距
  
    + padding:内边距
  
      默认情况下内边距会影响整个盒子的大小
  
      box-sizing:border-box；设置盒子的属性，让width和height就是最终盒子的大小
  
    + float：浮动
  
      left
  
      right

#### JavaScript

基础：

​	概念：是一门客户端脚本语言

​						运行在客户端浏览器中的，每一个浏览器都有JavaScript的解析引擎

​						脚本语言：不需要编译，直接就可以被浏览器解析执行了

​	功能：可以来增强用户和html的交互过程，可以来控制html元素，让页面有一些动态的效果，增强用户的体验。

​	JavaScript发展史：

​	1992年，Nombase公司，开发出第一门客户端脚本语言，专门用于表单的校验，命名为：C--，后来更名为ScriptEase

​	1995年，Netscape（网景）公司，开发了一门客户端脚本语言：LiveScript。后来，请来SUN公司的专家，修改LiveScript，命名为JavaScript。

​	1996年，微软抄袭了JavaScript开发出了JScript语言

​	1997年，ECMA（欧洲计算机制造商协会），ECMAScript，就是所有客户端脚本语言的标准。

​	JavaScript = ECMAScript + JavaScript自己特有的东西（BOM+DOM）



**ECMAScript**：客户端脚本语言标准

**1.基本语法**

+ 与html结合方式

  + 内部JS：

    定义`<script>`，标签体内容就是js代码

  + 外部JS：

    定义`<script>`，通过src属性引入外部的js文件

  注意：

  ​	1，`<script>`可以定义在html页面的任何地方，但是定义的位置会影响执行顺序。

  ​	2，`<script>`可以定义多个。

+ 注释

  + 单行注释：//注释内容
  + 多行注释：/*  注释内容  */

+ 数据类型

  + 原始数据类型(基本数据类型):
    + number:数字。整数/小数/NaN(not a number  一个不是数字的数字类型)
    + string:字符串。字符串  "abc"    "a"   'abc'
    + boolean:true和false
    + null:一个对象为空的占位符
    + undefined:未定义。如果一个变量么没有初始化值，则会被默认赋值为undefined
  + 引用数据类型   对象
  
+ 变量

  + 就是一小块存储数据的内存空间

  + Java语言是强类型语言，而JavaScript是弱类型语言。

    + 强类型：在开辟变量存储空间时，定义了空间将来存储的数据的数据类型，只能存储固定类型的数据
    + 弱类型：在开辟变量存储空间时，不定义空间将来的存储数据类型，可以存放任意类型的数据。

  + 语法
    var 变量名 = 初始化值;

  + typeof运算符：获取变量的类型

    注意：null运算后得到的是object

+ 运算符

  + 一元运算符：只有一个运算数的运算符

    ++  --   +(正号) -(负号)

    ++ -- :自增自减

    +(-)：正负号

    ​	注意：在JS中，如果运算数不是运算符所要求的类型，那么js引擎会自动的将运算数进行类型转换

    ​	其他类型转number：

    ​		string 转number：按照字面值转换，如果字面值不是数字，则转为NaN(不是数字的数字)

    ​		boolean转number：true转为1，false转为0；

  + 算数运算符：

    ```
    + - * / % ...
    ```

  + 赋值运算符：

    =  +=  -=  ....

  + 比较运算符：

    ```
    >   <  >=  <=  == ===(全等于)
    ```

    比较方式：

    ​	1.类型相同：直接比较

    ​		字符串：按照字典顺序比较，按位逐一比较，直到得出大小为止

    ​	2.类型不同:先进行类型转换，在比较

    ​		===：全等于，在比较之前，先判断类型，如果类型不一样，则直接返回false；

  + 逻辑运算符：

    && ：与（短路）

    ||  ：或（短路）

    !：非

    ​	其他类型转boolean：

    ​		1.number：0或NaN为假，其他为真

    ​		2.string：除了空字符串("")，其他为真

    ​		3.null&undefined：都是false

    ​		4.对象：所有对象都为true

  + 三元运算符

    ？=

+ 流程控制语句

  + if..else...

  + switch

    在java中switch中，switch语句可以接受的数据类型：byte  int  short  char  枚举(1.5) ，String(1.7)

    在JS中，switch语句可以接受任意的原始数据类型

  + while

  + do...while

  + for

+ JS特殊语法

  + 语句以;结尾，如果一行只有一条语句，则;可以省略（不建议）

  + 变量的定义使用var关键字，也可以不使用

    用：定义的变量是局部变量

    不用：定义的变量是全局变量（不建议）

**2.基本对象**

+ Function：函数（方法）对象

  + 创建

    + var fun = new Function(形式参数列表,方法体);

    + ```
      function 方法名称(形式参数列表){
      	方法体
      }
      ```

    + ```
      var 方法名 = function(形式参数列表){
      	方法体
      }
      ```

  + 属性

    + length：代表形参的个数

  + 特点

    + 定义方法时，形参的类型不用写，返回值类型也不写
    + 方法是一个对象，如果定义名称相同的方法，会覆盖
    + 在JS中，方法的调用只与方法的名称有关，和参数列表无关
    + 在方法声明中，有一个隐藏的内置对象(数组),arguments,封装所有的实际参数

  + 调用

    + 方法名称(实际参数列表);

+ Array：数组对象
  + 创建
    + var arr = new Array(元素列表);
    + var arr = new Array(默认长度);
    + var arr= [元素列表];
  + 特点
    + JS中，数组元素的类型是可变的
    + JS中，数组的长度是可变的
  + 方法
    + join(参数)：将数组中的元素按照指定的分隔符拼接为字符串
    + push()：向数组的末尾添加一个或更多的元素，并返回新的长度
  + 属性
    + length：数组的长度

+ Boolean：原始数据类型的包装类对象

+ Date：日期对象

  + 创建
    + var date = new Date();
  + 方法
    + toLocaleString()：返回当前date对象对应的时间本地字符串格式
    + getTime()：获取毫秒值，返回当前日期对象描述的时间到1970年1月1日零点的毫秒值差

+ Math：数学对象

  + 创建
    + 特点：Math对象不用创建，直接使用。 Math.方法名();
  + 方法
    + random()：返回0~1之间的随机数，含0不含1
    + ceil(x)：对数进行上取整
    + floor(x)：对数进行下取整
    + round(x)：把数四舍五入为最接近的整数
  + 属性
    + PI

+ Number：原始数据类型的包装类对象

+ String：原始数据类型的包装类对象

+ RegExp：正则表达式对象

  + 正则表达式：定义字符串的组成规则

    + 单个字符：[]   如 [a]   [ab]    [a-zA-Z0-9_] 

      特殊符号代表特殊含义的单个字符：

      \d：单个数字字符[0-9]

      \w：单个单词字符[a-zA-Z0-9_]

    + 量词符号

      ？：表示出现0次或1次

      *：表示出现0次或多次

      +：出现1次或多次

      {m,n}：表示m<=  数量   <= n

      m如果缺省：{,n} ：最多n次

      n如果缺省：{m,}：最少m次

    + 开始结束符号

      ^：开始

      $：结束

  + 正则对象：

    + 创建

      var reg  = new RegExp("正则表达式");

      var reg = /正则表达式/;

    + 方法

      test(参数)：验证指定的字符串是否符合正则定义的规范

+ Global

  + 特点：全局对象，这个Global中封装的方法不需要对象就可以直接使用。方法名();

  + 方法

    + encodeURI()：url编码

    + decodeURI()：url解码

    + encodeURIComponent()：url编码，编码的字符更多

    + decodeURICompenent()：url解码

    + parseInt()：将字符串转为数字

      逐一判断每一字符是否为数字，直到不是数字为止，将前边的数字部分转为number

    + isNaN()：判断一个值是否是NaN

      NaN六亲不认，连自己都不认识，NaN参与的==比较全部为false

    + eval()：将JavaScript字符串，把它作为脚本来执行

  + URL编码

    URL = %E4%BC%A0%E6%99%BA%E6%92%AD%E5%AE%A2

#### BOM

+ 概念：Browser Object Model  浏览器对象模型

  将浏览器的各个组成部分封装成对象

+ 组成：

  + Window：窗口对象

    + 创建

    + 方法

      **# 与弹出框有关的方法**

      **alert()**    显示带有一段消息和一个确认按钮的警告框

      **confirm()**   显示带有一段消息以继确认和取消按钮的对话框

      ​	*****如果用户点击确定按钮，则方法返回true，点击取消返回false；

      **prompt()**   显示可提示用户输入的对话框

      ​	*****返回值：获取用户输入的值

      **# 与打开关闭有关的方法**

      **close()**   关闭浏览器窗口

      ​	*****谁调用我，我关闭谁

      **open()**  打开一个新的浏览器窗口

      ​	*****返回一个新的浏览器窗口

      **# 与定时器有关的方式**

      **setTimeout()**   在指定的毫秒数后调用函数或计算表达式

      ​	参数：1，js代码或者方法对象   2，毫秒值

      ​	返回值：唯一标识，用于取消定时器

      **clearTimeout()**   取消由 setTimeout()  方法设置的  timeout

      

      **setInteral()**  按照指定的周期(以毫秒计) 来调用函数或计算表达式

      ​	参数：1，js代码或者方法对象   2，毫秒值

      ​	返回值：唯一标识，用于取消定时器

      **clearInterval()**  取消由setInterval()  设置的 timeout

    + 属性

      1.获取其他BOM对象

      ​	history

      ​	location

      ​	Nabigator

      ​	Screen

      2.获取DOM对象

      ​	document

    + 特点

      Window对象不需要创建可以直接使用   window 使用  window.方法名();

      Window引用可以省略。方法名();

  + Navigator：浏览器对象

  + Screen：显示器屏幕对象

  + History：历史记录对象

    + 创建(获取)：

      window.history

      history

    + 方法

      back()         加载history列表中的前一个URL

      forward()   加载history列表中的下一个URL

      go(参数)	  加载history列表中的某个具体页面

      ​	参数：正数：前进几个历史纪录

      ​				负数：后退几个历史记录

    + 属性

      length()     返回当前窗口历史列表中的URL数量

  + Location：地址栏对象

    + 创建(获取)：

      window.location

      location

    + 方法

      reload()  重新加载当前文档。刷新

    + 属性

      href   设置或返回完整的URL

#### DOM

**DOM简单学习**

功能：控制html文档的内容

代码：获取页面的标签(元素)对象  Element

​			document.getElementById("id值")：通过元素的id获取元素对象

操作Element对象：

​	修改属性值：

​		明确获取的对象是哪一个

​		查看API文档，找其中有哪些属性可以设置

​	修改标签体内容：

​		属性：innerHTML



**DOM**

+ 概念：Document Object Model  文档对象模型

​		将标记语言文档的各个组成部分，封装为对象。可以使用这些对象，对标记语言文档进行CRUD的动态操作

+ W3C DOM  标准被划分为3个不同的部分：

  + 核心DOM  -   针对任何结构化文档的标准模型

    + Document：文档对象

    + Element：元素对象

    + Attrubute：属性对象

    + Text：文本对象

    + Comment：注释对象

      

    + Node：节点对象

  + XML  DOM  -  针对XML文档的标准模型

  + HTML DOM  -   针对HTML文档的标准模型



+ 核心DOM模型：

  + **Document：文档对象**

    + 创建(获取)：在html dom模型中可以使用window对象来获取

      window.document

      document

    + 方法

      **# 获取Element对象：**

      getElementById()：根据id属性值来获取元素对象。id属性值一般唯一

      getElementsByTagName()：根据元素名称获取元素对象们。返回值是一个数组

      getElementsByClassName()：根据Class属性值获取元素对象们。返回值是一个数组

      getElementsByName()：根据name属性值获取元素对象们。返回值是一个数组

      **# 创建其他的DOM对象**

      createAttribute(name)

      createComment()

      createElement()

      createTextNode()

    + 属性

  + Element：元素对象

    + 创建(获取)：通过document来获取和创建

    + 方法

      removeAttribute()：删除属性

      setAttribute()：设置属性

  + Node：节点对象，其他5个的父对象

    + 特点：所有dom对象都可以被认为是一个节点

    + 方法：

      CRUD dom树：

      ​	appendChild()：向节点的子节点列表的结尾添加新的子节点

      ​	removeChild()：删除(并返回)当前节点的指定子节点

      ​	replaceChild()：用新节点替换一个子节点

    + 属性：

      parentNode  返回节点的父节点
  
+ HTML  DOM

  + 标签体的设置和获取：innerHTML

  + 使用html元素对象的属性   查看api文档

  + 控制元素样式

    + 使用元素的style属性来设置

      如：

      ```
      //修改样式方式1
      div1.style.border = "1px solid red";
      div1.style.width = "200px";
      //font-size--->fontSize
      div1.style.fontSize = "20px";
      ```

    + 提前定义好类选择器的样式，通过元素的className属性来设置其class属性值。



**事件**

+ 概念：某些组件被执行了某些操作后，触发某些代码的执行。

+ 如何绑定事件：

  1.直接在html标签上，指定事件的属性(操作),属性值就是js代码

  ​	事件：onclick-----单击事件

  2.通过js获取元素对象，指定事件属性，设置一个函数

+ 事件监听机制：

  + 事件：某些操作。如：单机，双击，键盘按下了，鼠标移动了

  + 事件源：组件。如：按钮  文本输入框...

  + 监听器：代码。

  + 注册监听：将事件，事件源，监听器结合在一起。当事件源上发生了某个事件，则触发执行某个监听器代码。

+ 常见的事件：

  + 点击事件：

    + onclick：单击事件

    + ondblclick：双击事件

  + 焦点事件：

    + onblur：失去焦点   一般用于表单校验

    + onfocus：获得焦点

  + 加载事件：

    + conload：一张页面或一幅图像完成加载

  + 鼠标事件：

    + onmousedown    鼠标按钮被按下

      定义方法时，定义一个形参，接受event对象

      event对象的button属性可以获取鼠标按钮键被点击了

    + onmouseup		 鼠标按键被松开

    + onmousemove    鼠标被移动

    + onmouseover       鼠标移到某元素之上

    + onmouseout          鼠标从某元素移开

  + 键盘事件：

    + onkeydown	某个键盘按键被按下

    + onkeyup		 某个键盘按键被松开

    + onkeypress     某个键盘按键被按下并松开

  + 选中和改变：

    + onchange		域的内容被改变

    + onselect		   本本被选中

  + 表单事件：

    + onsubmit		确认按钮被点击

      可以阻止表单的提交

      方法返回false，则表单被阻止提交

    + onreset 		  重置按钮被点击

#### Bootstrap

概念：一个前端开发的框架

​	框架：一个半成品软件，开发人员可以在框架基础上，在进行开发，简化编码。

好处：

​		1.定义了很多的css样式和js插件，我们开发人员直接可以使用这些样式和插件得到丰富的页面效果

​		2.响应式布局：同一套页面可以兼容不同分辨率的设备。



+ 响应式布局

  + 同一套页面可以兼容不同分辨率的设备

  + 实现：依赖于栅格系统，将一行平均分成12个格子，可以指定元素占几个格子

  + 步骤：

    + 定义容器。相当于之前的table

      容器分类：

      container：两边留白

      container-fluid：每一种设备都是100%宽度

    + 定义行。相当于之前的tr   样式：row

    + 定义元素。指定该元素在不同的设备上，所占各自的数目。样式：col-设备代号-各自数目

      设备代号：

      1.xs：超小屏幕    手机(<768px)：col-xs-12

      2.sm：小屏幕   平板 (>=768px)

      3.md：中等屏幕   桌面显示器(>=992px)

      4.lg：大屏幕    大桌面显示器(>=1200px)

    + 注意

      1.一行中如果格子数目超过12，则超出部分自动换行

      2.栅格类属性可以向上兼容，栅格类适用于与屏幕宽度大于或者等于分界点大小的设备

      3.如果真实设备宽度小于了设置栅格类属性的设备代码的最小值，会一个元素占满一整行

      

+ 全局CSS样式

  + 按钮：class = "btn btn-defaule";

  + 图片：

    + class = "img-responsive";  图片在任意尺寸都占100%

    + 图片形状

      ```
      <img src = "..."  alt = "..."  class = "img-rounded"> ：方形
      <img src = "..."  alt = "..."  class = "img-circle">  ：圆形
      <img src = "..."  alt = "..."  class = "img-thumbnail">：相框
      ```

  + 表格：

    table：表格样式

    table-bordered：边框

    table-hover：鼠标悬浮的效果

  + 表单：

    给表单项添加：class="form-control"

+ 组件：

  + 导航条

  + 分页条

+ JS插件：

  + 轮播图



#### XML

+ 概念：Extensible Markup  Language    可扩展标记语言

  可扩展：标签都是自定义的。<use>   <student>

+ xml和html的区别(w3c：万维网联盟)

  + xml的标签都是自定义的，html标签是预定义。
  + xml的语法严格，html语法松散
  + xml是存储数据的，html是展示数据

+ 功能：存储数据

  + 配置文件

  + 在网络中传输

+ 语法：

  + 基本语法：

    + xml文档的后缀名为.xml
    + xml第一行必须定义为文档声明
    + xml文档中有且仅有一个根标签
    + 属性值必须使用引号(单双都可)引起来
    + 标签必须正确关闭
    + xml标签名称==区分==大小写

  + 快速入门

    ```
    <?xml version='1.0' ?>
    
    <users>
    	<user id = '1'>
    		<name>zhangsan</name>
    		<age>23</age>
    		<gender>male</gender>
    	</user>
    	
    	<user id = '2'>
    		<name>lisi</name>
    		<age>34</age>
    		<gender>female</gender>
    	</user>
    </users>
    ```

  + 组成部分

    文档声明：

    + 格式：<?xml 属性列表  ?>

    + 属性列表：

      version：版本号，必须的属性

      encoding：编码格式，告知解析引擎当前文档使用的字符集，默认值:ISO-8859-1

      standalone：是否独立

      ​	确实：yes：不依赖其他文件

      ​				no：  依赖其他文件

    指令(了解)：结合CSS

    + <?xml-stylesheet type="text/css" href="a.css" ?>

    标签：标签名称自定义的

    + 名称可以包含字母，数字以及其他的字符
    + 名称不能以数字或者标点符号开始
    + 名称不能以字母xml(或者XML，Xml  等等) 开始
    + 名称不能包含空格

    属性：

    id属性值唯一

    文本：

    CDATA区：在该区域中的数据会被原样展示

    格式：<![CDATA[ 数据 ]]>

    

  + 约束：规定xml文档的书写规则

    

    作为框架的使用者(程序员):

    1.能够在xml中引入约束文档

    2.能够简单的读懂约束文档

    **分类：**

    + DTD：一种简单的约束技术

      引入dtd文档到xml中

      ​	内部dtd：将约束的规则定义在xml文档中

      ​	外部dtd：将约束的规则定义在外部的dtd文件中

      ​			*本地：<!DOCTYPE 根标签名 SYSTEM "dtd文件的位置">

      ​			*网络：<!DOCTYPE 根标签名 PUBLIC "dtd文件的名字" "dtd文件的位置URL">

    + Schema：一种复杂的约束技术

      

+ 解析：操作xml文档，将文档中的数据读取到内存中

  + 操作xml文档

    ​	解析(读取)：将文档中的数据读到内存中

    ​	写入：将内存中的数据保存到xml文档中。持久化的存储

  + 解析xml的方式

    ​	DOM：将标记语言文档一次性加载进内存，在内存中形成一棵dom树

    ​		优点：操作方便，可以对文档进行CRUD的所有操作

    ​		缺点：占内存

    ​	SAX：逐行读取，基于事件驱动的。

    ​		优点：不占内存

    ​		缺点：只能读取，不能增删改

  + xml常见的解析器

    + JAXP：sun公司提供的解析器，支持dom和sax两种思想

    + DOM4J：一款非常优秀的解析器

    + Jsoup：是一款Java的HTML解析器，可直接解析某个URL地址，HTML文本内容。它提供了一套非常省里的API，可通过DOM，CSS以及类似于JQuery的操作方法来取出和操作数据。
    + PULL：Android操作系统内置的解析器，sax方式的。

#### Jsoup

​	快速入门：

​	步骤：

​	1.导入jar包

​	2.获取Document对象

​	3.获取对应的标签Element对象

​	4.获取数据

代码：

```
//获取student.xml的path
String path = JsoutDemo1.class.getClassLoader().getResource("student.xml").getPath();
//解析xml文档，加载文档进内存，获取dom树---->Document
Document document = Jsoup.parse(new File(path),"utf-8");
//获取元素对象Element
Elements elements = doucument.getElementsByTag("name");
System.out.println(elements.size());
//获取第一个name的Element对象
Element element = elements.get(0);
//获取数据
String name = element.text();
System.out.println(name);
```

+ 对象：

  + Jsoup：工具类，可以解析html或xml文档，返回Document

    + parse：解析html或xml文档，返回Document对象

      parse(FIle in,String charsetName)：解析xml或者html文件的。

      parse(String html)：解析xml或者html字符串

      parse(URL url,int timeoutMillis)：通过网络路径获取指定的html或xml的文档对象

  + Document：文档对象，代表内存中的dom树

    + 获取Element对象

      getElementById(Sring id)：根据id的属性值获取唯一的element对象

      getElementsByTag(String tagName)：根据标签名称获取元素对象集合

      getElementsByAttribute(String key)：根据属性名称获取元素对象集合

      getElementsByAttributeValue(String key,String value)：根据对应的属性名和属性值获取元素对象集合

  + Elements：元素Element对象额集合，可以当作ArrayList<Element>来使用

  + Element：元素对象

    + 获取子元素对象

      getElementById(Sring id)：根据id的属性值获取唯一的element对象

      getElementsByTag(String tagName)：根据标签名称获取元素对象集合

      getElementsByAttribute(String key)：根据属性名称获取元素对象集合

      getElementsByAttributeValue(String key,String value)：根据对应的属性名和属性值获取元素对象集合

    + 获取属性值

      String attr(String key)：根据属性名称获取属性值

    + 获取文本内容

      String text()：获取文本内容

      String html()：获取标签体的所有内容(包括子标签的标签和文本内容)

  + Node：节点对象

    + 是Document和Element的父类



+ 快捷查询方式

  + selector：选择器

    使用的方法：Elements	select(String cssQuery)

    ​	语法：参考Selector类中定义的语法

  + XPath：XPath即为xml的路径语言，他是一种用来确定xml（标准通用标记语言的子集）文档中某部分位置的语言

    + 使用Jsoup的xpath需要额外导入jar包
    + 查询w3cshool参考手册，使用xpath的语法完成查询



### 03Web核心

软件架构：

​	C/S：客服端/服务器端

​	B/S：浏览器/服务器端

资源分类：

​	1.静态资源：所有用户访问后，得到的结果都是一样的，称为静态资源，静态资源可以直接被浏览器解析。如：html，css，JavaScript

​	2.动态资源：每个用户访问相同的资源后，得到的结果可能不一样，称为动态资源。动态资源被访问后，需要先转换位静态资源，再返回给浏览器。如：servlet/jsp，php，asp...

网络通信三要素：

IP：电子设备(计算机)在网络中的唯一标识
端口：应用程序在计算机中唯一标识。0~65535
传输协议：规定了数据传输的协议
	基础协议：tcp：安全协议，三次握手，速度稍慢

​					   udp：不安全协议，速度快



#### Web服务器软件

服务器：安装了服务器软件的计算机

服务器软件：接收用户的请求，处理请求，做出响应

web服务器软件：接受用户的请求，处理请求，做出响应

​	在web服务器软件中，可以部署web项目，让用户通过浏览器来访问这些项目

​	web容器

常见的java相关的web服务器软件：

​	webLogic：oracle公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费

​	webSphere：IBM公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费

​	JBOSS：JBOSS公司，大型的JavaEE服务器，支持所有的JavaEE规范，收费

​	Tomcat：Apache基金组织，中小型的JavaEE服务器，仅仅支持少量的JavaEE规范servlet/jsp。开源的，免费的



#### **Tomcat**

**tomcat：web服务器软件**

+ 下载：http://tomcat.apache.org/

+ 安装：解压安装包即可     注意：安装目录建议不要有中文和空格

+ 卸载：删除目录就可以了

+ 启动：

  bin/startup.bat,双击运行该文件即可

  访问：浏览器输入：http://localhost:8080  回车访问自己

  ​								   http://别人的ip:8080   访问别人

  可能遇到的问题：

  ​	黑窗口一闪而过：

  ​		原因：没有正确配置JAVA_HOME环境变量

  ​		解决方案：正确配置JAVA_HOME环境变量

  ​	启动报错：

  ​		暴力：找到占用的端口号，并且找到对应的进程，杀死该进程

  ​					netstat -ano

  ​		温柔：修改自身的端口号

  ​					conf/server.xml

  ​					<Connector port="8080" protocol="HTTP/1.1"

  ​					connectionTimeout="20000"

  ​					redirectPort="8445"  />

  ​					一般会将tomcat的默认端口号修改为80。80端口号是http协议的默认端口号。

  ​					好处：在访问时，就不用输入端口号

+ 关闭：

  正常关闭：

  ​	bin/shutdown.bat

  ​	ctrl+c

  强制关闭：点击启动窗口的x

+ 配置：

  **部署项目的方式：**

  + 直接将项目放到webapps目录下即可

    /hello：项目的访问路径---->虚拟目录

    简化部署：将项目打包成一个war包，在将war包放到webapps目录下，war包会自动解压缩

  + 配置conf/server.xml文件

    在<Host>标签体中配置

    <Context docBase = "D:\hello" path="/hehe"   />

    docBase：项目存放的目录

    path：虚拟目录

  + 在conf\Catalina\localhost创建任意名称的xml文件。在文件中编写

    <Context docBase = "D:\hello" />

    虚拟目录：xml文件的名称

    

  **静态项目和动态项目：**

  + 目录结构

    java动态项目的目录结构

    ​	-- 项目的根目录
    ​		-- WEB-INF目录：
    ​			-- web.xml：web项目的核心配置文件
    ​			-- classes目录：放置字节码文件的目录
    ​			-- lib目录：放置以来的jar包

    

  **将Tomcat集成到IDEA中，并且创建JavaEE的项目，部署项目**



#### **Servlet**

+ 概念：运行在服务器端的小程序

​		Servlet就是一个接口，定义了Java类被浏览器访问到(tomcat识别)的规则。

​		将来我们自定义一个类，实现Servlet接口，复写方法。

+ 快速入门：

  ​	创建JavaEE项目

  ​	定义一个类，实Servlet接口

  ​	实现接口中的抽象方法

  ​	配置Servlet

  ​		在web.xml中配置：

```
    <servlet>
        <servlet-name>demo1</servlet-name>
        <servlet-class>cn.itcast.servlet.servletdemo1</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>demo1</servlet-name>
        <url-pattern>/demo1</url-pattern>
    </servlet-mapping>
```

+ 执行原理：

  1.当服务器接收到客户端浏览器的请求后，会解析请求URL路径，获取访问的Servlet的资源路径

  2.查找web.xml文件，是否有对应的<url-pattern>标签体内容

  3.如果有，再找到对应的<servlet-class>全类名

  4.tomcat会将字节码文件加载进内存，并且创建其对象

  5.调用其方法。



+ Servlet中的生命周期：

  + 被创建：执行init方法，只执行一次

    1.Servlet什么时候被创建：默认情况下，第一次被访问时，Servlet被创建，也可以配置	执行Servlet的创建时机

    ​	在<servlet>标签下配置

    ```
    第一次被访问时创建
        load-on-startup 为负数，
    在服务器启动时创建
        load-on-startup  0或正整数
    ```

    2.Servlet的init方法，只执行一次，说明一个Servlet在内存中只存在一个对象，Servlet是单例的

    ​	多个用户同时访问时，可能存在线程安全问题

    ​	解决：尽量不要在Servlet中定义成员变量，即使定义了，也不要对其修改值。

  + 提供服务：执行service，执行多次

    每次访问Servlet时，Service方法都会被调用一次

  + 被销毁：执行destroy，只执行一次

    Serlvet被销毁时执行，服务器关闭时，Servlet被销毁

    只有服务器正常关闭时，才会执行destroy方法

    destroy方法在Servlet被销毁之前执行，一般用于释放资源

+ Servlet3.0：

  好处：

  ​	支持注解配置。可以不需要web.xml了。

  步骤：

  ​	1.创建JavaEE项目，选择Servlet的版本3.0以上，可以不创建web.xml

  ​	2.定义一个类，实现Servlet接口

  ​	3.复写方法

  ​	4.在类上使用@WebServelt注解，进行配置

  ​			@WebServlet("资源路径")

+ IDEA和tomcat的相关配置

  + IDEA会为每一个tomcat部署的项目单独建立一份配置文件

    查看控制台的log：Using CATALINA_BASE:   "C:\Users\Admin\.IntelliJIdea2019.3\system\tomcat\Tomcat_8_5_71_Java_2"

  + 工作空间项目      和         tomcat部署的web项目

    tomcat真正访问的是“tomcat部署的web项目”  ，“tomcat部署的web项目”对应着“工作空间项目”  的web目录下的所有资源

    WEB-INF目录下的资源不能被浏览器直接访问

  + 断点调试：使用“小虫子”启动   debug启动



+ Servlet的体系结构

  Servlet  ---   接口

  ​	 |

  GenericServlet   --- 抽象类

  ​	 |

  HttpServlet   --- 抽象类



​	GenericServlet：将Servlet接口中其他的方法做了默认空实现，只将service（）方法作为抽象

​		将来定义servlet类时，可以继承GenericServlet，实现service（）方法即可。



​	==HttpServlet：对http协议的一种封装，简化操作==
​				1.定义类继承HttpServlet

​				2.复写doGet/doPost方法



+ Servelt相关配置

  urlpartten：Servlet访问路径

  + 一个Servlet可以定义多个访问路径：@WebServlet({"/d4","/dd4","/ddd4"})
  + 路径定义规则：
    + /xxx
    + /xxx/xxx：多层路径，目录结构
    + *.do     查找以do为后缀的路径名



#### HTTP

+ 概念：Hyper Text Transfer Protocol  超文本传输协议

  传输协议：定义了客户端和服务器端通信时，发送数据的格式

  特点：

  ​	1.基于TCP/IP的高级协议

  ​	2.默认端口号：80

  ​	3.基于请求/相应模型的：一次请求对应一次相应

  ​	4.无状态的：每次请求之间相互独立，不能交互数据

  历史版本：

  ​	1.0：每一次请求相应都会建立新的连接

  ​	1.1：复用连接

+ 请求消息数据格式

  + 请求行

    + 请求方式 /请求url 请求协议/版本

      GET /login.html HTTP/1.1

    + 请求方式有7种，常用的有2种：

      GET：请求参数在请求行中，在url后。

      ​			请求的url长度有限制

      ​			不太安全

      POST：请求参数在请求体中

      ​			 请求的url长度没有限制

      ​			  相对安全

  + 请求头

    + 请求头名称：请求头值

    + 常见的请求头：

      User-Agent：浏览器告诉服务器，我访问你使用的浏览器版本信息

      ​	可以在服务器端获取该头的信息，解决浏览器的兼容性问题

      Referer：http：//localhost/login.html

      ​	告诉服务器，我（当前请求）从哪里来

      ​	作用：1，防盗链    2，统计工作

  + 请求空行

    + 空行，即使用来分割POST请求的请求头，和请求体的

  + 请求体（正文）：

    + 封装POST请求消息的请求参数的

    + 参数

      username  =  zhangsan;





```
GET /JavaWebServlet/login.html HTTP/1.1
Host: localhost:8080
Connection: keep-alive
Cache-Control: max-age=0
sec-ch-ua: "Chromium";v="94", "Google Chrome";v="94", ";Not A Brand";v="99"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Windows"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/94.0.4606.71 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Sec-Fetch-Site: none
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9
Cookie: JSESSIONID=E782AA2100FB60A420B65505353D81A7; Idea-419b8ae3=badab74f-e472-4638-b1e2-968b9016aa5b
If-None-Match: W/"289-1634086799995"
If-Modified-Since: Wed, 13 Oct 2021 00:59:59 GMT
```



+ Request

  + request对象和response对象的原理
    + request和response对象是由服务器创建的，我们来使用
    + request对象是用来获取请求消息，response对象是用来设置响应消息

  + request对象的继承体系结构

    ServletRequest                  -- 接口
    		|	继承
    HttpServletRequest		-- 接口
    		|	实现
    org.apache.catalina.connector.RequestFacade  类(tomcat)

  + request功能：

    + 获取请求消息数据：

      ​	**获取请求行数据：**GET /day14/demo1?name=zhangsan HTTP/1.1

      ​		方法：

      ​				获取请求方式：GET

      ​						String getMtehod()

      ​				(*)获取虚拟目录：/day14

      ​						String getContextPath()

      ​				获取Servlet路径：/demo1

      ​						String getServletPath()

      ​				获取get方式的请求参数：name = shangsan

      ​						String getQueryString()

      ​				(*)获取请求URI：/day14/demo1

      ​						String getRequestURI():   /day14/demo1

      ​						StringBuffer getRequestURL():	http://localhost/day14/demo1

      ​				获取协议及版本：HTTP/1.1

      ​						String getProtocol()

      ​				获取客户机的IP地址：

      ​						String getRemoteAddr()

      

      ​	**获取请求头数据：**

      ​		方法：

      ​				String getHeader(String name)：通过请求头的名称获取请求头的值

      ​				Enumeration<String>	getHeaderNames()：获取所有请求头名称

      ​	**获取请求体数据：**

      ​		请求体：只有POST请求方式，才有请求体，在请求体中封装了POST请求的请求参数

      ​		步骤：

      ​			1.获取流对象

      ​					BufferedReader getReader()：获取字符输入流，只能操作字符数据

      ​					ServletInputStream getInputStream()：获取字节输入流，可以操作所有类型数据

      ​			2.再从流对象中拿数据

      ​					

    + ==其他功能：==

      **1.获取请求参数通用方式**：不论get还是post请求方式都可以使用下列方法来获取请求参数
      	1.String getParameter(String name)：根据参数名称获取参数值   username=zs&password=123

      ​	2.String[] getParameterValues(String name)：根据参数名称获取参数值的数组	hobby=xx&hobby=game

      ​	3.Enumeration<String> getParameterNames()：获取所有请求的参数名称

      ​	4.Map<String,String[]> getParameterMap()：获取所有参数的map集合

      ​	***中文乱码问题：***

      ​		*get方式：tomcat 8 已经将get方式乱码问题解决了*

      ​		*post方式：会乱码*
      ​			*解决方法：在获取参数前，设置request的编码request.setCharacterEncoding("utf-8");*

      **2.请求转发**

      ​	1.步骤：

      ​			1.通过request对象获取请求转发器对象：RequestDispatcher getRequestDispatcher(String path)

      ​			2.使用RequestDispatcher对象来进行转发：forward(ServletRequest request,ServletResponse response)

      ​	2.特点：

      ​			1.浏览器地址栏路径不发生变化

      ​			2.只能转发到当前服务器内部资源中

      ​			3.转发是一次请求

      **3.共享数据**

      ​	域对象：一个有作用范围的对象，可以在范围内共享数据

      ​	request域：代表一次请求的范围，一般用于请求转发的多个资源中共享数据

      ​	方法：

      ​		1.void setAttribute(String name,Object obj)：存储数据

      ​		2.object getAttribute(String name)：通过键获取值

      ​		3.void removeAttribute(String name)：通过键移除键值对

      **4.获取ServletContext**

      ​	ServletContext getServletContext()

==login.html中form表单中的action路径的写法 ：虚拟目录+Servlet的资源路径==



==BeanUtils工具类，简化数据封装==
用于封装JavaBean的

​	1.JavaBean：标准的Java类
​			1.要求：
​				1.类必须被public修饰
​				2.必须提供空参的构造器
​				3.成员变量必须使用private修饰
​				4.提供公共的setter和getter方法

​			2.功能：封装数据

​	2.概念：	
​		成员变量：
​		属性：setter和getter方法截取后的产物
​				例如：getUsername()  -->Username  -->   username

​	3.方法：
​		1.setProperty()
​		2.getProperty()
​		3.populate(Object obj,Map map)：将map集合的键值对信息，封装到对应的JavaBean对象中



+ 响应消息数据格式

  + 响应行

    + 组成：协议/版本	响应状态码 状态码描述

    + 响应状态码：服务器告诉客户端浏览器本次请求和响应的一个状态

      ​	状态码都是3位数字

      ​	分类：

      ​		1.1xx：服务器接收客户端消息，但没有接收完成，等待一段时间后，发送1xx多状态码

      ​		2.2xx：成功。代表：200

      ​		3.2xx：重定向。代表：302(重定向),304(访问缓存)

      ​		4.4xx：客户端错误。代表：404(请求路径没有对应的资源)   

      ​														405(请求方式没有对应的doxxx方法)

      ​		5.5xx：服务器端错误。代表：500(服务器内部出现异常)

  + 响应头

    + 格式：头名称：值

    + 常见的响应头：

      Content-Type：服务器告诉客户端本次响应体数据格式以及编码格式

      Content-disposition：服务器告诉客户端以什么格式打开相应体数据

      ​		值：in-line：默认值，在当前页面打开

      ​				attachment;filename=xxx：以附件形式打开响应体，文件下载

  + 响应空行

  + 响应体：传输的数据

    

  响应字符串格式：

  ```
  HTTP/1.1 200 
  Content-Type: text/html;charset=UTF-8
  Content-Length: 90
  Date: Thu, 14 Oct 2021 01:51:42 GMT
  Keep-Alive: timeout=20
  Connection: keep-alive
  
  <html>
    <head>
      <title>$Title$</title>
    </head>
    <body>
    $END$
    </body>
  </html>
  ```



+ Response

  + 功能：设置响应消息

    ​	1.设置响应行：
    ​			1.格式：HTTP/1.1 200 ok
    ​			2.设置状态码：setStatus(int sc)

    

    ​	2.设置响应头：setHeader(String name,String value)

    

    ​	3.设置响应体：
    ​			使用步骤：
    ​					获取输出流
    ​							字符输出流：PrintWriter getWriter()
    ​							字节输出流：ServletOutputStream getOutputStrean()
    ​					使用输出流，将数据输出到客户端浏览器

    

  + **重定向：资源跳转的方式**

    代码实现：

    ```
    //1.设置装填码为302
    response.setStatus(302);
    //2.设置响应头location
    response.setHeader("location","/day15/responseDemo2");
    
    //简单的重定向
    response.sendRedirect("/day15/responseDemo2");
    ```

    **重定向的特点：redirect**

    ​	1.地址栏发生变化

    ​	2.重定向可以访问其他站点(服务器)的资源

    ​	3.重定向是两次请求，不能使用request对象来共享数据

    **转发的特点：forward**

    ​	1.转发地址栏路径不变

    ​	2.转发只能访问当前服务器下的资源

    ​	3.转发是一次请求，可以使用request对象来共享数据

    **路径写法：**

    ​	1.路径分类：

    ​			1.相对路径：通过相对路径不可以确定唯一资源

    ​					如：./index.html
    ​					不以/开头，以.开头的路径

    ​					规则：找到当前资源和目标资源之间的相对位置关系
    ​								./：当前目录
    ​								../：后退一级目录

    ​			2.绝对路径：通过绝对路径可以确定唯一资源

    ​					如：http://localhost/day15/responseDemo2          /day15/responseDemo
    ​					以/开头的路径

    ​					规则：判断定义的路径给谁用？判断请求将来从哪里来
    ​								给客户端浏览器使用：需要加虚拟目录(项目的访问路径)

    ​									建议虚拟目录动态获取：request.getContextPath()
    ​									<a>,<form> 重定向

    ​								给服务器使用：不需要加虚拟目录

    ​									转发路径

    

  + **服务器输出字符数据到浏览器**

    步骤：

    + 获取字符输出流
    + 输出数据

    注意：

    + 乱码问题：

      1.PrintWriter pw = response.getWriter();	获取的流默认编码是ISO-8859-1

      2.设置该流的默认编码

      3.告诉浏览器响应体使用的编码

      //简单的形式，设置编码，实在获取流之前设置

      response.setContentType("text/html;charset=utf-8");

  + **服务器输出字节数据到浏览器**

    步骤：

    + 获取字节输出流
    + 输出数据

  + **验证码**

    + 本质：一张图片
    + 目的：防止恶意表单注册



+ ServletContext对象

  + 概念：代表整个web应用，可以和程序的容器(服务器)来通信

  + 获取：

    + 通过request对对象获取

      request.getServletContext();

    + 通过HttpServlet获取

      this.getServletContext();

  + 功能：

    + 获取MIME类型：

      MIME类型：在互联网通信过程中定义的一种文件数据类型

      ​	格式：大类型/小类型	text/html	image/jpeg

      获取：String getMimeType(String file)

    + 域对象：共享数据

      setAttribute(String name,object value)

      getAttribute(String name)

      removeAttribute(String name)

      ***ServletContext对象范围：所用用户所有请求的数据***

    + 获取文件的真实(服务器)路径

      方法：String getRealPath()

      ```
      //web目录下资源访问
      String b = context.getRealPath("/b.btxt");
      System.out.println(b);aaac
      //WEB-INF目录下的资源访问c
      String a = context.getRebalPath("/a.txt");c
      System.out.println(a);
      //src目录下的资源访问
      String c = context.getRealPath("/c.txt");
      System.out.println(c);
      ```



#### 会话技术(Cookie,Session)

**会话：**一次会话中包含多次请求和响应
	一次会话：浏览器第一次给服务器资源发送请求，会话建立，直到有一方断开为止

**功能：**在一次会话的范围内的多次请求间，共享数据

**方式：**

​	客户端会话技术：Cookie

​	服务器端会话技术：Session



+ Cookie

  + 概念：客户端会话技术，将数据保存到客户端

  + 快速入门

    创建Cookie对象，绑定数据 
    		new Cookie(String name,String value)

    发送数据
    		response.addCookie(Cookie cookie)

    获取Cookie，拿到数据
    		Cookie[]	request.getCookies()

  + 原理分析

    基于响应头set-cookie和请求头cookie实现

    <img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20211014164139470.png" alt="image-20211014164139470" style="zoom:50%;" />

  + Cookie的细节

    + 一次可以创建多个Cookie对象，使用response调用多次addCookie方法发送cookie即可

    + 默认情况下，当浏览器关闭后，Cookie数据被销毁

      持久化存储：setMaxAge(int seconds)

      ​						正数：将cookie数据写到硬盘的文件中，持久化存储。cookie存活时间

      ​						负数：默认值

      ​						零：删除cookie信息

    + 在tomcat 8 之前 cookie中不能直接存储中文数据
      需要将中文数据转码-----一般采用URL编码(%E3)

      在tomcat 8 之后 cookie支持中文数据

    + Cookie共享问题：

      + 假设在一个tomcat服务器中，部署了多个web项目
        默认情况下cookie不能共享

        setPath(String path)：设置cookie的获取范围，默认情况下，设置当前的虚拟目录
        如果要共享，则可以将path设置为"/"

      + 不同的tomacat服务器间cookie共享问题
        setDomain(String path)：如果设置一级域名相同，那么多个服务器之间cookie可以共享

        例如：setDomain(".baidu.com"），那么tieba.baidu.com和news.baidu.com中cookie可以共享

  + Cookie的特点和作用

    ***特点：***

    + cookie存储数据在客户端浏览器
    + 浏览器对于单个cookie的大小有限制(4kb)以及 对同一个域名下的总cookie数量有限制(20个)

    ***作用：***

    + cookie一般用于存储少量的不太敏感的数据
    + 在不登陆的情况下，完成服务器对客户端的身份识别



+ JSP：入门学习

  **概念：**

  ​	Java Server Pages：java服务器端页面

  ​		可以理解为：一个特殊的页面，其中既可以指定定义html标签，又可以定义java代码
  ​		用于简化书写！！！

  **原理：**

  ​	JSP本质上就是一个Servlet

  **JSP的脚本：JSP定义Java代码的方式**

  ​	**<%	代码	%>：**定义的java代码，在service方法中；service方法中可以定义什么，此脚本中就可以定义什么。
  ​	**<%!	代码	%>：**定义的java代码，在jsp转换后的java类的成员位置
  ​	**<%=	代码	%>：**定义的java代码，会输出到页面上；输出语句中可以定义什么，该脚本中就可以定义什么。

  **JSP内置对象：**

  ​	在jsp页面中不需要获取和创建，可以直接使用的对象

  ​	jsp一共有9个内置对象
  ​	先了解3个：
  ​		request
  ​		response
  ​		out：字符输出流对象。可以将数据输出到页面上。和response.getWriter()类似

  ​				response.getWriter()和out.write()的区别：

  ​					在tomcat服务器真正给客户端做出响应之前，会先找response缓冲区数据，再找out缓冲区数据。

  ​					response.getWriter()数据输出永远在out.write()之前



+ Session：

  + 概念：服务器端会话技术，再一次会话的多次请求间共享数据，将数据保存在服务器端的对象中。HttpSession

  + 快速入门：

    获取HttpSession对象：

    ​		HttpSession session = request.getSession();

    使用HttpSession对象：

    ​		Object getAttribute(String name)
    ​		void setAttribute(String name,object value)
    ​		void removeAttribute(String name)

  + 原理：

    ***Session的实现是依赖于Cookie的***

  + 细节：

    + 当客户端关闭后，服务器不关闭，两次获取session是否为同一个？

      默认情况下不是。
      如果需要相同，则可以创建Cookie，键为JSESSIONID，设置最大存活时间，让cookie持久化保存。

      ```
      Cookie c  = new Cookie("JSESSIONID",session.getId());
      c.setMaxAge(60*60);
      response.addCookie(c);
      ```

    + 客户端不关闭，服务器关闭后，两次获取的session是同一个吗？

      不是同一个，但是要确保啊数据不丢失

      ​	session的钝化：
      ​			在服务器正常关闭之前，将session对象序列化到硬盘上
      ​	session的活化：
      ​			在服务器启动后，将session文件转化为内存中session对象即可。

    + session什么时候被销毁？

      ​	服务器关闭

      ​	session对象调用invalidate().

      ​	session默认失效时间 30分钟

      ​			选择性配置修改
      ​		<session-config>
      ​				<session-timeout>30</session-timeout>
      ​		</session-fonfig>

    + session的特点

      session用于存储一次会话的多次请求的数据，存在服务器

      session用于存储任意类型，任意大小的数据

      ***session与Cookie的区别：***

      ​	session存储数据在服务器端，Cookie在客户端

      ​	session没有数据大小限制，Cookie有

      ​	session数据安全，Cookie相对于不安全

#### JSP

+ 指令

  + 作用：用于配置JSP页面，以及导入资源文件

  + 格式：

    ​	<%@	指令名称	属性名1=属性值1	属性名2=属性值2...	%>

  + 分类：

    + page：配置JSP页面的

      1.contentType：等同于response.setContentType()
      	设置响应体的mime类型以及字符集
      	设置当前jsp页面的编码(只能是高级的IDE才能生效，如果使用低级工具，则需要设置pageEncoding属性设置当前页面的字符集)

      2.import：导包

      3.errorPage：当前页面发生异常后，会自动跳转到指定的错误页面

      4.isErrorPage：标识当前页面是否是错误页面
      	true：是，可以使用内置对象exception
      	false：否，默认值。不可以使用内置对象exception

    + include：页面包含的，导入页面的资源文件
      <%@include  file="top.jsp"%>

    + taglib：导入资源
      <%@ taglib prefix="c" uri="http://java.sun.com/jstl/core" %>
                    prefix：前缀，自定义的

+ 注释：

  + html注释：
    <! --     -->：只能注释html代码片段  
  + jsp注释：推荐使用
    <%--    --%>：可以注释所有

+ 内置对象

  + 在jsp页面中不需要创建，直接使用对象
    一共有9个

    |   变量名    |      真实类型       |                     作用                     |
    | :---------: | :-----------------: | :------------------------------------------: |
    | pageContext |     PageContext     | 当前页面共享数据，还可以获取其他八个内置对象 |
    |   request   | HttpServletRequest  |         一次请求访问的多个资源(转发)         |
    |   session   |     HttpSession     |             一次会话的多个请求间             |
    | application |   ServletContext    |              所有用户间共享数据              |
    |  response   | HttpServletResponse |                   响应对象                   |
    |    page     |       Object        |        当前页面(Servlet)的对象   this        |
    |     out     |      JspWriter      |          输出对象，数据输出到页面上          |
    |   config    |    ServletConfig    |              Servlet的配置对象               |
    |  exception  |      Throwable      |                   异常对象                   |



#### MVC：开发模式

+ **jsp演变历史** 

  + 早期只有Servlet，只能使用response输出标签数据，非常麻烦
  + 后来有了jsp，简化了Servlet的开发，如果过度使用jsp，在jsp中写大量的java代码，又写html表，造成难于维护，难于分工协作
  + 在后来，java的web开发，借鉴mvc开发模式，是的程序的设计更加合理

+ MVC：

  + M：model，模型
    + 完成具体的业务操作，如，查询数据库，封装模型
  + V：View，视图
    + 展示数据
  + C：Controller，控制器。Servlet
    + 获取用户的输入
    + 调用模型
    + 将数据交给视图进行展示

  优缺点：

  + 优点：
    + 耦合性低，方便维护，可以利用分工协作
    + 重用性高
  + 缺点：
    + 使得项目架构变得复杂，对开发人员要求高

#### EL表达式

概念：Expression Language

作用：替换和简化jsp页面中java代码的编写

语法：${表达式}

注意：
	jsp默认支持el表达式。如果忽略el表达式
		1.设置jsp中page指令中：isELIgnored="true", 忽略当前jsp页面中所有的el表达式
		2.\\${表达式}：忽略当前这个el表达式

使用：

+ 运算：
  	运算符：
  	算数运算符：+   -  *   /(div)   %(mod)
  	比较运算符： >   <  >=    <=   ==    !=
  	逻辑运算符： &&(and)   ||(or)     !(not)
  	空运算符：empty        
  			功能：用于判断字符串，集合，数据对象是否为null并且长度是否为0
  			${empty list}：判断字符串，集合，数据对象是否为null并且长度是否为0
  			${not empty str}：表示判断字符串，集合，数组对象是否不为null  并且  长度>0

+ 获取值:

  + el表达式只能从域对象中获取值

  + 语法：

    + ${与名称.键名}：从指定域中获取指定键的值
      ==**域名称：(从上往下域的范围从小到大)**==
      	pageScope				---->	pageContext
      	requestScope		   ---->	request
      	sessonScope			 ---->	session
      	applicationScope	  ---->	application(ServletContext)
      举例：在request域中存储了name=张三
      获取：${requestScope.name}

    + ${键名}：表示一次从最小的域中查找是否有该键对应的值，直到找到为止。

    + 获取对象，List集合，Map集合的值

      1.对象：${域名称.键名.属性名}
      	本质上会去调用对象的getter方法

      2.List集合：${域名称.键名[索引]}

      3.Map集合：
      	${域名称.键名.key名称}
      	${域名称.键名["key名称"]}

+ 隐式对象：

  el表达式中有11个隐式对象

  pageContext：
  	获取jsp其他八个内置对象
  			${pageContext.request.contextPath}：动态获取虚拟目录

#### JSTL

+ 概念：JavaServer Pages Tag Library	JSP标准标签库

  是由Apache组织提供的开源的免费的jsp标签	<标签>

+ 作用：用于简化和替换jsp页面上的java代码

+ 使用步骤

  + 导入jstl相关jar包
  + 引入标签库：taglib指令：<%@ taglib %>
  + 使用标签

+ 常用的JSTL标签

  + if			：相当于java代码的if语句

    + 属性：

      test 必须属性，接受boolean表达式
      		如果表达式为true，则显示if标签体内容，如果为false，则不显示标签体内容
      		一般情况下，test属性值会结合el表达式一起使用

    + 注意：

      c：if标签没有else情况，如果想要else情况，则可以再定义一个c：if标签

  + choose  ：相当于java代码的switch语句

    + 使用choose标签声明			相当于switch声明
    + 使用when标签做判断           相当于case
    + 使用otherwise标签做其他情况的声明      相当于default

  + foreach  ：相当于java代码的for语句

    + 完成重复的操作
      begin：开始值
      end：结束值
      var：临时变量
      step：步长
      varStatus：循环状态对象
      			index：容器中元素的索引   从0开始
      			count：循环次数  从1开始
    + 遍历容器
      items：容器对象
      var：容器中元素的临时变量
      varStatus：循环状态对象
                  index：容器中元素的索引   从0开始
      			count：循环次数  从1开始

#### 三层架构：软件设计框架

界面层(表示层)：用户看到的界面。用户可以通过界面上的组件和服务器进行交互

业务逻辑层：处理业务逻辑的

数据访问层：操作数据存储文件

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20211019152636537.png" alt="image-20211019152636537" style="zoom:80%;" />

#### Filter：过滤器

+ 概念：

  生活中的过滤器： 净水器，空气净化器，土匪

  web中的过滤器：当访问服务器资源时，过滤器可以将请求拦截下来，完成一些特殊的操作。

  过滤器的作用：

  ​	一般用于完成通用的操纵。如：登录验证，统一的编码处理，敏感字符过滤...

+ 快速入门:

  步骤：

  + 定义一个类，实现接口Filter
  + 复写方法
  + 配置拦截路径
    + web.xml
    + 注解

+ 过滤器细节：

  + web.xml配置

    ```
    <filter>
    	<filter-name>demo</filter-name>
    	<filter-class>cn.itcast.web.filter.filterDemo</filter-class>
    </filter>
    <filter-mapping>
    	<filter-name>demo</filter-name>
    	<!-- 拦截路径 -->
    	<url-pattern>/*<url-pattern>
    </filter-mapping>
    ```

  + 过滤器执行流程

    + 执行过滤器
    + 执行放行后的资源
    + 回来执行过滤器放行代码下边的代码

  + 过滤器生命周期方法

    + init：在服务器启动后，会创建Filter对象，然后调用init方法，只执行一次，用于加载资源
    + doFilter：每一次请求被拦截资源时，会执行，执行多次。
    + destroy：在服务器关闭后，Filter对象会被销毁，如果服务器是正常关闭，则会执行destroy方法，只执行一次，用于释放资源。

  + 过滤器配置详解

    + 拦截路径配置：

      1.具体的资源路径：/index.jsp	只有访问index.jsp资源时，过滤器才会被执行

      2.拦截目录：/user/*	访问/user 下的所有资源时，过滤器都会被执行

      3.后缀名拦截：*.jsp	访问所有后缀名为jsp资源时，过滤器会被执行

      4.拦截所有资源：/*	访问所有资源时间，过滤器都会被执行

    + 拦截方法配置：资源被访问的方式

      1.注解配置：

      ​	设置dispatcherTypes属性

      ​		1.REQUEST：默认值。浏览器直接请求资源

      ​		2.FORWARD：转发访问资源

      ​		3.INCLUDE：包含访问资源

      ​		4.ERROR：错误跳转资源

      ​		5.ASYNC：异步访问资源

      2.web.xml配置

      ​	设置<dispatcher></dispatcher>标签即可

  + 过滤器链(配置多个过滤器)

    + 执行顺序：如果有两个过滤器：过滤器1和过滤器2

      1.过滤器1

      2.过滤器2

      3.资源执行

      4.过滤器2

      5.过滤器1

    + 过滤器先后顺序问题：

      1.注解配置：按照类名的字符串比较规则比较，值小的先执行

      ​	如：AFilter和BFilter，AFilter就先执行了

      2.web.xml配置：<filter-mapping>谁定义在上边，谁先执行
    
  + 增强对象的功能

    ***设计模式：一些通用的解决固定问题的方式***

    + 装饰模式

    + 代理模式
      概念：1.真实对象：被代理的对象
      			2.代理对象
      			3.代理模式：代理对象代理真实对象，达到增强真实对象功能的目的

      实现方式：
      		静态代理：有一个类文件描述代理模式
      		动态代理：在内存中形成代理类
      					实现步骤： 1.代理对象和真实对象实现相同的接口
      										2.代理对象 = Proxy.newProxyInstance();
      										3.使用代理对象调用方法
      										4.增强方法

      ​					增强方式： 1.增强参数列表
      ​										2.增强返回值类型
      ​										3.增强方法体执行逻辑									

#### Listener：监听器

+ 概念：web的三大组件之一

  ​	事件监听机制

  ​		事件：一件事情

  ​		事件源：事件发生的地方

  ​		监听器：一个对象

  ​		注册监听：将事件，事件源，监听器绑定在一起，当事件源上发生某个事件后，执行监听器代码。

+ ServletContextListener：监听ServletContext对象的创建和销毁

  + 方法
    + void contextDestroyed(ServletContextEvent sce)：ServletContext对象被销毁之前会调用该方法
    + void contextInitidlized(ServletContextEvent sce)：ServletContext对象创建后会调用该方法

  + 步骤

    + 定义一个类，实现ServletContextListener接口

    + 复写方法

    + 配置
      1.web.xml

      ```
      <listener>
      	<listener-class>
      		cn.itcast.web.listener.ContextLoaderListener
      	</listener-class>
      </listener>	
      ```

      指定初始化参数<context-param>

      2.注解
      @webListener



#### JQuery

+ 概念：一个JavaScript框架，简化JS开发。

  JQuery是一个快速，简洁的JavaScript框架，是继Prototype之后的又一个优秀的JavaScript代码库(或JavaScript框架)。jQuery设计的宗旨就是"write Less,Do More"，即倡导写更少的代码，做更多的事情，它封装JavaScript常用的功能代码，提供一种简便的JavaScript设计模式，优化HTML文档操纵，事件处理，动画设计和Ajax交互。

  JavaScript框架：本质上就是一些js文件，封装了js的原生代码而已

+ 快速入门

  1.步骤：
  	1.下载JQuery

  ​	jquery-xxx.js和jquery-xxx.min.js区别
  ​		1，jquery-xxx.js：开发版本。给程序员看的，有良好的缩进和注释，体积大一些。
  ​		2，jquery-xxx.min.js：生产版本。程序中使用，没有缩进，体积小一些。程序加载更快。

  ​	2.导入JQuery的js文件
  ​	3.使用
  ​		var div1 = $("#div1");
  ​		alert(div1.html());

+ JQuery对象和JS对象的区别和转换

  1.JQuery对象在操作使，更加方便。
  2.JQuery对象和js对象的方法是不通用的。
  3.两者互相转换
  	jq  --->  js：jq对象[索引]  或者 jq对象.get(索引)
  	js  ---->  jq：$(js对象)

+ 选择器：筛选具有相似特征的元素(标签)

  + 基本操作学习：

    + 事件绑定：

      ```
      //1.绑定b1按钮
      $("#b1").click(function(){
      	alert("abc");
      });
      ```

    + 入口函数：

      ```
      $(function (){
      
      });
      ```

      window.onload  和 $(function) 区别

      ​	window.onload只能定义一次，如果定义多次，后边的会将前面的覆盖掉
      ​	$(function)可以定义多次的。

    + 样式控制：css方法

      ```
      //$("#div1").css("background-color","red");
      $("#div1").css("backgroundColor","pink");
      ```

  + 分类：

    + 基本选择器

      + 标签选择器(元素选择器)
        语法：$("html标签名")   获得所有匹配标签名称的元素

      + id选择器
        语法：$("#id的属性值")  获得与指定id属性值匹配的元素

      + 类选择器

        语法：$(".class的属性")  获得与hiding的class属性值匹配的元素

      + 并集选择器

        语法：$("选择器1，选择器2.....")  获取多个选择器选中的所有元素

    + 层级选择器

      + 后代选择器

        语法：$("A B ") 选择A元素内部的所有B元素

      + 子选择器

        语法：$("A > B ") 选择A元素内部的所有B子元素

    + 属性选择器

      + 属性名称选择器

        语法：$("A[属性名]")  包含指定属性的选择器

      + 属性选择器

        语法：$("A[属性名='值']")  包含指定属性等于指定值的选择器

      + 复合属性选择器

        语法：$("A[属性名='值'] [] ...")  包含多个属性条件的选择器

    + 过滤选择器

      + 首元素选择器

        语法：	:first   获得选择的元素中的第一个元素

      + 尾元素选择器

        语法：	:last	获得选择的元素中的最后一个元素

      + 非元素选择器

        语法：	:not(selecter)	不包括指定内同的元素

      + 偶数选择器

        语法：	:even  偶数，从0开始计数

      + 奇数选择器

        语法：	:odd	奇数，从0开始计数

      + 等于索引选择器

        语法：	:eq(index) 	指定索引元素

      + 大于索引选择器

        语法：	:gt(index)	大于指定索引元素

      + 小于索引选择器

        语法：	:lt(index)	小于指定索引元素

      + 标题选择器

        语法：	:header	获得标题元素，固定写法
      
    + 表单过滤选择器
    
      + 可用元素选择器
    
        语法：	:enabled	获得可用元素
    
      + 不可用元素选择器
    
        语法：	:disabled	获得不可用元素
    
      + 选中选择器
    
        语法：	:checked	获得单选/复选框中的元素
    
      + 选中选择器
    
        语法：	:selected	获得下拉框中的元素
  
+ DOM操作

  + 内容操作

    + html()：获取/设置元素的标签体内容	

      ```
      <a><font>内容</font></a>		---》 <font>内容</font>
      ```

    + text()：获取/设置元素的标签体纯文本内容

      ```
      <a><font>内容</font></a>		---》  内容
      ```

    + val()：获取/设置元素的value属性

  + 属性操作

    + 通用属性操作
      + attr()：获取/设置元素的属性值
      + removeAttr()：删除属性
      + prop()：获取/设置元素的属性值
      + removePrep()：删除属性

      ***==attr和prop的区别：==***

      ​	***如果操作的是元素的固有属性，则建议使用prop***

      ​	***如果操作的是元素自定义的属性，则建议使用attr***

    + 对class属性操作

      + addClass()：添加class属性值
      + removeClass()：删除class属性值
      + toggleClass()：切换class属性值
      + css()：

      ***toggleClass("one"):***

      ***判断如果元素对象上存在class="one",则将属性值one删除掉，如果元素对象上不存在class="one"，则添加***

  + CRUD操作

    + append()：父元素将子元素追加到末尾

      对象1.append(对象2)：将对象2添加到对象1元素内部，并且在末尾

    + perpend()：父元素将子元素追加到开头

      对象1.prepend(对象2)：将对象2添加到对象1元素内部，并且在开头

    + appendTo()：

      对象1.appendTo(对象2)：将对象1添加到对象2内部，并且在末尾

    + prependTo()：

      对象1.prependTo(对象2)：将对象1添加到对象2内部，并且在开头

    + after()：添加元素到元素后边

      对象1.after(对象2)：将对象2添加到对象1后边，对象1和对象2是兄弟关系

    + before()：添加元素到元素前边

      对象1.before(对象2)：将对象2添加到对象1前边，对象1和对象2是兄弟关系

    + insertAfter()：

      对象1.insertAfter(对象2)：将对象1添加到对象2后边，对象1和对象2是兄弟关系

    + insertBefore()：

      对象1.insertBefore(对象2)：将对象1添加到对象2前边，对象1和对象2是兄弟关系

    + remove()：移除元素

      对象.remove()：将对象删除掉

    + empty()：清空元素的所有后代元素

      对象.empty()：将对象的后代元素全部请空，但是保留当前对象的及其属性节点
      
      

#### JQuery高级

+ 动画：

  + 三种显示和隐藏方式

    + 默认的显示和隐藏方式

      + show([speed],[easing],[fn])

        参数：

        ​	speed：动画的速度，三个于定义的值("slow","normal","fast")

        ​	easing：用来指定切换的效果，默认是"swing" ,可用参数"linear"

        ​				swing：动画执行时效果是 先慢，中间快，最后又慢

        ​				linear：动画执行时速度时匀速的

        ​	fn：在动画完成时执行的函数，每个元素执行一次

      + hide([speeed],[easing],[fn])

      + toggle([speed],[easing],[fn])

    + 滑动显示和隐藏方式

      + slideDown([speed],[easing],[fn])
      + slideUp([speed],[easing],[fn])
      + slideToggle([speed],[easing],[fn])

    + 淡入淡出显示和隐藏方式

      + fadeIn([speed],[easing],[fn])
      + fadeOut([speed],[easing],[fn])
      + fadeToggle([speed],[easing],[fn])

+ 遍历

  + js的遍历方式

    + for(初始化值，循环结束条件，步长)

  + jq的遍历方式

    + jq对象.each(callback) 

      语法：

      ​	jquery对象.each(function(index,element){ });

      ​	index：就是元素在集合中的索引

      ​	element：就是集合中的每一个元素对象

      ​	this：集合中的每一个元素对象

      回调函数返回值：

      ​	false：如果当前function返回为false，则结束循环(break)

      ​	true：如果当前function返回值为true，则结束本次循环，继续下一次循环(continue)

    + $.each(object,[callback])

    + for..of：jQuery 3.0之后提供的方式

      for(元素对象  of   容器对象)

    

+ 事件绑定

  + jquery标准的绑定方式

    jq对象.事件方法(回调函数);

    ​	注意：如果调用事件方法，不传递回调函数，则会出发浏览器默认行为

    ​				表单对象.submit();//让表单提交

  + on绑定事件/off解除绑定

    jq对象.on("事件名称",回调函数)

    jq对象.off("事件名称")

    ​	注意：如果off方法不传递任何参数，则将组件上的所有事件全部解绑

  + 事件切换：toggle

    jq对象.toggle(fn1,fn2...)

    ​	注意：当单机jq对象对应的组件后，会执行fn1，第二次点击会执行fn2....

    ​				1.9版本  toggle（）方法删除，jQuery Migrate（迁移）插件可以恢复此功能

    ```
    <script 
    	src="../js/jquery-mirage-1.0.0.js" type="text/javascript" charset="utf-8">
    </script>
    ```

    

+ 插件：增强JQuery的功能

  + 实现方式：

    + $.fn.extend(object)

      增强通过Jquery获取的对象的功能  $.("#id")

    + $.extend(object)

      增强JQuery对象自身的功能   $/jQuery

      

#### AJAX

+ 概念：ASynchronous JavaScript And XML	异步的JavaScript  和  XML

  + 异步和同步：客户端和服务器端相互通信的基础上

    + 同步：客户端必须等待服务器端的响应，在等待的期间客户端不能做其他事情

    + 异步：客户端不需要等待服务器端的响应，在服务器处理请求的过程中，客户端可以进行其他的操作。

    + Ajax是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术

      通过在后台与服务器进行少量的数据交换，Ajax可以使网页实现异步更新，这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

      传统的网页(不适用 Ajax)  如果需要更新内容，必须重新加载整个网页页面。

      提升用户体验。
      
      

+ 实现方式

  + 原生的JS实现方式

  + JQuery实现方式

    + $.ajax()

      ```
      语法：$.ajax({键值对});
      //使用$.ajax()发送异步请求
      $.ajax({
      	url:"ajaxServlet" ,//请求路径
      	type:"POST" ,//请求方式
      	//data:{"username":"jack","age":23} ,//请求参数
      	data:"username=jack&age=23" ,
      	success:function(data){
      		alert(data);
      	},//响应成功后的回调函数
      	error:function(){
      		alert("出错了。。。");
      	},//表示如果请求响应出现错误，会执行的回调函数
      	
      	dataType:"text"//设置接受到的响应数据的格式
      });
      ```

    + $.get()：发送get请求

      ```
      语法：$.get(url,[data],[callbakc],[type])
      参数：
      	url：请求路径
      	data：请求参数
      	callback：回调函数
      	type：响应结果的类型
      ```

      

    + $.post()

#### JSON

+ 概念：JavaScript Object Notation		JavaScript对象表示法

  ​	Person p = new Person();
  ​	p.setName("张三");
  ​	p.setAge(23);
  ​	p.setGender("男");

  ​	var p = {"name":"张三","age":23,"gender":"男"};

  ​	json现在多用于存储和交换文本信息的语法

  ​	进行数据的传输

  ​	JSON 比 XML 更小，更快，更易解析。

+ 语法

  + 基本规则

    + 数据在名称/值对中：json数据是由键值对构成的
      键用引号(单双都可)引起来，也可以不使用引号
      值的取值类型：
      	1.数字（整数或浮点数）
      	2.字符串（在双引号中）
      	3.逻辑值（true 或 false）
      	4.数组（在方括号中）{"persons":[{},{}]}
      	5.对象（在花括号中）{"address":{"provice":"陕西"....}}
      	6.null
    + 数据由逗号分隔：多个键值对由逗号分隔
    + 花括号保存对象：使用{}定义json格式
    + 方括号保存数据：[]

  + 获取数据

    + json对象.键名

    + json对象["键名"]

    + 数据对象[索引]

    + 遍历

      ```
      //1.定义基本格式
      var person = {"name":"张三","age":23,"gender":true};
      
      var ps = [{"name":"张三","age":23,"gender":true},
      		  {"name":"李四","age":24,"gender":true},
                {"name":"王五","age":25,"gender":false},
      		];
      		
      //获取person对象中的所有键和值
      //for in 循环
      for(var key in person){
      	//这样的方式获取不行。因为相当于 person."name"
      	//alert(key + ":" +person.key);
      	alert(key+":"+person[key]);
      }
      //获取ps中的所有值
      for(var i = 0;i<ps.length;i++){
      	var p = ps[i];
      	for(var key in p){
      		aleret(key+":"+p[key]);
      	}
      }
      ```

+ JSON数据和Java对象的相互转换

  + JSON解析器：

    常见的解析器：Jsonlib，Gson，fastjson，jackson

  + JSON转为Java对象

    使用步骤：

    + 导入jackson的相关jar包

    + 创建jackson核心对象 ObjectMapper

    + 调用ObjectMapper的相关方法进行转换

      ```
      1.转换方法：
      readValue(json字符串数据,Class)
      ```

      

  + Java对象转换为JSON

    使用步骤：

    + 导入jackson的相关jar包

    + 创建jackson核心对象 ObjectMapper

    + 调用ObjectMapper的相关方法进行转换

      ```
      1.转换方法：
      writeValue(参数1，obj):
      	参数1：
      		File：将obj对象转换为JSON字符串，并保存到指定的文件中
      		Writer：将obj对象转换位JSON字符串，并将json数据填充到字符输出流中
      		OutputStream：将obj对象转换为JSON字符串，并将json数据填充到字节输出流中
      writeValueAsString(obj)：将对象转为json字符串
      2.注解
      	1.@JsonIgnore：排除属性
      	2.@JsonFormat：属性值的格式化
      	
      3.复杂的java对象转换
      	1.List：数组
      	2.Map：对象格式一致
      ```

+ 案例：检验用户名是否存在

  1.服务器响应的数据，在客户端使用时，要想当作json数据格式使用

  ​	1.$.get(type)：将最后一个参数type指定为"json"

  ​	2.在服务器端设置MIME类型

  ​		response.setContentType("application/json;charset=utf-8");
  
  

#### redis

+ 概念：redis是一款高性能的NOSQL系列的非关系型数据库

  关系型数据库与NOSQL数据库并非对立而是互补的关系，即通常情况下使用关系型数据库，在适合使用使用NOSQL的时候使用NOSQL数据库，让NOSQL数据库对关系型数据库的不足进行弥补。

  Redis是用C预言开发的一个开源的高性能键值对(key-value)数据库，官方提供测试数据，50个并发执行100000个请求，读的速度是110000次/秒，写的速度是81000次/秒，且Redis通过提供多种键值数据类型来适应不同场景下的存储需求，目前位置Redis支持的键值数据类型如下：

  ​	1.字符串类型	string

  ​	2.哈希类型	hash

  ​	3.列表类型	list

  ​	4.集合类型	set

  ​	5.有序集合类型	sortedset

  redis应用的场景：

  ​	1.缓存(数据查询，短链接，新闻内容，商品内容等等)

  ​	2.聊天室的在线好友列表

  ​	3.任务队列。(秒杀，抢购，12306等等)

  ​	4.应用排行榜

  ​	5.网站访问统计

  ​	6.数据过期处理(可以精确到毫秒)

  ​	7.分布式集群架构中的session分离

+ 下载安装

  + 官网redis.io
  + 中文网redis.net.cn
  + 解压直接可以使用
    + redis.windows.conf：配置文件
    + redis-cli.exe：redis的客户端
    + redis-server.exe：redis的服务器端

+ 命令操作

  + redis的数据结构：

    + redis存储的是：key,value格式的数据，其中key都是字符串，value有5种不同的数据结构

      ​	1.字符串类型	string

      ​	2.哈希类型	hash	：map格式

      ​	3.列表类型	list		：Linkedlist格式

      ​	4.集合类型	set		：

      ​	5.有序集合类型	sortedset

  + 字符串类型：string

    存储：set key value

    获取：get key

    删除：del key

  + 哈希类型：hash

    存储：hset key field value

    获取：

    ​		hget key field：获取指定的field对象的值

    ​		hgetall key：获取所有的field和value

    删除：hdel key field

  + 列表类型 list：简单的字符串列表，按照插入顺序排序。可以添加一个元素到列表的头部(左边)和尾部(右边)

    存储：

    ​		1.lpush key value：从左边存入列表

    ​		2.rpush key value：从右边存入列表
    
    获取：
    
    ​		lrange key start end：范围获取
    
    删除：
    
    ​		lpop key：从列表的最左边移除一个元素，并将元素返回
    
    ​		rpop key：从列表的最右边移除一个元素，并将元素返回
    
  + 集合类型  set   ：不允许重复元素
  
    存储：sadd key value
  
    获取：smembers key：获取set集合中所有元素
  
    删除：srem key value：删除set集合中的某个元素
  
  + 有序集合类型 sortedset：不允许重复的元素，且元素有序
  
    存储：zadd key score calue：
  
    获取：zrange key start end：
  
    删除：zrem key value：
  
  + 通用命令：
  
    + keys *：查询所有的键
    + type key：获取键对应的value类型
    + del key：删除指定的key value
  
+ 持久化

  + redis是一个内存数据库，当redis服务器重启，获取电脑重启，数据会丢失，我们可以将redis内存中的数据持久化保存到硬盘文件中

  + redis持久化机制：

    + RDB：默认方式，不需要进行配置，默认就使用这种机制

      在一定的间隔时间中，检测key的变化情况，然后持久化数据

      1.编辑redis.windows.conf文件

      ```
      #	after 900 sec (15min) if at least 1 key changed
      sava 900 1
      #	after 300 sec (5min) if at least 10 keys changed
      sava 300 10
      #	after 60 sec (1min) if at least 10000 keys changed
      sava 60  10000
      ```

      2.重新启动redis服务器，并指定配置文件名称

      D:\JavaWeb2018\day23_redis\资料\redis\windows-64\redis-2.8.9>redis-server.exe redis.windows.conf

    + AOF：日志纪录的方式，可以纪录每一条命令的操作，可以每一次命令操作后，持久化数据

      1.编辑redis.windows.conf文件

      appendonly no (关闭aof)  -->   appendonly yes  (开启aof)

      #appendfsync always：每一次操作都进行持久化

      appendfsync everysec：每隔一秒进行一次持久化

      #appendfsync  no		：不进行持久化

      

+ Java客户端   Jedis

  Jedis：一款java操作redis数据库的工具

  使用步骤：

  + 下载jedis的jar

  + 使用

    ```
    //1.获取连接
    Jedis jedis = new Jedis("localhost",6379);
    //2.操作
    jedis.set("username","zhangsan");
    //3.关闭连接
    jedis.close();
    ```

  + Jedis操作各种redis中的数据结构

    + 1.字符串类型 string
      set
      get

      ```
      //获取连接
      Jedis jedis = new Jedis();//如果使用空参构造，默认值"localhost",6379端口
      //操作
      //存储
      Jedis.set("username","zhangsan");
      //获取
      String username = jedis.get("usesrname");
      System.out.println(username);
      //可以使用setex()方法存储可以指定过期时间的key value
      jedis.setex("activecode",20,"hehe");//将activecode：hehe键值对存入redis，并且在20秒后自动删除该键值对
      //关闭连接
      jedis.close();
      ```

      

    + 2.哈希类型 hash：map格式

      hset

      hget

      hgetAll

      ```
      //获取连接
      Jedis jedis = new Jedis();//如果使用空参构造，默认值"localhost",6379端口
      //操作
      //存储
      jedis.hset("user","name","lisi");
      jedis.hset("user","age","23");
      jedis.hset("user","gender","female");
      //获取hash
      String name = jedis.hget("user","name");
      System.out.println(name);
      //获取hash的所有map中的数据
      Map<String,String> user = jedis.hgetAll("user");
      //keyset
      Set<String> keyset = user.keySet();
      for(String key:keySet){
      	//获取value
      	String value = user.get(key);
      	System.out.println(key + ":" + value);
      }
      //关闭连接
      jedis.close();
      ```

      

    + 3.列表类型 list：linkedlist格式。支持重复元素

      lpush / rpush

      lpop / rpop

      lrange start end

      ```
      //获取连接
      Jedis jedis = new Jedis();//如果使用空参构造，默认值"localhost",6379端口
      //操作
      //存储
      jedis.lpush("mylist","a","b","c");//从左边存
      jedis.rpush("mylist","a","b","c");//从右边存
      //list范围获取
      List<String> mylist = jedis.lrange("mylist",0,-1);
      System.out.println(mylist);
      //list弹出
      String element1 = jedis.lpop("mylist");//c
      String element2 = jedis.rpop("mylist");//c
      //关闭连接
      jedis.close();
      ```

      

    + 4.集合类型 set ：不允许重复元素

      sadd

      smembers：获取所有元素

      ```
      //获取连接
      Jedis jedis = new Jedis();//如果使用空参构造，默认值"localhost",6379端口
      //操作
      //存储
      jedis.sadd("myset","java","php","c++");
      //获取
      Set<String> myset = jedis.smembers("myset");
      System.out.println(myset);
      //关闭连接
      jedis.close();
      ```

      

    + 5.有序集合类型 sortedset：不允许重复元素，且元素有顺序

      zadd

      zrange start end

      ```
      //获取连接
      Jedis jedis = new Jedis();//如果使用空参构造，默认值"localhost",6379端口
      //操作
      //存储
      jedis.zadd("mysortedset",3,"亚瑟");
      jedis.zadd("mysortedset",30,"后裔");
      jedis.zadd("mysortedset",25,"孙悟空");
      //获取
      Set<String> mysortedset = jedis.zrange("mysortedset",0,-1);
      System.out.println(mysortedset);
      //关闭连接
      jedis.close();
      ```

  

+ Jedis连接池：JedisPool

  使用：

  + 创建JedisPool连接池对象
  + 调用方法  getResource()方法获取Jedis连接

### 04Maven

#### Maven安装

**maven：**maven是一个项目管理工具，它包含了一个**项目对象模型**(POM:Project Object Model)，一组**标准集合**，一个**项目生命周期**(Project Lifecycle)，一个**依赖管理系统**(Dependency Management System)，和**用来运行定义在生命周期阶段(phase)中插件(plugin)目标(goal)的逻辑。**

**Maven 的作用：**
1.管理jar包
2.管理项目的生命周期(编译代码，打包，生成报表)
3.自动运行单元测试
4.部署项目，生成web网站

**Maven安装**：baidu --->Maven官网 -->download   --> apache-maven-3.6.0-bin.zip 下载   解压即安装

配置环境变量  新建环境变量MAVEN_HOME  值为  maven的安装目录

在PATH  变量中添加新的环境变量   %MAVEN_HOME%\bin\

cmd  中输入  mvn -v      

若出现下面情况即安装成功

```
Apache Maven 3.8.4 (9b656c72d54e5bacbed989b64718c159fe39b537)
Maven home: F:\maven\apache-maven-3.8.4
Java version: 1.8.0_121, vendor: Oracle Corporation, runtime: C:\Program Files\Java\jdk1.8.0_121\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

#### Maven仓库

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/javaweb/image-20211210095045451.png" alt="image-20211210095045451" style="zoom:50%;" />

**目录结构：**

```
项目的根目录：
    --src:源码
        --main:主工程代码
            --java:主工程代码
            --resources:需要使用的配置文件
            --webapp:web项目的资源目录(jsp/html/WEB-INF..)
        --test:测试代码
            --java:测试代码
            --resources:测试需要使用的配置文件
    --pom.xml:项目的核心配置文件
```

注意：只有web项目才有webapp目录，如果项目为java工程，将没有webapp

#### Maven命令

maven生命周期：同一套生命周期中，执行到后面的操作，会自动执行前边所有的操作。

​				     compile    test   package   install

清理				编译   	测试		打包		安装			站点发布

**每一个生命周期对应的命令就是一些插件**

#### Maven坐标

**坐标：被Maven管理的资源的唯一标识**

​	**groupId:组织名称**

​	**atifactId:模块名称**

​	**version:版本号**



#### Maven高级