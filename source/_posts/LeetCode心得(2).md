---
title: LC_training(2)
category: Leetcode
tag: [LC,易错,JS]
---

## 易错点

1. forEach()函数体中,无法使用return终止运行,也不能使用break,continue. 应该使用其他语法.

   <!--more-->

   ```js
   MyHashSet.prototype.contains = function(key) {
       this.set.forEach(function(e){
           if(e == key) return true;
       });
       return false;
   };
   
   ```

2. 今天遇到一个还挺厉害的简单题解法:

   有效的括号-20

   给定一个只包括 `'('，')'，'{'，'}'，'['，']'` 的字符串，判断字符串是否有效。其实就是栈问题.[leetcode-cn.com/problems/va…](https://leetcode-cn.com/problems/valid-parentheses)

   常规写法:

   略

   进阶写法:

```js
var isValid = function(s) {
    let map = {
        "{":"}",
        "(":")",
        "[":"]"
    };
    var stack = [];
    let len = s.length;
    let top = "";
    for (let char of s){ //char of s将s当做一个数组??如何办到的
        if (map[char]) { //利用map取代一个个判断条件
            stack.push(char);
            top  = char;
        }
        else {
            if(char == map[top]){ //利用map构建对应关系
                stack.pop();
                top = stack[stack.length-1];
            }
            else return false;
        }
    }

    return !stack.length; //return的条件简化
};
```

