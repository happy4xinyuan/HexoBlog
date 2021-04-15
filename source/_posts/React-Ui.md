---
title: react-UI(Ant Design为主)
category: react
tag: [react]
---

###  material-ui(国外)

1)         官网: http://www.material-ui.com/#/

###  ant-design(国内蚂蚁金服)

1)         PC官网: https://ant.design/index-cn

2)     移动官网: https://mobile.ant.design/index-cn

<!--more-->

## ant-design基本使用

### 1. 安装[#](https://mobile.ant.design/docs/react/introduce-cn#2.-安装)

```bash
$ npm install antd-mobile --save
```

### 2. 使用[#](https://mobile.ant.design/docs/react/introduce-cn#3.-使用)

入口页面 (html 或 模板) 相关设置：

> 引入 [FastClick](https://github.com/ftlabs/fastclick) 并且设置 html `meta` (更多参考 [#576](https://github.com/ant-design/ant-design-mobile/issues/576))
>
> 引入 Promise 的 fallback 支持 (部分安卓手机不支持 Promise)

```html
<!DOCTYPE html>
<html>
<head>
  <!-- set `maximum-scale` for some compatibility issues -->
  <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, minimum-scale=1, user-scalable=no" />
  <script src="https://as.alipayobjects.com/g/component/fastclick/1.0.6/fastclick.js"></script>
  <script>
    if ('addEventListener' in document) {
      document.addEventListener('DOMContentLoaded', function() {
        FastClick.attach(document.body);
      }, false);
    }
    if(!window.Promise) {
      document.writeln('<script src="https://as.alipayobjects.com/g/component/es6-promise/3.2.2/es6-promise.min.js"'+'>'+'<'+'/'+'script>');
    }
  </script>
</head>
<body></body>
</html>
```

组件使用实例：

```jsx
import { Button } from 'antd-mobile';
ReactDOM.render(<Button>Start</Button>, mountNode);
```

引入样式：

```jsx
import 'antd-mobile/dist/antd-mobile.css';  // or 'antd-mobile/dist/antd-mobile.less'
```



## 按需加载基优化

1)     下载依赖包

npm i react-app-rewired --save-dev //表示只有编译时用到

npm i babel-plugin-import --save --dev



2)     修改默认配置: 

l package.json 修改编译指令

```js
"scripts": {
  "start": "react-app-rewired start",
  "build": "react-app-rewired build",
  "test": "react-app-rewired test --env=jsdom"
}
```

 

l config-overrides.js 增加该文件, 设置全局配置(该文件由react-app-rewired start指派)

```js
const {injectBabelPlugin} = require('react-app-rewired');
module.exports = function override(config, env) {
    
  config = injectBabelPlugin(['import', {libraryName: 'antd-mobile', style: 'css'}], config); 
    //该代码注入Babel-plugin-import插件, 第二参数为插件参数,针对antd-mobile库进行按需加载. 同时顺带加载css样式
  return config;
};
```

 

3)     编码 无需再导入css

```js
// import 'antd-mobile/dist/antd-mobile.css'

// import Button from 'antd-mobile/lib/button'
// import Toast from 'antd-mobile/lib/toast'
import {Button, Toast} from 'antd-mobile'

```