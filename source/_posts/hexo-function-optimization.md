---
title: Hexo 搭建个人博客：功能强化
copyright: true
related_posts: true
tags:
  - Hexo
  - 搜索
  - DaoVoice
categories: 博客
abbrlink: e0970dc8
date: 2019-05-19 22:08:03
---


开源的力量让第三方插件的支持越来越多，本文介绍了在如何利用插件来加强网站的功能，主要包括:

+ 分类时间线
+ DaoVoice
+ 站内搜索
+ 图片灯箱
<!--more-->

## 分类添加时间线

归档页面的时间线会让文章显示得很有条理，但是分类里却没有，我们可以通过修改布局自己实现这个时间线功能。

在主题的分类布局文件中添加以下代码：

``` diff themes/next/layout/category.swig

	{% for post in page.posts %}
+		{# Show year #}
+			{% set year %}
+			{% set post.year = date(post.date, 'YYYY') %}
+			{% if post.year !== year %}
+			{% set year = post.year %}
+			<div class="collection-title">
+			<h2 class="archive-year motion-element" id="archive-year-{{ year }}">{{ year }}</h2>
+			</div>
+			{% endif %}
+		{# endshow #}
		{{ post_template.render(post) }}
	{% endfor %}
	
	……
	
{% block sidebar %}
  {{ sidebar_template.render(false) }}
{% endblock %}

+{% block script_extra %}
+  {% if theme.use_motion %}
+		<script type="text/javascript" id="motion.page.archive">
+			$('.archive-year').velocity('transition.slideLeftIn');
+		</script>
+	{% endif %}
+{% endblock %}
```

## 在线联系插件DaoVoice

首先去 [DaoVoice官网](http://dashboard.daovoice.io) 注册，这里需要一个 [邀请码](http://dashboard.daovoice.io/get-started?invite_code=3d64b7fd)，贴一个3d64b7fd，或者直接点击邀请码的链接。
注册后就可以查看你的`app_id` ，复制app_id，在**主题配置**文件中添加daovoice的配置：

``` yaml themes\next\_config.yml
# Online contact
daovoice: true
daovoice_app_id: {your app_id}
```

然后在head的布局模板中插入相关代码：
``` diff themes/next/layout/_partials/head.swig

+{% if theme.daovoice %}
+	<script>
+		(function(i,s,o,g,r,a,m){i["DaoVoiceObject"]=r;i[r]=i[r]||function(){(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;a.charset="utf-8";m.parentNode.insertBefore(a,m)})(window,document,"script",('https:' == document.location.protocol ? 'https:' : 'http:') + "//widget.daovoice.io/widget/${your_app_id}.js","daovoice")
+		daovoice('init', {
+			app_id: "{{theme.daovoice_app_id}}"
+		});
+		daovoice('update');
+	</script>
+{% endif %}

{# Export some HEXO Configurations to Front-End #}
<script id="hexo.configurations">
```

具体样式设计可以在 应用设置-->聊天设置 后边改。


## 站内搜索功能

该功能由 [hexo-generator-searchdb](https://github.com/theme-next/hexo-generator-searchdb) 提供。

在根目录下执行以下命令安装相关依赖：

``` bash
$ npm install hexo-generator-searchdb --save
```

在**主题配置**文件中修改相关字段：

``` yaml themes\next\_config.yml
local_search:
  enable: true		# 开启站内搜索
  trigger: auto		# 自动和手动触发
  top_n_per_article: 3  # 每篇文章显示的搜索结果数量
  unescape: false
```

搜索弹框的页边距有点点挤，在自定义样式文件中添加样式规则来增加页边距：

``` css themes\next\source\css\_custom\custom.styl
//增加搜索弹窗的页边距
.local-search-popup #local-search-result {
  padding: 25px 40px
  height: calc(100% - 95px)
}
```

## 添加图片灯箱

添加灯箱功能，实现点击图片后放大聚焦图片，并支持幻灯片播放、全屏播放、缩略图、快速分享到社交媒体等，该功能由 [fancyBox](https://github.com/fancyapps/fancybox) 提供。

在根目录下执行以下命令安装相关依赖：

``` bash
$ git clone https://github.com/theme-next/theme-next-fancybox3 themes/next/source/lib/fancybox
```

在主题配置文件中设置 `fancybox: true`：

``` yaml themes\next\_config.yml
fancybox: true
```

刷新浏览器即可生效。

## 结束语

按照 [yearito](yearito.cn) 的优化精髓，在集成第三方插件或者自定义新功能的过程中尽量做到：

+ 可以在站点配置 / 主题配置文件中方便快捷的开启 / 关闭插件服务
+ 可以在语言包 zh-CN.yml 中快速修改页面中的自定义文案，而不是在代码中将文字表述写死
+ 尽量在自定义样式文件 custom.styl 和自定义布局文件 custom.swig 中添加代码，而非修改主题源码
新增文件统一放在 _custom 目录下
+ 所有以上这些原则，尽管实现起来可能更复杂，需要更多的代码，但都是为了让站点更好维护，更灵活方便。


