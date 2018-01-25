---
title: Hexo-next-weibo秀
tags:
  - Hexo
  - next
  - weibo
categories:
  - 前端
date: 2018-01-25 14:20:19
---

## 微博秀代码的获取

我们要登录[新浪微博开放平台](http://app.weibo.com/tool/weiboshow)来获取微博秀的代码

## 新建微博组件

在**themes/next/layout/_marco**目录下面新建**weibo.swig**这个组件

```bash

{% if true %}
  <div class="widget-wrap">
    <h3 class="widget-title">Weibo Show</h3>
    <div class="widget-weibo">
      <iframe width="100%" height="550" class="share_self"  frameborder="0" scrolling="no" src="//widget.weibo.com/weiboshow/index.php?language=&width=0&height=550&fansRow=2&ptype=1&speed=0&skin=5&isTitle=1&noborder=1&isWeibo=1&isFans=1&uid=5143642701&verifier=c044f752&dpc=1"></iframe>
    </div>
  </div>
{% endif %}

```

将微博秀的样式放在**themes/next/source/css/_common/components/sidebar**目录下面的**sidebar.styl**

```bash

.widget-title{
  font-size: 14px;
  font-weight: 600;
  text-align:center;
}

.widget-weibo{
  text-shadow: 0 1px #fff
  border-radius: 3px
}

```

## 微博秀组件的引用

在**themes/next/layout/_marco**目录下面的**sidebar.swig**组件里面引入

具体的位置视情况而定，我是放在友情链接下面的

```
{% if theme.links %}

 ...

{% endif %}

{% include './weibo.swig' %}

```    

## 踩过的坑

1、本地预览的时候要将**localhost:4000**替换成**127.0.0.1**，否则看不到

2、直接在微博里面复制的微博秀代码包含**http://widget.weibo.com**...,一定记得将**http**去掉，否则会谷歌的**https**认为是不安全链接，被浏览器拦截。

  