---
layout: post
title: "javascript判断数据类型"
date: 2016-05-21 21:07:35 +0800
comments: true
categories: javascript基础
tags: [javascript基础]
categories: 
---
###题目
实现一个函数typeof()，输入一个数据，返回数据的基本类型。
如：

```
typeof([]) => array
typeof({}) => object
typeof("") => string
等等
```
<!--more-->
###解析
   由于javascript这门语言辉(keng)煌(die)的历史，所以连这种简单的需求都需要自己来实现，唉，说多了，都是泪啊。
   
   这儿题目相对来说应该是比较简单的，但是也是有不少坑，想要真正实现的很好，还是需要用到不少知识的。
   
   一开始，肯定有人想到使用`typeof`，顾名思义嘛，就是判断数据的类型，但是，可是，实际真的是这样吗？

####typeof操作符
>typeof 操作符（和 instanceof 一起）或许是 JavaScript 中最大的设计缺陷， 因为几乎不可能从它们那里得到想要的结果。         --javascript秘密花园

尽管 instanceof 还有一些极少数的应用场景，typeof 只有一个实际的应用，就是用来检测一个对象是否已经定义或者是否已经赋值，而这个应用却不是用来检查对象的类型。（好吧，这个其实貌似也并没有什么卵用。。。）

在下面表格中，Type 一列表示 typeof 操作符的运算结果。其中，JavaScript 标准文档中定义: [[Class]] 的值只可能是下面12个字符串中的一个： `Arguments`, `Array`, `Boolean`, `Date`, `Error`, `Function`, `JSON`, `Math`, `Number`, `Object`, `RegExp`, `String`。可以看到，这个值在大多数情况下都返回 "object"。

| Value     |          Class   |   Type |
| ------------- |:-------------:| -----:|
| "foo"             |  String  |   string |
| new String("foo") |  String  |   object |
| 1.2               |  Number  |   number |
| new Number(1.2)   |  Number  |   object |
| true              |  Boolean |   boolean |
| new Boolean(true) |  Boolean |   object  |
| new Date()        |  Date    |   object  |
| new Error()       |  Error   |   object  |
| [1,2,3]           |  Array   |   object  | 
| new Array(1, 2, 3)|  Array   |   object  |
| new Function("")  |  Function |   function |
| /abc/g            |  RegExp   |  object (function in Nitro/V8) |
| new RegExp("meow") | RegExp   |  object (function in Nitro/V8) |
| {}                |  Object   |  object  |
| new Object()      |  Object    | object     |

 #####测试为定义变量

```
typeof foo !== 'undefined'
```
上面代码会检测 foo 是否已经定义；如果没有定义而直接使用会导致 `ReferenceError` 的异常。 这是 typeof `唯一有用`的地方。

####instanceof 操作符   
刚说完，typeof,肯定又有人想用instanceof，但是，instanceof真的有用吗？

instanceof 操作符用来比较两个操作数的`构造函数`，instanceof 运算符与 typeof 运算符相似，用于识别正在处理的对象的类型。具体的可以看看这个[JavaScript instanceof 运算符深入剖析](https://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/)。
因此，instanceof在判断一个对象是不是一个类的`实例`只有在比较自定义的对象时才有意义。 如果用来比较内置类型，将会和 typeof 操作符 一样用处不大。

#####比较自定义对象

```
function Foo() {}
function Bar() {}
Bar.prototype = new Foo();

new Bar() instanceof Bar; // true
new Bar() instanceof Foo; // true

// 如果仅仅设置 Bar.prototype 为函数 Foo 本身，而不是 Foo 构造函数的一个实例。
Bar.prototype = Foo;
new Bar() instanceof Foo; // false
```

####instanceof 比较内置类型
但是，不是通过构造函数创建的对象使用instanceof比较，那得到的，可能就不是你想要的结果。
```
new String('foo') instanceof String; // true
new String('foo') instanceof Object; // true

'foo' instanceof String; // false
'foo' instanceof Object; // false
```
#####注意
还有有一点需要注意，instanceof 用来比较属于不同 JavaScript 上下文的对象（比如，浏览器中不同的文档结构）时将会出错， 因为它们的构造函数不会是同一个对象。

看到这里，是不是很震惊？你所知道的知道的方法，都是错的。。。唉，当初我知道了这个也是泪流满面啊。。。


####解决方法
javascript对象的内部属性 [[Class]] 的值就包含有j其对象的类型，为了获取对象的 [[Class]]，我们需要使用定义在 Object.prototype 上的方法 toString。

```
function is(type, obj) {
    var clas = Object.prototype.toString.call(obj).slice(8, -1);
    return obj !== undefined && obj !== null && clas === type;
}

is('String', 'test'); // true
is('String', new String('test')); // true

```
Object.prototype.toString 返回一种标准格式字符串，所以上例可以通过 slice 截取指定位置的字符串，如下所示：

```
Object.prototype.toString.call([])    // "[object Array]"
Object.prototype.toString.call({})    // "[object Object]"
Object.prototype.toString.call(2)    // "[object Number]"
```

####看看大神的解决方案
如果我没记错，在`jQuery`和`underscore`等库中都有判断数据类型的函数，可能平时大家也就用用，没有仔细了解过它们的底层是怎么实现的，

**我们要会使用框架，但不要依赖框架**

以后大家再碰到类似的问题的时候，不妨查看一下这些成熟框架或库的实现源码，这里，我抛出jQuery的实现源码，抛砖引玉。

```
var class2type = {} ;
"Boolean Number String Function Array Date RegExp Object Error".split(" ").forEach(function(e,i){
    class2type[ "[object " + e + "]" ] = e.toLowerCase();
}) ;
//当然为了兼容IE低版本，forEach需要一个polyfill，不作细谈了。
function _typeof(obj){
    if ( obj == null ){
        return String( obj );
    }
    return typeof obj === "object" || typeof obj === "function" ?
    class2type[ Object.prototype.toString.call(obj) ] || "object" :
        typeof obj;
}
```


###结论：
看源码是程序员快速成长的重要方式。
