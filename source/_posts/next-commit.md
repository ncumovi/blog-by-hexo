---
title: 配置畅言评论
date: 2018-01-16 15:56:08
tags:
  - hexo
  - next
categories:
  - 前端
comments: true
---


## 为何选择畅言

先在网上搜了一下，发现有好多评论体系，比如 多说、disqus、畅言、gitmmit等，

很多网友说多说已经停止服务，而disqus在国内会被墙，gitmmit则需要有github账号才能评论，

不够亲民，不够接地气，于是果断选择了畅言评论体系，畅言可以支持微信等账号登录。

## 如何注册畅言

这是一个相对比较蛋疼的问题。[注册畅言](http://changyan.kuaizhan.com/)，

痛点就在于畅言需要ICP备案，我一没域名，二没备案，急煞我也。

还好度娘里有大神，有一个神奇的方法，找已经通过ICP备案的网站，

在注册的时候,填入该网站、ICP备案，待审核通过后再把地址和白名单改成自己博客的地址。

## 修改站点配置文件

```
changyan:
  enable: true
  appid: 
  appkey: 
```
将畅言注册所得的appid和appkey填入即可。

## 发表的文章代码开启或者关闭评论功能

```bash
---
title: 配置畅言评论
date: 2018-01-16 15:56:08
tags:
  - hexo
  - next
categories:
  - 前端
comments: true
---
```

将`comments`改为false即可关闭评论功能。