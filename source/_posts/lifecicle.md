---
title: created mounted 更新获取DOM问题
date: 2018-07-19 17:12:05
tags: vue 生命周期
categories: web
---
口水记录一个问题。 ┓(;´_｀)┏ 

写代码的时候，遇到一个问题，就是在`created`里面调用接口获取数据，利用`v-for`渲染`DOM`后，再在`mounted`里面调用渲染后的`DOM`时，一直无法获取到，显示为`undefined`。
<!--more-->

按照`VUE`的生命周期来说  
> * `created` 组件实例创建完成，属性已绑定，但`DOM`还未生成，`$el`属性还不存在。  
> * `mounted `模板编译/挂载之后。  

按道理，在`mounted`里面是可以获取到`created`阶段渲染的`DOM`的。后来通过`console.log()`来进行判断，发现在`mounted`里面的代码开始执行时，`created`阶段的`DOM`渲染还未完成。  

把`mounted`改为`updated`，`DOM`是可以获取到的，但是由于我的代码中涉及到倒计时等频繁更新`DOM`的状态，所以放弃了`updated`的这个方法。  
> * `updated`  组件更新之后

后来通过查阅文档发现，有一个方法可以解决这种问题，调用`$nextTick`这个`vue`的全局方法。即在`mounted`里面调用此方法，用于`DOM`加载更新完后进行的一次回调函数性质的操作。  

        mounted(){
            this.$nextTick(_=>{
                //你的代码部分
            })
        }

但是很可悲 = = ，我的`created`里渲染的`DOM`还是比它要晚执行完，所以获取`DOM`依然是`undefined`。  

最后用了一个简单粗暴的方法解决了此问题。  

        mounted() {
            setTimeout(_ => {
            //你的代码部分
            }, 1000);
        }  

 (o°ω°o) 

