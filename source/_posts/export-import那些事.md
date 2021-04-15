---
title: export-import那些事
category: react
tag: [react]
---

## 1. export default

```js
export default class App extends Component {
    render (){ ...
```

```js
import App from './component/app'; //其实App不需要和export一直,import本身就默认是App导入了,此处只是自定一个引用名
```

<!--more--> 

## 2. export

```js
export function counter(state =0, action){
...
```

```js
import {INCREASE,DECREASE,INCREASASYCN} from "./action-types";
```



## 3. import * as

```js
export function increaseCreator(num){
...
```

```js
import * as actionCreator from '../redux/action-creator'
actionCreator.increaseCreator(num)
```

