---
title: GundamFans V1.3
category: Application
tag: [Full Stack,nodejs,express,ejs,mongoDB,Bootstrap] 
thumbnail: /img/gfv1-3.png
---

> 更新:
>
> 增加authentication

<!--more-->

## 依赖库安装

```
npm i passport
npm i passport-local
npm i passport-local-mongoose
```

## UserSchema设计

```
let mongoose = require("mongoose");
let passportLocalMongoose = require("passport-local-mongoose");

let UserSchema = new mongoose.Schema({
    username: String,
    password: String
});

//为User植入authentication各种方法
UserSchema.plugin(passportLocalMongoose);

module.exports = mongoose.model("User",UserSchema);
```

值得注意的是, UserSchema中的属性名称必须为username和password

## passport config

```
app.use(require("express-session")({
    secret: "My favorite gundam is exia!",
    resave: false,
    saveUninitialized: false,
}));
app.use(passport.initialize());
app.use(passport.session());
passport.use(new LocalStrategy(User.authenticate()));
passport.serializeUser(User.serializeUser());
passport.deserializeUser(User.deserializeUser());
```

## Routes设计

**register设计:**

```
//===============
//AUTH ROUTES
//===============
//show register form
app.get("/register",function(req,res){
    res.render("register");
})
//handle sign up request
app.post("/register",function(req,res){
    let newUser = new User({username: req.body.username}); //form与User中必须定义为username
    User.register(newUser, req.body.password, function(err, user){
        if(err){
            console.log(err);
            res.render("register");
        } else {
            //验证登录
            passport.authenticate("local",
            { 
                successRedirect: '/wiki',
                failureRedirect: '/register', 
            })(req, res, function(){
            })
        }
    })
})
```

在 register form中设计表项name 也必须为”username” “password”对应.

**login设计:**

```
//show login form
app.get("/login",function(req,res){
    res.render("login");
})

//handle login request
//app.post("login", middleware, callback)
app.post("/login",passport.authenticate("local",
    {
        successRedirect: '/wiki',
        failureRedirect: '/login', 
    }), function(req,res){
})
```

处于middleware的函数会被立即执行. passport.authenticate直接根据login form中的username 和 password 判断能否登录.

## 加密页面设置

设计函数isLoggedIn()如下:

```
function isLoggedIn(req,res,next){
    if(req.isAuthenticated()){
        return next();//继续执行后续代码(下一个函数)
    }
    res.redirect("/login");
}
```

将该函数当做middleware function传入需要登录的页面对应的routes, 即可实现进入该页面前验证登录状态功能. 如: 需要用户登录之后才可以使用评论功能:

```
app.get("/wiki/:id/comment/new",isLoggedIn ,function(req,res){
```

## 页面元素响应用户状态

为了实现页面根据用户状态不同而现实不同内容, 则需要将user信息传入对应route, 在express route中天然req.user即是用户信息结构体, 包含username, _id信息. 可以以参数形式传入:

```
res.render("index",{User: req.user});
```

对于实现navbar随着用户状态而选择显示”Login”或”Logout”的功能, 需要在每个页面中都传入req.user数据. 为了简便, 可以使用app.use()功能, 该功能将参数函数当做middleware, 在每个route执行前运行, res.locals.currentUser设置res本地变量供ejs直接使用.

```
app.use(function(req,res,next){
    res.locals.currentUser = req.user;
    next();
});
```

### 易错点

- 定义UserSchema时属性必须为username, password.
- ejs的form中, 对应input的name也必须为username, password.
- bootstrap 4 的navbar需要靠右排列时, 使用`.ml-auto`类.
- navbar toggle dont work -> include JQuery!!!

