---
title: SmartSpring框架 -- 05.常见问题
copyright: true
related_posts: true
categories: 框架
tags:
  - smartSpring
  - 框架
abbrlink: bdc7564d
date: 2018-04-18 14:56:15
---
#### Q : 返回值 *code* 400405
*A* : GET/POST请求使用错误。 

#### Q : 消费者调用了别人的提供者服务，导致调试困难
*A* : 这是怎么造成的呢？<!-- more -->服务版本号设置是`dubbo.service.version= 0.5_${user.name}`，<br>只要`user.name`不同，理论上是不会存在交叉调用情况的；<br> 但这个`user.name`其实是操作系统当前登陆的用户名，大多数人都是Administrator用户登陆的，<br> 因此会出现版本号相同的情况，dubbo默认的负载均衡策略为Random Loadbalance（随机均衡算法），此时就有可能调用到其他人的服务了。<br>怎样避免这种情况发生呢？修改 user.name

``` text
找到IDEA的安装目录，比如 C:\Program Files\JetBrains\IntelliJ IDEA 2017.2.3\bin 
用文本编辑器打开idea.exe.vmoptions（win32bit）和idea64.exe.vmoptions（win64bit），新建一行
添加-Duser.name=your name
```

#### Q : *service* 项目启动时，日志不停刷新内容：*asyncTotalCount: {false}*

``` java
paginator.PageInterceptor - dialectClass: {***.paginator.dialect.OracleDialect} 
paginator.PageInterceptor - asyncTotalCount: {false}  
```

*A* : 这是由于Mapper.xml中分页的sql有错误，检查近期提交的sql，特别是`parameterType`和`resultType`

#### Q : *service* 项目启动时报错 *object is not an instance of declaring class*
*A* : 这是由于接口类没有暴露方法，导致服务没有实例化。

#### Q : 调用 *dubbo-service* 提供者的接口时报错 *Failed to invoke the method*

``` java
com.alibaba.dubbo.rpc.RpcExeptiom:Failed to invoke the method
com.alibaba.dubbo.rpc.cluster.support.FailoverClusterInvoker.doInvoke(FailoverClusterInvoker.java:101)
```

*A* : 这是由于在zookeeper中没有找到服务提供者，请检查服务提供者是否正常启动、服务版本号是否正确、接口参数类型是否正确。<br> 必要时可参考dubbo-admin控制台信息。

#### Q : 部署在 *linux*服务器上，*service*服务在*zookeeper*上注册的地址与服务器地址不一致。
*A* : 可能是hosts ip 或者 DNS 设置错误

* 检查`/etc/sysconfig/network`中的 **HOSTNAME** 与 `/etc/hosts`中的 **${HOSTNAME}** 对应的 hosts ip地址是否正确；
* 检查`/etc/resolv.conf`中的 **nameserver** 对应的DNS地址是的正确。

#### Q : *service*服务出现异常 *java.io.IOException: Connection reset by peer*
*A* : 出现 Connection reset by peer 原因可能是多方面的，不过更常见的原因是

* 服务器的并发连接数超过了其承载量，服务器会将其中一些连接Down掉；
* 客户关掉了浏览器，而服务器还在给客户端发送数据；

解决方法是：修改zookeeper安装目录下 conf/zoo.cfg文件。将maxClientCnxns参数改成更大的值。

#### Q : *ajax*请求接口返回信息是空，基本信息 *flag、code、message*都没有
*A* : SpringMVC框架返回值为null时，无法对返回值进行Result对象封装，请注意！
