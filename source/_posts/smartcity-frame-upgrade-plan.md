---
title: smartcity框架 -- 06.升级计划
copyright: true
related_posts: true
categories: 框架
tags:
  - smartcity
  - 框架
abbrlink: 521b8a35
date: 2018-04-18 14:50:05
---
### *V 0.5.0-SNAPSHOT* 
    初始化版本，完成时间 2017-07-20
* 技术选型落地，规范开发流程和项目结构
* 在`pom.xml`里预定义常用开源组件依赖版本
* 拦截`Response`的返回值，自动封装成 [Result](https://ws1.sinaimg.cn/large/006tNc79ly1g2jvhtn6yqj30ts0l8tb1.jpg) 
* 拦截`RuntimeException`异常，统一处理自动封装
<!-- more -->
* 融合 *Hibernate Validator* 验证框架，简化参数校验流程
* 初始化常用工具类，包含字符处理、集合处理、参数验证、时间格式、Json处理、SQL过滤等


### *V 0.5.0*
    第一个迭代版本，完成时间 2018-04-08
* 增加了插件配置信息和环境配置信息预定义
* 集中管理分布式应用的`APP Session`
* 根据 Token 认证增强 APP 接口安全性
* 支持处理自定义异常
* 自动执行*SQL*初始化脚本
* 支持自定义打包环境路径

### *V 0.5.1*
    优化0.5.0版本中提出的问题，进行中……
* 为 APP 接口安全性验证增加一个开关，方便开发调试
* 优化 *mybatis-generator* 插件：实现批量生成和指定生成的功能

