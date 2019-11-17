---
title: jQuery核心功能函数之callback
tags:
  - jQuery
  - callback
categories:
  - 源码分析
date: 2019-11-17 17:21:19
---
#### jQuery内部核心函数callback

$.callback可以通过add添加回调函数，fire执行回调函数。初始化创建callback实例的时候，可以传递三个参数的组合，stopOnfalse表示如果其中一个回调函数的返回值是false,则后续的回调函数不再执行；once表示只执行一次，即add进去的回调函数只会在第一次fire的时候会执行，如果后面再继续调用fire,回调函数不会再执行；memory表示再调用fire函数以后，后面再继续调用add添加函数的时候，该回调函数会立即执行，且之前执行过的回调不会再被执行，以下是代码的完整简易实现，要点以及个人理解都在代码注释里面。

    var cb = $.callbacks('stopOnfalse once memory');

    cb.add(function(){
        console.log(1)
        return false
    }, function(){
        console.log(2)
    });

    cb.add(function(){
        console.log(5)
    });
        
    cb.fire();

    cb.add(function(){
        console.log('3')
    })

#### callback之once

    var cb = $.callbacks('once');

    cb.add(function(){
        console.log(1)
    });
   
    cb.fire(); //输出1
    cb.fire(); //没有输出

#### callback之stopOnfalse

    var cb = $.callbacks('once');

    cb.add(function(){
        console.log(1)
        return false
    }, function(){
        console.log(2)
    });
   
    cb.fire(); //输出1 

#### callback之memory

    var cb = $.callbacks('stopOnfalse once memory');

    cb.add(function(){
        console.log(1)
    });

    cb.add(function(){
        console.log(5)
    });
        
    cb.fire(); //输出1和5

    //会立即执行
    cb.add(function(){
        console.log(3)
    })

以上会分别输出1，5，3

    

#### callback完整代码的简易实现
    ...
    callbacks: function(option){
        //回调函数列表 
        var list = [],
        //执行回调函数的下标 默认从第一个开始
            index,
        //手动设置执行回调函数列表的下标    
            start,
        //回调函数列表的长度
            listLength,
        //回调函数是否执行过
            hasRun;
        //当前回调队列策略 参数 once stopOnfalse memory
        option = typeof option === 'string' ? (optionCach[option] || createOptionCach(option)) : {};
        //当传进来的参数、模式不在缓存对象里面时候 需要重新创建
        function createOptionCach(option){
            //将空对象、缓存对象的对象引用给obj 并返回
            var obj = optionCach[option] = {};
            option.split(/\s+/).forEach(function(opt){
                obj[opt] = true;
            });
            return obj;
        }
        //循环执行回调函数列表里面的回调函数
        var fire = function(data){
            index = start || 0;
            hasRun = true;
            for(index; index < list.length; index++){
                //如果回调函数的返回值是false 同时又配置了stopOnfalse的时候就跳出循环
                if(list[index].call(data[0], data[1]) == false && option.stopOnfalse){
                    break;
                };
            }
        }
        var self = {
            add:function(){
                var args = Array.prototype.slice.call(arguments);
                //每次添加回调函数的时候 记录当前回调函数列表的长度
                listLength = list.length;
                //循环遍历args将add进来的回调函数全部放到事件列表list
                args.forEach(function(fn){
                    //如果传进来的参数是函数对象 则添加到回调函数列表
                    if(toString.call(fn) === '[object Function]'){
                        list.push(fn);
                    }
                })
                //当配置的memory的同时回调函数还没执行过 则将下次执行回调函数的下标置为当前事件列表的长度 避免执行的时候重复执行
                if(option.memory && hasRun){
                    start = listLength;
                    self.fire(arguments);
                }

            },
            fireWith:function(context, arg){
                var args = [context, arg];
                //如果是配置了只执行一次 或者还没执行过才执行
                if(!option.once || !hasRun){
                    fire(args)
                }
                
            },
            fire:function(){
                //把当前jQuery实例对象以及传递的参数通过fireWith传递
                self.fireWith(this, arguments);
            }
        }
        //这里返回self以实现链式调用
        return self;
    }