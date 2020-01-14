---
title: Flutter学习-第二阶段
date: 2020-01-14 10:39:09
tags:
---
<h2>应用</h2>

> <h4>路由管理</h4>

路由就是页面（page），在iOS中主要是指（ViewController）,路由 管理就是管理页面之间如何跳转，导航管理在Android还是iOS中，都是维护一个路由栈，push（打开新页面）和pop（关闭页面）页面；

	Navigator.push( context,
	           MaterialPageRoute(builder: (context) {
	              return NewRoute();
	           }));
	
1. <u>Navigator</u> 路由管理的组件，通过一个栈来管理活动路由集合；

2.  <u>MaterialPageRoute</u> 继承自PageRoute类，表示占有整个屏幕空间的一个模态路由页面，它还定义了路由构建及切换时过渡动画的相关接口及属性。MaterialPageRoute 是Material组件库提供的组件，它可以针对不同平台，实现与平台页面切换动画风格一致的路由切换动画；

	MaterialPageRoute({
	    WidgetBuilder builder,
	    RouteSettings settings,
	    bool maintainState = true,
	    bool fullscreenDialog = false,
	  })
<u>builder</u> : 是一个WidgetBuilder类型的回调函数，它的作用是构建路由页面的具体内容，返回值是一个widget。我们通常要实现此回调，返回新路由的实例。

 <u>settings</u> : 包含路由的配置信息，如路由名称、是否初始路由（首页）。

 <u>maintainState</u>：默认情况下，当入栈一个新路由时，原来的路由仍然会被保存在内存中，如果想在路由没用的时候释放其所占用的所有资源，可以设置maintainState为false。

 <u>fullscreenDialog</u>: 表示新的路由页面是否是一个全屏的模态对话框，在iOS中，如果fullscreenDialog为true，新页面将会从屏幕底部滑入（而不是水平方向）。

3. 传值

		TipRoute({
		    Key key,
		    @required this.text,  // 接收一个text参数
		  }) : super(key: key);
		  final String text; /* 这里是TipRoute页面声明一个text值，准备接收；*/
  
		MaterialPageRoute(
		              builder: (context) {
		                return TipRoute(
		                  // 路由参数
		                  text: "我是提示xxxx",
		                );
		              },
		            ),
		          ); /*这里是跳转TipRoute进行值传递*/
4. 命名路由

 >注册路由表
  
		  routes:{
		   "new_page":(context) => NewRoute(),
		    ... // 省略其它路由注册信息
		  } 
	  
 >打开新路由

		onPressed: () {
		  Navigator.pushNamed(context, "new_page");
		},

 >命名路由参数传递
 
		 Navigator.of(context).pushNamed("new_page", arguments: "hi");/*打开路由时传递参数*/
	 
		 class Route extends StatelessWidget {
		  @override
		  Widget build(BuildContext context) {
		    //获取路由参数  
		    var args=ModalRoute.of(context).settings.arguments;
		    //...省略无关代码
		  }
		} /*打开后路由获取路由参数*/
 
5. 路由器生成钩子
> onGenerateRoute回调

		MaterialApp(
		  ... //省略无关代码
		  onGenerateRoute:(RouteSettings settings){
		      return MaterialPageRoute(builder: (context){
		           String routeName = settings.name;
		       // 如果访问的路由页需要登录，但当前未登录，则直接返回登录页路由，
		       // 引导用户登录；其它情况则正常打开路由。
		     }
		   );
		  }
		);


> <h4>包管理</h4>

* name：应用或包名称
* description：应用或包的描述、简介
* version：应用或包的版本号
* dev_dependencies：开发环境依赖的工具包（而不是flutter应用本身依赖的包）
* flutter：flutter相关的配置选项









