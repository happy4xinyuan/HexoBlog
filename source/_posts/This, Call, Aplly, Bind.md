---
title: This, Call, Aplly, Bind.
category: JS
tag: [JS]
---

在JavaScript中，`call`、`apply`和`bind`是`Function`对象自带的三个方法，这三个方法的主要作用是改变函数中的`this`指向。

`bind` 是返回对应函数，便于稍后调用；`apply` 、`call` 则是立即调用 。

<!--more-->

## this的理解

一个易错点:

```js
function a(){
    var user = "xinyuan";
    console.log(this.user); //undefined
    console.log(this); //Window
}
a();
```

按照我们上面说的this最终指向的是调用它的对象，这里的函数a实际是被Window对象所点出来的，等价于`window.a();`

## why?

call,apply,bind干什么的？为什么要学这个？

　　一般用来指定this的环境，在没有学之前，通常会有这些问题。

```js
var a = {
    user:"xinyuan",
    fn:function(){
        console.log(this.user);
    }
}
var b = a.fn; //b获取了a.fn的方法内容,但是this只有被调用的时候才会被确定
b(); //undefined 想打印对象a里面的user却打印出来undefined? 其实也就是windows在调用
a.fn(); //xinyuan
```

## what?

**1、call()**

语法:

> someObj.call([thisObj[,arg1[, arg2[, [,.argN]]]]])

将someObj的this指针修改为thisObj对象.

```js
var a = {
    user:"xinyuan",
    fn:function(){
        console.log(this.user); //xinyuan
    }
}
var b = a.fn;
b.call(a);
```

call方法除了第一个参数以外还可以添加多个参数，如下：

```
var a = {
    user:"xinyuan",
    fn:function(e,ee){
        console.log(this.user); //xinyuan
        console.log(e+ee); //3
    }
}
var b = a.fn;
b.call(a,1,2);
```

**2、apply()**

apply方法和call方法有些相似，它也可以改变this的指向

```js
var a = {
    user:"xinyuan",
    fn:function(){
        console.log(this.user); //xinyuan
    }
}
var b = a.fn;
b.apply(a);
```

同样apply也可以有多个参数，但是不同的是，第二个参数必须是一个数组，如下：

```js
var a = {
    user:"xinyuan",
    fn:function(e,ee){
        console.log(this.user); //xinyuan
        console.log(e+ee); //11
    }
}
var b = a.fn;
b.apply(a,[10,1]);
```

* 注意如果call和apply的第一个参数写的是null，那么this指向的是window对象

```js
var a = {
    user:"xinyuan",
    fn:function(){
        console.log(this); //Window {external: Object, chrome: Object, document: document, a: Object, speechSynthesis: SpeechSynthesis…}
    }
}
var b = a.fn;
b.apply(null);
```

**3、bind()**

bind方法返回的是一个修改过后的函数。

```js
var a = {
    user:"xinyuan",
    fn:function(){
        console.log(this.user); //xinyuan
    }
}
var b = a.fn;
var c = b.bind(a);
c(); //xinyuan
```

ok，同样bind也可以有多个参数，并且参数可以执行的时候再次添加，但是要注意的是，参数是按照**形参的顺序**进行的。

```js
var a = {
    user:"xinyuan",
    fn:function(e,d,f){
        console.log(this.user); //xinyuan
        console.log(e,d,f); //10 1 2
    }
}
var b = a.fn;
var c = b.bind(a,10);
c(1,2);
```

 总结：call和apply都是改变上下文中的this并立即执行这个函数，bind方法可以让对应的函数想什么时候调就什么时候调用，并且可以将参数在执行的时候添加，这是它们的区别，根据自己的实际情况来选择使用。

## Application

（2）

```js
function Animal(){   
  this.name="animal";   
  this.showName=function(){   
    console.log(this.name);   
  }   
}   
function Dog(){   
  this.name="dog";   
}   
var animal=new Animal();   
var dog=new Dog();       

//animal.showName() 对应"animal"
animal.showName.call(dog);

//输出：dog
```

在上面的代码中，我们可以看到Dog里并没有showName方法，那为什么（this.name）的值是dog呢？

关键就在于最后一段代码（animal.showName.call(dog)），意思是把animal的方法放到dog上执行，也可以说，把animal 的showName()方法放到 dog上来执行，所以this.name 应该是 dog。

（3）继承

```js
function Animal(name){   
  this.name=name;   
  this.showName=function(){   
    console.log(this.name);   
  }   
}   
function Dog(name){   
  Animal.call(this,name);   
}   
var dog=new Dog("Crazy dog");   
dog.showName();

输出：Crazy dog

```

Animal.call(this) 的意思就是使用 Animal对象代替this对象，那么Dog就能直接调用Animal的所有属性和方法。