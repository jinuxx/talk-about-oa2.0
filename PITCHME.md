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
```
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
@snap[west span-45]
### SpringCloud
@fa[quote-left](快速搭建分布式系统)
#### [Version @fa[external-link]](https://spring.io/projects/spring-cloud#release-trains)
@ul[text-08]
- Spring Boot: 2.0.9.RELEASE(1.5.19.RELAESE)
- Spring Cloud: Finchley.SR2(Edgware.SR6)
@ulend
@snapend
@snap[east span-45]
![VERSION](assets/img/spring-cloud-version.png)
@snapend


