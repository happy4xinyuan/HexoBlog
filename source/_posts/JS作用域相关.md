---
title: 日刷JS(2) 作用域相关
category: JS
tag: [JS]
thumbnail: /img/面经/变量提升.png
---

1. 作用域

   全局作用域, 函数作用域, 块级作用域 与 var, let, const的关系

   (原理未知)

2. 变量提升

   预编译在做什么? Lexical Environment

   Let, const 与 Var在此过程的区别 Temporal Dead Zone

   函数声明方式预编译会被写入, class写法与let类似, uninitialized

   ![变量提升](/img/面经/变量提升.png)

3. 闭包为什么可以将变量驻留内存

   1. 执行环境(EC), 变量对象(VO), 活动对象(AO), 作用域链(SC), 标志符解析过程
2. JS内存, 栈空间, 堆空间. 引用回收, Mark&Sweep机制
   3. 闭包函数作用域链存在对父函数的活动对象的引用, 因此该AO没有被回收

   ![执行环境](/img/面经/执行环境.png)

一个我的错题: 变量提升

```js
var salary = "1000$";

(function () {
    console.log("Original salary was " + salary);//尽管此时全局作用域中存在salary, 但是自身作用域中存在 salary:undefined优先访问

    var salary = "5000$";

    console.log("My New Salary " + salary);
})();
```

输出结果为: 

```js
Original salary was undefined
My New Salary 5000$
```

