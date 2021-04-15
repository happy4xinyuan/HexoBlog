---
title: LC_training(1)
category: Leetcode
tag: [LC,易错,JS]
---
## 犯过的那些错误

1. 一个专属JS的不起眼的坑!!!
   ```js
   for(var i in nums){
           if(nums[i]==nums[i+1]){
           ...
           }
   ```

   上述代码中nums[i+1]结果undifined, 因为for in遍历的原理是什么呢? 其实是把nums数组当做Obejct(一个键值对结构), 其中**i 是对于其key的遍历**, 因此i 其实是**字符串"0", 而i+1则表示"01"**, 自然是找不到结果. 

   <!--more-->

   对此问题我们深入研究, 
   
   ```js
   const arr = [ “ a”，“ b”，“ c” ];
   arr.test = “坏”；//既然数组是object,就可以添加非数字属性
   
   for (let i in arr) {
       console.log(i+":"+ arr[i]); // 打印"0:a, 1:b, 2:c, test:bad"
}
   ```

   因此在 Js的数组遍历中,我们应该**避免使用for/in**, 而可以选择for/of替代.
   
   ```js
   for (const element of arr) {
       console.log(element);
   }
   for (let i = 0; i < arr.length; ++i) {
       console.log(arr[i]);
}
   ```

   此外, `for`，`for/in`与`for/of`会保留外部作用域的`this`。

   对于`forEach`， 除非使用箭头函数，它的回调函数的 this 将会变化。
   
   ```js
   "use strict";
   
   const arr = ["a"];
   
   arr.forEach(function() {
       console.log(this); // 打印undefined
   });
   
   arr.forEach(() => {
       console.log(this); // 打印{}
});
   ```
   
2. JS对象 与 哈希表:

   js中的**对象是基于哈希表结构**的,而哈希表的查找时间复杂度为O(1),所以很多人喜欢用对象来做映射,减少遍历循环.

   对于找数组重复元素, 我们的核心是**利用key存储数据, 利用value来记录该元素是否存在(bool)**, 但是需要注意的是key值会被转化为字符串, 因此数组中元素类型应该保持一致.

   核心: object, 是属性名-属性值的键值对, 对于属性名(字符串)的ASII码进行哈希算法的应用, 这样就构建了高效(O(1)时间存取)的HashMap结构了.

3. 一个**很蠢**的问题:

   ```js
   let tmp = nums[i];
   nums[i] = nums[nums[i]];
   nums[nums[i]] = tmp;
   ```

   经常在自身哈希的题目中犯错, nums[i]的值被改变了呀!

   应该提前将nums[i]的值保存下来:

   ```js
   let numi = nums[i];
   [nums[i],nums[numi]] = [nums[numi],nums[i]]//解构赋值语法ES6
   ```

4. 拼写：querySelector,querySelectorAll,addEventListener,
5. querySelectorAll的函数返回为 Nodelist, 千万不要直接使用！！！

## 题解

3. ##### 找出数组中重复的数字。长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内

   * 排序之后遍历判断相邻元素: nlogn, n

   * ```JS
    var findRepeatNumber = function(nums) {
         nums.sort();
        for(let i=0; i<nums.length;i++){
             if(nums[i]==nums[i+1]){
                 return 9;
             }
         }
         return 0;
     };
     ```
   
     
   
   * set每插入一个, 检查size: n, n (基于假设set存取均为O(1))
   
   * ```js
     var findRepeatNumber = function(nums) {
         let numSet = new Set();
         let size = numSet.size;
         for (let x of nums){
         	numSet.add(x);
         	if(numSet.size==size){
         		return x;
         	}
         	else{
         		size ++;
         	}
         }
         return 0;
     };
     ```
   
     
   
   * 采用基于对象的hash映射, n, n (基于假设set存取均为O(1)):
   
   * ```js
    var findRepeatNumber = function(nums) {
          let obj = {};
          for(num of nums){
          	if(!obj[num]){
         		obj[num]=true;
          	}
          	else{
          		return num;
          	}
          }
      };
     ```
     
     
     
   * 所有数字都在 0 ～ n-1 的范围内。因此不需要额外开辟空间，每次遍历时，检查当前元素是否放在了正确位置上（例如元素 i 应该放在下标为 i 的位置上）。如果放在了正确位置上，那么继续循环。否则：
   
     下标为 num 的元素 == num，说明当前元素 num 是重复的，直接返回
     下标为 num 的元素 != num，交换当前元素和下标为 num 的元素，将当前元素放入到正确位置上
     
     n, 1 (基于假设set存取均为O(1))
     
     

4. #### [二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

   观察大小规则, 利用**单调性**查找元素!