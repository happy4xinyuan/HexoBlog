---
title: 实现github用户搜索页面
category: react
tag: [react]
---

几个重要实现点:

* cousin元素之间传递数据麻烦 -> 通过父元素作为中介
* 数据在哪个组件被使用, 被修改 -> 优先放在该组件中
* 假如中途触发请求 -> 在页面数据变化(如搜索关键词)发送请求 -> 在componentWillReceiveProps中使用axios发送异步ajax请求

<!--more-->

```js
    //接受新的属性时,回调
    componentWillReceiveProps(nextProps, nextContext) {
        const {SearchName} = nextProps;
        //更新状态
        this.setState({
            initView: false,
            loading: true,
        })
        //发送ajax请求
        const url = `https://api.github.com/search/users?q=${SearchName}`;
        axios.get(url)
            .then(res => {
                //请求成功,处理
                console.log(res);
                const data = res.data;
                const users = data.items.map(e => (
                        {
                            url: e.html_url,
                            avatarUrl: e.avatar_url,
                            name: e.login
                        }
                    )
                );
                this.setState({
                    loading: false,
                    users: users
                });
            })
            .catch(error => {
                this.setState({
                    initView: true,
                    errorMsg: error,
                })
            });
    }
```

