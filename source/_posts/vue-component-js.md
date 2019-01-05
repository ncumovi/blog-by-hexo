---
title: 如何用js调用组件
tags:
  - js
  - component
  - vue
categories:
  - 前端
date: 2019-01-05 15:57:36
---

## vue组件的正常使用

比如element-ui有个alert的组件，我们在使用的时候，需要先引入该组件，其次注册，并在template里面写相应的结构

    
    ...
   
     <el-alert
        title="成功提示的文案"
        type="success">
     </el-alert>
    
    ...
    
    //引入组件
    import { alert } from 'element-ui'
    
    ...
    
    //注册组件
    component:{
        ...
        alert
        ...
    }


#### 当我们多个页面要用这个组件的时候，我们不想多册引入该组件，注册组件，并写结构，如何实现通过js调用该组件呢

其实element-UI 里面的 messageBox组件就是通过js调用的

    this.$alert('这是一段内容', '标题名称', {
      confirmButtonText: '确定',
      callback: action => {
        this.$message({
          type: 'info',
          message: `action: ${ action }`
        });
      }
    });
    
如果是我们自己写了个messageBox组件或者其他组件，而且我也想只通过js调用，参考element-UI 源码，我写下了以下简陋的代码，实现此功能, Talk is cheap.Show you my code.

    //index.js
    import Vue from 'vue'; //引入vue
    import message from './src/message'; //引入自己的组件
    let Message = Vue.extend(message);
    
    //核心就是拿到这个组件的实例，先new实例，然后拿到该组件的dom,并在body上append该dom节点
    function init(options){
    
      let instance = new Message({
        // data: options
      });
      
      instance.vm = instance.$mount();
      document.body.appendChild(instance.vm.$el);
    
      defaultOptions = {
        hasMask:true,
        typeMessage:'alert',
        ownClass:'', 
        showMsgBox:true,
      }
      if(_.isString(options)){
        defaultOptions.messageText = options;
      }
      return instance
    }
    
    //以下就是把方法直接挂到vue原型上，方法里调用上面的init方法，然后再做something...
    Vue.prototype.alert = function(options){
      let instance = init(options);
      let ownDefaultOptions = {
        confirmButtonText:'确认',
        confirmedAction:function(){console.log("可选的确认按钮点击事件")},
      }
      instance.messageOptions  = _.assign(defaultOptions,ownDefaultOptions,options);
    }
    
    Vue.prototype.confirm = function(options){
      let instance = init(options);
      let ownDefaultOptions = {
        typeMessage:'confirm',
        confirmButtonText:'确认',
        cancelButtonText:'取消', 
        confirmedAction:function(){console.log("可选的确认按钮点击事件")},
        canceledAction:function(){console.log("可选的取消按钮点击事件")}
      }
      instance.messageOptions  = _.assign(defaultOptions,ownDefaultOptions,options);
    }
    
    Vue.prototype.promote = function(options){
      let instance = init(options);
      let ownDefaultOptions = {
        hasInput:true,
        confirmButtonText:'确认',
        cancelButtonText:'取消',
        validType: 'email',
        confirmedAction:function(){console.log("可选的确认按钮点击事件")},
        canceledAction:function(){console.log("可选的取消按钮点击事件")}
      }
      instance.messageOptions  = _.assign(defaultOptions,ownDefaultOptions,options);
    }

这样调用起来是不是方便很多呢，嘿嘿嘿。。。