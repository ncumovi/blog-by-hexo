---
title: js模块化
tags:
  - js
  - module
  - 模块
  - CommonJs
  - AMD
  - CMD
  - ES6
  - require
  - import
  - export
categories:
  - 前端
date: 2018-02-09 14:25:36
---

## Commonjs

* Node.js采用了这个规范。 根据CommonJS规范，一个单独的文件就是一个模块。模块的输出必须通过module.exports导出对外的变量或接口，加载模块使用require方法，该方法读取一个文件并执行，最后返回文件内部的exports对象。

* 在网上看了很多的Commonjs模块的定义，但是去没有说要在node的环境中，否则会报错**module is not defined**。只有在node环境中，每一个node.js执行文件，都自动创建一个module对象，同时，module对象会创建一个叫exports的属性，初始化的值是 {}。而exports是引用 module.exports的值。module.exports 被改变的时候，exports不会被改变，当模块导出的时候，真正导出的执行是module.exports，而不是exports。具体可以参考 [NodeJS中的modu.exports和exports](https://www.jianshu.com/p/7b9f84dbb576)


#### AMD规范

* 相比服务器端广泛使同步加载的CommonJS规范, AMD（异步模块定义）是为浏览器环境设计的。

* requirejs即为遵循AMD规范的模块化工具。通过define方法，将代码定义为模块，然后再require进来
```bash
    //a.js  定义模块
    define(function(require, exports, module) {
        //  导出模块内容
        return {
            a :'movi'
        }
    });

    //b.js 加载模块
    require(['a'], function (a) {
        console.log(a)
    });
```
#### CommonJS与AMD

上面也提到了CommonJS是同步加载模块，也就是说只有加载完成才能执行后续的操作，而AMD是异步加载，允许在回调函数中处理。

由于Node.js主要用于服务器编程，模块文件一般都已经存在于本地硬盘，所以加载起来比较快，不用考虑非同步加载的方式，所以CommonJS规范比较适用。但是，如果是浏览器环境，要从服务器端加载模块，这时就必须采用非同步模式，因此浏览器端一般采用AMD规范。

兼容CommonJS规范的AMD模块输出，这时候需要在定义模块的define方法中这样写：

    define(function(require,exports,module){
        var a = require("a");
        var b = require("b");
        ……
        exports.c = function(){

        }
    })

#### ES6 Modules

ES6语法里面的模块输出**export**和加载模块**import**。之前是在vue的脚手架里面开发，用import和export的时候比较多。因为是ES6语法里面的，所以是无法直接在html页面中使用的，必须要在脚手架或者经过webpack编译打包以后才能使用。

导出模块 a.js

    //导出变量
    export var color = "red";
    export let name = "cz";
    export const age = 25;

    //导出函数
    export function add(num1,num2){
        return num1+num2;
    }

导入模块 b.js

    import { color,add } from "./a.js"

可参考[CommonJS、requirejs、ES6的对比](http://blog.csdn.net/crystal6918/article/details/74906757)