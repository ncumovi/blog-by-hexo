---
title: mysql安装以及配置和启动
tags:
  - mysql
  - database
categories:
  - 后台
date: 2018-10-31 10:52:19
---

## mysql的下载及安装

#### mysql的下载

 [mysql下载地址](https://dev.mysql.com/downloads/mysql/)
 
 选择对应的下载文件。（我电脑是64位，所以这下载的是64位的下载文件）
 
#### mysql的安装

#### msi格式的

直接点击安装，按照它给出的安装提示进行安装<br>

#### zip格式的
zip格式是自己解压，解压缩之后其实MySQL就可以使用了。

## mysql的启动

    2-1、找到mysql的安装目录 **D:\tool\mysql-5.6.42-winx64\bin**
    
    2-2、双击mysqld.exe启动mysql服务
    
    2-3、启动cmd命令提示窗，cd到bin目录下，输入mysql -u root -p，并回车，
    
    这个时候提示输入密码，一般初始密码为空，直接回车就进入mysql服务

## [用户和数据库的创建以及相关操作](https://www.cnblogs.com/w-wfy/p/6487460.html)
    
#### 数据库的创建

    create database mydatabase;

#### 创建用户

    insert into mysql.user(Host,User,Password) values("localhost","test",password("1234"));
    
注意：此处的"localhost"，是指该用户只能在本地登录，不能在另外一台机器上远程登录。如果想远程登录的话，将"localhost"改为"%"，表示在任何一台电脑上都可以登录。也可以指定某台机器可以远程登录

#### 为用户授权
    
    授权格式：grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码";
    
    授权test用户拥有testDB数据库的所有权限（某个数据库的所有权限）
    grant all privileges on testDB.* to test@localhost identified by '1234';
    
    如果想指定部分权限给一用户:
    grant select,update on testDB.* to test@localhost identified by '1234';
    
    授权test用户拥有所有数据库的某些权限： 　
    grant select,delete,update,create,drop on *.* to test@"%" identified by "1234";

注：如果授权不成功，则先执行 **flush privileges**（刷新系统权限），然后再授权

#### 用户登录

    exit; //退出当前用户
    
    mysql -u test -p;
    然后输入密码登录
    
注：上一步的授权里不要用**test@'%'**，而是用**test@localhost**,否则会被拒绝登录，[这里有说明](https://blog.csdn.net/silyvin/article/details/53351146)。

#### [第三方工具软件的接入](https://jingyan.baidu.com/article/495ba841c1731b38b30ede84.html)

    创建mysql连接，输入刚刚创建的用户名和密码就可以成功连接到我们创建的数据库，
    
    然后就可嘿嘿嘿的创建各种表了,从而进行各种操作了。

