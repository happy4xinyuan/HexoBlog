---
title: JS闭包
category: JS
tag: JS
---

# js闭包是干什么用的 作用

来源 [阮老师的博客](https://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)

[B站视频-结合上下文, 作用域链的理解](https://www.bilibili.com/video/BV1iE411q7Qd)

<!--more-->

闭包（closure）是Javascript语言的一个难点，也是它的特色，很多高级应用都要依靠闭包实现。

**一、变量的作用域**

要理解闭包，首先必须理解Javascript特殊的变量作用域。

变量的作用域无非就是两种：全局变量和局部变量。

Javascript语言的**特殊之处**，就在于**函数内部可以直接读取全局变量**。

另一方面，在**函数外部自然无法读取函数内的局部变量**。(目标)

这里有一个地方需要注意，函数内部声明变量的时候，一定要使用var命令。如果不用的话，你实际上声明了一个全局变量！

> ```
> 　　function f1(){
> 　　　　n=999;
> 　　}
> 
> 　　f1();
> 
> 　　alert(n); // 999
> ```



**二、如何从外部读取局部变量？**

正常情况下，这是办不到的，只有通过变通方法才能实现。

那就是在函数的内部，再定义一个函数。

```js
function f1(){

    var n=999;

    function f2(){
        alert(n); // 999
    }
    return f2;
}

var result=f1();//只是函数代码,但是也包含了n的值?
result(); // 999
```

在上面的代码中，函数f2就被包括在函数f1内部，这时f1内部的所有局部变量，对f2都是可见的。

但是反过来就不行，f2内部的局部变量，对f1就是不可见的。

这就是Javascript语言特有的"链式作用域"结构（chain scope）:**子对象会一级一级地向上寻找所有父对象的变量**。所以，**父对象的所有变量，对子对象都是可见的**，反之则不成立。



**三、闭包的概念**

上一节代码中的f2函数，就是闭包。

各种专业文献上的"闭包"（closure）定义非常抽象，很难看懂。我的理解是，**闭包就是能够读取其他函数内部变量的函数。**

由于在Javascript语言中，只有函数内部的子函数才能读取局部变量，因此可以`**把闭包简单理解成"定义在一个函数内部的函数"。**

所以，在本质上，闭包就是将函数内部和函数外部连接起来的一座桥梁。



**四、闭包的用途**

一个是前面提到的可以**读取函数内部的变量**，另一个就是让这些**变量的值始终保持在内存中**。

```js
　　function f1(){

　　　　var n=999;

　　　　nAdd=function(){n+=1}

　　　　function f2(){
　　　　　　alert(n);
　　　　}

　　　　return f2;

　　}

　　var result=f1();

　　result(); // 999

　　nAdd(); //咋知道n的?, 这什么问题, 你咋不问f2怎么知道n的

　　result(); // 1000
```

该例子更加说明函数f1中的**局部变量n一直保存在内存中**，并没有在f1调用后被自动清除。

为什么会这样呢？原因就在于f1是f2的父函数，而**f2被赋给了一个全局变量**，这导致f2始终在内存中，而**f2的存在依赖于f1**，(因为父对象的所有变量对子对象可见).  因此f1也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

"nAdd=function(){n+=1}"这一行，首先在nAdd前面没有使用var关键字，因此nAdd是一个**全局变量**，而不是局部变量。其次，nAdd的值是一个**匿名函数**（anonymous function），而这个匿名函数本身也是一个闭包，所以nAdd相当于是一个setter，可以在函数外部对函数内部的局部变量进行操作。



**五、使用闭包的注意点**

1）由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，**在退出函数之前，将不使用的局部变量全部删除。**

2）闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。



**六、思考题**

如果你能理解下面两段代码的运行结果，应该就算理解闭包的运行机制了。

  

```
　　var name = "The Window";

　　var object = {
　　　　name : "My Object",

　　　　getNameFunc : function(){
　　　　　　return function(){
　　　　　　　　return this.name;
　　　　　　};

　　　　}

　　};

　　alert(object.getNameFunc()());
```

this的重新指代罢了.



**七. 补充**

```
function count() {
    var arr = [];
    for (var i=1; i<=3; i++) {
        arr.push(function () {
            return i * i;
        });
    }
    return arr;
}

var results = count();
var f1 = results[0];
var f2 = results[1];
var f3 = results[2];
```

在上面的例子中，每次循环，都创建了一个新的函数，然后，把创建的3个函数都添加到一个`Array`中返回了。

你可能认为调用`f1()`，`f2()`和`f3()`结果应该是`1`，`4`，`9`，但实际结果是：

```js
f1(); // 16
f2(); // 16
f3(); // 16
```

因为count之后, 每个arr项填入的是i*i, i被驻留内存, 最终被调用时, i已经是4.

返回闭包时牢记的一点就是：**返回函数不要引用任何循环变量，或者后续会发生变化的变量。**

通常，一个立即执行的匿名函数可以把函数体拆开，一般这么写：

```js
(function (x) {
    return x * x;
})(3);
```



**八. 闭包的真正用途**

在面向对象的程序设计语言里，比如Java和C++，要在对象内部封装一个私有变量，可以用`private`修饰一个成员变量。

在没有`class`机制，只有函数的语言里，借助闭包，同样可以封装一个私有变量。我们用JavaScript创建一个计数器：

```
'use strict';

function create_counter(initial) {
    var x = initial || 0;
    return {
        inc: function () {
            x += 1;
            return x;
        }
    }
}
```

它用起来像这样：

```
var c1 = create_counter();
c1.inc(); // 1
c1.inc(); // 2
c1.inc(); // 3

var c2 = create_counter(10);
c2.inc(); // 11
c2.inc(); // 12
c2.inc(); // 13
```

在返回的对象中，实现了一个闭包，该闭包携带了局部变量`x`，并且，从外部代码根本无法访问到变量`x`。换句话说，闭包就是携带状态的函数，并且它的状态可以完全对外隐藏起来。





# 

# js this指向的是谁

一个困惑

```js
// 创建一个含有bar方法的obj对象，
// bar返回一个函数，
// 这个函数返回this，
// 这个返回的函数是以箭头函数创建的，
// 所以它的this被永久绑定到了它外层函数的this。
// bar的值可以在调用中设置，这反过来又设置了返回函数的值。
var obj = {
  bar: function() {
    var x = (() => this);
    return x;
  }
};

// 作为obj对象的一个方法来调用bar，把它的this绑定到obj。
// 将返回的函数的引用赋值给fn。
var fn = obj.bar();

// 直接调用fn而不设置this，
// 通常(即不使用箭头函数的情况)默认为全局对象
// 若在严格模式则为undefined
console.log(fn() === obj); // true

// 但是注意，如果你只是引用obj的方法，
// 而没有调用它
var fn2 = obj.bar;
// 那么调用箭头函数后，this指向window，因为它从 bar 继承了this。
console.log(fn2()() == window); // true
```

