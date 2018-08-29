---
title: 跨域
date: 2018-07-23 09:32:02
tags: 跨域 JSONP 
categories: web
---

## 什么是同源策略？  
同源策略，它是由Netscape提出的一个著名的安全策略。
现在所有支持JavaScript 的浏览器都会使用这个策略。
所谓同源是指，域名，协议，端口相同。  
因为浏览器出于安全考虑，有同源策略。也就是说，如果协议、域名或者端口有一个不同就是跨域，Ajax 请求会失败。 
<!--more--> 

## JSONP  
`JSONP` 的原理很简单，就是利用 `<script>` 标签没有跨域限制的漏洞。通过 `<script>` 标签指向一个需要访问的地址并提供一个回调函数来接收数据当需要通讯时。  

        <script src="http://domain/api?param1=a&param2=b&callback=jsonp"></script>
        <script>
            function jsonp(data) {
                console.log(data)
            }
        </script>    
`JSONP` 使用简单且兼容性不错，但是只限于 `get` 请求。  

## 封装JSONP  

        function jsonp(url,jsonpCallback,success){
            let script = document.creatElement("script");
            script.src = url;
            script.async = true;
            script.type = "text/javascript";
            window[jsonpCallback] = function(data){
                success & success(data);
            };
            document.body.appendChild(script);
        }  

        jsonp(
        "http://xxx",
        "callback",
        function(value) {
            console.log(value);
        }
        );  

## CORS  
`CORS`需要浏览器和后端同时支持。IE 8 和 9 需要通过 XDomainRequest 来实现。

浏览器会自动进行 `CORS` 通信，实现CORS通信的关键是后端。只要后端实现了 `CORS`，就实现了跨域。

服务端设置 `Access-Control-Allow-Origin` 就可以开启 `CORS`。 该属性表示哪些域名可以访问资源，如果设置通配符则表示所有网站都可以访问资源。  

## postMessage  

这种方式通常用于获取嵌入页面中的第三方页面数据。一个页面发送消息，另一个页面判断来源并接收消息  

        // 发送消息端
        window.parent.postMessage('message', 'http://test.com');
        // 接收消息端
        var mc = new MessageChannel();
        mc.addEventListener('message', (event) => {
            var origin = event.origin || event.originalEvent.origin; 
            if (origin === 'http://test.com') {
                console.log('验证通过')
            }
        });

