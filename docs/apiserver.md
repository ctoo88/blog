## NodeJS+Express+MongoDB开发api服务器

2017/10/20

### 关键字

>[NodeJS](https://nodejs.org/en/) [Express](http://expressjs.com/) [MongoDB](https://www.mongodb.com/what-is-mongodb)

### 概述

之前用NodeJS搭建前端工作流，已经实现了本地静态服务器和代理服务器。配置代理服务器的目的就是为了调用其他服务器上的api接口，这次要自己实现一个api服务器。

### 准备工作

通过应用生成器工具 express 可以快速创建一个应用的骨架。

安装express：

    npm install express-generator -g

初始化项目：

    $ express myserver
    
      create : myserver
      create : myserver/package.json
      create : myserver/app.js
      create : myserver/public
      create : myserver/views
      create : myserver/views/index.jade
      create : myserver/views/layout.jade
      create : myserver/views/error.jade
      create : myserver/routes
      create : myserver/routes/index.js
      create : myserver/routes/users.js
      create : myserver/bin
      create : myserver/bin/www
      create : myserver/public/javascripts
      create : myserver/public/images
      create : myserver/public/stylesheets
      create : myserver/public/stylesheets/style.css
    
        install dependencies:
        $ cd myserver && npm install
    
        run the app:
        $ DEBUG=myserver:* npm start

启动服务器：

    $ DEBUG=myserver:* npm start
    
    > myserver@0.0.0 start /home/xu/www/myserver
    > node ./bin/www
    
      myserver:server Listening on port 3000 +0ms
    
启动成功了，在写api接口前，先看下app.js这个文件

    const express = require('express');
    const path = require('path');
    const favicon = require('serve-favicon');
    const logger = require('morgan');
    const cookieParser = require('cookie-parser');
    const bodyParser = require('body-parser');
    
    const index = require('./routes/index'); // 引入首页路由
    const users = require('./routes/users'); // 引入用户接口路由

    const app = express();

    // view engine setup
    app.set('views', path.join(__dirname, 'views'));
    app.set('view engine', 'jade'); // 模板引起

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'));
    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: false }));
    app.use(cookieParser());
    app.use(express.static(path.join(__dirname, 'public')));

    app.use('/', index); // 使用首页路由
    app.use('/users', users); // 使用用户路由

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      let err = new Error('Not Found');
      err.status = 404;
      next(err);
    });

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message;
      res.locals.error = req.app.get('env') === 'development' ? err : {};

      // render the error page
      res.status(err.status || 500);
      res.render('error');
    });

    module.exports = app;

再看下'./routes/index.js'和'./routes/users.js'两个文件

index.js

    /* GET home page. */
    router.get('/', function(req, res, next) {
      res.render('index', { title: 'Express' }); // 用到了'./views/index.jade'模板
    });

users.js

    /* GET users listing. */
    router.get('/', function(req, res, next) {
      res.send('user api');
    });

原理就是通过路由来判断用户请求，并给出响应。

### 第一个api接口

为了方便测试先用get请求。

需求：传入一个正确的id,返回对应的用户信息。

users.js

    const express = require('express');
    const router = express.Router();
    const URL = require('url'); // 获取url参数，使用方法可查看NodeJS官方文档

    /* GET users listing. */
    router.get('/', function(req, res, next) {
      res.send('user api');
    });

    router.get('/getUserInfo', function(req, res, next) {
      let user = {};
      let response = {};
      let params = URL.parse(req.url, true).query;

      if(params.id == '1') {
        user.name = "Prosth";
        user.age = "12";
        user.city = "杭州市";
        response.status = 1;
        response.data = user;
      }else{    
        response.status = 1001;
        response.msg = 'User session ID does not exist';
      }

      res.send(JSON.stringify(response));
    });

    module.exports = router;

测试：

    http://127.0.0.1:3000/users/getUserInfo
    http://127.0.0.1:3000/users/getUserInfo?id=0

    {"status":1001,"msg":"User session ID does not exist"}

    http://127.0.0.1:3000/users/getUserInfo?id=1

    {"status":1,"data":{"name":"Prosth","age":"12","city":"杭州市"}}

### MongoDB数据库

*未完待续*
