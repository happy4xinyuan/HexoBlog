---
title: Arrow Func & set map
category: JS
tag: ES6特性
---

## 箭头函数

箭头函数不会重新绑定this, **this指向最外层**; 而普通函数会重新绑定this到 **是谁调用了该函数**, 

<!--more-->

> ### **应用场合:**
>
> **普通函数会重新绑定this, 箭头函数不会重新绑定this.** 根据需求选择......
>
> > 面试经典: 当前this是什么?
>
> 举例子:
>
> ```js
> document.getElementById("the-button")
> .addEventListener("click",function(){
>     console.log(this);//输出的是button
>     this.classList.add("bigger");//成功添加监听
> })
> -----------------------------------------------------------
> 
> console.log(this) //输出window
> document.getElementById("the-button")
> .addEventListener("click",()=>{
>     console.log(this);//输出的是window
>     this.classList.add("bigger");//添加监听失败
> })
> ```
>
> 原因是**箭头函数不会重新绑定this, this指向最外层**;
>
> 而普**通函数会重新绑定this到 是谁调用了该函数**, 这里该函数作为addEventListener的参数, 其实是被button 调用
>
> 更进一步的例子:
>
> ```js
> document.getElementById("the-button")
> .addEventListener("click",function(){
>     console.log(this);//输出的是button
>     this.classList.add("bigger");//成功添加监听
>     //希望等待一秒后变化
>     setTimeout(function(){
>         console.log(this); //why! 指向window???? 因为这里发生函数调用,则重新绑定,而setTimeout不是被别人调用的,所以是window
>         this.innerHTML = "clicked";
>     })
> })
> 
> --------解决方法 一:保存临时this (很乱)-------------------
> document.getElementById("the-button")
> .addEventListener("click",function(){
>     console.log(this);//输出的是button
>     this.classList.add("bigger");
>     var tmpthis = this;
>     setTimeout(function(){
>         console.log(tmpthis);
>         tmpthis.innerHTML = "clicked";
>     })
> })  
> 
> --------最好的方法: 箭头函数替代--------------------------
> document.getElementById("the-button")
> .addEventListener("click",function(){
>     console.log(this);//输出的是button
>     this.classList.add("bigger");
> 
>     setTimeout(()=>{
>         console.log(this);
>         this.innerHTML = "clicked";
>     })
> })  
> ```
>
> ==待突破的难点:== 
>
> * ==this 的值是什么?==

## 例题一

```js
/*非严格模式*/

var name = 'window'

var obj1 = {
    name: '听风是风',
    fn1: function () {
        console.log(this.name)
    },
    fn2: () => console.log(this.name),
    fn3: function () {
        return function () {
            console.log(this.name)
        }
    },
    fn4: function () {
        return () => console.log(this.name)
    }
}
var obj2 = {
    name: '行星飞行'
};

obj1.fn1();//?风
obj1.fn1.call(obj2);//?行星

obj1.fn2();//?window
obj1.fn2.call(obj2);//?行星  ！！！且一旦绑定无法通过call，apply或者bind再次改变箭头函数的this。 答案应该是... window!!!!

obj1.fn3()();//?window
obj1.fn3().call(obj2);//?行星
obj1.fn3.call(obj2)();//?window

obj1.fn4()();//?window 
//var fn = obj1.fn4(); // 箭头函数并没有自己的this，它的this指向由上层执行上下文中的this决定
//window.fn();//无法改变箭头函数this


let fn = obj1.fn4;
fn4()(); // win
//虽说无法改变箭头函数this, 但其上层的fn4修改后, 箭头函数也会改变

obj1.fn4().call(obj2);//?行星
obj1.fn4.call(obj2)();//?window

```

## 例题二

```js
/*非严格模式*/
var name = 'window'

function Person(name) {
  this.name = name;
  this.fn1 = function () {
    console.log(this.name);
  };
  this.fn2 = () => console.log(this.name);
  this.fn3 = function () {
    return function () {
      console.log(this.name)
    };
  };
  this.fn4 = function () {
    return () => console.log(this.name);
  };
};

var obj1 = new Person('听风是风');
console.dir(obj1);
var obj2 = new Person('行星飞行');

obj1.fn1(); //风
obj1.fn1.call(obj2);//行

obj1.fn2();//风 ?
obj1.fn2.call(obj2);//风

obj1.fn3()();//win
obj1.fn3().call(obj2);//行
obj1.fn3.call(obj2)();//win

obj1.fn4()();//风 ?
obj1.fn4().call(obj2);//风
obj1.fn4.call(obj2)();//行
```




```js
const ages =[14,19,21];

ages.map(age=> "his age is "+age );

ages.filter(age=> age>18);
```



## set



```js
const numSet = new Set();

numSet.add(e)  //返回set

numSet.delete(e) //返回bool

numSet.has(e) //返回bool

numSet.size   //数组才是length

//遍历 或者for of (原本的for in)
numSet.forEach(number=> console.log(number))

```



## map 

```js
const person = new Map();

person.set("name","laobi")
person.set("age",18) //value可以是不同类型的

person.get("name") //返回value

person.size //数组才是length
 
person.has("name") //返回bool

//添加重复的key呢?
person.set("name","xiaocai") //将会替换原本的value

person.delete("age") //返回bool

//遍历 或者for of (原本的for in)
person.forEach(箭头函数)
```

