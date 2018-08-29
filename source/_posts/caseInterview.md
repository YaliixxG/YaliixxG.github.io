---
title: 最常见的前端面试题
date: 2018-07-27 10:59:26
tags: 前端面试题 
categories: web
---
持续更新 (*❦ω❦)
## 盒子模型  

* 在标准模式下，一个块的总宽度= width + margin(左右) + padding(左右) + border(左右)  
* 在怪异模式(IE)下，一个块的总宽度= width + margin(左右)（即width已经包含了padding和border值）
<!--more-->  

## position包含几种属性？absolute和relative的区别？  
* static：默认状态、没有定位、正常流 
* inherit：从父元素集成position属性的值
* fixed：生成绝对定位的元素（相对于浏览器窗口进行定位）
* absolute：生成绝对定位的元素（相位与static定位以外的第一个父元素定位）
* relative：生成相对定位的元素（相对于其正常位置定位）
>absolute和relative区别：父元素的padding对relative的子元素布局有影响，absolute的子元素不受影响  

## 前端性能优化  
>减少HTTP请求、使用内容发布网络、压缩组件、使用Expire头、JS放底部、CSS放顶部、避免CSS表达式等。（详情见web前端性能优化）

