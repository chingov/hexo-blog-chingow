---
title: Hexo 搭建个人博客：性能优化
copyright: true
related_posts: true
tags:
  - Hexo
  - Next
  - 七牛云
categories: 博客
abbrlink: e824570
date: 2019-05-20 17:15:23
---

在访问很多博客的时候，页面加载和响应速度往往都要上十秒，严重影响用户的体验。
本文将探究如何利用常用的方案来进行性能优化，主要包括:

- CDN加速
- Nginx压缩、缓存
- 图床

<!--more-->

首先，可利用 [Google PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/) 帮助分析网页加载速度，根据报告结果和优化建议进行针对性的优化。
常见的网站提速方案有：cdn加速，压缩源文件，nginx gzip压缩，减少网站一些不必要的引入，图片大小等。

### CDN加速

在阅读下文之前，如果你还不知道 CDN 是什么，请先移步[百度百科：CDN词条](https://baike.baidu.com/item/CDN)进行一些了解。
在所有静态资源中，对加载速度影响较大且存在大幅优化空间的主要还是「JavaScript 第三方库」脚本，设定成合适的 CDN 地址，此特性可以加速静态资源的加载。
对于我 Hexo 博客来说，Next 主题已经做好了配置，只需添加 CDN 加载源，将其改为从公共 CDN 加载即可。
在主题配置文件中修改`vendors`：

``` yaml themes/next/_config.yml
  # Example:
  # jquery: //cdn.jsdelivr.net/npm/jquery@2/dist/jquery.min.js
  # jquery: //cdnjs.cloudflare.com/ajax/libs/jquery/2.1.3/jquery.min.js
  jquery: //cdn.jsdelivr.net/npm/jquery@2.1.3/dist/jquery.min.js

  ...  
```

比较常用的开源项目 CDN 服务商主要有 unpkg、bootcdn、 cdnjs、jsdelivr 和 cloudflare，本站主要使用 jsdelivr 提供的 CDN 加速服务。

### Nginx压缩、缓存

Nginx 是一个高性能的 Web 服务器，合理配置可以有效提高网站的响应速度。

#### 开启gzip

gzip压缩页面需要浏览器和服务器双方都支持，实际上就是服务器端压缩，传到浏览器后浏览器解压并解析。
修改nginx.conf，在http模块中增加gzip配置：

``` html
  #开启gzip压缩;
  gzip  on;

  #设置允许压缩的页面最小字节数;
  gzip_min_length 1k;

  #设置压缩缓冲区大小，此处设置为4个16K内存作为压缩结果流缓存
  gzip_buffers 4 16k;

  #压缩版本
  gzip_http_version 1.1;

  #设置压缩比率，最小为1，处理速度快，传输速度慢；9为最大压缩比，处理速度慢，传输速度快;级别越高，压缩就越小
  gzip_comp_level 6;

  #制定压缩的类型
  gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/javascript application/json image/svg+xml application/x-font-ttf font/opentype image/x-icon;

  #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
  gzip_disable "MSIE [1-6]\.";

  #选择支持vary header；改选项可以让前端的缓存服务器缓存经过gzip压缩的页面; 这个可以不写
  gzip_vary on;

```

#### 开启缓存

修改nginx.conf，在server中配置缓存和失效时间：

``` html
location ~* ^.+\.(ico|gif|jpg|jpeg|png)$ {
    access_log off;
    expires 30d;
}

location ~* ^.+\.(css|js|txt|xml|swf|wav)$ {
    access_log off;
    expires 24h;
}

location ~* ^.+\.(html|htm)$ {
     expires 1h;
}
```

### 图床的好处

- 可以减轻服务器的存储压力；
- 减轻应为图片带来的额外的流量消耗；
- 图床一般都是具有cdn加速的，可以让你的网页变得更快。
  
我主要是看中了cdn加速这点，这个对网站的性能提升太重要了。

--- 待完成 ---
