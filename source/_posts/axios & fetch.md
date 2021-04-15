---
title: axios & fetch
category: react
tag: [react]
---

##  理解
根据需求, 如"一开始就请求信息, 利用信息绘制网页" -> 在页面数据初始化时就发送请求 -> 在componentDidMount中使用axios发送异步ajax请求

假如中途触发请求 -> 在页面数据变化(如搜索关键词)发送请求 -> 在componentWillReceiveProps中使用axios发送异步ajax请求

<!--more-->

### 4.1.1. 前置说明

1) React 本身只关注于界面, 并不包含发送 ajax 请求的代码 

2) 前端应用需要通过 ajax 请求与后台进行交互(json 数据) 

3) react 应用中需要集成第三方 ajax 库(或自己封装)

### 4.1.2. 常用的 ajax 请求库
1) jQuery: 比较重, 如果需要另外引入不建议使用 

2) axios: 轻量级, 建议使用 

  a. 封装 XmlHttpRequest 对象的 ajax 

  b. promise 风格 

  c. 可以用在浏览器端和 node 服务器端 

3) fetch: 原生函数, 但老版本浏览器不支持 

  a. 不再使用 XmlHttpRequest 对象提交 ajax 请求

  b. 为了兼容低版本的浏览器, 可以引入兼容库 fetch.js

## axios
### 4.2.1. 文档
https://github.com/axios/axios

### 4.2.2. 相关 API

1) GET 请求

```js
axios.get('/user?ID=12345') 
    .then(function(response){ 
    	console.log(response); }) 
    .catch(function(error){ 
    	console.log(error); });
//注意response是一个对象,response.data中包含我们所需数据
//确实, 应该先通过输出调试,来观察其结构,然后确定接口
axios.get('/user',{ params:{ ID:12345 } }) 
    	.then(function(response){ 
    console.log(response); }) 
    	.catch(function(error){ console.log(error); });
```

2) POST 请求

```js
axios.post('/user',{ firstName:'Fred', lastName:'Flintstone' }) 			.then(function(response){ console.log(response); }) 					.catch(function(error){ console.log(error); });
```

## 4.3.Fetch
### 4.3.1. 文档
1) https://github.github.io/fetch/ 

2) https://segmentfault.com/a/1190000003810652

### 4.3.2. 相关 API
1) GET 请求 

```js
fetch(url).then(function(response){ 
    return  response.json() }) //多了一步操作
    .then(function(data){ console.log(data) })
    .catch(function(e){ console.log(e) });
```

2) POST 请求

```js
fetch(url,{ method:"POST", body:JSON.stringify(data), })
    .then(function(data){ console.log(data)})
    .catch(function(e){ console.log(e) })
```

