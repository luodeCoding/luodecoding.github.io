---
title: swfit问题总结一
date: 2018-11-06 17:48:42
tags:
---
>swift项目桥接OC第三方库

###### 言归正传，先上图 -->

1. 创建.h文件

![image](https://luodecoding.github.io/img/swiftBridgeOcImg/swiftBridgeOc1.png)

2. 在本项目的taget下路径：target->Build Setting->Search Path->User Header Search Paths 设置目录的路径${SRCROOT},然后选择recursive

![image](https://luodecoding.github.io/img/swiftBridgeOcImg/swiftBridgeOc3.png)
3. 在本项目的taget下路径：target->Build Setting->Swift Compiler - General ->Objective-C Bridging Header 双击输入
`$(SRCROOT)/本项目名称/xxx.h  `

![image](https://luodecoding.github.io/img/swiftBridgeOcImg/swiftBridgeOc2.png)

4. 在Pod的taget下路径：target->Build Setting->Search Path->User Header Search Paths 设置目录的路径${SRCROOT},然后选择recursive

![image](https://luodecoding.github.io/img/swiftBridgeOcImg/swiftBridgeOc4.png)

<h1>这样就大功靠成了，可以在桥接文件随便import第三方库的东西啦！！！<h1/>







