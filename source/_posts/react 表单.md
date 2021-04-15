---
title: react-表单
category: react
tag: [react]
---

包含表单的组件分类

a.     受控组件: 表单项输入数据能自动收集成状态

b.     非受控组件: 需要时才手动读取表单输入框中的数据

<!--more-->

下述代码中, `<input type="text" value={this.state.username} onChange={this.handleChange} />`采用受控组件, 自动将输入框的内容收集进入状态.

`<input type="password" ref={(input) => this.pwdInput = input} />`采用 非受控组件, 需要时才手动读取表单输入框中的数据. 必须点击submit后才提交

> React 的官方文档: 
>
> 在大多数情况下，我们**推荐使用 [受控组件](https://zh-hans.reactjs.org/docs/forms.html#controlled-components) 来处理表单数据**。在一个受控组件中，表单数据是由 React 组件来管理的。另一种替代方案是使用非受控组件，这时表单数据将交由 DOM 节点来处理。
>
> 如果你不介意代码美观性，并且希望**快速编写代码**，使用非受控组件往往可以减少你的代码量。否则，你应该使用受控组件。
>
> 使用受控组件更符合react的思想，少使用DOM

```react
  /*
  1. 问题: 在react应用中, 如何收集表单输入数据
  2. 包含表单的组件分类
    受控组件
    非受控组件
  */
  /*
  需求: 自定义包含表单的组件
    1. 界面如下所示
    2. 输入用户名密码后, 点击登陆提示输入信息
    3. 不提交表单
  */
  class LoginForm extends React.Component {
    constructor(props) {
      super(props)
      this.state = {
        username: ''
      }
      this.handleSubmit = this.handleSubmit.bind(this)
      this.handleChange = this.handleChange.bind(this)
    }

    handleChange(event) {
      //event表示当前事件, event.target表示触发该事件的元素
      this.setState({username: event.target.value})
    }

    handleSubmit(event) {
      alert(`准备提交的用户名为: ${this.state.username}, 密码:${this.pwdInput.value}`)

      // 阻止事件的默认行为: 提交表单
      event.preventDefault()
    }
    render () {

      return (
        //处理submit事件
        <form onSubmit={this.handleSubmit} action="/test">
          <label>
            用户名:
            <input type="text" value={this.state.username} onChange={this.handleChange} />
            //value绑定输入框与state中的数据, 只有添加onChange之后才能变化.
          </label>&nbsp;
          <label>
            密码:
            <input type="password" ref={(input) => this.pwdInput = input} />
          </label>&nbsp;
          <input type="submit" value="登陆" />
        </form>
      )
    }
  }
  
  ReactDOM.render(<LoginForm />, document.getElementById('example'))
```

