## 基于 spring cloud 的 OA2.0 框架搭建开发和问题解决
徐 进


---?color=linear-gradient(90deg, white 50%, black 50%)

@snap[west span-40 text-center]


@fa[quote-left](工欲善其事<br>必先利其器)

@ul[text-07 mt]
- 框架搭建过程中的思路
- 遇到的问题
- 我会用到的工具
- @ulend

@snap[mt]
![Google](https://www.google.com/images/branding/googlelogo/1x/googlelogo_color_272x92dp.png)
@snap[end]

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

#### Maven

version: 3.5.4

```
&lt;mirror>
    &lt;id>nexus-naiyun&lt;/id>
    &lt;mirrorOf>*&lt;/mirrorOf>
    &lt;name>Nexus Naiyun&lt;/name>
    &lt;url>http://192.168.1.130:58082/nexus/content/groups/public/&lt;/url>
&lt;/mirror>
```


---

```java
    <modules>
        <module>common</module>
        <module>config-server</module>
        <module>eureka-server</module>
        <module>gateway</module>
        <module>services</module>
        <module>admin-monitor</module>
        <module>gateway-app</module>
    </modules>
```
+++

### GraphQL
@fa[quote-left](A query language for your API)
![GRAPHQL](https://www.baidu.com/img/bd_logo1.png?where=super)



