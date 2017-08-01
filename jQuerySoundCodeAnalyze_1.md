---
title: jQuery源码分析--简化jQuery
date: 2017-07-05 20:00:40
categories: 前端
tags: [jQuery]
---
<Excerpt in index | 首页摘要> 
jQuery源码分析--简化jQuery
<!-- more -->
<The rest of contents | 余下全文>

-----
### 1
官网：http://jquery.com/
##### 1.1 版本选择
jQuery在2.0版本以后就不在支持IE6/7/8了，我们的版本选2.0.3。之所以选择这个，是因为他不支持IE6/7/8以后会在代码里面减少很多兼容的写法！（未压缩版）

##### 1.2简化jQuery框架
- 21-94 定义了一些变量和函数jQuery=function（）{}；

- 96-283: 给jq对象，添加一些方法和属性

- 285-347:extend:jq的继承方法。如果不明白，找点资料看看js的继承。他就是为了后续添加的方法把它挂在jQuery对象上，便于扩展

- 349-817：jQuery.extend():扩展一些工具方法。什么叫扩展工具的方法？
```javascript
$().css();
$().html();

$.trim();
$.proxy()
```
12和34是由区别的。12在面向对象中是实例的方法，34中$是函数，在函数中扩展方法是一些静态的。所以在函数下面来扩展方法的话，就是来扩展一些静态的方法。所以在jq中来给面向对象扩展静态属性和静态方法的时候，我们就把它叫做扩展工具方法。34与12的区别就是，34既可以给jQuery对象来用，有可以原生的js来用。而34只能给jQuery对象用。

可以吧静态（34）方法看做是在jQuery中最底层的东西，而实例方法（12）是上一层的更高级的东西。所以很多都是实例方法里面调用的工具方法 



- 877-2856：都是完成一个功能---->Sizzle：他的一个作用就是复杂选择器的一个实现，他是一个独立的部分。如果我们平时只是用他的选择功能，可以独立下载这个文件。在jQuery的官网左上角的第四个按钮就是。

比如：
```
$("ul li + span input.userName")
```

- 2880-3042：jQuery中的回调对象，Callbacks通过回调对象，对函数进行统一管理
新增方法，Callback里面还可以写入参数
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<script type="text/javascript" src="jquery.js"></script>
	<script type="text/javascript">
		function fn1(){
			alert(1);
		};
		function fn2(){
			alert(2);
		};
		var cb=$.Callbacks();
		cb.add(fn1);
		cb.add(fn2);
		cb.fire();//弹出1，2
	</script>
	<title></title>
</head>
<body>
	
</body>
</html>
```
移除方法：
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<script type="text/javascript" src="jquery.js"></script>
	<script type="text/javascript">
		function fn1(){
			alert(1);
		};
		function fn2(){
			alert(2);
		};
		var cb=$.Callbacks();
		cb.add(fn1);
		cb.add(fn2);
		cb.fire();//弹出1，2
		cb.remove(fn2);//移除了fn2方法
		cb.fire();//再次调用，这时候只有只弹出1
	</script>
	<title></title>
</head>
<body>
	
</body>
</html>
```

- 3043-3183:Deferred:实现的延迟对象,对异步的统一管理
在js中有很多的操作都是异步的，比如定时器，比如ajax，

**问题**
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<script type="text/javascript">
		setTimeout(function(){
			alert(1);
		},1000)
		alert(2);
		//定时器就是异步的，所以在定时器执行的时候，不会影响后续代码的执行，按照上面的写法，
		//应该先弹出2，在弹出1。但是按照我们的正常思维，应该是按照代码的顺序来执行的，也就
		//是先弹出1，在弹出2，要实现这个效果，只能将alert(2)放在setTimeout()里面。这样方便维护，
		//也看着方便。但是这对于我们来说不方便
		//我们的延迟对象defferred就是来解决这个问题的
	</script>
	<title></title>
</head>
<body>
	
</body>
</html>
```
**使用defferred解决**
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<script type="text/javascript" src="jquery.js"></script>
	<script type="text/javascript">
		var dfd =$.Deferred();
		setTimeout(function(){
			alert(1);
			dfd.resolve();
		},1000);
		dfd.done(function(){
			alert(2);
		});
		//这样就会先弹1，再弹出2
	</script>
	<title></title>
</head>
<body>
	
</body>
</html>
```

- 3184-3295:实现support:实现功能检测，对于不同的浏览器进行检测方法是否可用
**比如**3200行，根据他的注释可以看出，check或者radio的值，在老版本和新版本的webkit里面不一样，老版本为空，新版本为on 
```javascript
	// Support: Safari 5.1, iOS 5.1, Android 4.x, Android 2.3
	// Check the default checkbox/radio value ("" on old WebKit; "on" elsewhere)
	support.checkOn = input.value !== "";
``` 

- 3308-3652:实现data()功能，作用就是数据缓存，就是和数据有关的
**例如**
```html
<script type="text/javascript">
	$("#div1").data("name","李明");
	$("#div1").data("name");//这样就可以获取到数据“李明”
</script>
```

- 3653-3797:实现queue()功能：队列管理，入队；dequeue:出队
**常用到的地方就是时间管理** 
```html
<script type="text/javascript">
	$("#div1").animate({left:100});
	$("#div1").animate({top:100});
	$("#div1").animate({width:300});
	//如果保证他的动画是按照顺序一个个执行的，这里就用到了queue队列管理。他的作用就是把
	//上面三个存到一个队列里面，当一个走完，让他出队dequeue
</script>
```
- 3803-4299:完成像attr,prop,val,addClass方法的封装等：对元素属性的操作

- 4300-5128：on,trigger():这里放的都是事件触发的操作，事件的管理


- 5140-6057：实现的dom操作的方法，比如dom的添加，获取，删除，包装等
- 6058-6620：css()的方法，专门针对样式的操作
- 6621-7854：提交的数据和ajax的操作，实现ajax功能的：ajax(),load(),getJson()等
- 7855-8584：animate()的操作，还有show,fadeIn,fadeOut等
- 8585-8792：offerset():位置和尺寸的一些方法。
- 8804-8821：jQuery支持模块化的一个模式 
- 8826 将接口对外暴露
```javascript
window.jQuery = window.$ = jQuery;
``` 
##### 1.3 匿名函数自执行
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<script type="text/javascript">
		(function(){
			//好处就是变量是局部的
			var a=10;
			//函数也是局部的
			function $(){
				alert(a);
			};
		})()
		//alert(a);
		$();
		//结果就是都是报错，报未定义
	</script>
	<title>匿名函数自执行的好处</title>
</head>
<body>
	
</body>
</html>
```
jQuery把所有的代码都放在了自执行函数里面，这样他的变量就是局部的，防止和其他的变量有冲突。在jQuery里面$()和jQuery()是一样的 

##### 1.4 对外提供接口
把对外提供的方法挂在window的下面，像上面的$方法，我们挂在window的下面，就可以调用不在报错
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<script type="text/javascript">
		(function(){
			//好处就是变量是局部的
			var a=10;
			//函数也是局部的
			function $(){
				alert(a);
			};
			window.$=$;
		})()
		//alert(a);
		$();
		//此时在调用$方法，会弹出10
	</script>
	<title></title>
</head>
<body>
	
</body>
</html>
```

在当前版本中，21-94行都是定义了一些变量和函数，其中有一个函数最为重要
```javascript
jQuery=function(){};
```
这个就是我们平时用的$(),或者jQuery().jQuery中对外提供接口的位置在8860行
```javascript
window.$=window.jQuery=jQuery；
```

##### 1.5 jQuery是一个基于面向对象的
前面是一个对象，（这个里面的执行结果是对象，也可以）才能够调用后面的方法，比如jQuery中

```
$("#div1").css();
$("#div2").html();
```
这些方法的前面都是一个对象，再来看原生的
```
var arr=new Array();
arr.push();
arr.pop();
```
arr之所以后面可以跟一个方法，就是因为arr是一个数组的实例（对象）。jQuery代码里面的第63行：

```
return new jQuery.fn.init( selector, context, rootjQuery );
```
