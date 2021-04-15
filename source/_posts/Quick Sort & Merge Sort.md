---
title: Quick Sort & Merge Sort
category: Algorithm
tag: [LC, Algorithm]
thumbnail: /img/8-16-2.png
---
快速排序 和 归并排序 都是采用分治法（Divide and Conquer）的非常典型的应用。

<!--more-->

## Quick Sort  vs Merge Sort

快速排序使用分治法（Divide and conquer）策略来把一个串行（list）分为两个子串行（sub-lists）。

本质上来看，快速排序应该算是在冒泡排序基础上的递归分治法。

事实上，快速排序通常明显比其他 Ο(nlogn) 算法更快，因为它的内部循环（inner loop）可以在大部分的架构上很有效率地被实现出来。

> *快速排序的最坏运行情况是 O(n²)，比如说顺序数列的快排。但它的平摊期望时间是 O(nlogn)，且 O(nlogn) 记号中隐含的常数因子很小，比复杂度稳定等于 O(nlogn) 的归并排序要小很多。所以，对绝大多数顺序性较弱的随机数列而言，快速排序总是优于归并排序。*

![8-16-3](/img/8-16-3.png)

归并排序（Merge sort）是建立在归并操作上的一种有效的排序算法, 是采用分治法（Divide and Conquer）的一个非常典型的应用。

归并排序的实现由两种方法：

- 自上而下的递归 recursion（所有递归的方法都可以用迭代重写，所以就有了第 2 种方法）；
- 自下而上的迭代 iteration；

在《数据结构与算法 JavaScript 描述》中，作者给出了自下而上的迭代方法。但是对于递归法，作者却认为：

>However, it is not possible to do so in JavaScript, as the recursion goes too deep for the language to handle.
>
>然而，在 JavaScript 中这种方式不太可行，因为这个算法的递归深度对它来讲太深了。

![8-16-4](/img/8-16-4.png)



## 具体实现 

### 快排

总体逻辑:

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
```

partition实现:

```js
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

![8-16-1](/img/8-16-1.png)

### 归并排序

recursion实现:

```js
function merge(leftArr, rightArr){  
    var result = [];  
    while (leftArr.length > 0 && rightArr.length > 0){  
      if (leftArr[0] < rightArr[0])  
        result.push(leftArr.shift()); //把最小的最先取出，放到结果集中   
      else   
        result.push(rightArr.shift());  
    }   
    return result.concat(leftArr).concat(rightArr);  //剩下的就是合并，这样就排好序了  
}  

function mergeSort(array){  
    if (array.length == 1) return array;  
    var middle = Math.floor(array.length / 2);       //求出中点  
    var left = array.slice(0, middle);               //分割数组  
    var right = array.slice(middle);  
    return merge(mergeSort(left), mergeSort(right)); //递归合并与排序  
}  

var arr = mergeSort([32,12,56,78,76,45,36]);
console.log(arr);   // [12, 32, 36, 45, 56, 76, 78]
```

iteration实现:

```js
function mergeSort(arr){
  if(arr.length<2){
    return;
  }
  //设置子序列的大小
  var step=1; 
  var left,right;
  while(step<arr.length){
    left=0;
    right=step;
    while(right+step<=arr.length){
      mergeArrays(arr,left,left+step,right,right+step);
      left=right+step;
      right=left+step;
    }
    if(right<arr.length){
      mergeArrays(arr,left,left+step,right,arr.length);
    }
    step*=2;
  }
  return arr;
}
//对左右序列进行排序
function mergeArrays(arr,startLeft,stopLeft,startRight,stopRight){
  // 建立一个左、右数组
  var rightArr=new Array(stopRight-startRight+1);
  var leftArr=new Array(stopLeft-startLeft+1);
  // 给右数组赋值
  k=startRight;
  for(var i=0;i<(rightArr.length-1);++i){
    rightArr[i]=arr[k];
    ++k;
  }
   // 给左数组赋值
  k=startLeft;
  for(var i=0;i<(leftArr.length-1);++i){
    leftArr[i]=arr[k];
    ++k;
  }
  //设置哨兵值，当左子列或右子列读取到最后一位时，即Infinity，可以让另一个剩下的列中的值直接插入到数组中
  rightArr[rightArr.length-1]=Infinity;
  leftArr[leftArr.length-1]=Infinity;
  var m=0;
  var n=0;
  // 比较左子列和右子列第一个值的大小，小的先填入数组，接着再进行比较
  for(var k=startLeft;k<stopRight;++k){
    if(leftArr[m]<=rightArr[n]){
      arr[k]=leftArr[m];
      m++; 
    }
    else{
      arr[k]=rightArr[n];
      n++;
    }
  }
}
// 测试数据
var nums=[6,10,1,9,4,8,2,7,3,5];
console.log(mergeSort(nums)); 
```

# 疑问

![sort](/img/sort.png)

**空间复杂度**如何分析!!!!

与朱博抡交流得知: 对于快排, 尽管每次quicksort都只使用常量, 但一共log(n)轮累积计算, 空间复杂度为O(log n).

![8-16-5](/img/8-16-5.png)