---
tags:
  - dubbo
  - nacos
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
comments: true
date: 2025-08-15 16:55:00
updated: 2025-08-15 16:55:00
title: Dubbo+Nacos实现远程调用
---

### **完整教程：Dubbo 3 + Nacos 2.2.0 集成示例（IDEA + Maven）**

本教程将手把手教你搭建 **Dubbo 3.x** 集成 **Nacos 2.2.0** 的完整示例，包括：

- ✅ **项目结构搭建**
- ✅ **Dubbo API** **模块**
- ✅ **Dubbo Provider** **服务提供者**
- ✅ **Dubbo Consumer** **服务消费者**
- ✅ **Nacos** **服务注册与发现**
- ✅ **常见问题解决（依赖缺失、Maven 构建、服务自动退出等）**



##### **1. 环境准备**

###### 1.1 软件要求

| **软件**       | **版本** | **备注**                                                     |
| -------------- | -------- | ------------------------------------------------------------ |
| JDK            | 1.8+     | 推荐 JDK 8/11                                                |
| IntelliJ  IDEA | 2021+    | 社区版/旗舰版均可                                            |
| Maven          | 3.6+     | 配置阿里云镜像加速                                           |
| Nacos  Server  | 2.2.0    | [下载地址](https://github.com/alibaba/nacos/releases/tag/2.2.0) |

###### 1.2 启动 Nacos Server

```bash
# Linux/Mac
sh startup.sh -m standalone
# Windows
startup.cmd -m standalone
```

访问控制台：http://localhost:8848/nacos（默认账号 nacos/nacos）



##### **2. 创建 Maven 多模块项目**

###### 2.1 创建父工程

1. **File → New → Project → Maven**

2. 填写：

3. - **GroupId**: com.example
   - **ArtifactId**: dubbo-nacos-demo
   - **Version**: 1.0.0（不要用 SNAPSHOT）

4. **Finish**

###### 2.2 修改父工程 pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.corilead</groupId>
  <artifactId>dubbo-nacos-demo</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
  <modules>
    <module>dubbo-api</module>
    <module>dubbo-provider</module>
    <module>dubbo-consumer</module>
  </modules>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <dubbo.version>3.0.9</dubbo.version>
    <nacos.version>2.2.0</nacos.version>
  </properties>

  <dependencyManagement>
    <dependencies>

      <!-- 添加Spring Boot BOM -->
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>2.7.0</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!-- Dubbo -->
      <dependency>
        <groupId>org.apache.dubbo</groupId>
        <artifactId>dubbo-bom</artifactId>
        <version>${dubbo.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>

      <!-- Nacos -->
      <dependency>
        <groupId>com.alibaba.nacos</groupId>
        <artifactId>nacos-client</artifactId>
        <version>${nacos.version}</version>
      </dependency>

      <!-- Other dependencies -->
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.36</version>
      </dependency>
      <dependency>
        <groupId>ch.qos.logback</groupId>
        <artifactId>logback-classic</artifactId>
        <version>1.2.11</version>
      </dependency>
    </dependencies>
  </dependencyManagement>

</project>
```



##### **3. 创建 dubbo-api 模块**

###### 3.1 创建模块

1. **右键父工程 → New → Module → Maven**
2. 填写 **Name**: dubbo-api
3. **Finish**

###### 3.2 修改 dubbo-api/pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.corilead</groupId>
    <artifactId>dubbo-nacos-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>

  <artifactId>dubbo-api</artifactId>

  <dependencies>

    <!-- 添加Spring Boot Starter -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
    </dependency>

    <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-common</artifactId>
    </dependency>
  </dependencies>

</project>
```



###### 3.3 定义接口

```java
package com.example.service;
public interface HelloService{
    
}
```



##### **4. 创建 dubbo-provider 模块**

###### 4.1 创建模块

1. **右键父工程 → New → Module → Maven**
2. 填写 **Name**: dubbo-provider
3. **Finish**

###### 4.2 修改 dubbo-provider/pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.corilead</groupId>
    <artifactId>dubbo-nacos-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>

  <artifactId>dubbo-provider</artifactId>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- 添加Spring Boot Starter -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <!-- API -->
    <dependency>
      <groupId>com.corilead</groupId>
      <artifactId>dubbo-api</artifactId>
      <version>1.0-SNAPSHOT</version>
    </dependency>

    <!-- Dubbo -->
    <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo</artifactId>
    </dependency>
    <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-registry-nacos</artifactId>
    </dependency>

    <!-- Nacos -->
    <dependency>
      <groupId>com.alibaba.nacos</groupId>
      <artifactId>nacos-client</artifactId>
    </dependency>

    <!-- Logging -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
    </dependency>
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
    </dependency>
    <dependency>
      <groupId>com.corilead</groupId>
      <artifactId>dubbo-api</artifactId>
      <version>1.0-SNAPSHOT</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <executions>
          <execution>
            <goals>
              <goal>repackage</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>

</project>
```



###### 4.3 实现接口

```java
package com.example.service.impl;
import com.example.service.HelloService;
import org.apache.dubbo.config.annotation.DubboService;
@DubboService
public class HelloServiceImpl implements HelloService{
    @Override
    public String sayHello(String name){
        return "Hello, " + name + "! (from Dubbo provider)";
    }
}
```



###### 4.4 配置 application.yml

```yml
dubbo:
  application:
    name: dubbo-provider
  protocol:
    name: dubbo
    port: 20880
  registry:
    address: nacos://localhost:8848
  config-center:
    address: nacos://localhost:8848
  metadata-report:
    address: nacos://localhost:8848
server:
	port: 8081 #避免和consumer冲突
	
```



###### 4.5 启动类

```java
package com.example;

import org.apache.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableDubbo
public class ProviderApplication {
  public static void main(String[] args) {
    SpringApplication.run(ProviderApplication.class, args);
  }
}
```



##### **5. 创建 dubbo-consumer 模块**

###### 5.1 创建模块

1. **右键父工程 → New → Module → Maven**
2. 填写 **Name**: dubbo-consumer
3. **Finish**

###### 5.2 修改 dubbo-consumer/pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>com.corilead</groupId>
    <artifactId>dubbo-nacos-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>

  <artifactId>dubbo-consumer</artifactId>

  <dependencies>
    <!-- API -->
    <dependency>
      <groupId>com.corilead</groupId>
      <artifactId>dubbo-api</artifactId>
      <version>1.0-SNAPSHOT</version>
    </dependency>

    <!-- Dubbo -->
    <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo</artifactId>
    </dependency>
    <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-registry-nacos</artifactId>
    </dependency>

    <!-- Nacos -->
    <dependency>
      <groupId>com.alibaba.nacos</groupId>
      <artifactId>nacos-client</artifactId>
    </dependency>

    <!-- Spring Web for test -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <version>2.7.0</version>
    </dependency>

    <!-- Logging -->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
    </dependency>
    <dependency>
      <groupId>ch.qos.logback</groupId>
      <artifactId>logback-classic</artifactId>
    </dependency>
    <dependency>
      <groupId>com.corilead</groupId>
      <artifactId>dubbo-api</artifactId>
      <version>1.0-SNAPSHOT</version>
      <scope>compile</scope>
    </dependency>
  </dependencies>

</project>
```



###### 5.3 配置 application.yml

```yaml
dubbo:
  application:
    name: dubbo-consumer
  registry:
    address: nacos://localhost:8848
  config-center:
    address: nacos://localhost:8848
  metadata-report:
    address: nacos://localhost:8848
server:
  port: 8080
```



###### 5.4 启动类 + 测试接口

```java
package com.example;
import com.example.service.HelloService;
import org.apache.dubbo.config.annotation.DubboReference;
import org.apache.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
@SpringBootApplication
@EnableDubbo
@RestController
public class ConsumerApplication{
    
    @DubboReference
    private HelloService helloservice;
    @GetMapping("/hello/{name}")
    public String sayHello(@PathVariable String name){
        return helloService.sayHello(name);
    }
    public static void main(String[] args){
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```



##### **6. 运行测试**

###### 6.1 启动 Provider

1. 运行 ProviderApplication
2. 检查 Nacos 控制台是否有 dubbo-provider 服务

###### 6.2 启动 Consumer

1. 运行 ConsumerApplication
2. 访问 http://localhost:8080/hello/World，应该返回：

```txt
Hello, World! (from Dubbo provider)
```



##### **7. 常见问题解决**

###### 7.1 Could not find artifact com.example:dubbo-api

🔹 **原因**：dubbo-api 未安装到本地仓库
🔹 **解决**：

```sh
cd dubbo-api
mvn clean install
```



###### 7.2 Provider 自动退出

🔹 **原因**：没有 Web 服务保持运行
🔹 **解决**：确保 dubbo-provider 添加了 spring-boot-starter-web。

###### 7.3 Nacos 连接失败

🔹 **检查**：

1. Nacos Server 是否启动
2. application.yml 中地址是否正确（nacos://localhost:8848）



##### **8. 总结**

✅ **Dubbo + Nacos** **集成成功！**
✅ **Provider 注册服务到 Nacos**
✅ **Consumer 通过 Nacos 发现并调用 Provider**

##### 9.附件：

✅ **maven软件包：<a href="https://github.com/pairs-vip/blog-downloads/releases/download/v1.0.0/apache-maven-3.6.3.rar" download="apache-maven-3.6.3.rar">
  apache-maven-3.6.3.rar</a>**
✅ **nacos部署包：  <a href="https://github.com/pairs-vip/blog-downloads/releases/download/v1.0.0/nacos-server-2.2.0.zip" download="nacos-server-2.2.0.zip">
  nacos-server-2.2.0.zip
</a>**
✅ **demo源码包：  <a href="https://github.com/pairs-vip/blog-downloads/releases/download/v1.0.0/dubbo-nacos-demo.rar" download="dubbo-nacos-demo.rar">
  dubbo-nacos-demo.rar
</a>**



