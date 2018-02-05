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

