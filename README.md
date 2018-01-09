## blog-by-hexo

基于hexo框架结合github服务器搭建的个人博客

## live demo

http://ncumovi.github.io

## hexo的安装以及初始化项目

#### 安装hexo

npm install -g hexo

#### 初始化项目
hexo init 

#### 生成静态页面

hexo generate（hexo g也可以）

#### 本地启动

启动本地服务，进行文章预览调试，命令：

hexo server

浏览器输入http://localhost:4000

## 配置Github

1、建立与你用户名对应的仓库，仓库名必须为【your_user_name.github.io】

2、找到hexo初始化项目下面的站点配置文件_config.yml

3、修改如下配置

    deploy:

       type: git

       repo: git@github.com:yourname/yourname.github.io.git   //千万注意用ssh

       branch: master

## 部署到github服务器上

1、安装插件 否则提示找不到git

    npm install hexo-deployer-git --save     

2、 推送服务器
    
    hexo deploy

## 常用hexo命令

    hexo new"postName" #新建文章

    hexo new page"pageName" #新建页面

    hexo generate #生成静态页面至public目录

    hexo server #开启预览访问端口 本地服务

    hexo deploy #将.deploy目录部署到GitHub

    hexo help # 查看帮助

    hexo version #查看Hexo的版本

