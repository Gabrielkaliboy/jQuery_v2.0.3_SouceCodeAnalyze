# jQuery_v2.0.3_SouceCodeAnalyze
jQuery源码分析。选择的版本是2.0.3

### 将jQuery拆分成小部分
### 1. [总体框架](https://github.com/Gabrielkaliboy/jQuery_v2.0.3_SouceCodeAnalyze/blob/master/jQuerySoundCodeAnalyze_1.md)

### 2. [21-94](https://github.com/Gabrielkaliboy/jQuery_v2.0.3_SouceCodeAnalyze/blob/master/jQuerySoundCodeAnalyze_21_91.md)
定义了一些变量和函数  jQuery=function(){}

### 3. [96-283](https://github.com/Gabrielkaliboy/jQuery_v2.0.3_SouceCodeAnalyze/blob/master/jQuerySoundCodeAnalyze_96_283.md)
给jQuery增加一些属性和方法

jQuery.fn=jQuery.prototype={
    //添加实例属性和方法
    jQuery：版本

    constructor:修正指向问题

    init： 初始化和参数管理
        在init里面可以做哪些事情？我们可以给他传入不同的类型的参数

            $()  jQuery()
            
            $('li','ul')

            $('')  $(null) $(undefined)  $(false)
            
            $("#div1")  $(".box")  $("div")  $("#div1 div.box")

            $("<li>")  $("<li>2</li><li>3</li>)

            $(this) $(document)

            $(function(){})

            $([]) $({})

    selector：存储选择字符串

    length： this对象的长度

    toArray: 转数组

    get():转原生集合

    pushStack() :jQuery对象的入栈

    each(): 遍历集合

    ready():DOM加载的接口

    slice(): 集合的截取

    first() :集合的第一项

    last():集合的最后一项

    eq(): 集合的指定项

    map():返回新集合

    end():返回集合的前一个状态

    push(): (内部使用)

    sort(): (内部使用)

    splice():(内部使用)

}

### 4.[285-347]()
extend:jq的继承方法

### 5.[349-817]()
jQuery.extend():扩展一些工具方法

### 6.[877-2856]()
Sizzle:复杂选择器的实现

### 7.[2880-3042]()
Callbacks:回调对象：对函数的统一管理

### 8.[3043-3183]()
Deferred:延迟对象：对异步的统一管理

### 9. [3184-3295]()
support:功能检测

### 10.[3308-3652]()
data():数据缓存

### 11.[3653-3797]()
实现queue()功能：队列管理，入队；dequeue:出队 常用到的地方就是时间管理

### 12.[3803-4299]()
完成像attr,prop,val,addClass方法的封装等：对元素属性的操作

### 13.[4300-5128]()
on,trigger():这里放的都是事件触发的操作，事件的管理

### 14.[5140-6057]()
实现的dom操作的方法，比如dom的添加，获取，删除，包装等

### 15.[6058-6620]()
css()的方法，专门针对样式的操作

### 16.[6621-7854]()
提交的数据和ajax的操作，实现ajax功能的：ajax(),load(),getJson()等

### 17.[7855-8584]()
animate()的操作，还有show,fadeIn,fadeOut等

### 18.[8585-8792]()
offerset():位置和尺寸的一些方法

### 19.[8804-8821]()
jQuery支持模块化的一个模式

### 20.[8826 ]()
 将接口对外暴露