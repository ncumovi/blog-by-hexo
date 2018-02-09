---
title: 使用electron打包桌面应用
tags:
  - electron
  - desk-application
  - 桌面应用
  - node
  - vue
categories:
  - 前端
date: 2018-02-05 10:31:19
---

## 项目代码

[github仓库代码](https://github.com/ncumovi/gallery-desk-application)

## vue项目

确保自己有一个vue项目，可以是vue脚手架工程目录，也可以是已经生成的静态资源文件

## electron安装与使用

#### 安装electron以及相关工具

在项目根目录下面运行**npm install electron --save-dev** [安装electron](https://electronjs.org/)

**npm install electron-packager --save-dev** [安装exe打包工具](https://github.com/electron-userland/electron-packager)

**npm install grunt --save-dev** 安装grunt用于打包可安装的exe

**npm install --save-dev grunt-electron-installer** [安装grunt打包可安装的exe插件](https://github.com/electron-archive/grunt-electron-installer)


#### 在本地克隆electron官方demo项目

git clone https://github.com/electron/electron-quick-start


#### 在Vue项目里面引入electron主程序main.js

把electron-quick-start项目中的main.js搬到vue的build文件中，并改个名字electron.js。

将electron.js里面的文件路径修改成符合本项目的路径

    mainWindow.loadURL(url.format({
        pathname: path.join(__dirname, '../dist/index.html'),
        protocol: 'file:',
        slashes: true
    }))

#### 在package.json中添加入口

    "scripts": {
        "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
        "start": "npm run dev",
        "build": "node build/build.js",
        "electron_dev": "electron build/electron.js",  //本地调试用
        //打包成不用安装的exe文件
        "electron_build": "electron-packager ./dist/ beanSprout --win --out beanSprout --platform=win32 --arch=x64 --icon=./src/assets/favicon.ico --version 1.3.4.0  --ignore=node_modules --overwrite", 
        "ele_setup_build": "grunt" //打包成可安装的安装包
    },

## dev 调试环境

先运行**npm run build**将vue项目打包成静态资源文件，再运行**electron_dev**即可看到调试环境下的桌面应用

## 打包不用安装的exe文件(electron-packager)

运行**npm run electron_build**即可打包成不用安装的exe文件

"electron-packager ./dist/ beanSprout --win --out beanSprout --platform=win32 --arch=x64 --icon=./src/assets/favicon.ico --version 1.3.4.0  --ignore=node_modules --overwrite"

1、 './dist' 表示打包的源文件，即dist目录下面打包好的资源文件(需要在这个目录下面添加electron运行的入口)

    dist
    ├── static
    ├── index.html
    ├── main.js
    └── package.json

这里的main.js就是build目录下面的electron.js，不过这里的路径要修改一下

    mainWindow.loadURL(url.format({
        pathname: path.join(__dirname, 'index.html'),  //这里的路径要修改
        protocol: 'file:',
        slashes: true
    }))

2、'beanSprout' 打包exe的名字

3、 '--win --out beanSprout' 输出的文件夹名字

4、'--platform=win32' 平台

5、 '--icon=./src/assets/favicon.ico' 打包以后的icon，即exe图标

打包成功以后的目录如下

    beanSprout
        └── beanSprout-win32-x64
                    ├── ...
                    └── beanSprout.exe  //双击即可运行

本例参看 [j_bleach博客-electron 将pc端（vue）页面打包为桌面端应用](http://blog.csdn.net/j_bleach/article/details/78513282)

## 打包需要安装的exe文件目录(grunt-electron-installer)

首先在根目录下面新建**Gruntfile.js**

    var grunt = require('grunt');

    //进行配置
    grunt.config.init({
    pkg:grunt.file.readJSON('package.json'),
    'create-windows-installer': {
        x64: {
            appDirectory: './beanSprout/beanSprout-win32-x64', //已经打包的electron App目录
            outputDirectory: './beanSprout/beanSproutSetup',    //输出的资源目录
            authors: 'movi',    //发布者
            exe: 'beanSprout.exe',  //这里的名字要和之前打包成不需安装exe文件的名字保持一致
            description:" A desk application based on vue and elecron",
            loadingGif:"./src/assets/install.gif",  //安装动画
            setupIcon:"./src/assets/favicon.ico" //生成桌面快捷方式的图标
        }
    }
    })

    //加载任务
    grunt.loadNpmTasks('grunt-electron-installer')

    //设置默认任务
    grunt.registerTask('default',['create-windows-installer'])

    grunt.tasks()


运行**npm run ele_setup_build**即可打包成需要安装的exe文件目录

打包成功以后的目录如下

    beanSprout
        └── beanSproutSetup
                ├── ...
                └── Setup.exe  //双击开始安装

可参看 [Long-Island的博客-使用Grunt 插件打包Electron Windows应用](http://blog.csdn.net/w342916053/article/details/51701722)

## 自动生成快捷方式

需要在./dist/main.js加上如下代码

    var handleStartupEvent = function() {
    if (process.platform !== 'win32') {
        return false;
    }

    var squirrelCommand = process.argv[1];
    switch (squirrelCommand) {
        case '--squirrel-install':
        case '--squirrel-updated':
        install();
        return true;
        case '--squirrel-uninstall':
        uninstall();
        app.quit();
        return true;
        case '--squirrel-obsolete':
        app.quit();
        return true;
    }

    // 安装
    function install() {
        var cp = require('child_process');
        var updateDotExe = path.resolve(path.dirname(process.execPath), '..', 'update.exe');
        var target = path.basename(process.execPath);
        var child = cp.spawn(updateDotExe, ["--createShortcut", target], { detached: true });
        child.on('close', function(code) {
            app.quit();
        });
    }


    // 卸载
    function uninstall() {
        var cp = require('child_process');
        var updateDotExe = path.resolve(path.dirname(process.execPath), '..', 'update.exe');
        var target = path.basename(process.execPath);
        var child = cp.spawn(updateDotExe, ["--removeShortcut", target], { detached: true });
        child.on('close', function(code) {
            app.quit();
        });
    }

    };

    if (handleStartupEvent()) {
    return;
    }

## 注意

当在dist目录下面生成electron app时，在packjson里面定义版本号
```
"name": "beanSprout",
"version": "1.0.1"   //亲测1.0.0和1.0.1有用
```

这里的版本不能随便写，不然在用**grunt-electron-installer**打包成安装应用时会提示版本号不对，不符合规范等，导致打包失败

## 新版功能用到的知识

1、引入electron shell模块运行bat脚本

    const {shell} = require('electron');
    shell.openItem(path); //path 本地bat文件的路径

增加*.bat脚本 可以执行相关js操作 类似于cmd窗口，在bat脚本前面加入以下代码可以隐藏cmd运行窗口

    @echo off
    if "%1" == "h" goto begin
    mshta vbscript:createobject("wscript.shell").run("""%~nx0"" h",0)(window.close)&&exit
    :begin

2、将根目录下面的package.json文件中的入口修改如下,删除build下面的electron.js

    "scripts": {
        ...
        "electron_dev": "electron dist/main.js ",
        ...
    },

3、新增add-new.html用于上传图片储存本地，用到的相关模块如下

    node.js的fs模块的fs.open()、fs.write()、fs.writeFile()写文件和修改文件

4、页面之间的通讯

    //add-new.html文件里面js
    const ipc = require('electron').ipcMain;    
    ipc.send('build') //build的指令。send到主进程index.js中。

    //main.js主程序接收事件
    const ipc = require('electron').ipcMain;
    ipc.on('build',function() {
        shell.openItem(path_run_dev);
    })

## 思考

最初的想法是想在electron外壳上加菜单栏，属于系统菜单，因此新建菜单。但是实际过程中因为路径的问题操作起来比较费劲，在你打包成exe文件的时候，只是把vue打包生成的静态资源文件夹dist打包了过去，如果你只是单纯的手动在dist里面添加页面，那么在打包的时候一定会报错。后来发现应该在vue里面写add-new模块的功能，在页面实现菜单相关功能会更容易实现些。