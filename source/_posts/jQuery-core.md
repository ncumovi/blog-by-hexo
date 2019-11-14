---
title: jQuery核心功能函数揭秘
tags:
  - jQuery
  - extend
  - init
  - fn
  - $
  - prototype
categories:
  - 源码分析
date: 2019-11-01 21:42:19
---
#### jQuery无new化构建

    我们平时使用jQuery的时候使用的$,然后就会返回一个jQuery的实例，如何做到在调用的时候不使用new也可以得到jQuery的实例呢

    (function(global){

        //类型校验 防错处理
        if(typeof type!='string'){
            throw new Error('type must be a string');
        }
       
        var jQuery = function (){
            //返回jQuery原型对象上init对象的实例 不可直接返回new jQuery(),否则在创建自身实例的时候执行jQuery构造函数，重复执行陷入死循环
            return new jQuery.prototype.init();
        }

        //jQuery原型对象别名fn
        jQuery.fn = jQuery.prototype = {
            init: function(){

            },

            css:function(){

            }
        }

        //让jQuery和init共享原型 让$返回的对象享有jQuery原型上的所有方法
        jQuery.fn.init.prototype = jQuery.fn;


        global.$ =  global.jQuery = jQuery;

    })(this)



#### 共享原型设计：

共享原型设计的核心在于创建jQuery实例的时候，构造函数里面返回的是另一个和jQuery共享原型对象的init构造方法的实例

    ...
    var jQuery = function (){
        //返回jQuery原型对象上init对象的实例 不可直接返回new jQuery(),否则在创建自身实例的时候执行jQuery构造函数，重复执行陷入死循环
        return jQuery.prototype.init();
    }
    ...
    //让jQuery和init共享原型 让$返回的实例对象享有jQuery原型上的所有方法
    jQuery.prototype.init.prototype = jQuery.prototype;
    ...

#### 核心函数extend
    ...
    //extend
    jQuery.fn.extend = jQuery.extend = function(){
        var target = arguments[0] || {};
        var length = arguments.length;
        var i = 1;
        var deep = false;
        var options, name, copy, src, copyIsArray, clone;

        //如果传的第一个值是boolean值 则说明是深拷贝 
        if(typeof target === 'boolean'){
            deep = target;
            target = arguments[1];
        }

        //扩展的第一个对象不是对象的时候 创建空对象复制给他
        if(typeof target != 'object'){
            target = {};
        }
        //参数的个数 如果只有一个则需要拿到当前调用对象 jQuery或者jQuery实例
        if(length === i){ //本身扩展
            target = this; //指向本身
            //把下标变成0，这样就可以在扩展自己本身的时候拿到需要扩展进去的对象   {name:'movi'}
            //$.extend({name:'movi'})
            i--;
        }

        //浅拷贝
        for(; i <length; i++){
            if((options = arguments[i]) != null){
                //options 代表要扩展的对象 {name:'max' ,list:{age:'20'}}    $.extend({}, {name:'max',list:{age:'20'}}); 
                for(name in options){
                    copy = options[name]; // name:'max'  list:{age:'20'}
                    src = target[name]; // undefined
                    //如果是深拷贝 则需要判断当前要扩展的对象是数组或者是对象
                    if(deep && (jQuery.isPlainObject(copy) || (copyIsArray = jQuery.isArray(copy)))){
                        if(copyIsArray){
                            //被扩展的源对象本身不是数组或者没有就创建空数组给他，否则就是自己本身
                            copyIsArray = false; //需要重置 保证每次循环都是最新值
                            clone = src && jQuery.isArray(src) ? src : [];
                        }else{
                             //被扩展的源对象本身不是对象或者没有 就创建空对象给他
                            clone = src && jQuery.isPlainObject(src) ? src : {};
                        }
                        target[name] = jQuery.extend(deep, clone, copy)
                    }else if(copy != undefined){ 
                        target[name] = copy;
                    }
                   
                }
            }
        }

        return target;
    }
    ...


#### 提取标签元素名 并创建dom
    //用正则捕获标签内的元素名字 '<a>' => 'a'
    var  rejectExp = /^<(\w+)\s*\/?>(?:<\/\1>|)$/;
    ...
    //将标签解析为具体的元素名 然后创建该dom对象
    parseHtml: function(selector, context){
        if(!selector || typeof selector != 'string'){
            return null
        }
        //过滤掉尖括号 获得元素名字 '<a>' => 'a'
        var parse = rejectExp.exec(selector); //[<p>, p]
        //创建dom
        return [context.createElement(parse[1])];
    }

#### merge合并数组到对象
    //将数组或者类数组对象和并到this即jQuery实例对象上
    merge: function(first, second){
        var l = first.length;
        var j = second.length;
        var i = 0;
        //如果传进来的第二个参数具有length属性 则遍历second数组 将值赋给first对象 下班从first length++开始
        if(typeof j === 'number'){
            for(i; i < j; i++){
                first[l++] = second[i];
            }
        }else{
            //如果second是类数组 不具备length属性 则直接用while循环 将second每一项值赋给second
            while(second[i] != undefined){
                first[l++] = second[i++];
            }
        }
        //这里将自增以后的l值作为传进来first的length属性值
        first.length = l;
        return first;
    },

#### jQuery初始化方法init 创建dom或者查询dom
    init: function(selector, context){
        context = context || document;
        var match;
        //如果没有传selctor值 则返回当前jQuery实例对象
        if(!selector){
            return this;
        }
        if(typeof selector === 'string'){
            //如果传进来的是字符串 且是类似"<a>"这种带标签的 则表示创建dom节点
            if(selector.charAt(0) === '<' && selector.charAt(selector.length-1) === '>' && selector.length === 3){
                match = [selector];
            }
            if(match){
                //创建dom
                //将创建的dom对象数组 [dom] 与当前jQuery实例合并在一起
                jQuery.merge(this, jQuery.parseHtml(selector, context));
            }else{
                //查询dom 使用querySelectorAll api查询到所有的dom节点
                var ele = document.querySelectorAll(selector);
                //将查询到的dom类数组对象转换为数组对象 并遍历dom数组对象 将他们挂载到jQuery实例对象上
                var eles = Array.prototype.slice.call(ele);
                var len = eles.length;
                for(var i = 0; i < len; i++){
                    this[i] = eles[i];
                }
                this.selector = selector;
                this.context = context;
                this.length = len;
            }
        }else if(selector.nodeType){
            //传过来的是对象且具有nodeType属性 则把当前对象作为当前实例对象的context
            this.context = this[0] = selector;
            this.length = 1;
        }
    }

#### 完整代码
    (function(global){
        //正则捕获标签内的元素名字
        var  rejectExp = /^<(\w+)\s*\/?>(?:<\/\1>|)$/;

        var jQuery = function (selector, context){
            //返回jQuery原型对象上init对象的实例
            return new jQuery.prototype.init(selector, context);
        }

        jQuery.fn = jQuery.prototype = {
            length:0,
            selector:'',
            init: function(selector, context){
                context = context || document;
                var match;
                //如果没有传selctor值 则返回当前jQuery实例对象
                if(!selector){
                    return this;
                }
                if(typeof selector === 'string'){
                    //如果传进来的是字符串 且是类似"<a>"这种带标签的 则表示创建dom节点
                    if(selector.charAt(0) === '<' && selector.charAt(selector.length-1) === '>' && selector.length === 3){
                        match = [selector];
                    }
                    if(match){
                        //创建dom
                        //将创建的dom对象数组 [dom] 与当前jQuery实例合并在一起
                        jQuery.merge(this, jQuery.parseHtml(selector, context));
                    }else{
                        //查询dom 使用querySelectorAll api查询到所有的dom节点
                        var ele = document.querySelectorAll(selector);
                        //将查询到的dom类数组对象转换为数组对象 并遍历dom数组对象 将他们挂载到jQuery实例对象上
                        var eles = Array.prototype.slice.call(ele);
                        var len = eles.length;
                        for(var i = 0; i < len; i++){
                            this[i] = eles[i];
                        }
                        this.selector = selector;
                        this.context = context;
                        this.length = len;
                    }
                }else if(selector.nodeType){
                    //传过来的是对象且具有nodeType属性 则把当前对象作为当前实例对象的context
                    this.context = this[0] = selector;
                    this.length = 1;
                }
            }
        }

        //extend
        jQuery.fn.extend = jQuery.extend = function(){
            var target = arguments[0] || {};
            var length = arguments.length;
            var i = 1;
            var deep = false;
            var options, name, copy, src, copyIsArray, clone;

            if(typeof target === 'boolean'){
                deep = target;
                target = arguments[1];
            }

            //扩展的第一个对象不是对象的时候 创建空对象复制给他
            if(typeof target != 'object'){
                target = {};
            }
            //参数的个数 如果只有一个则需要拿到当前调用对象 jQuery或者jQuery实例
            if(length === i){ //本身扩展
                target = this; //指向本身
                //把下标变成0，这样就可以在扩展自己本身的时候拿到需要扩展进去的对象   {name:'movi'}
                //$.extend({name:'movi'})
                i--;
            }

            //浅拷贝
            for(; i <length; i++){
                if((options = arguments[i]) != null){
                    //options 代表要扩展的对象 {name:'max' ,list:{age:'20'}}    $.extend({}, {name:'max',list:{age:'20'}}); 
                    for(name in options){
                        copy = options[name]; // name:'max'  list:{age:'20'}
                        src = target[name]; // undefined
                        //如果是深拷贝 则需要判断当前要扩展的对象是数组或者是对象
                        if(deep && (jQuery.isPlainObject(copy) || (copyIsArray = jQuery.isArray(copy)))){
                            if(copyIsArray){
                                //被扩展的源对象本身不是数组或者没有就创建空数组给他，否则就是自己本身
                                copyIsArray = false; //需要重置 保证每次循环都是最新值
                                clone = src && jQuery.isArray(src) ? src : [];
                            }else{
                                //被扩展的源对象本身不是对象或者没有 就创建空对象给他
                                clone = src && jQuery.isPlainObject(src) ? src : {};
                            }
                            target[name] = jQuery.extend(deep, clone, copy)
                        }else if(copy != undefined){ 
                            target[name] = copy;
                        }
                    
                    }
                }
            }

            return target;
        }

        //让jQuery和init共享原型 让$返回的对象享有jQuery原型上的所有方法
        jQuery.fn.init.prototype = jQuery.fn;

        jQuery.extend({
            //给jQuery本身扩展类型检测的方法
            isPlainObject: function(obj){
                return toString.call(obj) === '[object Object]';
            },
            isArray: function(obj){
                return toString.call(obj) === '[object Array]';
            },
            //将数组或者类数组对象和并到this即jQuery实例对象上
            merge: function(first, second){
                var l = first.length;
                var j = second.length;
                var i = 0;
                //如果传进来的第二个参数具有length属性 则遍历second数组 将值赋给first对象 下班从first length++开始
                if(typeof j === 'number'){
                    for(i; i < j; i++){
                        first[l++] = second[i];
                    }
                }else{
                    //如果second是类数组 不具备length属性 则直接用while循环 将second每一项值赋给second
                    while(second[i] != undefined){
                        first[l++] = second[i++];
                    }
                }
                //这里将自增以后的l值作为传进来first的length属性值
                first.length = l;
                return first;
            },
            //将标签解析为具体的元素名 然后创建该dom对象
            parseHtml: function(selector, context){
                if(!selector || typeof selector != 'string'){
                    return null
                }
                //过滤掉尖括号 获得元素名字 '<a>' => 'a'
                var parse = rejectExp.exec(selector); //[<p>, p]
                return [context.createElement(parse[1])];
            }

        }) 

        global.$ =  global.jQuery = jQuery;
    })(this)    