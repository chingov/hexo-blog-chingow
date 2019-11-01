---
title: Spring Boot - 条件化注解Conditional
copyright: true
related_posts: true
categories: Spring Boot
tags:
  - SpringBoot
  - Spring
  - Conditional
  - ConditionalOnProperty
abbrlink: 4478b9ec
date: 2019-06-17 23:43:32
---

在我们的实际业务开发过程中，往往会有这样的场景：

- 一套接口中有多种不同的实现，但在不同的场景下需使用指定的实现方式（比如微信小程序里仅仅支持微信支付）
- 在集群环境里，存在一个定时任务，定时任务不可重复执行，因此需限制只在一个节点中执行，其他节点不执行

>常规思路中要实现以上的场景需求还是比较麻烦的，可能需要硬编码做 **if** 或者 **swith** 的判断，亦或是使用控制路由在调用的代码里做选择初始化。这些方式都不够优雅，也增强了组件间的耦合性！！！
> 现在SpringBoot里有了 `@Conditional`注解 和 `@ConditionalOnProperty`注解，将变得简单许多。
> 
<!-- more -->

### ConditionalOnProperty注解介绍

Spring4.X中新加入了注解` @Conditional`，可以实现不同条件创建不同的Bean。
SpringBoot框架中封装了注解`@ConditionalOnProperty`，它将配置信息转换成控制某个configuration是否生效的条件。

### ConditionalOnProperty源码解析

``` java  springboot-autoconfigure.jar
package org.springframework.boot.autoconfigure.condition;

@Retention(RetentionPolicy.RUNTIME)
@Target({ ElementType.TYPE, ElementType.METHOD })
@Documented
@Conditional(OnPropertyCondition.class)
public @interface ConditionalOnProperty {

    /**
    * String数组 ，对应property名称的值，与name()不可同时使用，
    * 当 value() 所对应配置文件中的值为false时加载配置，不为fasle不加载配置
    * value() 有多个值时，只要有一个值对应为false,则不加载配置
    */
   String[] value() default {};

    /**
    * 配置中 property 的前缀，可有可无；可与 value 或 name 组合使用
    */
    String prefix() default "";

   /**
    * String数组 ，property完整名称或部分名称，与 value()作用一致但不可同时使用
    */
    String[] name() default {};

    /**
    * 比较获取到的属性值与 havingValue() 给定的值是否相同，相同才加载配置；需与 value 或 name 组合使用需
    */
    String havingValue() default "";

    /**
    * 配置中缺少对应 property 时是否可以加载；为true时缺少对应配置也可加载
    */ 
    boolean matchIfMissing() default false;
}

```

由此可见：它主要通过几个属性值来实现：

- prefix：属性-配置前缀，可不填；
- name：配置文件application.yml中定义的属性名；
- havingValue：生效时的预期值，一般设置为true。

{% note info %}
根据 name 在 application.yml 中获取属性的值，该值与havingValue的值比较，与其相等则当前configuration生效加载此Bean，否则则不生效。当name的值在application.yml 中不存在则返回false。
{% endnote %}

### code示例

``` java 
public interface IPayService {
	void pay();
}


@ConditionalOnProperty(prefix = "pay", name = "type", havingValue = "alipay")
@Service("payService")
public class AliPayServiceImlp implements IPayService {
  @Override
  public void pay() {
    log.info("=====>>>>>使用支付宝支付");
  }
}


@ConditionalOnProperty(prefix = "pay", name = "type", havingValue = "weixin")
@Service("payService")
public class WinxinPayServiceImpl implements IPayService {
  @Override
  public void pay() {
    log.info("=====>>>>>使用微信支付");
  }
}


@RequestMapping(path = "test")
@RestController
public class TempRestController {

  @Autowired
  IPayService payService;

  @PostMapping(path = "pay")
  public void pay() {
    payService.pay();
  }
}
```

在指定的节点配置 pay.type=alipay 或者 weixin

``` properties application.properties
pay.type = alipay
----------
pay.type = weixin
```

### code示例2

``` java 
@ConditionalOnProperty(prefix = "scheduling", name = "enabled", havingValue = "true")
@Component
public class WorkTimer {
  @Scheduled(cron = "0/5 * * * * *")
  public void scheduled() {
    // ...
  }
}
```

在指定的节点加上配置 scheduling.enabled=true，其余节点加上配置scheduling.enabled=false 

``` properties application.properties
scheduling.enabled = false   （或为空，此时定时任务不生效）
----------
scheduling.enabled = true    （此时定时任务生效）
```

### 知识拓展

ConditionalOnProperty注解只能解决简单配置的动态注入，下面还有几种常用的条件注解使用方式，有兴趣的同学可以继续深入学习：

- @ConditionalOnBean：基于Bean的条件注解，根据Bean是否存在来判断是否满足条件。（可用于Bean之间相互依赖的场景）

  ```java
  @Component
  @ConditionalOnBean(name="redisTemplate")
  public class RedisOperBean {
    private final RedisTemplate redisTemplate;
    public RedisOperBean(RedisTemplate redisTemplate) {
        // ...
    }
  }

  // tips：不会因为找不到 RestTemplate 的bean，导致无法实例化 RedisOperBean，从而抛出异常
  ```

- @ConditionalOnMissingBean：和前面一个作用正好相反的，判断Bean是否不存在

- @ConditionalOnClass：从使用来看，和 `ConditionalOnBean`类似，基于class是否存在来做条件判断

- @ConditionalOnExpression ：基于SPEL表达式的条件注解，执行Spel表达式，根据返回的true/false来判断是否满足条件
