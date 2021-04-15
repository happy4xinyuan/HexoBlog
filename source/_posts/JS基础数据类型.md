---
title: 日刷JS(1) 基础数据类型
category: JS
tag: [JS]
thumbnail: /img/JS(1).png
---

## 基础数据类型

数字（number）、字符串（string）、布尔值（boolean）、undefined、null、对象（Object）。  后面新增: **symbol,  bigint**

<!--more-->



![img](/img/JS(1).png)



## 判断方法

### 1. typeof 运算符

typeof 返回一个表示数据类型的字符串，返回结果包括：**number、string、boolean、undefined、object、function。**

typeof 可以对基本类型**number、string 、boolean、undefined**做出准确的判断

但是 **typeof obj 和 typeof null 都是返回 object,** 因此无法区别. (干脆直接判断 ===null就行了. 注意 **undefined == null 也成立**, 因此应该使用 ==)



### 2. instanceof 运算符

为了更进一步确定对象实例 属于哪个对象类型, 使用 instanceof, 本质是**判断左操作数对象的原型链上是否有右边这个构造函数的prototype属性**

注意 **原始数据类型不是对象**, 因此 `'hello' instanceof String // false`



## 如何判断一个对象是否是Array

### 1. 利用 `x instanceof Array` 来判断

不准确! x 也可能是Array的子类

### 2. `x._proto_ === Array.prototype` 判断 

准确! 实例对象的proto指向其构造函数的prototype

### 3. `Object.prototype.toString.call(x) === '[object Array]'`

准确! 其实数组自己也含有toString方法, 但是是输出自己的内容, 而调用toString时只会沿着原型链去搜索, 搜到自己的toString之后就停止了. 假如要使用Object的方法, 则**需要使用call方法**. 可以判断null 和 undefined





### 10.7 面试问题: 

1. JS的基础数据类型, number,string,bool不是对象,为什么有方法
2. JS的变量提升是怎么回事? EC,VO,AO是什么?
3. 什么是函数