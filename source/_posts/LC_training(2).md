---
title: LC_training(2)
category: Leetcode
tag: [LC]
---

1. ## 数组扁平化

   let flatArray = board.flat(Infinity);

   <!--more-->

2. ## reduce

   ```
   arr.reduce(callback(accumulator, currentValue[, index[, array]])[, initialValue])
   ```

   ### 参数

   ``callback`执行数组中每个值 (如果没有提供 `initialValue则第一个值除外`)的函数，包含四个参数：

     **`accumulator`**累计器累计回调的返回值; **它是上一次调用回调时返回的累积值**，或`initialValue`（见于下方）。 所以你的callback函数应该写成**primeArray.reduce((sum,e)=>{return sum+e},0)** 或者 **primeArray.reduce((sum,e)=>sum+e,0)** 就是说**你的返回值是下一次的sum**

     `currentValue`数组中正在处理的元素。

     `index` 可选数组中正在处理的当前元素的索引。 

      如果提供了`initialValue`，则起始索引号为0，否则从索引1起始。`array`可选调用`reduce()`的数组

   

2. ## 新建填满元素的数组

     **let primeArray = new Array(num).fill(true,0,num)**

     new Array(num) 返回的数组内是num个 empty.

     fill(填充字符, 起始索引, 终止索引), 其中终止索引不包含. 若不带索引, 则全部替换

4. ## 素数判定 与 筛选

     ```js
     //素数问题:  一个大于1的自然数，除了1和它自身外，不能被其他自然数整除的数叫做质数；否则称为合数（规定1既不是质数也不是合数）。
         var isPrimeUntilSqrt = function (num){
         	for(let i =2; i<=Math.sqrt(num); i++){
         		if(num%i == 0){
         			return false;
         		}
         	}
         	return true;
         }
         
     // 求小于num的素数个数, 且 不使用 isPrime函数
         var countPrimeWithElimination = function (num){
             let primeArray = new Array(num).fill(true);
             for(let i = 2; i<=Math.sqrt(num); i++){//由对称性, i计数到sqrtNUm即可
             	if(primeArray[i]){ //从2开始, 假如一个数是素数, 则其倍数不是素数
             		for(let j= i; j*i<=num; j++){ //由对称性, j从i计数即可
             	        primeArray[j*i] = false;
             	    }
             	}
             }
             let res = 
             return primeArray.slice(2).reduce((sum,e) => sum+(e===true),0);
         }
     ```

5. ## JS的char字符转num

     ```js
     'a'.charCodeAt()
     // 返回 97
     String.fromCharCode('a'.charCodeAt()+1)
     // 返回 'b'
     ```

     