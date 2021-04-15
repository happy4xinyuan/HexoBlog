---
title: 练手react-router
category: react
tag: [react]
---

## 基本标签的使用

1. 在index.js中, render的对象需要用` <BrowserRouter>`包裹:

<!--more-->

```js
import {BrowserRouter, HashRouter} from 'react-router-dom';

ReactDOM.render(
    (
        <BrowserRouter>
            <App />
        </BrowserRouter>

    ),
    document.getElementById('root')
);

```

2. 导航标签:

```js
import {NavLink, Switch, Route, Redirect}} from "react-router-dom";
<NavLink to='/about'>About</NavLink> 
```

3. 切换标签, 路由标签, 重定向标签

```js
{/*可切换的路由组件, 只显示其中一个*/}
<Switch>
    {/*Route组件定义一个映射关系, 将组件与路径url对应*/}
    <Route path='/about' component={About}/>
    <Route path='/home' component={Home}/>
 
    <Redirect to='/about'/> //表示默认重定向路由
</Switch>
```

## 自定义NavLink标签样式

[官方文档](https://reactrouter.com/web/guides/quick-start)

```js
<NavLink to="/faq" activeClassName="selected">
  FAQs
</NavLink>
```

修改activeClassName (该属性表示被激活的NavLink被添加的类名, 默认 为 'active' , 因此被BS4修饰)

接着可以在index.css中对`selected`对象进行修饰.

* 对于css多文件修饰对象, 可以在属性中增加`!important`防止被覆盖!

* ```css
  .selected{
      color: palevioletred !important;
  }
  ```

  

但是一个NavLink都要这样改, 好麻烦 啊!?

-> 自定义MyNavLink

```js
import React, {Component} from 'react'
import {NavLink} from "react-router-dom";

export default class MyNavLink extends Component {
    render (){
        return (
            // 将外部所有属性传入NavLink
            <NavLink {...this.props} activeClassName='selected'></NavLink>
        )
    }
}
```

其中`...`运算符十分关键, 因为你不知道外部传入了多少个属性, 因此没法在props中一个个声明. 但是可以直接全部展开并传入NavLink中.

```js
<MyNavLink className="list-group-item" to='/about' >About</MyNavLink>
```


## 嵌套路由

没什么难的, 复习一下. 路由的核心:

1. 网页的入口: `NavLink to='/home/news'`

2. 需要动态显示的部分: `<Switch></Switch>`

3. 网页路径 与 显示部件的映射关系: `<Route path='/home/message' component={Message}/>`

## 路由传参 `this.props.match.params`

1. 路由参数

`:paramname`

```
<Route path='/home/message/message-detail/:id' component={MessageDetail}>
```

2. 组件取参

   `this.props.match.params` 中包含url中的参数

* a href是普通链接, 只有NavLink才是路由链接. 有时候出现点击链接后boostrap样式丢失, 可能是因为点的是普通链接的原因.

## 小技巧

1. 对数组的find方法, 提供一个判断函数!

```js
const massage = massages.find((m) => m.id == id); //匹配第一个未true的数组元素
```