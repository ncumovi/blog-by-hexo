---
title: ideal启动tomcat
tags:
  - ideal
  - tomcat
categories:
  - 工具
date: 2018-11-09 13:59:19
---

## ideal 启动 tomcat 环境前置条件

前置条件就是你安装了 IDEA 编辑器、JDK 以及 Tomcat。同时本地要有一个 java 项目，否则拿什么去启动，当然你也可以本地新建一个，不过不在本次讲解范畴之内。。。

## 配置项目

#### 打开 project structure 进行配置

![打开项目结构进行设置](img/ideal-tomcat/open_project_structure.png)

#### project 的配置

1、项目名称随便写，一般会默认当前项目名称

2、选择安装好的 jdk

3、选择项目语言级别，这里选 8，否则编译会报错，有兴趣的童鞋可以尝试一下

4、选择项目编译输出，项目在编译的时候会有一个输出项目，我们这里直接选择当前项目目录下的**out**文件夹，没有的话可以直接编辑成**/当前项目/out**,这里的 out 文件夹在后面编译的时候回生成。

![project配置](img/ideal-tomcat/project.png)

#### module 的配置

![module](img/ideal-tomcat/module.png)

1、点开 module，这里 ideal 已经自动帮我们加载好了我们的项目

2、点击 source 目录，然后选择我们要运行的 java 目录，也就时项目目录下**src\main\java**,将其作为编译的资源文件

![配置module的source](img/ideal-tomcat/moudle_source.png)

这里选择第一个，也就是我们项目编译输出的目录**out**

![配置module的path](img/ideal-tomcat/module_path.png)

module 里面的 dependencies 里面不用选择，ideal 会帮我们自动填好，直接 ok 行

![配置module的dependencies](img/ideal-tomcat/module_dependenciespng.png)

#### libraries 的配置

新增 java,选择当前项目运行所依赖的 jar 包

![新增依赖](img/ideal-tomcat/libraries.png)
![新增依赖](img/ideal-tomcat/library_dep.png)
![选择当前module](img/ideal-tomcat/choose_module.png)

#### Facets 的配置

点击**+**新增 web

![新增web](img/ideal-tomcat/facets_web.png)

![选择当前项目模块](img/ideal-tomcat/facet_web1.png)

![注意web的路径](img/ideal-tomcat/facet_web2.png)

#### Artifacts 的配置

点击**+**新增,选择 Web Application:Exploded,然后选择 from modules

![新增artifacts](img/ideal-tomcat/artifacts_add.png)

选择当前项目

![选择模块](img/ideal-tomcat/art_modules.png)

## 配置 tomcat

点击 file，打开设置选项，搜索**Application Server**,然后选则 server，添加**tomcat server**，选择 tomcat 的安装路径

![添加tomcat server](img/ideal-tomcat/tomcat_config1.png)

![选择tomcat路径](img/ideal-tomcat/tomcat_path.png)

点击 add configurations，配置 tomcat

![add configurations](img/ideal-tomcat/add_config.png)

点击**+**选择 tomcat server,选择本地 local。

![新增本地tomcat](img/ideal-tomcat/tomcat_add1.png)

输入 tomcat 的名称

![输入tomat的名称](img/ideal-tomcat/tomat_last.png)

最后一步，需要配置 tomcat 的 Deployment,设置依赖

![配置tomcat的Deployment](img/ideal-tomcat/tomat_last1.png)

## 配置完成,大功告成。

![点击run开启tomcat](img/ideal-tomcat/tomcat_run.png)

## 如何在配置好启动本地服务后，用本地后台代码打包成 jar 包

![本地编译打包1](img/ideal-tomcat/buildJar1.png)

![本地编译打包2](img/ideal-tomcat/buildJar2.png)

然后就会在 target 文件夹下面生成 jar 包
