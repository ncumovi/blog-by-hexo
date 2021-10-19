---
title: 前端知识查缺补漏
date: 2021-10-18 17:29:21
tags:
  - 原型链
  - 变量提升
  - 数据类型校验
  - bind函数
  - 函数柯里化
  - V8引擎垃圾回收
  - new操作符的实现
  - 事件循环记住
categories:
  - 前端
---

## 原型链

```
//所有对象都有**proto**属性，指向其构造函数的 prototype 对象，
person.**proto** === Person.prototype
//每个原型都有一个 constructor 属性指向关联的构造函数
Person === Person.prototype.constructor
//终极原型
Object.prototype.**proto** === null
```

## 继承

```
//原型继承 把父相关属性和方法挂在子实例的prototype上
//缺点：1.引用类型的属性被所有实例共享 2.在创建 Child 的实例时，不能向Parent传参
function Parent(){
    this.name='movi'
}
Parent.prototype.getName = function () {
    console.log(this.name);
}
function Child () {

}
Child.prototype = new Parent()
var child1 = new Child();

//构造函数继承 把父相关属性和方法挂在子实例对象上
//优点：1.避免了引用类型的属性被所有实例共享 2.可以在 Child 中向 Parent 传参
//缺点：方法都在构造函数中定义，每次创建实例都会创建一遍方法。
function Parent () {
    this.name = ['kevin', 'daisy'];
}

function Child () {
    Parent.call(this);
    this.getName = function () {
        console.log(this.name)
    }
}
var child1 = new Child();

//组合继承
//缺点：会调用两次父构造函数。子实例里面会有继承来的属性和方法 原型对象prototype里面也会有属性和方法，会重复
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}
Parent.prototype.getName = function () {
    console.log(this.name)
}
function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}
Child.prototype = new Parent();
Child.prototype.constructor = Child;
var child1 = new Child('kevin', '18');

//寄生组合式继承
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}
Parent.prototype.getName = function () {
    console.log(this.name)
}
function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}
/*
function createObj(o){
    function F(){}
    F.prototype = o
    return new F()
}
function extend(child, parent){
    var proto = createObj(parent.prototype)
    proto.constructor = parent
    child.prototype = proto
}
*/
或者简单点
function F(){}
F.prototype = Parent.prototype
Child.prototype = new F();
```

## 变量提升

全局上下文的变量对象初始化是全局对象
函数上下文的变量对象初始化只包括 Arguments 对象
在进入执行上下文时会给变量对象添加形参、函数声明、变量声明等初始的属性值
在进入执行上下文时，首先会处理函数声明，其次会处理变量声明，如果变量名称跟已经声明的形式参数或函数相同，则变量声明不会干扰已经存在的这类属性
在代码执行阶段，会再次修改变量对象的属性值

## js 校验数据类型

![校验数据类型](img/front-engineer/typeOf.png)

## bind 的实现

```
Function.prototype.myBind = function(context){
//利用函数闭包 记录传过来的参数 不包含上下文 以及执行对象
var args = Array.prototype.slice.call(arguments, 1),
that = this,
fEBind = function(){},
fBind = function(){
//获取传参并和第一次传参做合并
var bindAgrs = Array.prototype.slice.call(arguments, 1);
//如果是作为普通函数 this 指向 window
return that.apply( this instance fBind ? this : context, args.concat(bindArgs))
};
//实例就可以继承绑定函数的原型中的值
fEBind.prototype = this.prototype;
fBind.prototype = new fEbind();
return fBind;
}
```

## 函数柯里化

```
//就是将调用一个有多个入参的函数改造为调用多个只有一个入参的函数
function curry(fn, args) {
var length = fn.length; //3

    args = args || []; //[]

    return function() {

        var _args = args.slice(0), // []

            arg, i;

        for (i = 0; i < arguments.length; i++) { //arguments = [a,b,c]

            arg = arguments[i];

            _args.push(arg);

        }
        if (_args.length < length) { //_args = [a,b,c]
            return curry.call(this, fn, _args);
        }
        else {
            return fn.apply(this, _args);
        }
    }

}

var fn = curry(function(a, b, c) {
console.log([a, b, c]);
});

fn("a", "b", "c") // ["a", "b", "c"]
fn("a", "b")("c") // ["a", "b", "c"]
fn("a")("b")("c") // ["a", "b", "c"]
fn("a")("b", "c") // ["a", "b", "c"]
```

## V8 引擎的垃圾回收机制

1、V8 引擎采用分代式垃圾回收机制，分为新生代、老生代、大对象区等。大量新创建的对象都存放在新生代区，该区的垃圾回收较为频繁，新生代分为 from 和 to 空间，from 空间存放刚创建的变量对象
等，垃圾回收时，如果 from 空间的对象没有被引用，则直接回收，如果被引用，则复制到 to 空间。下次垃圾回收时，检查 to 空间的对象如果 to 空间的对象没有被引用，则直接回收，如果被引用，则复制
到 from 空间。如果对象经过一次 Scavenge 算法，且 To 空间的内存占比已经超过 25%，则将对象直接晋升到老生代区。老生代区管理着大量的存活对象，因此采用标记清除算法，早期的引用计数法无法解
决循环引用的问题导致内存溢出已被弃用。将老生代区活动对象标记并移至一端，然后清除剩下的对象。

2、避免内存溢出的方法:
2.1 减少全局变量的定义，因为标记清除法是以根节点也就是全局对象出发标记的
2.2 及时清除定时器、延时器等
2.3 少用闭包
2.4 清除对 dom 的引用

## new 操作符的模拟实现

```
function myNew(){
var obj = new Object(),
Constructor = [].shift.call(arguments);
obj.**proto** = Constructor.prototype;
var ret = Constructor.apply(obj, arguments);
return typeOf ret == 'object' ? ret : obj
}
```

## 事件循环机制

![校验数据类型](img/front-engineer/eventLoop.png)
