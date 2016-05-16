---
layout: post
title: "修改this指针"
date: 2016-05-16 23:49:23 +0800
comments: true
categories: javascript基础
tags: [javascript基础] 
---

###题目
  封装函数 f，使 f 的 this 指向指定的对象 。
####输入例子
```
bindThis(function(a, b) {
	return this.test + a + b；
}, {test: 1})(2, 3)；
```
####输出例子
```
6
```

###分析
####题目拆解
  该题目的要求是：封装一个函数bindThis，该函数有两个参数，第一个参数是一个内部有使用this指针的函数f，第二个参数是一个对象obj，执行bindThis之后，返回一个函数，该函数里面的this就被绑定到obj上面。。。好吧，我承认我刚看到这个时，也很晕，但是如果我把它写成下面的样子，是不是就感觉好多了呢？

```
function f(a, b) {
	return this.test + a + b；
}

function bindThis(f, obj) {
	//你实现的部分
}

//执行函数
var a = bindThis(f,{test:1});
a(2,3);
```

####什么是this
  this是Javascript语言的一个关键字。它代表函数`运行时`，自动生成的一个内部对象，只能在函数内部使用(类似的还有arguments)。具体的大家可以看看[阮一峰](http://www.ruanyifeng.com/blog)的[Javascript的this用法](http://www.ruanyifeng.com/blog/2010/04/using_this_keyword_in_javascript.html)
和变量不同，关键字this没有作用域的限制，嵌套的函数不会从调用它的函数中继承this。
简单的将this的指向分为四种情况：

- 函数调用

	这是函数的最通常用法，属于全局性调用，其this的值不是全局对象Global（非严格模式下）就是undefined（严格模式下）。

- 对象方法调用

	函数还可以作为某个对象的方法调用，这时this就指这个上级对象.

- 构造函数调用

	所谓构造函数，就是通过这个函数生成一个新对象（object）。这时，this就指这个新对象。

- bind apply call

	apply()是函数对象的一个方法，它的作用是改变函数的调用对象，它的第一个参数就表示改变后的调用这个函数的对象。因此，this指的就是这第一个参数。bind,call类似。

总结：**this关键字就是，谁调用我，我就指向谁。**
###解决方法
首先，看到`this的绑定`这几个字，你是不想条件反射的想起了javascript的三剑客:bind apply call?
####bind apply call的区别
call 和 apply 都是为了改变某个函数运行时的 context 即上下文而存在的，换句话说，就是为了`改变函数体内部 this 的指向`。因为 JavaScript 的函数存在「定义时上下文」和「运行时上下文」以及「上下文是可以改变的」这样的概念。

- 相同点：
	都可以为函数绑定this。
- 不同点：
	call和apply基本的区别：参数不同。apply() 接收两个参数，第一个是绑定 this 的值，第二个是一个参数数组。而 call() 呢，它的第一个参数也是绑定给 this 的值，但是后面接受的是不定参数，而不再是一个数组，也就是说你可以像平时给函数传参那样把这些参数一个一个传递。
bind的区别：创建一个新的函数。具体的可以看看这篇文章[理解 Javascript 的 Function.prototype.bind](http://andyyou.logdown.com/posts/233010-understanding-javascript-functionprototypebind)

####解决方法一：使用bind()
下面的例子是其中一个解决方法，同时也说明了bind()方法是创建了一个新的函数。

```
function f(a, b) {
	return this.test + a + b;
}

function bindThis(f, obj) {
	//你实现的部分
	return f.bind(obj);
}

//执行函数
var a = bindThis(f,{test:1});
console.log(a(2,3));
console.log(f(2,3));
```

**输出结果**：

```
6
NaN
```
####解决方法二：使用apply()
```
function bindThis(f, obj) {
    //你实现的部分
    return function () {
        return f.apply(obj, arguments);
    };
}
```
这里使用一个函数包装了一下apply方法，然后返回该函数。

`arguments`是JavaScript 中每个函数内都能访问一个特别变量 arguments。这个变量维护着所有传递到这个函数中的参数列表。

注意: 由于 arguments 已经被定义为函数内的一个变量。 因此通过 var 关键字定义 arguments 或者将 arguments 声明为一个形式参数， 都将导致原生的 arguments 不会被创建。

arguments 变量不是一个数组（Array）。 尽管在语法上它有数组相关的属性 length，但它不从 Array.prototype 继承，实际上它是一个对象（Object）。

####解决方法三：使用call()
和apply()类似，仅参数的传入方式不同。

```
function bindThis(f, obj) {
    //你实现的部分
    return function (a,b) {
        return f.call(obj, a,b);
    };
}
```

###总结
该题目主要考察的是this关键字及改变this指向的方法，再下才疏学浅，只能找到这几个方法了，但是鉴于javascript这门语言的灵活性，我老感觉应该是还有其他的方法的，希望各位知道了能够不吝赐教。

