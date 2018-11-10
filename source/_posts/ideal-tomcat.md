---
title: ideal启动tomcat
tags:
  - ideal
  - tomcat
categories:
  - 工具
date: 2018-11-09 13:59:19
---

## ideal启动tomcat环境前置条件

前置条件就是你安装了IDEA编辑器、JDK以及Tomcat。同时本地要有一个java项目，否则拿什么去启动，当然你也可以本地新建一个，不过不在本次讲解范畴之内。。。


## 配置项目

#### 打开project structure进行配置
![打开项目结构进行设置](/img/ideal-tomcat/open_project_structure.png)

#### project的配置

1、项目名称随便写，一般会默认当前项目名称

2、选择安装好的jdk

3、选择项目语言级别，这里选8，否则编译会报错，有兴趣的童鞋可以尝试一下

4、选择项目编译输出，项目在编译的时候会有一个输出项目，我们这里直接选择当前项目目录下的**out**文件夹，没有的话可以直接编辑成**/当前项目/out**,这里的out文件夹在后面编译的时候回生成。

![project配置](/img/ideal-tomcat/project.png)

#### module的配置

![module](/img/ideal-tomcat/module.png)

1、点开module，这里ideal已经自动帮我们加载好了我们的项目

2、点击source目录，然后选择我们要运行的java目录，也就时项目目录下**src\main\java**,将其作为编译的资源文件

![配置module的source](/img/ideal-tomcat/moudle_source.png)

这里选择第一个，也就是我们项目编译输出的目录**out**

![配置module的path](/img/ideal-tomcat/module_path.png)

module里面的dependencies里面不用选择，ideal会帮我们自动填好，直接ok行

![配置module的dependencies](/img/ideal-tomcat/module_dependenciespng.png)

#### libraries的配置

新增java,选择当前项目运行所依赖的jar包

![新增依赖](/img/ideal-tomcat/libraries.png)
![新增依赖](/img/ideal-tomcat/library_dep.png)
![选择当前module](/img/ideal-tomcat/choose_module.png)

#### Facets的配置

点击**+**新增web

![新增web](/img/ideal-tomcat/facets_web.png)

![选择当前项目模块](/img/ideal-tomcat/facet_web1.png)

#### Artifacts的配置

点击**+**新增,选择Web Application:Exploded,然后选择from modules
 
![新增artifacts](/img/ideal-tomcat/artifacts_add.png)

选择当前项目

![选择模块](/img/ideal-tomcat/art_modules.png)


## 配置tomcat

点击file，打开设置选项，搜索**Application Server**,然后选则server，添加**tomcat server**，选择tomcat的安装路径

![添加tomcat server](/img/ideal-tomcat/tomcat_config1.png)

![选择tomcat路径](/img/ideal-tomcat/tomcat_path.png)

点击add configurations，配置tomcat

![add configurations](/img/ideal-tomcat/add_config.png)

点击**+**选择tomcat server,选择本地local。

![新增本地tomcat](/img/ideal-tomcat/tomcat_add1.png)

输入tomcat的名称

![输入tomat的名称](/img/ideal-tomcat/tomat_last.png)


## 配置完成,大功告成。

![点击run开启tomcat](/img/ideal-tomcat/tomcat_run.png)

