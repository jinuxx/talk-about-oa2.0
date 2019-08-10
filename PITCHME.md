@snap[text-09]
## 基于 spring cloud 的 OA2.0 框架搭建开发和问题解决
@snapend
@snap[text-09]
徐  进
@snapend


---?color=linear-gradient(90deg, white 50%, black 50%)
@snap[west span-40 text-center]

@ul[text-07 mt]
- 框架搭建过程中的思路
- 遇到的问题
- 我会用到的工具
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
@box[border-solid-gray](Intellij Idea <br>&gt; 2018.1.5)
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
&lt;mirror>
    &lt;id>nexus-naiyun&lt;/id>
    &lt;mirrorOf>*&lt;/mirrorOf>
    &lt;name>Nexus Naiyun&lt;/name>
    &lt;url>http://192.168.1.130:58082/nexus/content/groups/public/&lt;/url>
&lt;/mirror>
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
&lt;parent&gt;
    &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
    &lt;artifactId&gt;spring-boot-starter-parent&lt;/artifactId&gt;
    &lt;version&gt;2.0.9.RELEASE&lt;/version&gt;
&lt;/parent&gt;
&lt;!-- 聚合工程/传递依赖 --&gt;
&lt;packaging&gt;pom&lt;/packaging&gt;
&lt;properties&gt;
    &lt;java.version&gt;1.8&lt;/java.version&gt;
    &lt;!-- 乱码问题 --&gt;
    &lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;
    &lt;project.reporting.outputEncoding&gt;UTF-8&lt;/project.reporting.outputEncoding&gt;
    &lt;!-- 编译器版本问题 --&gt;
    &lt;maven.compiler.source&gt;1.8&lt;/maven.compiler.source&gt;
    &lt;maven.compiler.target&gt;1.8&lt;/maven.compiler.target&gt;
    ...
    &lt;spring-cloud.version&gt;Finchley.SR2&lt;/spring-cloud.version&gt;
    &lt;lombok.version&gt;1.18.4&lt;/lombok.version&gt;
&lt;/properties&gt;

...
```
@snapend


+++
##### pom.xml 文件配置

@snap[border-dashed-black]
```xml
...
&lt;!-- 声明依赖包，不引入 --&gt;
&lt;dependencyManagement&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.springframework.cloud&lt;/groupId&gt;
        &lt;artifactId&gt;spring-cloud-dependencies&lt;/artifactId&gt;
        &lt;version&gt;${spring-cloud.version}&lt;/version&gt;
        &lt;type&gt;pom&lt;/type&gt;
        &lt;scope&gt;import&lt;/scope&gt;
    &lt;/dependency&gt;
    ...
&lt;/dependencyManagement&gt;
...
&lt;!-- 自动引入依赖包 --&gt;
&lt;dependencies&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.projectlombok&lt;/groupId&gt;
        &lt;artifactId&gt;lombok&lt;/artifactId&gt;
        &lt;version&gt;${lombok.version}&lt;/version&gt;
    &lt;/dependency&gt;
    ...
&lt;/dependencies&gt;

...
```
@snapend


+++
##### pom.xml 文件配置


@snap[border-dashed-black]
```xml
...
&lt;build&gt;
    &lt;finalName&gt;${project.artifactId}&lt;/finalName&gt;
    &lt;plugins&gt;
        &lt;plugin&gt;
            &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
            &lt;artifactId&gt;spring-boot-maven-plugin&lt;/artifactId&gt;
        &lt;/plugin&gt;
    &lt;/plugins&gt;
&lt;/build&gt;
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
&lt;dependencies&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.springframework.cloud&lt;/groupId&gt;
        &lt;artifactId&gt;spring-cloud-starter-netflix-eureka-server&lt;/artifactId&gt;
    &lt;/dependency&gt;
&lt;/dependencies&gt;
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
&lt;packaging&gt;pom&lt;/packaging&gt;
&lt;dependencies&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.springframework.cloud&lt;/groupId&gt;
        &lt;artifactId&gt;spring-cloud-starter-netflix-eureka-client&lt;/artifactId&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
        &lt;artifactId&gt;spring-boot-starter-web&lt;/artifactId&gt;
    &lt;/dependency&gt;
&lt;/dependencies&gt;
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
### 日志模块
@snap[mt]
Logback  
配置文件： [logback-spring.xml](https://gitee.com/jinuxx/configs/blob/master/logback-spring.xml)
@snapend
@ul
- 每个服务都要复制，太麻烦！
@ulend