---
title: JS深拷贝
category: JS
tag: [JS]
---

### ES6扩展运算符实现数组的深拷贝

用下面的方法实现数组的深拷贝是最简单的。

```js
var arr = [1,2,3,4,5]
var [ ...arr2 ] = arr
arr[2] = 5
console.log(arr)
console.log(arr2)
```

太妙了!

<!--more-->

这就不得不提一提ES6:扩展运算符了:

##### 1. 替换apply

> ##### 扩展运算符可以展开数组，代替`apply()`将数组转化为函数参数的需求。

```js
//ES5
Math.max.apply(null, [1, 3, 5]);
//ES6
Math.max(...[1, 3, 5]);  

```

##### 2. 合并数组

```js
//ES5
var arr1 = [8]
var arr2 = [9,11,12,13]
arr1.push(arr2);
//[8,[9,11,12,13]]
Array.prototype.push.apply(arr1,arr2);
//[8,9,11,12,13]

// ES6
arr1.push(...arr2);
console.log(arr1)
//[8,9,11,12,13]

//可以直接写
var newArr = [...arr1, ...arr2]
console.log(newArr)

```

##### 3. 结构赋值

```js
var [a, ...b] = [2,3,4,5,6];
console.log(a, b);
//2 [3,4,5,6]
var [c, ...d] = [2];
console.log(c, d);
//2 []
var [...x,y] = [2,3,4,5,6];
//Uncaught SyntaxError: Rest element must be last element

```

> ##### 当扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。

##### 4. 对象的扩展运算符

```js
var {a, b, ...c} = {a : 12, b : 23, d : 34, c : 345}
//c ->{d:34,c:345}

var obj = {name : 'coco'}
var obj1 = {age : 27}
var newObj = {...obj, ...obj1};
var newObj1 = {obj, obj1};
console.log(newObj);//{name: "coco", age: 27}
console.log(newObj1);//{name: "coco"} {age: 27}

```

##### 5. 把字符串转化为数组

```js
var strArr = [...'kiwi']
console.log(strArr)
//["k","i","w","i"]

```

> ##### 扩展运算符还解决了JavaScript 将 32 位 Unicode 字符，识别为 2 个字符的问题。

太牛了吧! riririririiriririr!

简单的深拷贝:

```js
function jsonClone(obj) {
    return JSON.parse(JSON.stringify(obj));
}
var clone = jsonClone({ a:1 });
```

**阿远的深拷贝!**

```js
//深拷贝
    var deepClone = function (obj){
    	let objClone = obj instanceof Array? []: {};
    	for(key in obj){ // for in 判断的属性是 proto链上所有属性
    		if(obj.hasOwnProperty(key)){
    			if(obj[key] instanceof Object ){
    		        objClone[key] = deepClone(obj[key]);
    			} else {
    				objClone[key] = obj[key];
    			}
    		}
    	}
    	return objClone;
    }

    let A = {x:1,y:[1,2,3],z:{a:2}};
    let B = [1,2,3,4,[1,2],{}];
    console.log(deepClone(A));
    console.log(deepClone(B));
```

