---
title: 关于React VDOM的一些理解
category: React
tag: [React]
thumbnail: /img/JS(1).png
---

### JSX和createElement

我们在实现一个`React`组件时可以选择两种编码方式，第一种是使用`JSX`编写：

```
class Hello extends Component {
  render() {
    return <div>Hello ConardLi</div>;
  }
}
```

第二种是直接使用`React.createElement`编写：

```
class Hello extends Component {
  render() {
    return React.createElement('div', null, `Hello ConardLi`);
  }
}
```

实际上，上面两种写法是等价的，`JSX`只是为 `React.createElement(component, props, ...children) `方法提供的语法糖。也就是说所有的`JSX `代码最后都会转换成`React.createElement(...) `，`Babel`帮助我们完成了这个转换的过程。

* **为什么React自定义组件需要大写首字母?**  注意，`babel`在编译时会判断`JSX`中组件的首字母，当首字母为小写时，其被认定为原生`DOM`标签，`createElement`的第一个变量被编译为字符串；当首字母为大写时，其被认定为自定义组件，`createElement`的第一个变量被编译为对象；



