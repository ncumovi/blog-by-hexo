---
title: Navicat连接sqlServer
tags:
  - sqlServer
  - database
  - Navicat
categories:
  - 数据库
date: 2018-11-07 10:32:19
---

## Navicat 的下载及安装

#### [Navicat 下载](http://www.formysql.com/)

当然也可以下载破解版的，百搭一下，大把资源。

#### Navicat 的安装

[一路傻瓜式的安装即可](https://blog.csdn.net/pdcfighting/article/details/81669537)

## [Navicat 创建 sql server 连接](https://jingyan.baidu.com/article/48b558e32f2bb67f38c09a01.html)

![创建连接](img/sql-server/create_connection.png)

注：主机名或 ip 地址和端口之间用','隔开。如：127.0.0.1,5050

## Navicat 连接 sql server 遇到的问题

#### 提示安装 sql server native client

    navicat自带sqlncli_x64.msi(64位的)，就在安装目录下，安装后问题解决！

#### [安装 sqlncli_x64.msi 提示**..0x80070422**错误](http://www.win7zhijia.cn/jiaocheng/win7_10193.html)

    这个安装失败是因为系统window update被禁用了，需要window + R 打开窗口运行service.msc,如下图所示：

![service](img/sql-server/service.png)

    找到Window Update服务，将禁用改为手动或者自动，然后确定并应用

![windwo Update](img/sql-server/windows_update.png)

#### 重新再 navicat 上面连接 sql server 数据库即可
