---
title: Flutter学习-第一阶段
date: 2020-01-10 19:36:10
tags:
---
## Flutter第一阶段
> <h3>环境配置 注意事项</h3>

<h5>1.官方文档中当你把flutter下载到电脑之后，要想全局命令运行flutter，需要配置环境变量 </h5>

	export PATH=$PATH:/Users/roder/Desktop/flutter/bin
	
<h6>这里注意 ：$PATH:后面的是你下载的flutter里面的bin的路径，可以通过找到bin这个文件夹拖到终端命令里面就会自动显示该路径。</h6>

<h5>2.打开终端执行</h5>

	open ~/.bash_profile
	
<h6>弹出一个编辑框后，把第一步的路径复制进去，保存。</h6>

<h5>3.再在终端执行</h5>

	source .bash_profile
	
<h6>基本上到这一步都是可以了，但是如果你的终端命令配置的是zshrc ，那么就需要，继续看下面</h6>

<h5>4.在终端执行</h5>
	
	open .zshrc
	
<h5>5.弹出编辑框后，再把第一步的路径粘贴进去，换行，把第3步的命令粘贴进去，保存，再执行</h5>
	
	source .zshrc
	
<h6>最后验证在终端执行</h6>

	flutter doctor


