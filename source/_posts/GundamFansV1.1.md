---
title: GundamFans V1.1
category: Application
tag: [Full Stack,nodejs,express,ejs,mongoDB,Bootstrap] 
thumbnail: /img/gf2.png
---

> GundamFans意在建立一个高达动漫同好分享网站, 分享自己喜爱的高达机体.
>
> 后端主体为nodeJS的express框架, 数据库采用mongoDB, 使用mongoose库进行操作. 前端部分使用ejs来编写网页, 使用bootstrap进行装饰, 具有较好的整合性.

<!--more-->

# 主体文件

文件树结构:

> D:.
> │   index.js
> │   package-lock.json
> │   package.json
>
> ├───public //需要app.use(express.static("public"));指示express来serve public目录
> │       global.css //全局css设置
> │       
> └───views //express 默认文件夹
>     │   index.ejs
>     │   landing.ejs
>     │   newms.ejs
>     │   show.ejs
>     │   
>     └───partials //公用代码
>             header.ejs
>             tail.ejs
>
> ├───node_modules(略)
>
> 

# 关键点

* 框架与库的区别

* <%- include("partials/header") %>实现头尾部ejs代码公用

* post指令无法直接解析req.body.param, 需要body-parser库

* ejs中<% %>为逻辑代码不显示, <%= %>为显示代码, <%- %>显示且作为html运行?

* <%- %>读取输入时, 对方可以输入<script 代码进行攻击. sanitize消毒

  

# 代码逻辑

首先在本地开启mongoDB, `mongod`

npm库依赖:

```js
const { render } = require("ejs");

let express = require("express"),
    app = express(),
    bodyparser = require("body-parser");
    mongoose = require("mongoose");
```

相关设置与端口监听:

```js
//指示express来serve public目录
app.use(express.static("public"));
// 设置使得body自动被parse,可以直接使用req.body.name
app.use(bodyparser.urlencoded({extended:true}));
//默认使用ejs解析views内容
app.set("view engine","ejs");
app.listen(3000,function(){
    console.log("gundam runnig");
})
```

连接mongoDB, `mongoose.connect("mongodb://localhost:27017/gundam_fans",{useUnifiedTopology: true,useNewUrlParser: true });`

构建机体视图schema, 构建模型变量.

```js
let gundamMSSchema = new mongoose.Schema({
    name: String,
    img: String,
    description: String
});
let gundamMS = mongoose.model("gundamMS",gundamMSSchema);
```

### Router设计:

| url       | HTTP verb | purpose                                                 | 对应ejs |
| --------- | --------- | ------------------------------------------------------- | ------- |
| /         | get       | 加载Landing Page                                        | landing |
| /wiki     | get       | 展示所有机体信息                                        | index   |
| /wiki     | post      | 根据页面body表单信息创建机体, redirect("/wiki")         | -       |
| /wiki/new | get       | 加载机体创建表格, < form action="/wiki" method="POST" > | newms   |
| /wiki/:id | get       | 加载id对应机体详情页                                    | show    |

### 具体实现:

* /wiki get 实现

```js
app.get("/wiki",function(req,res){
    //获取所有ms信息
    //find()
    gundamMS.find({},function(err,mslist){
        if(err){
            console.log(err);
        } else {
            res.render("index",{mslist: mslist});
        }
    })
})
```

搜索结果mslist, 传入index页, 取出单个结构即为gundamMSSchema, 多了一个"_id"属性.

e.name等属性直接显示, e_id作为参数传入链接MoreInfo. ` a href="/wiki/<%= e._id%>" `

* 通过点击MoreInfo, 进入机体详情页:

```js
//显示更多机体信息
app.get("/wiki/:id",function(req,res){
    //index中的btn link包含_id,利用_id去获取description,(req.params.id获取域名中的参数)
    gundamMS.findById(req.params.id,function(err,findms){
        if(err){
            console.log(err);
        } else {
            res.render("show",{ms:findms});
        }
    })
    
})
```

对于url中带有的参数`:id`使用 `req.params.id`进行访问.

* 新建机体信息的设置:

首先是在`wiki/new`下render`newms`页面, 在该页面下提供form. 该表格将信息以POST报文的形式传给`/wiki`页面.

```js
<form action="/wiki" method="POST" >
                <div class="form-group">
                    <input class="form-control" type="text" name="msname" placeholder="MS name">
                </div>
                <div class="form-group">
                    <input class="form-control" type="text" name="msimg" placeholder="MS picture url">
                </div>
                <div class="form-group">
                    <input class="form-control" type="text" name="description" placeholder="MS description">
                </div>
                <div class="form-group">
                    <button type="submit" class="btn btn-success btn-block">GO!</button>
                </div>               
</form>
```

`/wiki`对POST报文进行响应:

```js
app.post("/wiki",function(req,res){

    let newms = {
        name:req.body.msname,
        img:req.body.msimg,
        description:req.body.description,
    }
    gundamMS.create(newms,function(err,newlycreated){
        if(err){
            console.log(err);
        } else {
            res.redirect("/wiki");
        }
    })
    
})
```

其中注意页面信息由req.body.msname获取, 注意此处需要提前引入bodyparser依赖:

```js
bodyparser = require("body-parser");
// 设置使得body自动被parse,可以直接使用req.body.name
app.use(bodyparser.urlencoded({extended:true}));
```

### MongoDB使用整理:

首先在本地开启mongoDB, `mongod`

连接mongoDB, `mongoose.connect("mongodb://localhost:27017/gundam_fans",{useUnifiedTopology: true,useNewUrlParser: true });`

构建机体视图schema, 构建模型变量.

```js
let gundamMSSchema = new mongoose.Schema({
    name: String,
    img: String,
    description: String
});
// 使用模式“编译”模型
let gundamMS = mongoose.model("gundamMS",gundamMSSchema);
```

第一个参数是为模型所创建集合的别名（Mongoose 将为 SomeModel 模型创建数据库集合），第二个参数是创建模型时使用的模式。

### 创建和修改文档

插入新数据操作:

```js
gundamMS.create(
    {name:"ASW-G-08 Gundam Barbatos",
     img:"...",
     description:"..."
    },function(err,ms){
    if(err){
        console.log(err);
    }
    else{
        console.log(ms);
    }
});
```

或使用save():

```js
// 创建一个 SomeModel 模型的实例
const awesome_instance = new SomeModel({ name: '牛人' });

// 传递回调以保存这个新建的模型实例
awesome_instance.save( function (err) {
  if (err) {
    return handleError(err);
  } // 已保存
});
```

可以使用“圆点”加字段名来访问、修改新记录中的字段。修改后必须调用 `save()` 或 `update()` 以将改动保存回数据库。

```js
// 使用圆点来访问模型的字段值
console.log(awesome_instance.name); // 控制台将显示 '也是牛人'

// 修改字段内容并调用 save() 以修改记录
awesome_instance.name = "酷毙了的牛人";
awesome_instance.save( function(err) {
   if (err) {
     return handleError(err);
   } // 已保存
});
```

### 搜索纪录

- `findById()`：用指定 `id` 查找文档（每个文档都有一个唯一 `id`）。
- `findOne()`：查找与指定条件匹配的第一个文档。
- `findByIdAndRemove()`、`findByIdAndUpdate()`、`findOneAndRemove()`、 `findOneAndUpdate()`：通过 `id` 或条件查找单个文档，并进行更新或删除。以上是更新和删除记录的便利函数。

查找获取list of elements:

```js
gundamMS.find({},function(err,mslist){
        if(err){
            console.log(err);
        } else {
            console.log(mslist);
        }
    })
```

查找获取单个element:

```js
gundamMS.findById(req.params.id,function(err,findms){
    if(err){
        console.log(err);
    } else {
        res.render("show",{ms:findms});
    }
})
```

进阶: 以下代码展示了如何在数据库中找到所有网球运动员，并返回运动员姓名和年龄字段。

```js
const Athlete = mongoose.model('Athlete', yourSchema);

// SELECT name, age FROM Athlete WHERE sport='Tennis'
Athlete.find(
  { 'sport': 'Tennis' },
  'name age',
  function (err, athletes) {
    if (err) {
      return handleError(err);
    } // 'athletes' 中保存一个符合条件的运动员的列表
  }
);
```

若像上述代码那样指定回调，则查询将立即执行。搜索完成后将调用回调。

若未指定回调，则 API 将返回 [Query](http://mongoosejs.com/docs/api.html#query-js) 类型的变量。可以使用该查询对象来构建查询，随后使用 `exec()` 方法执行（使用回调）。

```js
// 寻找所有网球运动员
const query = Athlete.find({ 'sport': 'Tennis' });

// 查找 name, age 两个字段
query.select('name age');

// 只查找前 5 条记录
query.limit(5);

// 按年龄排序
query.sort({ age: -1 });

// 以后某个时间运行该查询
query.exec(function (err, athletes) {
  if (err) {
    return handleError(err);
  } // athletes 中保存网球运动员列表，按年龄排序，共 5 条记录
})

//等价于
Athlete.
  find().
  where('sport').equals('Tennis').
  where('age').gt(17).lt(50).  // 附加 WHERE 查询
  limit(5).
  sort({ age: -1 }).
  select('name age').
  exec(callback); // 回调函数的名字是 callback
```

查询还能做更多。请参阅 [查询](http://mongoosejs.com/docs/queries.html)（Mongoose 英文文档）

### 公用html代码

header部分

```html
<!DOCTYPE html>
<html>
    <head>
        <title>I'm Gundam!!!</title>
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css">
        <link rel="stylesheet" href="/global.css">
    </head>
	<!-- 设计navbar -->
    <body>
```

tail部分

```js
<p class="text-center">Copyright: XinyuanCai.07/2020</p>
</body>
</html>
```

### 升级

req.body.blog

blog[name]

blog[img]

npm i express-sanitizer

```js
form action: /xx/_id?method=PUT method="POST"

let expressSanitizer = require("express-sanitizer");
let methodOverride = require("method-override");

//after bodyparser
app.use(expressSanitizer())
app.use(methodOverride("_method"))

// 博客的content 我们支持html格式,则...
//<%- %>读取输入时, 对方可以输入<script 代码进行攻击. sanitize消毒消除script部分

```

