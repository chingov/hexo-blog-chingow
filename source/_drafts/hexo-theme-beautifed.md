---
title: Hexo 搭建个人博客：主题美化
copyright: true
related_posts: false
categories: 博客
tags:
  - Hexo
  - Monda
  - 备案
abbrlink: c7372a12
date: 2019-05-17 10:07:53
---

本文介绍了在 Next 主题的基础上进行美化的方案，主要包括:

+ Font：字体、动态背景
+ Top：进度条，菜单高度收缩
+ Footer：建站时间、备案、访客统计
+ Sidebar：标题、作者、头像、浏览进度、左边显示

大家可以选择自己喜欢的方案美化自己的博客。

  <!--more-->

### Font
前端的美化，可以做的比较多，比如修改字体、修改背景等等，首先修改主题配置文件 _config.yml里的配置`font`

``` diff themes\next\_config.yml
font:
- enable: false
+ enable: true	# 开启前端设置
```

#### 修改字体

微软雅黑太平常了 (lll￢ω￢)，浏览其他人网站的时候看到了一种很有科技感的字体 『**Monda**』，可以从 [frontyukle](https://www.fontyukle.net/cn/1,MONDA) 下载，然后在自服务器上安装字体。修改主题配置里的全局字体配置

``` diff themes\next\_config.yml
global:
- family: Lato
+ family: Monda	# 设置Monda字体
```

#### 设置三角丝带背景

该功能由 Vue 作者 [尤雨溪](http://evanyou.me/) 首创。注意：添加动态背景会极大增加页面内存占用及 CPU 消耗。

首先，在根目录下执行以下命令安装相关依赖：

``` bash
$ git clone https://github.com/theme-next/theme-next-canvas-ribbon themes/next/source/lib/canvas_ribbon
```

然后，修改主题配置文件 _config.yml 里的配置`canvas_ribbon`

``` diff themes\next\_config.yml
canvas_ribbon:
- enable: false
- size: 300
+ enable: true	# 开启随机三角丝带背景
+ size: 90	# 设置丝带宽度
```

---

### Top

#### 加载进度条

首先，在根目录下执行以下命令安装相关依赖：
``` bash
$ git clone https://github.com/theme-next/theme-next-pace themes/next/source/lib/pace
```

然后，修改主题配置文件 _config.yml 里的配置`pace` 改为 `true`，并从上面提供的样式中选择一种填入`pace_theme`中就可以了。

#### 菜单阴影和高度自动收缩

---

### Footer

#### 添加建站时间

#### ICP备案和公安备案

#### 添加访客统计

---

### Sidebar

#### 浏览进度显示

#### 侧边栏左边显示

---
