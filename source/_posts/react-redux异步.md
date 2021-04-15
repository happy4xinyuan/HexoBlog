---
title: react-redux 异步(redux-thunk)
category: react
tag: [react]
---

> 缘由: 在react-redux的redux部分不支持异步代码, 当前我们其实是redux部分reducer与action-creator共同提供修改状态的方法. 异步部分在component中实现. 
>
> 为了在redux中设计异步操作, 我们需要使用 redux-thunk(异步中间件)

<!--more-->

1. 下载redux插件

   npm install --save redux-thunk

2. 生成store时使用

   ```js
   import {createStore, applyMiddleware} from 'redux'
   import thunk from 'redux-thunk'
   // 根据counter函数创建store对象
   const store = createStore(
     counter,
     applyMiddleware(thunk) // 应用上异步中间件
   )
   ```

   如此一来我们便可以在action-creator中生成 函数action , 使用异步代码.

3. 异步action creator(返回一个函数)

   ```js
   export const incrementAsync = number => {
     return dispatch => {
       setTimeout(() => {
         dispatch(increment(number))
       }, 1000)
     }
   }
   
   ```

   



