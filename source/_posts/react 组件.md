---
title: react-组件化开发
category: react
tag: [react]
thumbnail: /img/component组合使用.gif
---

组件化开发

<!--more-->

## 1.1. 基本理解和使用
```js
1). 自定义的标签: 组件类(函数)/标签
2). 创建组件类
	//方式1: 无状态函数(简单组件, 推荐使用)
	function MyComponent1(props) {
		return <h1>自定义组件标题11111</h1>
	}
	//方式2: ES6类语法(复杂组件, 推荐使用)
	class MyComponent3 extends React.Component {
		render () {
		  return <h1>自定义组件标题33333</h1>
		}
	}
3). 渲染组件标签
	ReactDOM.render(<MyComp />,  cotainerEle)
4). ReactDOM.render()渲染组件标签的基本流程
	React内部会创建组件实例对象/调用组件函数, 得到虚拟DOM对象
	将虚拟DOM并解析为真实DOM
	插入到指定的页面元素内部
```

## 2.1. 组件的3大属性: state
```js
1. 组件被称为"状态机", 页面的显示是根据组件的state属性的数据来显示
2. 初始化指定:
    constructor() {
      super()
      this.state = {
        stateName1 : stateValue1,
        stateName2 : stateValue2
      }
    }
3. 读取显示: 
    this.state.stateName1
4. 更新状态-->更新界面 : 
    this.setState({stateName1 : newValue})
```

## 2.2. 组件的3大属性: props
```js
所有组件标签的属性的集合对象
给标签指定属性, 保存外部数据(可能是一个function)
在组件内部读取属性: this.props.propertyName
作用: 从目标组件外部向组件内部传递数据
对props中的属性值进行类型限制和必要性限制
	Person.propTypes = {
		name: React.PropTypes.string.isRequired,
		age: React.PropTypes.number.isRequired
	}
扩展属性: 将对象的所有属性通过props传递
    <Person {...person}/>
```

## 2.3. 组件的3大属性: refs
	组件内包含ref属性的标签元素的集合对象
	给操作目标标签指定ref属性, 打一个标识
	在组件内部获得标签对象: this.refs.refName(只是得到了标签元素对象)
	作用: 找到组件内部的真实dom元素对象, 进而操作它
面试题
问题: 请区别一下组件的 props 和 state 属性? 

1) state: 组件自身内部可变化的数据 

2) props: 从组件外部向组件内部传递数据, 组件内部只读不修



## 3.3. 组件的组合

设计思路

1) 拆分组件: 拆分界面,抽取组件 

2) 实现静态组件: 使用组件实现静态页面效果 

3) 实现动态组件 a. 动态显示初始化数据 b. 交互功能(从绑定事件监听开始

思考问题: 数据保存在那个组件中?

两个子组件, 一个读, 一个写. 故放在父组件中.

```js
//li显示常用技巧: 
class TodoList extends React.Component {
      render () {
        const {todos} = this.props
        return (
          <ul>
            {
                //map函数!!!
              todos.map((todo, index) => <li key={index}>{todo}</li>)
            }
          </ul>
        )
      }
    }
```



## 4. 实例

实现效果:

![component组合使用](/img/component组合使用.gif)

* html 内容: 导入库, 设置容器

```js
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>05_components_composing</title>
</head>
<body>

  <div id="example"></div>

	//导入所需react库, 与babel库
  <script type="text/javascript" src="../js/react.development.js"></script>
  <script type="text/javascript" src="../js/react-dom.development.js"></script>
  <script type="text/javascript" src="../js/prop-types.js"></script>
  <script type="text/javascript" src="../js/babel.min.js"></script>
  <script type="text/babel">
	...
  </script>
</body>
</html>

```
组件设计思路:
具体实现:

```react
    /*
    1)拆分组件: 拆分界面,抽取组件
    2)实现静态组件: 使用组件实现静态页面效果
    3)实现动态组件
        ① 动态显示初始化数据
        ② 交互功能(从绑定事件监听开始)
     */
    // 应用组件[state]
    class App extends React.Component {
      constructor (props) {
        super(props)
        // 初始化状态
        this.state = {
          todos: ['吃饭', '睡觉', '打豆豆']
        }
        this.add = this.add.bind(this)
      }
      add (todo) {
        const {todos} = this.state
        todos.unshift(todo)
        //更新状态 必须使用set State( 修改之后的完整state )
        this.setState({todos})
      }
      render () {
        const {todos} = this.state
        return (
          //只能有一个根标签, 所以用div包括
          <div>
            // 必须制定this.add, 否则只会在render的域搜索
            <TodoAdd add={this.add} count={todos.length} />
            //此处将APP的state中的todos内容, 传给TodoList的props中的todos属性
            <TodoList todos={todos} />
          </div>
        )
      }
    }
```

```react
    // 添加todo组件 [props,ref]
    class TodoAdd extends React.Component {
       //固定套路,记得写上
      constructor (props) {
        super(props)
        this.addTodo = this.addTodo.bind(this)
      }
      //
      addTodo () {
        // 读取输入数据
        const text = this.sinput.value.trim()
        // 查检
        if(!text) {
          return
        }
        // 保存到todos
        this.props.add(text)
        // 清除输入
        this.input.value = ''
      }
      render () {
        return (
          <div>
            <h2>Simple TODO List</h2>
            //利用ref属性完成外部输入的数据流入到本组件的属性内
            //其中ref={input => this.sinput=input} sinput为定义给TodoAdd组件的属性,可以自己修改. 
            //可以在其他地方通过this.sinput指示该输入框元素,利用this.sinput.value获取填入值
            <input type="text" ref={input => this.input=input}/>
			//对应失去焦点的函数为onBlur
            <button onClick={this.addTodo}>Add #{this.props.count}</button>
          </div>
        )
      }
    }
    //由App组件传入的数据, 可以通过this.props.add直接调用
    TodoAdd.propTypes = {
      add: PropTypes.func.isRequired,
      count: PropTypes.number.isRequired
    }
```

```react
    // todo列表组件 [props组件]
    class TodoList extends React.Component {
      render () {
        // 什么意思? props是一个对象,todo就是取出了这个对象的内容,即array
        const {todos} = this.props
        return (
          <ul>
            {// 对arry执行map, 其中设置key为index是默认操作
                //一行的箭头函数包含了return操作
              todos.map((todo, index) => <li key={index}>{todo}</li>)
            }
          </ul>
        )
      }
    }
    //增加属性, 实际是增加到props下. 相当于增加接受数据的入口.
    TodoList.propTypes = {
      todos: PropTypes.array.isRequired
    }
```

```js
    // 渲染应用组件标签
    ReactDOM.render(<App />, document.getElementById('example'))
```

