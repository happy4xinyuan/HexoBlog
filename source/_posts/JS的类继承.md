---
title: JS类继承--我终于悟了!
category: JS
tag: JS
---

学习来源: [深入理解JS中的对象（三）：class 的工作原理 - forcheng - 博客园](https://www.cnblogs.com/forcheng/p/12913103.html)

学习方法: 在babelijc网站 利用ES6转ES5原码功能, 阅读class原码.

<!--more-->

实验代码:


```JS
class People {
    constructor(name) {
        this.name = name
    }
    getName(){};
    static sayHello(){};
}
  
class Man extends People {
    constructor(name, sex) {
        super(name)
        this.sex = sex
    }
    getSex(){};
    static sayFuck(){};
}

var teacher = new People('teacher');
var Wang = new Man('Tom', 'M');
```
一图看懂js class实现的继承的原型链关系: 

![class继承](/img/面经/class继承.jpg)

