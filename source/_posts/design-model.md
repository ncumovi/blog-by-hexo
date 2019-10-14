---
title: JS设计模式
tags:
  - js
  - design
  - model
categories:
  - 理论
date: 2019-10-14 20:08:19
---
#### 关于js设计模式的简要总结

按照功能我们将设计模式分为 创建型设计模式、结构性设计模式、行为设计模式、技巧型设计模式

#### 创建型设计模式(创建对象的方式)：

(1)、工厂模式：定义一个方法，返回给你对象。需要创建对象的时候，只需要调用和这个工厂方法即返回对象，分为单一工厂模式和建造者模式。

a、单一工厂模式：需要创建大量对象的时候，不关心创建过程，只关注创建结果，典型代表：JQuery

    function factory(type){
        //类型校验 防错处理
        if(typeof type!='string'){
            throw new Error('type must be a string');
        }
    
        //如果调用的时候用new创建了新对象 则直接返回 无须再new 创建
        if(this instanceof factory){
            return this[type]();
        }else{
            return new factory(type);
        }
    }
    factory.prototype={
    basketball:function(){
        this.name='basketball';
    },
    tennis:function(){
        this.name='tennis';
    }
    };

b、建造者模式：创建一个定制化的对象，关注创建的过程，典型代表：Vue

    new Vue({
        data(){
            return {
                message:'txt'
            }
        },
        methods:{
            ...
        }
        ...
    })

(2)、原型模式:创建一个共享的对象作为其他构造函数的原型对象，通过拷贝这个对象来创建新的类，用于创建重复的对象，带来性能上的提升。 

    var animalProto = {
        name:'animal',
        getName: function(){
            return this.name;
        }
    }

    function Animal(){
        
    }
    Animal.prototype = animalProto;

(3)、单例模式：保证全局只有一个对象，不重复创建对象

    //自执行函数返回一个对象

    var single=(function(){
        //定义私有变量
        function _a(){
            this.a=123;
        };
        var ob = new _a();
        return {
        get:function(name){
            return ob[name];
        }
        }
    })();

#### 结构型设计模式：功能模块的拆分

(1)、外观模式：为一组复杂的子系统接口提供一个更加高级的统一接口，在js中可以用于消除一些的底层的兼容性，和设计原则之迪米特法则相对应，对外暴露同意方法或接口，组装细分功能例如下例中的绑定事件，该方法中做了一系列的判断兼容处理，对外暴露的绑定事件方法无须关心底层是如何兼容的，直接调用即可

    function addEvent(dom,type,fn){
        if(dom.addEventListener){ //支持dom2级操作
            dom.addEventListener(type,fn)
        }else if(dom.attachEvent){ //不支持dom2 但支持attach方法
            dom.attachEvent('on'+type,fn)
        }else{ //上面上个方法都不支持的方式
            dom['on'+type] = fn;
        }
    }
(2)、适配器模式：把不适合的接口适配成另一个接口

    var bizPublic = (function(){
        return newBizPublic.$bizMethod
    })()

(3)、装饰器模式：把修改源码的行为改成扩展源码的行为，在不改变对象自身的基础上，动态的给某个对象添加额外的职责，不会影响原有接口的功能。一开始我们只有跑的功能，现在要求增加跳的功能，我们不是直接在run方法里面新增跳的功能，而是写一个装饰器函数，包含run和jump方法

    function run(){
        console.log('run')
    }

    function decoratorFn(fn,wrapedFn){
        return function(){
            fn();
            wrapedFn();
        }
    }
    decoratorFn(run, function(){
        console.log('jump')
    })

(4)、享元模式：分析对象内部方法和外部方法。然后把外部方法提取出来，内部方法继续保留。(重复的部分只做一次，if else只做差异性；只创建一次对象，类似于单例模式，共享这一个对象，个性化通过调用方法传不同的参数实现)

    function uploadFile(){
        this.upload = function(fileName){
            ...
        }
    }

    var uploadFileObj = new uploadFile();
    var arr = ['file1','file2', ...];
    for(var i=0;i<arr.length;i++){
        uploadFileObj.upload(arr[i]);
    }

#### 行为设计模式：模块之间的沟通

(1)、观察者模式：两个模块之间无法直接沟通或者不方便沟通，多用于异步

    //观察者
    var observer = {
        eventObj:{

        },
        add:function(type, fn){
            this.eventObj[type] = fn;
        },
        remove:function(type){
            this.eventObj[type] = null;
        },
        fire(type){
            this.eventObj[type]();
        }
    }
    //注册事件
    observer.add('test', function(){
        console.log('test');
    });
    //触发事件
    observer.fire('test');

(2)、状态模式:封装了一个对象的不同状态，只针对自己的对象提供服务

    function Mary {
        this._status = {
            run:function(){
                console.log('run');
            },
            jump:function(){
                console.log('jump');
            }
            ...
        }
    }
    Mary.prototype.changeStatus = function(status){
        if(Array.isArray(status)){
            for(let action of status){
                this._status[action]();
            }
        }else{
            this._status[status]();
        }
    }

    var maryObj = new Mary();
    maryObj.changeStatus('run');
    maryObj.changeStatus(['run','jump']);

(3)、策略模式：封装了多个策略的对象，可以为多个对象提供服务，和状态模式相同都是为了解决if else分支过多的问题。

    function priceMethods(){
        //策略
        var methods = {
            percentage50(price){
                return price*0.5
            },
            percentage70(price){
                return price*0.7
            },
            percentage90(price){
                return price*0.9
            }
        };
        return function(methodName, args){
            methods[methodName] && methods[methodName].apply(this ,args)
        }
    }

(4)、命令模式：只关注命令本身，用一种松耦合的方式来设计程序，使得请求发送者和请求接收者能够消除彼此之间的耦合关系。

    function setCommod(btn, fn){
        btn.onClick = function(){
            fn();
        }
    }
    //命令或者理解为策略
    var refreshMenu = {
        refresh:function(){
            console.log('刷新菜单');
        }
    }
    setCommod(btn1 ,refreshMenu.refresh)


    var msgCm = {
        type:'warn',
        msg:'警告',
    }

    function command(){
        function create(iconDom ,msg){
            var msgBox = document.createElement('div');
            msgBox.innerHTML = msg
            return iconDom + msg;
        }
        function display(content){
            var bodyDom = document.getElementsByTagName('body');
            bodyDom.appendChild(content);
        }
        function _excute(command){
        var state={
            wran:"<img src='imgage/wran.svg'/>",
            confirm:"",
        }
        display(create(state[command.type],command.msg));
        }
        return {
            excute:_excute
        }
    }

    command().excute(cm);

(5)、迭代器模式：对于集合内部结果常常变化各异，我们不想暴露其内部结构的话，但又想让客户代码透明地访问其中的元素，这种情况下我们可以使用迭代器模式。典型的就是forEach,map以及jQ的$each方法。

(6)、解释器模式：给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子。

#### 技巧型设计模式:一种优化方式

(1)、链模式：可简单理解为链式调用

(2)、委托模式：给事件发生的上一层最近的父元素绑定事件就是典型的委托方式

(3)、节流模式：函数节流，设置延时器，一定时间以后执行，如果延时期间再次被触发，则清掉当前的延时器，重新设置新的延时器，直到最后只执行最后一次调用。

    var timer;
    function delay(){
        clearTimeout(timer);
        timer  = setTimeout(function(){
            console.log('1')
        },1000)
    }
    btn.onClick = function(){
        delay();
    }

(4)、惰性模式：初始化的时候制定不变，下次初始化的时候再重新指定。减少每次代码 执行时重复的分支判断，通过对对象重新定义屏蔽原对象中的分支判断。

    var type = 'warn';
    var msg = (function (){
        if(type == 'warn'){
        return  function(){
                console.log('wran')
            }
        }else{
            return function(){
                console.log('error')
            }
        }
    })()

(5)、等待者模式：可参考promise.all的实现原理





