---
title: GundamFans V1.2
category: Application
tag: [Full Stack,nodejs,express,ejs,mongoDB,Bootstrap] 
thumbnail: /img/gfv1-2.png
---

> 更新:
>
> Comment模块设计: Data Association in Mongoose, 为了安全性使用expressSanitizer
>
> Code Refactoring: 划分module, RESTful routing, seedDB()函数生成初试测试数据

<!--more-->

# RESTful routes

RESTful routes 是一种约定俗成的router定义方式, 对应CURD(Create, Update, Read, Delete)的需求, 设计方式如下:

| HTTP VERB | ROUTE                | Action          | Used For                                              |
| --------- | -------------------- | --------------- | ----------------------------------------------------- |
| GET       | '/wiki'          | index action    | index page to display all gundam info          |
| GET       | '/wiki/new'      | new action      | displays create gundam info form              |
| POST      | '/wiki'          | create action   | creates one gundam                             |
| GET       | '/wiki/:id'      | show action     | displays one gundambased on ID in the url     |
| GET       | '/wiki/:id/edit' | edit action     | displays edit form based on ID in the url             |
| PUT       | '/wiki/:id'      | update action   | *replaces* an existing article based on ID in the url |
| DELETE    | '/wiki/:id'      | delete action   | deletes one article based on ID in the url            |
| *PATCH*   | *'/wiki/:id'*    | *update action* | *modifies an existing article based on ID in the url* |

# Comment Module

### 模式设计

```js
let gundamMSSchema = new mongoose.Schema({
    name: String,
    img: String,
    description: {type:String, default:"Not available"},
    comments: [{ type: mongoose.Schema.Types.ObjectId, ref: 'comment' }] //一个机体可以有多条评价,“`ref`”告知模式分配哪个模型给该字段。
});
let commentSchema = new mongoose.Schema({
    author: String,
    text: String,
});
```

读入数据时, 使用populate方法将 id 替换为实际数据:

```js
gundamMS.findById(req.params.id) //搜索结果为json的集合
            .populate('comments')// 使用作者 id 填充实际作者信息
            .exec(function(err,findms){//exec使用回调函数
                if(err){
                    console.log(err);
                } else {
                    console.log(findms);
                    res.render("show",{ms:findms});
                }
            });
//使用时comments对象由id的数组,变为结构体的数组
```

### 安全性expressSanitizer

为了使评论格式多样, 在show页面的ejs中, 使用`<%-  %>`进行显示输出, 为了避免用户输入`<script></script>`导致执行恶意代码, 使用expressSanitized:

```js
npm i express-sanitized

let expressSanitized = require("express-sanitized");
app.use(bodyparser.urlencoded({extended:true}));
app.use(expressSanitized());//bodyparser之后

//<script>alert("you have been hacked")</script>
//<%- %>读取输入时, 对方可以输入<script 代码进行攻击. sanitize消毒消除script部分
```

### data association in mongoose知识补充

模式声明时, 属性可以包含其他模式.

```js
//模式定义
const schema = new Schema(
{
  name: String,
  binary: Buffer,
  living: Boolean,
  updated: { type: Date, default: Date.now },
  age: { type: Number, min: 18, max: 65, required: true },
  mixed: Schema.Types.Mixed,
  _someId: Schema.Types.ObjectId, //其他模式
  array: [],
  ofString: [String], // 其他类型也可使用数组
  nested: { stuff: { type: String, lowercase: true, trim: true } }
})
```

`ObjectId`：表示数据库中某一模型的特定实例。例如，一本书可能会使用它来表示其作者对象。它实际只包含指定对象的唯一 ID（`_id`） 。可以使用 `populate()` 方法在需要时提取相关信息。

# Code Refactoring

将schema分到单独的文件, 使用`module.exports =..`来设置出口变量. 

同时设计seedDB()函数为数据库增加原始数据. **注意** :为了保证操作的运行次序, 必须把存在先后次序的操作放在call back中.

```js
    function seedDB(){
        //remove all
        gundamMS.remove({},function(err){
            if(err){
                console.log(err);
            } else {
                console.log("removed all MS");
                //add MS
                data.forEach(function(e){
                    gundamMS.create(e,function(err,newms){
                        if(err){
                            console.log(err);
                        } else {
                            console.log(e);
                            comment.create({
                                author: "TOM",
                                text: "I love this gundam!!!!!"
                            }, function(err,newcomment){
                                if(err){
                                    console.log(err);
                                } else{
                                    newms.comments.push(newcomment);
                                    newms.save();
                                }
                            }
                            )
                        }
                    })
                })
                }
        });
    }
```

