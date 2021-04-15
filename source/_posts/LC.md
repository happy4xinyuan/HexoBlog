---
title: LC_training(3)
category: Leetcode
tag: [LC,易错,JS]
---

1. quickSort 会写递归版本, partition不开新空间. nlogn, logn(只考虑算法运行空间,但是n个元素必然需要n的空间)

2. mergeSort 会写递归版本, merge需要新空间, nlogn, n+logn; 

3. mergeSort 会写迭代版本, merge需要新空间, nlogn, n;

   <!--more-->

4. 手写快排()

   ```js
   function quickSort(arr, left, right) {
       var len = arr.length,
           partitionIndex,
           left = typeof left != 'number' ? 0 : left,
           right = typeof right != 'number' ? len - 1 : right;
   
       if (left < right) {
           partitionIndex = partition(arr, left, right);
           quickSort(arr, left, partitionIndex-1);
           quickSort(arr, partitionIndex+1, right);
       }
       return arr;
   }
   function partition(arr, left ,right) {     // 分区操作
       var pivot = left,                      // 设定基准值（pivot）
           index = pivot + 1;
       for (var i = index; i <= right; i++) {
           if (arr[i] < arr[pivot]) {
               swap(arr, i, index);
               index++;
           }        
       }
       swap(arr, pivot, index - 1);
       return index-1;
   }
   ```

   时间复杂度: nlogn, 空间复杂度: logn (递归的压栈导致)

   

5. mergeSort的递归和迭代

   ```js
   var mergeSortArr = function(arr) {
   	if(arr.length == 1) return arr;
       let mid = Math.floor(arr.length/2);
       return myMerge(mergeSort(arr.slice(0,mid)), mergeSort(arr.slice(mid,arr.length)));
   }
   
   //trick of array operation
   var myMergeArr = function(arr1,arr2){
       let res =[];
       while(arr1.length>0&&arr2.length>0){
           if(arr1[0]<arr2[0]){
           	res.push(arr1.shift());
           } else {
           	res.push(arr2.shift());
           }
       }
       return res.concat(arr1).concat(arr2);
   }
   ```

   ```js
   var mergeSortArrIterate = function(arr) {
   	let step = 1;
   	for(step = 1; step<arr.length;step = step<<1){
   		let left = 0,right = left+step;
           while(right+step<arr.length){
               myMergeArr_ChangeFormer(arr, left,left+step,right,right+step)
               left = right+step;
           	right = left+step;
           } 
           if(right<arr.length){
           	myMergeArr_ChangeFormer(arr, left,left+step,right,arr.length);
           }
   	}
   	return arr;
   }
   var myMergeArr_ChangeFormer = function(arr,lb,le,rb,re){
       let res =[];
       let arr1 = arr.slice(lb,le);
       let arr2 = arr.slice(rb,re);
       while(arr1.length>0&&arr2.length>0){
           if(arr1[0]<arr2[0]){
           	res.push(arr1.shift());
           } else {
           	res.push(arr2.shift());
           }
       }
       res = res.concat(arr1).concat(arr2);
       for (i=lb;res.length;i++){
       	arr[i] = res.shift();
       }
   }
   
   ```

   

6. 链表的翻转

   ```js
   var reverseListIterate = function(head) {
   	let currNode = head;
   	let resHead = null;
   	let tmp = null;
   	while(currNode){
           tmp = currNode.next;
           currNode.next = resHead;
           resHead = currNode;
           currNode = tmp;
   	}
   	return resHead;
   }
   ```

   最重要的是不要忘了链表的**头插法**

7. 快速找到链表的二分结点: **快慢指针**

   因为指针走到最后时, 已经忘记了mid在何处.

   ```
   var findMidNode = function(head){
   	let p1 = p2 =head;
   	while(p2&&p2.next){
   		p2 = p2.next.next;
   		p1 = p1.next;
   	}
   	return p1;
   }
   ```

8. `concat()` 方法用于合并两个或多个数组。此方法不会更改现有数组，而是**返回一个新数组**。

   ```js
   const array1 = ['a', 'b', 'c'];
   const array2 = ['d', 'e', 'f'];
   const array3 = array1.concat(array2);
   
   console.log(array3);
   // expected output: Array ["a", "b", "c", "d", "e", "f"]
   ```

   `slice()` 方法返回一个**新的数组对象**，这一对象是一个由 `begin` 和 `end` 决定的原数组的**浅拷贝**（包括 `begin`，不包括`end`）。原始数组不会被改变。

   ```js
   const animals = ['ant', 'bison', 'camel', 'duck', 'elephant'];
   
   console.log(animals.slice(2));
   // expected output: Array ["camel", "duck", "elephant"]
   
   console.log(animals.slice(2, 4));
   // expected output: Array ["camel", "duck"]
   ```

9. array的这个merge写的太好了, 不得不看:

   ```js
   var myMergeArr = function(arr1,arr2){
       let res =[];
       while(arr1.length>0&&arr2.length>0){
           if(arr1[0]<arr2[0]){
           	res.push(arr1.shift()); //巧妙地利用shift,减少了指针的遍历
           } else {
           	res.push(arr2.shift());
           }
       }
       return res.concat(arr1).concat(arr2);//假如一个为空,也无所谓
   }
   ```

   

