---
title: react-native Bug问题汇总
date: 2018-08-22 16:20:57
tags: react-native bug 修复 问题
categories: web
---

## react-native 环境构建

[点击前往 react-native 环境构建](https://reactnative.cn/docs/getting-started.html)

## react-native bug

1. 端口号 8081 被占用  
   `修改端口号：react-native start --port 9999`  
   `修改模拟器地址： ip+9999，例如：192.165.1.1:9999`
2. adb 版本不一致：adb server version (31) doesn’t match this client (36); killing…  
   `复制sdk里面的adb.exe改名nox_adb.exe,将nox/bin里的nox_adb.ex替换`
3. git 上拉去 react native,react-native start 运行报错  
   `cd android`  
   `gradlew clean`
       <!--more-->
4. no connect devices(没有链接设备)  
   `adb devices(这个命令是查看是否连接设备)`  
   `adb conncet 127.0.0.1:62001(手动连接设备)`
5. INSTALL_PARSE_FAILED_INCONSISTENT_CERTIFICATES(模拟器上已经安装了此 APP)  
   `卸载重装即可`
6. unable to resolve module 'ACCESSIBILITYINFO' （react-native 版本问题）  
   `切换版本 react-native@0.55.4 react-native-cli@1.2.0`
7. plugin 0 specified in xxx （babel-preset-react-native 的包文件错误）  
   `npm install --save -dev babel-preset-react-native@2.1.0`
8. javascript 与 native 版本不一致报错  
   `npm install react-native@要求的版本号` 然后卸载老版本即可
9. 启动页白屏  
   关闭程序，再重新打开一次
