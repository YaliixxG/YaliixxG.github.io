---
title: eventLoop
date: 2018-07-23 11:18:46
tags: 事件循环 event loop 
categories: web
---  
微任务（`microtask`）称为 `jobs` 。包括 `process.nextTick` ，`promise` ，`Object.observe` ，`MutationObserver`  

宏任务（`macrotask）`称为 `task` 。包括 `script` ， `setTimeout` ，`setInterval` ，`setImmediate` ，`I/O` ，`UI rendering`  

## Event loop 执行顺序  
1. 执行同步代码，这属于宏任务
2. 执行栈为空，查询是否有微任务需要执行
3. 执行所有微任务
4. 必要的话渲染 `UI`
5. 然后开始下一轮 `Event loop`，执行宏任务中的异步代码  

很多人有个误区，认为微任务快于宏任务，其实是错误的。因为宏任务中包括了 `script` ，浏览器会先执行一个宏任务，接下来有异步代码的话就先执行微任务。  

通过上述的 `Event loop` 顺序可知，如果宏任务中的异步代码有大量的计算并且需要操作 `DOM` 的话，为了更快的界面响应，我们可以把操作 `DOM` 放入微任务中。
