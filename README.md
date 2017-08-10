# jQuery_v2.0.3_SouceCodeAnalyze
jQuery源码分析。选择的版本是2.0.3

### 将jQuery拆分成小部分
### 1. [总体框架](https://github.com/Gabrielkaliboy/jQuery_v2.0.3_SouceCodeAnalyze/blob/master/jQuerySoundCodeAnalyze_1.md)

### 2. [21-91](https://github.com/Gabrielkaliboy/jQuery_v2.0.3_SouceCodeAnalyze/blob/master/jQuerySoundCodeAnalyze_21_91.md)

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