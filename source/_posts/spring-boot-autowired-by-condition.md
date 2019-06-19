---
title: Spring Boot - 根据配置动态注入Bean
copyright: true
related_posts: true
tags:
  - SpringBoot
  - Spring
  - Conditional
  - ConditionalOnProperty
categories: Spring Boot
abbrlink: 4478b9ec
date: 2019-06-17 23:43:32
---


假设我们有这样一些场景：
- 一套支付接口中有多种不同的支付实现：支付宝、微信支付等，有些场景下只能使用某一种或几种支付方式（比如微信小程序里仅仅支持微信支付）
- 在集群环境里，存在一个定时任务，定时任务不可重复执行，因此需限制只在一个节点中执行，其他节点不执行

Spring4.X中新加入了注解` @Conditional`，可以实现不同条件创建不同的Bean。
SpringBoot中有个注解 `@ConditionalOnProperty`，它能够根据将配置信息转换成控制某个configuration是否生效的条件。
<!--more-->

`ConditionalOnProperty` 注解源码解析

``` java
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

{% note info %}
由此可见：它主要通过几个属性值来实现：

- prefix：属性-配置前缀，可不填；
- name：配置文件application.yml中定义的属性名；
- havingValue：生效时的预期值，一般设置为true。
{% endnote %}

根据 name 在 application.yml 中获取属性的值，该值与havingValue的值比较，与其相等则当前configuration生效加载此Bean，否则则不生效。当name的值在application.yml 中不存在则返回false。

常规思路中要实现以上的场景需求还是比较麻烦的，可能需要硬编码做if判断，亦或是在调用的代码里做选择初始化。这些方式都不够优雅，也增强了组件间的耦合性！！！
那么现在有了 `@Conditional`注解 和 `@ConditionalOnProperty`注解后，事情是不是就简单多了？