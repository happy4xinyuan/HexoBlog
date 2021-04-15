---
title: react-生命周期
category: react
tag: [react]
thumbnail: http://i.imgur.com/h5khD9F.png
---

1. 组件的三个生命周期状态:
	Mount：插入真实 DOM
	Update：被重新渲染
	Unmount：被移出真实 DOM

<!--more-->

1. 生命周期流程:
  * 第一次初始化显示: ReactDOM.render(<Xxx/>, containDom)
    	constructor()
        	componentWillMount() : 将要插入, 回调
        	render() : 用于插入虚拟DOM, 回调
        	componentDidMount() : 已经插入, 回调
  * 每次更新state: this.setState({})
      componentWillReceiveProps(): 接收父组件新的属性
      componentWillUpdate() : 将要更新回调
      render() : 更新(重新渲染)
      componentDidUpdate() : 已经更新回调
  * 删除组件: ReactDOM.unmountComponentAtNode(div): 移除组件
    	componentWillUnmount() : 组件将要被移除回调
2. 常用的方法
    render(): 必须重写, 返回一个自定义的虚拟DOM
      	constructor(): 初始化状态, 绑定this(可以箭头函数代替)
      	componentDidMount() : 只执行一次, 已经在dom树中, 适合启动/设置一些监听

  

![组件生命周期](http://i.imgur.com/h5khD9F.png)



```

```

