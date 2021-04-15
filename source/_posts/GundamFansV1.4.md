---
title: GundamFans V1.4
category: Application
tag: [Full Stack,nodejs,express,ejs,mongoDB,Bootstrap] 
thumbnail: /img/gfv1-3.png
---

> 更新:
>
> route refactoring: 将各类别route封装在不同模块中
>
> data association: 增加user, comment 数据关联

准备秋招, 未完待续...

<!--more-->

## route refactoring

```js
let express = require("express");
let router = express.Router(); //使用router替代app
//...
module.exports = router;
```

```js
commentRouter = require("./routes/comment"),
app.use(commentRouter);
```

注意require时的路径问题!

## 用户权限设计

只有登录状态可以进行:

* 增加评论(自动记录评论用户名)
* 增加新的机体信息

非登录状态可以进行:

* 浏览机体信息

设计思路: 对get&post route均进行限制, 仅响应登录状态的request. 

## 增加user 与 comment 的数据关联

修改设计comment model 中的author属性:

```js
author: {
        id:{ type: mongoose.Schema.Types.ObjectId, ref: 'User' }, //对应User model的唯一标志_id
        username: String, //由于经常使用,所以专门提取出来
    },
```

修改在route中create comment的步骤:

```js
let newcomment = {
        text: req.body.comText,
        author:{id:req.user._id, username: req.user.username } 
    }
comment.create(newcomment,...
```

