---
title: WebStorm? 爽!
category: react
tag: [react]
---

## 1). 技巧

1. **Live reload 太爽啦!!!!**

<!--more-->

2. **代码自动补全**

webstorm -> settings -> live templates

设置自动代码补全如下:

```jsx
import React, {Component} from 'react'

export default class $className$ extends Component {
    render (){
        return (
        <div>
        
        </div>
        )
    }
}
```
## 2). 注意事项

1. 咋**导入svg图片**不能直接导入啊?!, 得这样

```js
import logo from '../logo.svg'

   <img className="logo" src={logo} alt="logo"/>
```

2. jsx 必须要有**标签的结束符**

比如 input 标签在html 中可能省略 /

```jsx
 <input type="text" class="form-control" placeholder="用户名" />
<img src="..." className="card-img-top" alt="..."/>
```

3. jsx 中必须使用**className,** 而不是class!

[原因](https://www.jackpu.com/you-qu-de-hua-ti-wei-shi-yao-jsxyong-classnameer-bu-shi-class/)

4. jsx中不能使用style='display: none' 的形式, 必须使用**style={(display: 'none')}**

解释: 其中第一层{}表示是js代码, 第二层{}表示是对象, 'none'表示是字符串

5. 自定义的MyComponent一定要是**组件&标签 首字母大写**, **否则不会被渲染!!!**

```jsx
import CommentAdd from "../comment-add/comment-add";
import CommentList from "../comment-list/comment-list";

<CommentAdd />
<CommentList />
```

6. 使用props属性时, 需要**导入`prop-types`库**

```jsx
import PropTypes from 'prop-types';

TodoAdd.propTypes = {
      add: PropTypes.func.isRequired,
      count: PropTypes.number.isRequired
    }

//更优写法, 咋TodoAdd组件对象内添加如下代码
static propTypes ={
        comments: PropTypes.array.isRequired
    }

```

7. 使用ref属性将外部输入导入组件属性时, 注意**使用this.input.value**  !

```js
<input type="text" ref={e=> this.user=e} className="form-control" placeholder="用户名" />
       
this.props.addcomment(this.user.value,this.text.value);
```

8. 不用每次bind this, 简便写法: **声明组件内自定义函数, 采用箭头函数**:

```jsx
    userChange = (event) =>{
        const author = event.target.value;
        this.setState({author})
    }
```

有点奇怪,  组件内的函数, 咋似乎不用声明, 可以直接写内容??? 似乎在对象中增加属性也是一样的?

* 此外**{author}是新的ES6语法, 等同于{author: author}**

9. 对于form标签注意:  
 **1. 不要放在p标签下** 
2.handle函数中增加 **event.preventDefault() // 阻止事件的默认行为**: 提交表单会刷新网页
             