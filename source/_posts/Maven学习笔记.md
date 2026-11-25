---
title: Maven 学习笔记
date: 2026-11-25 18:03:00
tags:
  - Maven
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
---

# Maven基础

### 1.简介

+ Maven的本质是一个项目管理工具，将项目开发和管理过程抽象成一个项目对象模型(POM)
+ POM(Project Object Model)：项目对象模型

![image-20220316175216883](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220316175216883.png)

**Maven的作用：**

+ 项目构建：提供标准的，跨平台的的自动化项目构建方式
+ 依赖管理：方便快捷的管理项目依赖的资源(jar包),避免资源间的版本冲突问题
+ 统一开发结构：提供标准的，统一的项目结构

### 2.下载和安装

**下载：**

+ 官网：http://maven.apache.org/
+ 下载地址：http://maven.apache.org/dowload.cgi

**安装：**Maven属于绿色版软件，解压即安装

**Maven环境变量配置：**

+ 依赖Java，需要配置JAVA_HOME

+ 设置MAVEN自身的运行环境，需要配置MAVEN_HOME

  + 打开系统环境变量，新建MAVEN_HOME，值为maven的安装路径
  + 然后找的Path变量，新增一条%MAVEN_HOME%\bin\
  + 打开cmd，直接输入指令mvn，结果为下面，代表成功

  ![image-20220316180609515](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220316180609515.png)

### 3.Maven基础概念

#### 3.1仓库

+ 仓库：用于存储资源，包含各种jar包

  ![image-20220316180858301](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220316180858301.png)

+ 仓库分类：
  + 本地仓库：自己电脑上存储资源的仓库，连接远程仓库获取资源
  + 远程仓库：非本机电脑上的仓库，为本地仓库提供资源
    + 中央仓库：Maven团队维护，存储所有资源的仓库
    + 私服：部门/公司范围内存储资源的仓库，从中央仓库获取资源
  + 私服的作用：
    + 保存具有版权的资源，包含购买或自主研发的jar
      + 中央仓库中的jar都是开源的，不能存储具有版权的资源
    + 一定范围内共享资源，仅对内部开放，不能对外共享

#### 3.2坐标

**坐标**：Maven中的作用用于描述仓库中资源的位置

​			https://repo1.maven.org/maven2/

**Maven坐标的主要组成：**

+ groupId：定义当前Maven项目隶属组织名称(通常是域名反写，例如：org.mybatis)
+ artifactId：定义当前项目名称(通常是模块名称，例如：CRM，SMS)
+ version：定义当前项目版本号
+ packaging：定义当前项目的打包方式

**Maven坐标的作用：**

使用唯一标识，唯一性定位资源位置，通过该标识可以将资源的识别与下载工作交由机器完成

#### 3.3配置本地仓库

+ 新建一个本地文件夹repository

+ 进入到maven 的安装目录下，进入conf文件夹，进入settings.xml
+ 找到已经被注释掉的LocalRepository标签，复制一个出来，标签的内容就是新建的repository路径

#### 3.4远程仓库配置

镜像仓库配置：

+ 在setting文件中配置阿里云镜像仓库

+ 找到mirros标签，复制一个范例出来进行修改

  ```xml
  <mirror>
      <!--此镜像的位置标识符，用来区分不同的mirror元素-->
  	<id>mirrorId</id>
      <!--被镜像的名称，简单来说就是替代哪个仓库-->
      <mirrorOf>central</mirrorOf>
      <!--镜像名称，不重要-->
      <name>Nexus aliyun</name>
      <!--镜像URL-->
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
  </mirror>
  ```

全局setting和用户setting的区别：

+ 全局setting定义了当前计算机中Maven的公共配置
+ 用户setting定义当前用户的配置

### 4.手工制作Maven项目

+ **手动制作Maven工程目录结构**

![image-20220316184303331](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220316184303331.png)

​	在src同层目录下创建pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project
    xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.itheima</groupId>
    <artifactId>project-java</artifactId>
    <version>1.0</version>
    <packaging>jar</packaging>

    <!--由于测试需要junit所以此处直接导入junit依赖-->
    <dependencies>
    	<dependency>
        	<groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>

</project>
```

到这里一个maven工程结构就搭建好了。

+ **Maven项目构建**

  + Maven构建命令使用mvn开头，后面添加功能参数，可以一次执行多个命令，使用空格分开

    | 命令        | 作用           |
    | ----------- | -------------- |
    | mvn compile | 编译           |
    | mvn clean   | 清理           |
    | mvn test    | 测试           |
    | mvn package | 打包           |
    | mvn install | 安装到本地仓库 |

  + 打开cmd，进入到项目的根目录，即pom.xml所在的层级下执行maven命令

  + 执行compile指令会下载maven需要的插件，而且会在当前目录下生成一个target目录。

+ **利用插件创建maven工程**

  在项目目录下执行cmd指令：

  ![image-20220316193459042](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220316193459042.png)

**java工程和web工程目录结构**：

![image-20220316193613821](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220316193613821.png)

### 5.IDEA创建Maven项目

+ Idea对3.6.2及以上版本存在兼容性问题，为避免冲突，IDEA中安装使用3.6.1版本

**配置Maven：**

在项目中有键File打开Settings搜索Maven，更改Maven home directory,User setting file,Local repository为自己的。

创建空的Maven项目：添加模块选择Maven，不使用骨架结构，直接创建，如果缺目录手动补充目录

#### 5.1使用原型快速创建Java项目

添加模块选择Maven项目，勾选使用模板创建，选中quickstart的那个模板，创建，如果创建出来缺目录手动补充目录

#### 5.2使用原型快速创建web项目

添加模块选择Maven项目，勾选使用模板创建，选中webapp的那个模板，创建，如果创建出来缺目录手动补充目录，在WEB-INF文件夹下创建index.jsp 测试页面。

### 6.安装Tomcat服务器启动web项目

1.在项目的pom.xml中进行添加tomcat的插件坐标

```xml
<!--构建-->
<build>
    <!--设置插件-->
    <plugins>
    	<!--具体的插件配置-->
        <plugin>
        	<groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <version>2.1</version>
            <configuration>
            	<port>80</port>
                <path>/</path>
            </configuration>
        </plugin>
    </plugins>
</build>
```

在maven插件中就能看tomcat7。

点击tomcat7:run就能启动服务器。

### 7.依赖管理

#### 7.1依赖配置

+ 依赖指当前项目运行所需的jar，一个项目可以设置多个依赖

+ 格式：

  ```xml
  <!--设置当前项目所依赖的所有jar-->
  <dependencies>
  	<!--设置具体的依赖-->
      <dependency>
      	<!--依赖所属群组id-->
          <groupId>junit</groupId>
          <!--依赖所属项目id-->
          <artifactId>junit</artifactId>
          <!--依赖版本号-->
          <version>4.12</version>
      </dependency>
  </dependencies>
  ```

#### 7.2依赖传递

将项目1的pom配置像依赖一样配置到另项目2中，项目2就可以用项目1中的依赖，这就是依赖传递

+ 依赖具有传递性
  + 直接依赖：在当前项目中通过依赖配置简历依赖关系
  + 间接依赖：被当作资源的资源如果依赖其他资源，当前项目间接依赖其他资源

+ 依赖传递冲突问题
  + 路径优先：当依赖中出现相同资源时，层级越深，优先级越低，层级越浅，优先级越高
  + 声明优先：当资源在相同层级被依赖时，配置顺序考前的覆盖配置顺序靠后的
  + 特殊优先：当同级配置了相同资源的不同版本时，后配置的覆盖先配置的

#### 7.3可选依赖

+ 可选依赖指对外隐藏当前所依赖的资源--不透明

  ```xml
  <dependency>
  	<groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <optional>true</optional>
  </dependency>
  ```

  

#### 7.4排除依赖

+ 排除依赖指主动断开依赖的资源，被排除的资源无需指定版本--不需要

  ```xml
  <dependency>
  	<groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <exclusions>
      	<exclusion>
          	<groupId>org.hamcrest</groupId>
              <artifactId>hamcrest-core</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  ```


#### 7.5依赖范围

+ 依赖的jar默认情况下可在任何地方使用，可以通过scope标签设定其作用范围
+ 作用范围
  + 主程序范围有效(main文件夹范围内)
  + 测试程序范围有效(test文件夹范围内)
  + 是否参与打包(package指定范围内)

| scope         | 主代码 | 测试代码 | 打包 | 范例        |
| ------------- | ------ | -------- | ---- | ----------- |
| compile(默认) | Y      | Y        | Y    | log4j       |
| test          |        | Y        |      | junit       |
| provided      | Y      | Y        |      | servlet-api |
| runtime       |        |          | Y    | jdbc        |

+ 依赖范围传递性

  带有依赖范围的资源在进行传递时，作用范围将收到影响

  |          | compile | test | provided | runtime |
  | -------- | ------- | ---- | -------- | ------- |
  | compile  | compile | test | provided | runtime |
  | test     |         |      |          |         |
  | provided |         |      |          |         |
  | runtime  | runtime | test | provided | runtime |

  第一横行为直接依赖，第一数列为间接依赖，中间为结果

### 8.生命周期与插件

#### 8.1构建生命周期

**项目构建生命周期**

+ Maven构建生命周期描述的是一次构建过程经历了多少个事件

![image-20220316220527477](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220316220527477.png)

+ Maven对项目构建的生命周期划分为3套
  + clean：清理工作
  + default：核心工作，例如编译，测试，打包，部署等
  + site：产生报告，发布站点等

**clean生命周期**

+ pre-clean	 执行一些需要在clean之前完成的工作
+ clean            移除上一次构建生成的文件
+ post-clean   执行一些需要在clean之后立刻完成的工作

**default构建生命周期**

![image-20220316221035680](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220316221035680.png)

**site构建生命周期**

+ pre-site                 执行一些需要在生成站点文档之前完成的工作
+ site                        生成项目的站点文档
+ post-site               执行一些需要在生成站点文档之后的工作，并且为部署做好准备
+ site-deploy           将生成的站点文档部署到特定的服务器上

#### 8.2插件

+ 插件与生命周期内的阶段绑定，在执行到对应的生命周期时执行对应的插件功能
+ 默认maven在各个生命周期上绑定有预设的功能
+ 通过插件可以自定义其他功能

下面展示源代码打包插件：

```xml
<project>
    
    
    <build>
    	<plugins>
        	<plugin>
            	<groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-source-plugin</artifactId>
                <version>2.2.1</version>
                <executions>
                	<execution>
                    	<goals>
                        	<goal>jar</goal>
                        </goals>
                        <phase>generate-test-resoutces</phase>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
   
</project>
```

**根据项目生命周期来看，执行到complie这件事不干，执行到test这件事要干，执行到package这件事要干，执行到install这件事也要干**





# Maven高级

### 1.分模块开发与设计

**工程模块与模块划分**

![image-20220317131420674](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317131420674.png)



**ssm_pojo拆分**

+ 新建模块
+ 拷贝原始项目中对应的相关内容到ssm_pojo模块中
  + 实体类(User)
  + 配置文件(无)



**ssm_dao拆分**

+ 新建模块
+ 拷贝原始项目中对应的相关内容到ssm_dao模块中
  + 数据层几口(UserDao)
  + 配置文件：保留与数据层相关的配置
    + 注意：分页插件在配置中与SqlSessionFactoryBena绑定，需要保留
  + pom.xml：引入数据层相关坐标即可，删除springmvc坐标
    + spring
    + mybatis
    + spring整合mybatis
    + mysql
    + druid
    + pagehelper
    + 直接依赖ssm_pojo(对ssm_pojo模块执行install指令，将其安装到本地仓库)



**ssm_service拆分**

+ 新建模块
+ 拷贝原始项目中对应的相关内容到ssm_service模块中
  + 业务层接口与实现类(UserService,UserServiceImpl)
  + 配置文件：保留与数据层相关配置
  + pom.xml：引入数据层相关坐标，删除springmvc相关坐标
    + spring
    + junit
    + spring整合junit
    + 直接依赖ssm_dao(对ssm_dao模块执行install指令，将其安装到本地仓库)
    + 间接依赖ssm_pojo(由ssm_dao模块负责依赖关系的建立)
  + 修改service模块spring核心配置文件名，添加模块名称，格式：applicationContext-service.xml
  + 修改dao模块spring核心配置文件名，添加模块名称，格式：applicationContext-dao.xml
  + 修改单元测试引入的配置文件名称，由单个文件修改为多个文件即：@ContextConfiguration(locations = {"applicationContext-dao.xml","applicationContext-service.xml"})



**ssm_controller拆分**

+ 新建模块(使用webapp模板)
+ 拷贝原始项目中对应的相关内容到ssm_controller模块中
  + 表现层控制器类与相关设置类(UserController,异常相关)
  + 配置文件：保留与表现层相关配置文件，服务器相关配置文件
  + pom.xml：引入业务层相关坐标即可
    + spring
    + springmvc
    + jackson
    + servlet
    + tomcat服务器插件
    + 直接依赖ssm_service(对ssm_service模块执行install指令，将其安装到本地仓库)
    + 间接依赖ssm_dao,ssm_pojo
  + 修改web.xml配置文件中加载spring环境的配置文件名称，使用*通配，加载所有的applicationContext-开始的配置文件





**分模块开发要点**

+ 模块中仅包含当前模块对应的功能与配置文件
+ spring核心配置根据模块功能不同进行独立制作
+ 当前模块所依赖的模块通过导入坐标的形式加入到当前模块后才可以使用
+ web.xml需要加载所有的spring核心配置文件





### 2.模块聚合

#### 多模块构建维护

![image-20220317142740773](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317142740773.png)

**聚合**

+ 作用：聚合用于快速构建maven工程，一次性构建多个项目/模块

+ 制作方式：

  + 创建一个空模块，打包类型定义为pom

    ```xml
    <packaging>pom</packaging>
    ```

  + 定义当前模块进行构建操作时关联的其他模块名称

    ```xml
    <modules>
    	<module>../ssm_controller</module>
        <module>../ssm_service</module>
        <module>../ssm_dao</module>
        <module>../ssm_pojo</module>
    </modules>
    ```

+ 注意：参与聚合操作的模块最终执行顺序与模块间的依赖关系有关，与配置顺序无关





### 3.继承

**模块依赖关系维护**

![image-20220317143801652](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317143801652.png)



**继承**

+ 作用：通过继承可以实现在子工程中沿用父工程的配置

  + maven中继承与java中的继承类似，在子工程中配置继承关系

+ 制作方式：

  + 在子工程中声明其父工程的坐标与对应的位置

    ```xml
    <!--定义该工程的父工程-->
    <parent>
    	<groupId>com.itheima</groupId>
        <artifactId>ssm</artifactId>
        <version>1.0-SNAPSHOT</version>
        <!--填写父工程的pom文件-->
        <relativePath>../ssm/pom.xml</relativePath>
    </parent>
    ```

  + 在父工程中定义依赖管理

    ```xml
    <!--声明此处进行依赖管理-->
    <dependencyManagement>
    	<!--具体的依赖-->
        <dependencies>
        	<!--spring环境-->
            <dependency>
            	<groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>5.1.9.RELEASE</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    ```

  + 在子工程中定义依赖关系，无需声明依赖版本，版本参照父工程中依赖的版本

    ```xml
    <dependencies>
    	<!--spring环境-->
        <dependency>
        	<groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
        </dependency>
    </dependencies>
    ```





**继承的资源**

![image-20220317145426768](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317145426768.png)





**继承于聚合**

+ 作用：
  + 聚合用于快速构建项目
  + 继承用于快速配置
+ 相同点：
  + 聚合与继承的pom.xml文件打包方式均为pom，可以将两种关系制作到同一个pom文件中
  + 聚合与集成钧属于设计性模块，并无实际的模块内容
+ 不同点：
  + 聚合是在当前模块中配置关系，聚合可以感知到参与聚合的模块有哪些
  + 继承是在子模块中配置关系，父模块无法感知那些子模块继承了自己





### 4.属性

属性类别：

+ 自定义属性

  + 作用：等同于定义变量，方便统一维护

  + 定义格式：

    ```xml
    <!--定义自定义属性-->
    <properties>
    	<spring.version>5.1.9.RELEASE</spring.version>
    </properties>
    ```

  + 调用格式：

    ```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
    ```

+ 内置属性

  + 作用：使用maven内置属性，快速配置

  + 调用格式：

    ```xml
    ${basedir}
    ${version}
    ```

+ Setting属性

  + 作用：使用Maven配置文件setting.xml中的标签属性，用于动态配置

  + 调用格式：

    ```
    ${settings.localRepository}
    ```

+ Java系统属性

  + 作用：读取Java系统属性

  + 调用格式：

    ```
    ${user.home}
    ```

  + 系统属性查询方式：

    ```
    mvn help:system
    ```

+ 环境变量属性

  + 作用：读取环境变量

  + 调用格式：

    ```
    ${env.JAVA_HOME}
    ```

  + 环境变量查询方式：

    ```
    mvn help:system
    ```

    

### 5.版本管理

#### 5.1工程版本区分

<img src="https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317155638557.png" alt="image-20220317155638557" style="zoom:67%;" />

#### 5.2工程版本

+ SNAPSHOT（快照版本）
  + 项目开发过程中，为方便团队成员合作，解决模块间相互依赖和实时更新的问题，开发者对每个模块进行构建的时候，输出的临时版性版本叫快照版本(测试阶段版本)
  + 快照版本会随着开发的进展不断更新
+ RELEASE（发布版本）
  + 项目开发进入到阶段里程碑后，向团队外部发布较为稳定的版本，这种版本所对应的构建文件是稳定的，即便继续功能的后续开发，也不会改变当前发布版本内容，这种版本称为发布版本

**工程版本号约定**

+ 约定规范：
  + \<主版本>.<次版本>.<增量版本>.<里程碑版本>
  + 主版本：表示项目重大架构的变更，如：spring5相较于spring4的迭代
  + 次版本：表示有较大的功能增加和变化，或者全面系统地修复漏洞
  + 增量版本：表示有重大漏洞地修复
  + 里程碑版本：表名一个版本地里程碑(版本内部),这样地版本同下一个正式版本相比，相对来说不是很稳定，有待更多地测试
+ 范例：5.1.9.RELEASE





### 6.资源配置

**配置文件应用pom属性**

+ 作用：在任意配置文件中加载pom文件中定义地属性

+ 调用格式：

  ```
  ${jdbc.url}
  ```

+ 开启配置文件加载pom属性

  ```xml
  <!--配置资源文件对应地信息-->
  <resources>
  	<resource>
      	<!--设定配置文件对应地目录，支持使用属性动态设定路径-->
          <directory>${project.basedir}/src/main/resources</directory>
          <!--开启对配置文件的资源加载过滤-->
          <filtering>true</filtering>
      </resource>
  </resources>
  ```



### 7.多环境开发配置

**多环境兼容**

![image-20220317163258724](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317163258724.png)

**多环境配置**

```xml
<!--创建多环境-->
<profiles>
	<!--定义具体的环境：生产环境-->
    <profile>
    	<!--定义环境对应的唯一名称-->
        <id>pro_env</id>
        <!--定义环境中专用的属性值-->
        <properties>
        	<jdbc.url>jdbc:mysql://127.0.0.1:3306/ssm_db</jdbc.url>
        </properties>
        
        <!--设置默认启动-->
        <activation>
        	<activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    
    <!--定义具体的环境：开发环境-->
    <profile>
    	<id>dev_env</id>
        ....
    </profile>
</profiles>
```

**加载指定环境**

+ 作用：加载指定环境配置

+ 调用格式：

  ```
  mvn 指令 -P 环境定义id
  ```

+ 范例：

  ```
  mvn install -P pro_env
  ```







### 8.跳过测试

#### 8.1使用命令跳过测试

+ 命令：

  ```
  mvn 指令 -D skipTests
  ```

+ 注意事项

  + 执行的指令生命周期必须包含测试环节

#### 8.2使用界面操作跳过测试

![image-20220317165254042](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317165254042.png)

#### 8.3使用配置跳过测试

```xml
<plugin>
	<artifactId>maven-surefire-plugin</artifactId>
    <version>2.22.1</version>
    <configuration>
    	<skipTests>true</skipTests><!--设置跳过测试-->
        <includes><!--包含指定的测试用例-->
        	<include>**/User*Test.java</include>
        </includes>
        
        <excludes><!--排除指定的测试用例-->
        	<exclude>**/User*TestCase.java</exclude>
        </excludes>
    </configuration>
</plugin>
```







### 9.私服

#### 9.1**Nexus**

+ Nexus是Sonatype公司的一款maven私服产品
+ 下载地址：https://help.sonatype.com/repomanager3/download

**Nexus安装，启动与配置**

解压即安装

+ 启动服务器(命令行启动)

  ```
  nexus.exe /run nexus
  ```

+ 访问服务器(默认端口：8081)

  ```
  http://localhost:8081
  ```

+ 修该基础配置信息

  + 安装路径下etc目录中nexus-default.properties文件保存有nexus基础配置信息，例如默认访问端口

+ 修改服务器运行配置信息

  + 安装路径下bin目录中nexus.vmoptions文件保存有nexus服务器启动对应的配置信息，例如默认占用内存空间

#### 9.2私服资源获取

![image-20220317190741342](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317190741342.png)

**私服中的仓库（三种）：放资源的仓库，拿资源时候的仓库组以及与外界中央仓库打交道的特殊的仓库**

#### 9.3仓库分类

+ 宿主仓库hosted
  + 保存无法从中央仓库获取的资源
    + 自主研发
    + 第三方非开源项目
+ 代理仓库proxy
  + 代理远程仓库，通过nexus访问其他公共放库，例如中央仓库
+ 仓库组group
  + 将若干个仓库组成一个群组，简化配置
  + 仓库组不能保存资源，属于设计性仓库

#### 9.4资源上传

+ 上传资源时提供对应的信息
  + 保存的位置(宿主仓库)
  + 资源文件
  + 对应坐标

#### 9.5idea环境中资源上传和下载

![image-20220317191957375](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/maven/image-20220317191957375.png)

##### 9.5.1访问私服配置(本地仓库访问私服)

**在maven中配置私服的用户名和密码**

在settings.xml中找到servers标签，在他里面仿照他写：

```xml
<!--配置访问服务器的权限，用户名和密码-->
	<server>
    	<id>heima-release</id>
        <username>admin</username>
        <password>admin</password>
    </server>
	<server>
    	<id>heima-snapshot</id>
        <username>admin</username>
        <password>admin</password>
    </server>
```

**接下来配置什么样的资源来自于私服**

在settings.xml中找到mirror标签，之前配过一次

```xml
<mirror>
	<id>nexus-heima</id>
    <mirrorOf>*</mirrorOf>
    <url>http://localhost:8081/repository/maven-public/</url><!--对应私服访问的路径，在nexus服务器上找到指定的仓库组就可以直接copy-->
</mirror>
```



##### 9.5.2访问私服配置(项目工程访问私服)

+ 配置当前项目访问私服上传资源的保存位置(pom.xml)

  ```xml
  <distributionManagement>
  	<repository>
          <!--此id要与settings文件中配置的仓库名称一致-->
      	<id>heima-release</id> 
          <!--这个url是服务器上对应仓库的rul，去服务器直接复制-->
          <url>http://localhost:8081/repository/heima-release</url>   
      </repository>
      <snapshotRepository>
      	<id>heima-snapshot</id>
          <url>http://localhost:8081/repository/heima-snapshot</url>
      </snapshotRepository>
  </distributionManagement>
  ```

+ 输入指令或者在maven中操作deploy，发布资源到私服

  ```
  mvn deploy
  ```



**package只是做一个打包，是生成在当前的开发路径下**

**install是上传到本地仓库**

**deploy是上传到私服**

