---
title: 使用django框架搭建环境遇到的坑
tags:
  - django
  - mysql
  - web
categories:
  - 后台
date: 2018-10-31 16:44:19
---

#### 数据库的迁移

1、在你不适用自带的sqlite3数据库时，django会要求你将django自带的一些表迁移到你现有的数据库中
    
    python manage.py migrate
    
2、这个时候可能会问你安装了mysql客户端没
    
    Did you install mysqlclient?
    
3、然后运行 

    pip install mysqlclient
    
    又报错 Microsoft vistudo C++ 14.0 is required
    
4、找了一堆资料，安装了Visual Studio 2017，然而并没有什么卵用，后来又查资料说可能是兼容性的问题，因此需要直接安装[mysqlclent编译包](https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysqlclient)，然后下载资源，cp后面的数字代表python版本号。

5、最好将刚刚下载的资源放到你的项目目录里面，然后

    python install mysqlclient‑1.3.13‑cp37‑cp37m‑win_amd64.whl
    
终于黄天不负有心人，成功安装了mysqlclient

6、运行以下命令，迁移数据库

    python manage.py migrate
    
成功迁移

#### 创建model

1、在自己创建的项目包(polls)中，编辑models.py 

    from django.db import models
    
    class Question(models.Model):
        question_text = models.CharField(max_length=200)
        pub_date = models.DateTimeField('date published')
       
    class Choice(models.Model):
        question = models.ForeignKey(Question, on_delete=models.CASCADE)
        choice_text = models.CharField(max_length=200)
        votes = models.IntegerField(default=0)

2、在setings.py里面加入我们创建的项目apps.py

    INSTALLED_APPS = [
        'polls.apps.PollsConfig',
        'django.contrib.admin' - 管理站点。你很快就会用到它。
        'django.contrib.auth' - 认证系统。
        'django.contrib.contenttypes' - 内容类型的框架。
        'django.contrib.sessions' - 会话框架。
        'django.contrib.messages' - 消息传递框架。
        'django.contrib.staticfiles' - 用于管理静态文件的框架。
    ]
    
    
   
    
3、生产迁移数据库的初始数据(**polls/0001_inital.py**)

    py manage.py makemigrations polls

4、在命令窗口生产创建表格以及相关字段的sql语句

    py manage.py makemigrations polls 0001
    
5、 运行以下命令则将生成表格以及相关字段，同时迁移到我们自己创建的数据库中 

    py manage.py migrate
    
    
####   创建超级用户

    py manage.py createsuperuser
    
    Username: admin
    
    Email address: admin@example.com
    
    Password: **********
    Password (again): *********
    Superuser created successfully
    
注：输入密码没反应，窗口没任何显示，一片空白，其实已经输入了，只是没有显示，输完直接回车，再输入回车就会创建ok了。
    
    
#### 时区的设置

django默认是英文，时区是UTC
    
    LANGUAGE_CODE = 'en-us'

    TIME_ZONE = 'UTC'

语言改为中文(亲测改成zh-cn无效)，时区改为中国
    
    LANGUAGE_CODE = 'zh-Hans'

    TIME_ZONE = 'Asia/Shanghai'
    
    