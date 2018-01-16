---
title: Next主题配置
tags:
  - hexo
  - next
categories:
  - 前端
date: 2018-01-16 14:35:19
---

## 1、hexo博客标签页和分类页的生成

#### 标签页的生成

在项目的根目录下面运行该命令生成标签页  **hexo new page tags**

同时修改`/source/tags/index.md` 为

``` bash
title: 标签
date: 日期
type: "tags"
comments: false
```
#### 分类页的生成

在项目的根目录下面运行该命令生成分类页  **hexo new page categories**

同时修改`/source/category/index.md` 为

``` bash
title: 分类
date: 日期
type: "categories"
comments: false
```
#### 文章代码写法

    ---
    title: 新的博客
    tags:
      - 随笔
      - 感悟
    categories:
      - 文学
    date: 2018-01-08 10:49:19
    ---

## 2、背景音乐的插入

#### 获取网易云音乐的外链地址

![外链](music.png)![复制代码](music2.png)

#### 将外链放到指定的模板文件中 `themes\next\layout\_macro\sidebar.swig`

``` bash

{% if theme.links %}
  <div class="links-of-blogroll motion-element {{ "links-of-blogroll-" + theme.links_layout | default('inline') }}">
    <div class="links-of-blogroll-title">
      <i class="fa  fa-fw fa-{{ theme.links_icon | default('globe') | lower }}"></i>
      {{ theme.links_title }}
    </div>
    <ul class="links-of-blogroll-list">
      {% for name, link in theme.links %}
        <li class="links-of-blogroll-item">
          <a href="{{ link }}" title="{{ name }}" target="_blank">{{ name }}</a>
        </li>
      {% endfor %}
    </ul>
  </div>
{% endif %}

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=252563&auto=1&height=66"></iframe>

{% include '../_custom/sidebar.swig' %}


```

## 3、hexo图片插入

#### 找到站点配置文件 

修改 `_config.yml` 中有 `post_asset_folder:true`。

#### 安装图片插件

`npm install https://github.com/CodeFalling/hexo-asset-image --save`


#### 引用

完成安装后用hexo新建文章的时候就会在_post文件下面同步生成一个文件夹，

将静态资源文件放在该文件下即可

` ![图片文字说明](图片名称.png) `

## 4、hexo的next打赏功能

可参考  [next打赏](http://blog.csdn.net/lcyaiym/article/details/76796545)

## 5、hexo的next文字统计以及在线时长

可参考  [文字统计](http://blog.csdn.net/wangxw725/article/details/71602256?utm_source=itdadao&utm_medium=referral)

