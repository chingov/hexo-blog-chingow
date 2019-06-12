---
title: Spring Boot - 配置文件外置
copyright: true
related_posts: true
tags:
  - SpringBoot
  - Spring
categories: Spring Boot
abbrlink: ad708d57
date: 2019-06-12 18:44:33
---


Spring Boot启动会加载大量的自动配置类，相比以前 XML 的配置方式，很多显式方式申明是不需要的，从而可以更快速的开发。

> Spring Boot的配置文件有两种：**.properties **文件和 **.yml **文件。
> 使用固定的 `application.properties` 或者 `application.yml` 文件做为全局的配置文件，启动时会扫描它们作为默认配置文件。
>
>在很多场景下，我们需要去修改配置文件，如：端口、数据库地址等等。把所有配置全都打在包里，显然不是最好的做法，更常见的做法是把配置文件放在外面，可以在需要时不动代码的前提下修改配置。

本文章将介绍如何自定义Sping Boot配置文件的位置。
<!--more-->

### 配置文件默认加载位置

Spring Boot提供了将配置文件放置到包外面的方法，在没有特殊配置和命令的情况下，启动时会扫描以下位置的默认配置文件以获取配置：

``` properties
- file:./config/        # 当前目录下的/config目录
- file:./               # 当前目录
- classpath:/config/    # classpath里的/config目录
- classpath:/           # classpath的跟目录
```

### 配置文件加载优先级

[参考官方文档-SpringBootConfig](https://docs.spring.io/spring-boot/docs/2.0.4.RELEASE/reference/htmlsingle/#boot-features-external-config)
优先级由高到低，高优先级的配置会覆盖低优先级的配置，互补配置。

1. 命令行指定

  我们可以使用 **--spring.config.location=xxx** 这样的命令形式来配置指定目录下的配置文件

  ``` bash
  java -jar demo.jar --spring.config.location=file:/config.yml

  # 或者
  java -jar -Dspring.config.location=file:/config.yml demo.jar

  # 如果是指定目录的话，则路径后必须加 /
  java -jar demo.jar --spring.config.location=file:/config/
  ```

  如果不希望命令行指定配置文件的话，可以在**SpringApplication **中将其禁用 `SpringApplication.setAddCommandLineProperties(false)`
  
2. JNDI属性 `java:comp/env`

3. 操作系统环境变量

  tomcat启动war包应用时 ，在 tomcat/bin 的 catalina.sh文件中增加一行代码:

  ``` bash tomcat/bin/catalina.sh
  export CATALINA_OPTS="$CATALINA_OPTS -Dspring.config.location=/config.yml"
  ```

4. jar包外部的application-{profile}.properties或application.yml(带spring.profile)配置文件


5. jar包内部的application-{profile}.properties或application.yml(带spring.profile)配置文件


6. jar包外部的application.properties或application.yml(不带spring.profile)配置文件


7. jar包内部的application.properties或application.yml(不带spring.profile)配置文件


8. @Configuration注解类上的 `@PropertySource`

  ``` java
  @SpringBootApplication
  @PropertySource(value={"file:config.properties"}, ignoreResourceNotFound = true)
  public class SpringbootrestdemoApplication {
    public static void main(String[] args) {
      SpringApplication.run(SpringbootrestdemoApplication.class, args);
    }
  }

  // 注意：@PropertySource注解配置路径的方式不适用于 .yml 文件
  ```