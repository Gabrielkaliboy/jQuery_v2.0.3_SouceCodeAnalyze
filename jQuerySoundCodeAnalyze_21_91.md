---
title: jQuery源码分析---21至91
date: 2017-08-01 22:00:40
categories: 前端
tags: [jQuery]
---
<Excerpt in index | 首页摘要> 
jQuery源码分析---21至91
<!-- more -->
<The rest of contents | 余下全文>

-----
### 21-91 
#### 14
为什么出入参数window？
- 根据作用域链的原理，他的查找速度会变快

```html
(functioin(window){
	window
})(window)
```
- 压缩版本中，传入的是e,也就是方便压缩，减小代码量

```html
(functioin(e){
	e
})(e)
```
参数undefined
- 他是js的一个属性，可以在外部进行修改
- 兼容性，在Firefox，Chrome，IE9/10/11里面不管你定不定义，弹出来的都是undefined
```html
		var undefined=10;
		alert(undefined);
		//在Chrome、Firefox，IE9/10/11都是弹出的结果都是undefined
		//IE8/7弹出10
```
- 为了防止其他人在外面对undefined进行修改，所以他进行了一个传参的操作，防止被修改


#### 20 use strict使用严格模式
```
	"use strict";
	//不加var 关键字会报错
	a=10;
```
注释掉以后完全可以没问题

在例如
```html
	//再例如八进制数
	var a=010;
	//不允许使用八进制
```

#### 23 rootjQuery
- 他是我们的根节点，可以在866行找到他的赋值语句，这个变量在压缩版的时候可以直接变成一个字符。
- 方便维护，一目了然，就想下面这个一样

```
var isSpeed=10;
var a=a+isSpeed;

对比
var a=a+10;
```

#### 26 readyList
与dom加载有关的变量，在349-2856之间。到时候再说


#### 30 core_strundefined
存的是一个字符串形式的undefined

- 判断window下面的变量（属性）是否是不存在的
```
 window.a==undefined;
//返回true
```

- 或者
```
typeof window.a=="undefined";
//返回true
```

- 在很少的一些情况下，光是这么去判断可能判断不出来，这个东西在IE6/7/8/9有问题，加入我们判断一个xml一个节点或者属性的时候，这种时候，只用，window.a==undefined这种形式可能判断不出来他会认为条件不充分，所以为了做到所有情况的兼容，最好使用下面这种写法，出现这种情况的时候非常少


#### 33-35 对于一些window下的变量进行存储

#### 37-41 防止变量冲突
等到讲到防止冲突的时候在详细的讲
$可能在其他的库中也用到，所以，
```html
var $=10;
//这时候就会有冲突，jQuery会自动解决
```
上面代码中的$会赋值给41行的_$,如果没有冲突的话，两个变量存的值是undefined

这个在扩展一些工具方法里面用到了（349-817）

#### 44 定义了一个空的对象字面量

$.type首先用到的，用来判断每一个变量或者每一个元素的类型，	class2type = {},
里面会存成一个
```
class2type={"[Object/String]":"string","[Object Array]":"array"}
```
用来做类型判断的，两个类型的字符串格式，在$.type()的时候会用到

#### 47 core_deletedIds
定义一个和缓存数据有关的数组，在老版本中，和3308-3652里面的data()函数有关，在2.0.3版本中已经没有什么用处了

#### 49 core_version
存储的版本号

#### 52-58
把一些js中常用的字符串，数组函数等的方法进行局部存储，可以很方便的在后面使用，对压缩也有很大的帮助

#### 58 core_trim
去掉字符串的前后空格，老版本没有，需要自己写一个正则
eg:
```
//window.alert("("+ "   adfaf   "  +")");
//在字符串后面直接调用trim()方法，没有空格
window.alert("("+ "   adfaf   ".trim()  +")");
```

#### 61 jQuery
```html
jQuery = function( selector, context ) {
	return new jQuery.fn.init( selector, context, rootjQuery );
},
```
- 定义jQuery方法，可以看8843行，在jQuery中$()和jQuery()这两个是一样的，他俩最终调用的都是下面这个jQuery

- 这个函数里面返回的是一个对象，这样他的后面才可以接他拥有的方法，jQuery.fn.init是他的真正的构造函数,查看96行，jQuery.fn = jQuery.prototype，说明fn就是原型,就是prototype

- 看源码中的283行jQuery.fn.init.prototype = jQuery.fn，这里的fn就等于prototype	

- 普通的面向对象的写法
```html
<script>
    //先写构造函数
    function Aaa(){

    };
    //然后在构造函数下面加载原型
    //一般都是先给他一个初始化的方法
    Aaa.prototype.init=function(){

    };
    //然后在原型下面在加载其他的方法
    Aaa.prototype.css=function(){

    };
    //然后只要在下面new一个构造函数就可以新建一个对象出来
    var a1=new Aaa();
    //创建好对象以后，有初始化方法的就需要先进行初始化
    a1.init();

    //然后就可以通过a1这个对象来调用我们构造函数里面的方法了
    a1.css();
</script>
```
- jQuery的面向对象的写法
```html
<script>
    //先写一个构造函数
    //看源码中的283行jQuery.fn.init.prototype = jQuery.fn，这里的fn就等于prototype	
    function jQuery(){
        return new jQuery.prototype.init();
    };
    jQuery.prototype.init=function(){

    };
    jQuery.prototype.css=function(){

    };
    //加上这句话就可以使我们的对象能够拿到css这个方法了
    //init这个函数的原型等于jQuery的原型，这样就形成了一个对象的引用，把一个对象赋值给另外一个对象，这样就形成了
    //对象的引用关系，这样我们在后面这个构造函数原型上进行修改和在前面这个函数原型上面进行修改是一样的。
    //所以说我们的jQuery在jQuery这个构造函数下面写的任何的方法，同样可以通过jQuery.prototype.init这个构造
    //函数new出来的对象来使用来引用
    jQuery.prototype.init.prototype=jQuery.prototype;   
    //这里直接调用就可以
    jQuery().css();
</script>
```

- 61行，jQuery中的
```
return new jQuery.fn.init
```
结合96行，
```
jQuery.fn = jQuery.prototype
```
所以61行变为
```
jQuery.prototype.init
```
在结合283行
```
jQuery.fn.init.prototype = jQuery.fn
```
283变为
```
jQuery.prototype.init.prototype=jQuery.prototype
```
所以61行的，返回一个构建jQuery.prototype.init对象，就是返回jQuery对象！

#### 67 core_pnum
正则的作用就是去找匹配数字的，包括正数，负数，小数点，还有科学计数法，这个正则会在后面的css方法里面使用

#### 70 core_rnotwhite
匹配单词有关的，\s就是空格，调用这个正则就可以找到一个整体的单词，因为单词和单词之间是由空格分开的

#### 75 rquickExpr
匹配的是一个标签或者id.防止通过 location.hash进行xss注入，location.hash就是页面中的锚点，用来做快速定位
比如www.520wsr.com/#message,快速定位到页面中message这个锚点的位置，有的人可能就利用这个漏洞来注入xss,
他可能在后面直接写入一段代码，例如www.520wsr.com/#<div>....</div>,之前的jQuery看到这个会帮你去创建一个div
如果这个div里面有木马程序的话就会很危险。
下面这个正则就是为了防止上面说的情况，他会识别真正的id，而不是一些带有<>标签的，所以可以防止注入
匹配：<p>aaaa或者#div1这两种形式

#### 78 rsingleTag
匹配的是一个成对的空标签，比如<p></p>,<div></div>

#### 81 rmsPrefix
ms是IE的一个前缀，除了这个还有webkit,在css中写一个margin-lift,如果写入js中，是不能出现横杠的，可以写为maginLift。rmsPrefix和82rdashAlpha都是用来处理css3的带有-的元素的
```
//一般情况下的对应关系
margin-left:marginLeft
//webkit下对应关系
-webkit-margin-left:css3会转为webkitMarginLeft

//但是有一个就是比较特殊，那就是IE下面的ms,IE下面去转的话是这样,第一个m是大写
-ms-margin-left:MsMarginLeft
```

#### 82 rdashAlpha
找到横杆-和字符a-z，和上面的81行那个一起用，进行css3的转换
-2d也会转为2d

#### 85 fcamelCase 
转驼峰的回调函数

#### 90 completed
dom加载成功的时候触发的，后面讲到再说

