---
title: react-redux
category: react
tag: [react]
thumbnail: /img/09-06.jpg
---

## react-redux是什么

1)     一个**react插件库**

2)     专门用来**简化react应用中使用redux**

> 上一篇中我们已经利用redux实现了基本的通信需求. 然而为了简化其写法. 
>
> 如**读取数据** 采用`let state = this.props.store.getState();`每一次读取都需要调用getState
>
> **修改数据** 采用`this.props.store.dispatch({type:INCREASE,data: num })` 每一次修改都需要dispatch.
>
> 我们可以通过react-redux插件来用**更简单的写法**来**更加优雅地**实现上述功能.(以及解决多层传递问题)

<!--more--> 

## 思考一下react-redux到底干了什么?

### 首先让我们回忆一下redux在干什么?

redux本质是实现 **集中式管理**react应用中**多个组件共享的状态** , 是组件间通信的第三种方式. 通过redux中的store来存储状态与方法, 而不是将状态与方法存放在组件中. 

redux的思路: component只提供接口(props各属性), 通过各属性接受状态与方法.

redux实现步骤:

1. **redux部分:** 

   1. **定义状态, 状态变化规则 (各类reducer函数 )**, 根据state和action(type, data)返回新的state.

      目前暂且state的定义还是在传参的默认值设定.

      ```js
      export function counter(state =0, action){
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

   2. **根据reducer生成store**,`const store = createStore(counter);`

   3. **store传入所需component**, `<App store={store} />`
   
   4. store与render挂钩, **手动重绘** `store.subscribe(render)`

2. **component部分**
   1. 在props的store中**获取状态**, ` this.props.store.getState();`
   2. 在修改数据的**方法其实并没有真实地发送给component**, 而提供store的dispatch方法, 传入一个action, 进而在store中自己利用reducer来进行状态的改变. ( 而这也是为什么要**手动重绘**的核心 )
3. 其他辅助部分
   	1. action-types 定义action type, 将字符串封装成变量
    	2. action-creator 提供action生成函数

### 对比一下react-redux是如何解决上述问题的

​	React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。

UI 组件有以下几个特征。

> - 只负责 UI 的呈现，不带有任何业务逻辑
> - 没有状态（即不使用`this.state`这个变量）
> - 所有数据都由参数（`this.props`）提供
> - 不使用任何 Redux 的 API

容器组件的特征恰恰相反。

> - 负责管理数据和业务逻辑，不负责 UI 的呈现
> - 带有内部状态
> - 使用 Redux 的 API

其实和redux的解决思路的区别在于: redux是将整个store全部交给 presentational component, 因此其中会使用redux的API. 而react-redux则将store的解析交由connect函数处理.

1. **redux部分:** 

   1. **定义状态, 状态变化规则 (各类reducer函数 )**, 根据state和action(type, data)返回新的state.

      目前暂且state的定义还是在传参的默认值设定.

      ```js
      export function counter(state =0, action){
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

   2. **根据reducer生成store**,`const store = createStore(counter);`

      

   3. 不再是 **store传入所需component**, `<App store={store} />`

      变为 而是将store传给Provider组件

      ```js
      import {Provider} from 'react-redux';
      <Provider store={store}>
              <App  />
      </Provider>
      ```

   4. 不再是 store与render挂钩, **手动重绘** `store.subscribe(render)`

      变为 无需手动订阅, 在connect中自动订阅.

2. **component部分**

   1. UIcomponent需要的store中的**数据和方法都默认从props中获取**.` this.props.count` `this.props.increase(num)`

   2. 使用connect将store中的数据抽离交给UIcomponent.

      ```js
      export default connect(
          state =>({count: state}), //mapStateToProps,
          {increase: increaseCreator, decrease:dereaseCreator}//mapDispatchToProps
      )(Counter)// UI component
      ```

      **mapStateToProps**

      `mapStateToProps`是一个函数。建立一个从（外部的）`state`对象到（UI 组件的）`props`对象的映射关系。应该返回一个对象，里面的每一个键值对就是一个映射。

      `mapStateToProps`会**订阅 Store**，每当`state`更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染。

      `mapStateToProps`的第一个参数总是`state`对象，还可以使用第二个参数，代表容器组件的`props`对象, 用于获取组件的内部数据。

      `connect`方法可以省略`mapStateToProps`参数，那样的话，UI 组件就不会订阅Store，就是说 Store 的更新不会引起 UI 组件的更新。

      **mapDispatchToProps**

      `mapDispatchToProps`是`connect`函数的第二个参数，用来建立 UI 组件的参数到`store.dispatch`方法的映射。

      如果`mapDispatchToProps`是一个对象，它的每个键名也是对应 UI 组件的同名参数，键值应该是一个函数，会被当作 Action creator ，**返回的 Action 会由 Redux 自动发出**。对应本例子, 在UI component中调用对应 Action creator的同名参数, 其实`this.props.increase(num)`等价于纯redux版本中的`this.props.store.dispatch(actionCreator.increaseCreator(num))`即: 生成action后自动发出.

3. 其他辅助部分

    	1. action-types 定义action type, 将字符串封装成变量
        	2. action-creator 提供action生成函数

   

## react-redux优势

1. presentational component 中**不再包含redux API**, 很纯粹

2. **多级传递被解决!** 将`state`对象作为参数，传入容器组件。但是，这样做比较麻烦，尤其是容器组件可能在很深的层级，一级级将`state`传下去就很麻烦。`Provider`在根组件外面包了一层，这样一来，`App`的所有子组件就默认都可以拿到`state`了。

   原理: `React`组件的[`context`](https://facebook.github.io/react/docs/context.html)属性，请看源码。

   ```js
   class Provider extends Component {
     getChildContext() {
       return {
         store: this.props.store
       };
     }
     render() {
       return this.props.children;
     }
   }
   
   Provider.childContextTypes = {
     store: React.PropTypes.object
   }
   ```

   上面代码中，`store`放在了上下文对象`context`上面。然后，子组件就可以从`context`拿到`store`，代码大致如下。

   ```js
   class VisibleTodoList extends Component {
     componentDidMount() {
       const { store } = this.context;
       this.unsubscribe = store.subscribe(() =>
         this.forceUpdate()
       );
     }
   
     render() {
       const props = this.props;
       const { store } = this.context;
       const state = store.getState();
       // ...
     }
   }
   
   VisibleTodoList.contextTypes = {
     store: React.PropTypes.object
   }
   ```

   `React-Redux`自动生成的容器组件的代码，就类似上面这样，从而拿到`store`。



## 补充

使用`React-Router`的项目，与其他项目没有不同之处，也是使用`Provider`在`Router`外面包一层，毕竟`Provider`的唯一功能就是传入`store`对象。

> ```javascript
> const Root = ({ store }) => (
>   <Provider store={store}>
>     <Router>
>       <Route path="/" component={App} />
>     </Router>
>   </Provider>
> );
> ```



参考文章:[Redux 入门教程（三）：React-Redux 的用法](https://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_three_react-redux.html)