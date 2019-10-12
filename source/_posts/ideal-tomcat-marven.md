---
title: ideal启动tomcat(依赖marven插件)
tags:
  - ideal
  - tomcat
  - marven
categories:
  - 工具
date: 2019-10-12 11:45:19
---

#### 前情提要

此前写过一篇[《ideal启动tomcat》](https://ncumovi.github.io/2018/11/09/ideal-tomcat/)的博客，这篇文章建立在此基础之上，这里只讲述差异之处，相同之处不再赘述，为方便阐述，接下来我会将《ideal启动tomcat》这篇文章简称为上篇文章。



#### marven插件依赖选择

这里区别上篇文章所提到的libraries的配置。因为这里用的是marven插件库，故不用选择libraies，可以按照如下操作：

1、建立在我们打开项目的基础之上，选择左上角file下面的setting,找到build Tools选项，选择右侧的marven,然后选择覆盖marven设置文件和仓库文件夹，这里默认的是远程仓库，我们要替换掉user setting file 和 local respository。这里简要说明一下，settingFile是marven设置文件，里面有远程仓库的账户和密码，有这个文件我们才能下载依赖，local respository这个文件夹是用来放下载下来的依赖的。

![选择marven1](/img/ideal-tomcat-marven/select_marven1.png)

![选择marven2](/img/ideal-tomcat-marven/select_marven2.png)

2、我们需要在编辑器的右侧选择**marven** ，然后点击 ** + **，选择后台代码**atom**下面的**pom.xml**文件。

![选择pom1](/img/ideal-tomcat-marven/select_pom1.png)

点击刷新按钮开始下载插件

![选择pom2](/img/ideal-tomcat-marven/select_pom2.png)



## 配置tomcat
这里的配置和上篇文章提到的配置是一样的，只对下图这个选项做个解释，这里表示是否有具有更新功能，默认是重新启动服务，但是我们有时候修改个别class不需要重启服务，所以选择更新资源，重新编译，避免时间的浪费。
![选择p配置tomcatom](/img/ideal-tomcat-marven/set_tomcat.png)
