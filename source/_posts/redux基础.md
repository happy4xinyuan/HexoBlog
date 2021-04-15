---
title: redux 基础
category: react
tag: [react]
---

## 1.redux是什么?

1)     redux是一个独立专门用于做**状态管理的JS库** **(不是react插件库**) 你看他的名字就没有react!

2)     它可以用在react, angular, vue等项目中, 但基本与react配合使用

3)     作用: **集中式管理**react应用中**多个组件共享的状态**

>  我们目前就是'状态在哪里, 修改状态的行为就定义在哪里', 也算是集中式管理. 但对于大型项目就很麻烦了. 思考将所有状态和方法封装在redux中, 之后传递给需要的组件.

<!--more--> 

组件: 1. 显示(读数据) 2.交互(修改数据)

![](http://www.ruanyifeng.com/blogimg/asset/2016/bg2016091802.jpg)

## 2. 使用redux场景

1)       总体原则: 能不用就不用, 如果不用比较吃力才考虑使用

2)         某个组件的状态，需要共享

3)         某个状态需要在任何地方都可以拿到

4)         一个组件需要改变全局状态

5)         一个组件需要改变另一个组件的状态

不用? 多捞啊!

## 3. 基础应用

1. **下载依赖包**

   npm install --save redux

2. **设计reducers**

   reducers是一类函数, 根据 当前state 与 收到的action 返回新的state. 即定义数据的操作方法. 同时也可以在函数参数中设置默认值, 起到定义state的作用.

   ```js
   // 包含n个reducer函数的模块
   import {INCREASE,DECREASE,INCREASASYCN} from "./action-types";
   
   export function counter(state =0, action){
       console.log('counter()',state, action);
       switch (action.type){
           case INCREASE:
               return state + action.data
           case DECREASE:
               return state - action.data
           case INCREASASYCN:
               return state + action.data
           default:
               return state;
       }
   }
   
   ```

   action是一个对象, 主要是`type`定义操作类型, `data`传递参数. 

   其中为了代码正确性保证(字符串编译不会报错, 变量可以提前报错), 可以将各种action.type放在另一个文件中,`export const INCREASE = 'INCREASE';`

   `export`和`export default`的区别在于前者需要解构对象之后才能使用, 而后者指的已是对象, 不用解构.

3. **根据counter函数创建store对象, 并传给需要的组件**

   ```js
   const store = createStore(counter);
   
   ReactDOM.render(
       (
               <App store={store} />
       ),
       document.getElementById('root')
   );
   ```

4. 在组件中使用

   主要分为 读取state数据 和 操作修改数据

   **读取数据** 采用`let state = this.props.store.getState();`

   **修改数据** 采用`this.props.store.dispatch({type:INCREASE,data: num })` 其中dispatch函数接受一个action对象, 传递给state. 在state中使用reducer进行状态的修改.

5. 重绘

   redux导致react重绘有点难理解, 明明state变化了, 为什么没有自动重绘呢??

   因为我们的state定义在index.js中, state发生变化后App其实并未感知到. 因此需要**store.subscribe()来手动触发重绘**.
   
   ```js
   let render = ()=> ReactDOM.render(
       (
               <App store={store} />
       ),
   
       document.getElementById('root')
   );
   
   render()//初始化绘制
   
   store.subscribe(render)
   ```
   
   

## 4. 进一步包装

1. action-creator.js

```js
import {INCREASE,DECREASE,INCREASASYCN} from "./action-types";

export const increaseCreator = (num) =>({type:INCREASE,data: num })
export const dereaseCreator = (num) =>({type:DECREASE,data: num })
export const increaseAsyncCreator = (num) =>({type:INCREASASYCN,data: num })
```

2. store.js

```js
import {createStore} from "redux";
import {counter} from "./reducers";

const store = createStore(counter);
export default store;
```



## 5. 调试工具



### 1. 安装chrome浏览器插件

​       redux-devtools                         

### 2. 下载工具依赖包

```
npm install --save-dev redux-devtools-extension
```

### 3. 编码

```js
import { composeWithDevTools } from 'redux-devtools-extension'

const store = createStore(
  counter,
  composeWithDevTools(applyMiddleware(thunk)) 
)
```