@snap[text-09]
## 基于 Spring Cloud 的 OA2.0 框架搭建和问题解决
@snapend
@snap[text-09]
@snapend


---?color=linear-gradient(90deg, white 50%, black 50%)
@snap[west span-40 text-center]

@ul[text-07 mt]
- 框架搭建过程中的思路
- 遇到的问题
- 用到的工具
@ulend

@snapend

@snap[north-east span-40 text-08]
@box[bg-green](Part 1 # 环境与工具)
@snapend

@snap[east span-40 text-08]
@box[bg-blue](Part 2 # Spring Cloud)
@snapend

@snap[south-east span-40 text-08]
@box[bg-gold](Part 3 # 规范与约定)
@snapend


---
@snap[north span-100 text-06]
## 工欲善其事 必先利其器
请熟练使用自己的生产工具
@snapend

@snap[west span-33 text-center]
@box[border-solid-gray](JDK 1.8.***)
@snapend

@snap[midpoint span-33 text-center]
@box[border-solid-gray](Intellij Idea <br>> 2018.1.5)
@snapend

@snap[east span-33 text-center]
@box[border-solid-gray](Maven [3.5.4., ))
@snapend

@snap[south span-100 text-07 text-right]
<!-- [华为开源镜像站](https://mirrors.huaweicloud.com/) -->
[华为开源镜像站 @fa[external-link]](https://mirrors.huaweicloud.com)
@snapend

@snap[south span-26]
![LEGO](assets/img/lego.png)
@snapend


---
#### Maven

version: 3.5.4

@snap[border-dashed-black]
```xml 
<mirror>
    <id>nexus-naiyun</id>
    <mirrorOf>*</mirrorOf>
    <name>Nexus Naiyun</name>
    <url>http://192.168.1.130:58082/nexus/content/groups/public/</url>
</mirror>
```
@snapend

@snap[text-06 mt]
[推荐]配置文件位置: `C:\Users\<User Name>\.m2\settings.xml`
@snapend


---
@snap[west span-45 text-blue]
### SpringCloud
@fa[quote-left text-08](快速搭建分布式系统)
@snap[mt]
#### Version
@snapend
@ul[text-08 mt]
- Spring Boot: 2.0.9.RELEASE(1.5.19.RELAESE)
- Spring Cloud: Finchley.SR2(Edgware.SR6)
@ulend
@snapend
@snap[east span-45]
[![VERSION](https://raw.githubusercontent.com/jinuxx/spring-cloud-issues/master/assets/img/spring-cloud-version.png)](https://spring.io/projects/spring-cloud#release-trains)
@snapend


+++
@title[架构图]
[![VERSION](https://raw.githubusercontent.com/jinuxx/spring-cloud-issues/master/assets/img/oa-2.0-framework.png)](https://raw.githubusercontent.com/jinuxx/spring-cloud-issues/master/assets/img/oa-2.0-framework.png)


+++
@title[模块划分]
```
├─naiyun-oa------------------------------------ 父项目，公共依赖
│  │
│  ├─eureka-server----------------------------- 微服务注册中心
│  │
│  ├─gateway----------------------------------- 微服务网关
│  │
│  ├─config-server----------------------------- 配置中心
│  │
│  ├─zipkin------------------------------------ 微服务链路追踪，由 docker 启动
│  │
│  ├─admin-monitor----------------------------- 监控中心
│  │
│  ├─common
│  │  │
│  │  ├─common-log----------------------------- logback日志配置 想办法把配置抽出来
│  │  │
│  │  ├─common-base---------------------------- 静态常量和配置与大部分共用工具类
│  │  │
│  │  ├─common-web----------------------------- web 部分
│  │  │
│  │  ├─common-jwt----------------------------- jwt 部分

```


+++
```
│  │  │
│  │  ├─common-feign-client-------------------- feign 客户端
│  │  │ 
│  ├─services
│  │  │
│  │  ├─service-website------------------------ 网站基础
│  │  │
│  │  ├─service-user--------------------------- 用户
│  │  │
│  │  ├─service-bbs---------------------------- 论坛
│  │  │
│  │  ├─service-order-------------------------- 订单
│  │  │
│  │  ├─service-finance------------------------ 财务
│  │  │
│  │  ├─service-personnel---------------------- 人事
```


---
##### pom.xml 文件配置
@title[顶级pom配置]
@snap[border-dashed-black]
```xml
...
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.9.RELEASE</version>
</parent>
<!-- 聚合工程/传递依赖 -->
<packaging>pom</packaging>
<properties>
    <java.version>1.8</java.version>
    <!-- 乱码问题 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <!-- 编译器版本问题 -->
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    ...
    <spring-cloud.version>Finchley.SR2</spring-cloud.version>
    <lombok.version>1.18.4</lombok.version>
</properties>

...
```
@snapend


+++
##### pom.xml 文件配置

@snap[border-dashed-black]
```xml
...
<!-- 声明依赖包，不引入 -->
<dependencyManagement>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>${spring-cloud.version}</version>
        <type>pom</type>
        <scope>import</scope>
    </dependency>
    ...
</dependencyManagement>
...
<!-- 自动引入依赖包 -->
<dependencies>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
    </dependency>
    ...
</dependencies>

...
```
@snapend


+++
##### pom.xml 文件配置


@snap[border-dashed-black]
```xml
...
<build>
    <finalName>${project.artifactId}</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
...
```
@snapend
@snap[text-right text-blue]
开始创建模块 ->
@snapend


---
### eureka-server
@snap[border-dashed-black]
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
</dependencies>
```
@snapend


+++

### eureka-server

@snap[border-dashed-black]
```java
package com.naiyun;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaServer {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServer.class);
    }
}
```
@snapend


+++
### eureka-server
@snap[border-dashed-black]
```yaml
server:
  port: 8761

eureka:
  client:
    fetch-registry: false
    register-with-eureka: false
```
@snapend


---
### services 准备业务模块
@snap[border-dashed-black]
```xml
<packaging>pom</packaging>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```
@snapend


+++

### service-user
@snap[border-dashed-black]
```yml
spring:
  application:
    name: user

server:
  port: 8801

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```
@snapend

+++

### service-website
@snap[border-dashed-black]
```yml
spring:
  application:
    name: website

server:
  port: 8802

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```
@snapend

---
@snap[west span-45]
@snap[text-09]
#### 日志模块
@snapend
@snap[mt text-08]
Logback  
配置文件：<br> &nbsp;&nbsp;[logback-spring.xml](https://gitee.com/jinuxx/configs/blob/master/logback-spring.xml)

@ul
- 每个服务都要复制，太麻烦！
- 每次改动所有服务都要改动
@ulend
@snapend
@snapend

@snap[east span-45]
![ctrl-c-v](https://raw.githubusercontent.com/jinuxx/spring-cloud-issues/master/assets/img/ctrl-c-v.jpg)
@snapend

+++
@snap[border-dashed-black]
```yml
logging:
  config: https://gitee.com/jinuxx/configs/raw/master/logback-spring.xml
  level:
    com.naiyun.mapper: debug
```
@snapend
@ul
- 每个服务都要复制，太麻烦！
- 每次改动所有服务都要改动
@ulend


---

@snap[west span-45]
@snap[text-09 text-blue]
Config-server
@snapend
@snap[mt text-08 border-dashed-black]
```yml
spring:
  profiles:
    # 使用 svn 控制
    active: subversion
```
@snapend
@snapend

@snap[east span-45 border-dashed-black]
```yml
spring:
  profiles:
    active: dev
  application:
    name: eureka-server
  cloud:
    config:
      uri: http://192.168.8.88:8000
```
@snapend

@snap[north span-100]
@ul
- 还是太麻烦 -> 改造 pom.xml
- 高可用 HA
@ulend
@snapend

+++
@snap[east span-45 border-dashed-black]
```yml
spring:
  profiles:
    active: @active.profile@
  application:
    name: @pom.artifactId@
  cloud:
    config:
      uri: @config-server.url@
      fail-fast: true  # 启动遇错，快速失败
```

---
@snap[text-09 text-blue]
Common
@snapend
@snap[central span-100 border-dashed-black]
```
│  ├─common
│  │  │
│  │  ├─common-log----------------------------- logback日志配置
│  │  │
│  │  ├─common-base---------------------------- 静态常量和配置与大部分共用工具类
│  │  │
│  │  ├─common-web----------------------------- web 部分
│  │  │
│  │  ├─common-jwt----------------------------- jwt 部分
│  │  │
│  │  ├─common-feign-client-------------------- feign 客户端
```
@snapend


---
@snap[text-09 text-blue]
Gateway
@snapend
@ul
- 负载均衡 ribbon（轮询、随机、权重、地区...）
- 请求头传递 x-forwarded
- 接口限流 (令牌桶)
- JWT验证
- feign
- ...
@ulend


---
@snap[text-09 text-blue]
我的服务调到哪去了？？？
@snapend
@snap[central span-100 border-dashed-black]
```yaml
eureka:
  instance:
    metadataMap:
      zone: ${spring.cloud.client.hostname}
```
@snapend

---
@snap[text-09 text-blue]
[zipkin 链路跟踪](http://192.168.8.88:9411/zipkin/)
@snapend
![zipkin](https://raw.githubusercontent.com/jinuxx/spring-cloud-issues/master/assets/img/zipkin.png)


---
@snap[text-09 text-blue]
[spring-boot-admin(SBA)](http://192.168.8.88:8800/#/wallboard)
@snapend
![spring-boot-admin](https://raw.githubusercontent.com/jinuxx/spring-cloud-issues/master/assets/img/spring-boot-admin.png)

---
@snap[text-09]
@ul
- 代码整洁之道
- [提问的艺术](https://github.com/ryanhanwu/How-To-Ask-Questions-The-Smart-Way/blob/master/README-zh_CN.md)
- [程序员的“铁饭碗”秘籍](http://www.voidcn.com/article/p-bccqqpxd-mz.html)
@snapend


+++
@snap[text-09 text-blue]
代码整洁之道
@snapend
@ul
- 可读性高
- 注释
- 方法长度
- 测试！测试！
@ulend

+++
@snap[text-09 text-blue]
可读性高的代码才有可能是美的代码
@snapend
@ul
- 开发成本，维护成本
- 任何一个傻瓜都能写出计算机可以理解的代码。唯有写出人类容易理解的代码，才是优秀的程序员
- 讲故事
@ulend

+++
@snap[text-09 text-blue]
注释是一把双刃剑
@snapend
@ul
- 注释不会美化糟糕的代码
- TODO FIXME XXX
- 别说废话（喃喃自语、多余、误导性、循规式）
- 注释掉的代码
@ulend

---
Thanks~
