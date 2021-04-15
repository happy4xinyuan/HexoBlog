---
title: JS对象与继承
category: JS
tag: JS
---

## js new一个对象 做了什么事? & js继承怎么实现的?

[MDN继承与原型链](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

<!--more-->

> new 到底在干吗呢? 我们看看自己实现的new操作:
>
> ```js
> function myNew(Func) {
>   const instance = {};
>   if (Func.prototype) {
>     Object.setPrototypeOf(instance, Func.prototype);
>   }
>   const res = Func.apply(instance, [].slice.call(arguments, 1));//什么意思!!!
>   if (typeof res === "function" || (typeof res === "object" && res !== null)) {
>     return res;
>   }
>   return instance;
> }
> ```
>
> 

## 1. Js对象的创建:

[适合初学者的JavaScript面向对象](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object-oriented_JS)

**方法一:** **纯函数**

1. 让我们看看如何通过一个普通的函数定义一个”人“。在您的文件中添加以下代码:

   ```js
   function createNewPerson(name) {
     var obj = {};
     obj.name = name;
     obj.greeting = function () {
       alert('Hi! I\'m ' + this.name + '.');
     }
     return obj;
   }
   ```

2. 您现在可以通过调用这个函数创建一个新的叫 salva 的人，在您浏览器的JavaScript console 试试 ：

   ```js
   var salva = createNewPerson('salva');
   salva.name;
   salva.greeting();
   ```

   上述代码运行良好，但是有点冗长；如果我们知道如何创建一个对象，就没有必要创建一个新的空对象并且返回它。幸好 JavaScript 通过构建函数提供了一个便捷的方法，方法如下：

**方法二:** **new+构造函数**

```js
function Person(name) {
  this.name = name;
  this.greeting = function() {
    alert('Hi! I\'m ' + this.name + '.');
  };
}
```

```js
var person1 = new Person('Bob');
```

**注意构造函数一点要是首字母大写!**

**极其常见的对象定义模式是，在构造器（函数体）中定义属性、在 `prototype` 属性上定义方法。**

```js
// 构造器及其属性定义

function Test(a,b,c,d) {
  // 属性定义
};

// 定义第一个方法, 方法一定要定义在构造函数的prototype中

Test.prototype.x = function () { ... }

// 定义第二个方法

Test.prototype.y = function () { ... }
```

把方法卸载构造函数 this中 和 prototype 的区别在于, 写在this中的无法被继承, 若要继承则需写在prototype中

**方法五: class语法他那个代替构造函数**

```js
class Rectangle {
    // constructor
    constructor(height, width) {
        this.height = height;
        this.width = width;
    }
    // Getter
    get area() {
        return this.calcArea()
    }
    // Method
    //该写法等价为 calcaArea: function(){
    calcArea() {
        return this.height * this.width;
    }
}
```

其中class里直接定义的方法, 如calcArea(), 其实相当于Rectangle.prototype.calcArea, 都是将该方法放在了对象实例的`_proto_`中, 而不是直接放在其属性中. 因此在extend产生的子类, 可以直接继承父类的方法.

 super的使用: 1. 如果子类中定义了构造函数，那么它**必须先调用 `super()` 才能使用 `this`** 。2. `super` 关键字用于调用对象的父对象上的函数。

```js
class Animal { 
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(this.name + ' makes a noise.');
  }
}
class Dog extends Animal {
  constructor(name) {
    super(name); // 调用超类构造函数并传入name参数
  }
  speak() {
    super.speak();
    console.log(this.name + ' roars.');
  }
}
```



**方法三:** **Object()构造函数** 后面两种都是先创造, 然后再添加属性

1. 尝试在您浏览器中的Javascript控制台中输入以下代码：

   ```js
   var person1 = new Object();
   ```

2. 这样就在

   ```
   person1
   ```

   变量中存储了一个空对象。然后, 可以根据需要, 使用点或括号表示法向此对象添加属性和方法；试试这个例子：

   ```js
   person1.name = 'Chris';
   person1['age'] = 38;
   person1.greeting = function() {
     alert('Hi! I\'m ' + this.name + '.');
   }
   ```

3. 还可以**只能将对象文本传**递给Object() 构造函数作为参数， 以便用属性/方法填充它。请尝试以下操作：

   ```js
   var person1 = new Object({
     name : 'Chris',
     age : 38,
     greeting : function() {
       alert('Hi! I\'m ' + this.name + '.');
     }
   });
   ```

**方法四:** **Object.create();**  基于现有对象创建新的对象。

1. 在 JavaScript 控制台中尝试此操作：

   ```js
   var person2 = Object.create(person1);
   ```

2. 现在尝试这个：

   ```js
   person2.name
   person2.greeting()
   ```

优势: `person2`是基于`person1`创建的， 它们具有相同的属性和方法。这非常有用， 因为它允许您创建新的对象而无需定义构造函数。

`create()` 实际做的是从指定原型对象创建一个新的对象。这里以 `person1` 为原型对象创建了 `person2` 对象。在控制台输入：

```js
person2.__proto__ //结果返回对象person1。
```



## 2. JS原型链

[对象原型](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes)

在传统的 OOP 中，首先定义“类”，此后创建对象实例时，类中定义的所有属性和方法都被**复制**到实例中。

在 JavaScript 中并不如此复制——而是在对象实例和它的构造器之间建立一个**链接**（它是__proto__属性，是从构造函数的`prototype`属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法。

**记住一点: `tommy._proto_ = Person.prototype  `** 

## constructor 属性

每个**实例对象**都从原型中继承了一个constructor属性，该属性指向了用于构造此实例对象的构造函数。`person1.constructor`返回 `Person()` 构造器

为什么Person.prototype也有? 因为他也是对象?

## 原型链的好处

太牛了太牛了! 可以**修改构造函数, 然后所有的子类实例对象都可以使用新的方法,** 太好啦! 赋值继承就办不到!

一个小问题:

```js
//增加方法
Person.prototype.fullName = function() {
  alert(this.name.first + ' ' + this.name.last);
}
```

> 可行, 其中this在 tommy.fullName()时生效, 所以正确指向.

```js
Person.prototype.fullName = 'Bob Smith';
//修改为
Person.prototype.fullName = this.name.first + ' ' + this.name.last;
```

> **这么做是无效的，因为本例中 `this` 引用全局范围，而非函数范围。**???如何分析此处this指向



## 3. 原型式继承

> <<关于先定义好对象实例, 之后再修改构造函数这件事>>
>
> 1. `Teacher.prototype = Object.create(Person.prototype);`之前定义的对象实例的`_proto_`还是原本的Object. 之后定义的才会为Person.
>
>    //因为这个是确定继承关系? 所以应该最早确定
>
>    //可是这一步不就是**复制**了吗? 不符合我对原型链的理解呀
>
>    //事实上你看下面的第二点, 证明他并不是复制, 而是**链接**, 哎...暂时理解为是构造函数链接之前的对象, 在被创建之后链接本身不会更新; 但要是之前被链接好了, 自然父类更新会被识别.
>
> 2. `Person.prototype.getName=function(){}`之前和之后定义的对象实例都会获得新的方法. 因为传递的是引用?
>
>    //这个是增加新的方法, 之后增加也无所谓

这样看来, 似乎原型链上每一个构造函数的prototype都是一样的? 

对(假如你按照自顶向下构建且不增加新的**需要继承的方法**), 不对, 假如你每次增加新的需要继承的方法`Person.prototype.getName`, 则越顶层可用方法越少.

### 以一个例子开始

1. 按照标准设计对象模式Person

```js
function Person(first, last, age, gender, interests) {
  this.name = {
    first,
    last
  };
  this.age = age;
  this.gender = gender;
  this.interests = interests;
};
```

```js
Person.prototype.greeting = function() {
  alert('Hi! I\'m ' + this.name.first + '.');
};
```

2. 定义继承的子类构造器 (继承数据成员)

```js
function Teacher(first, last, age, gender, interests, subject) {
  Person.call(this, first, last, age, gender, interests);

  this.subject = subject;
}
```

注意**其中call的用法**, 以及为啥能直接Person啊?不用new?

哦原来Teacher也是构造函数呀, 不是真的要生成对象, 那没事了

3. 为子类构造器继承函数成员

```js
Teacher.prototype = Object.create(Person.prototype);
```

等价于`Object.setPrototypeOf(instance, Func.prototype);`吗?

但是这样修改了构造函数的prototype, 即对象实例的`_proto_`属性, constructor属性也在其中被修改为父类, 因此需要修改回来.

```js
Teacher.prototype.constructor = Teacher;
```

## [拓展总结](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)