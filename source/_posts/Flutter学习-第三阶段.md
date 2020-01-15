---
title: Flutter学习-第三阶段
date: 2020-01-15 15:20:20
tags:
---
<h2>基础控件</h2>

>Widget

1. 介绍
 
	* Widget实际上就是Element的配置数据，Widget树实际上是一个配置树，而真正的UI渲染树是由Element构成；不过，由于Element是通过Widget生成的，所以它们之间有对应关系，在大多数场景，我们可以宽泛地认为Widget树就是指UI控件树或UI渲染树。

 	* 一个Widget对象可以对应多个Element对象。这很好理解，根据同	一份配置（Widget），可以创建多个实例（Element）。

2. StatelessWidget

	* 

3. StatefulWidget

4. State
	* widget  
	* context

5. 回调函数
	* initState：
	* didChangeDependencies()：
	* build()：
	* reassemble()：
	* didUpdateWidget()：
	* deactivate()：
	* dispose()：

