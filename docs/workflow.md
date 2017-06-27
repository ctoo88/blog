## 原生Node.js搭建前端开发工作流（Vue + Browserify）

2017/06/27

### 关键字

>[Node.js](https://nodejs.org/en/) [Express](http://expressjs.com/) [body-parser](https://github.com/expressjs/body-parser) [Browserify](http://browserify.org/) [Vue](https://cn.vuejs.org/) [vueify](https://github.com/vuejs/vueify) [watchify](https://github.com/substack/watchify) [envify](https://github.com/hughsk/envify) [UglifyJS2](https://github.com/mishoo/UglifyJS2) [clean-css](https://github.com/jakubpawlowicz/clean-css)

### 概述

主要实现功能：

* 本地服务器（Node.js、Express）
* 代理服务器（Node.js、Express、body-parser）
* 模块化开发（Browserify、Vue、vueify）
* 开发环境下热更新（watchify）
* 生产环境下js/css代码压缩（envify、UglifyJS2、clean-css）

### 工程介绍

一个简单的工程目录：

    ├─node_modules/
    ├─public/ 服务器指定的静态目录
    │  ├─dist/ 编译打包后的文件
    │  │  ├─bundle.js
    │  │  ├─bundle.min.js
    │  │  └─bundle.min.css
    │  ├─img/
    │  │  └─logo.png
    │  └─index.html
    ├─src/
    │  ├─App.vue
    │  └─index.js
    ├─cfg.build.js 生产配置文件
    ├─cfg.dev.js 开发配置文件
    ├─package.json
    └─yarn.lock

生成package.json文件：

    npm init

安装依赖包：

    npm install envify -g
    npm install UglifyJS2 -g
    yarn add express
    yarn add body-parser
    yarn add vue
    yarn add browserify --dev
    yarn add watchify --dev
    yarn add vueify --dev
    yarn add clean-css --dev

添加CLI命令后完整的package.json文件如下：

    {
      "name": "vue-demo",
      "version": "1.0.0",
      "description": "",
      "main": "index.js",
      "scripts": {
        "ugly": "set NODE_ENV=production&&envify ./public/dist/bundle.js | uglifyjs -c -m > ./public/dist/bundle.min.js", // 压缩命令
        "dev": "node cfg.dev.js", // 开发命令
        "build": "node cfg.build.js && npm run ugly" // 生产命令
      },
      "author": "ctoo",
      "license": "ISC",
      "devDependencies": {
        "browserify": "^14.3.0",
        "clean-css": "^4.1.4",
        "vueify": "^9.4.1",
        "watchify": "^3.9.0"
      },
      "dependencies": {
        "body-parser": "^1.17.2",
        "express": "^4.15.3",
        "vue": "^2.3.3"
      }
    }

#### 开发环境

cfg.dev.js文件：

    const fs = require("fs")
        , http = require('http')
        , queryString = require('queryString')
        , express = require('express')
        , bodyParser = require('body-parser') // 获取post请求的请求体
        , browserify = require('browserify')
        , vueify = require('vueify')
        , watchify = require('watchify');

    let b = browserify()
      , app = express();

    function hotBundle() {
      b.bundle().pipe(fs.createWriteStream('./public/dist/bundle.js'));
    }

    function postCall(req, res) { // 配置代理
      let postUrl = req.originalUrl
        , postData = queryString.stringify(req.body)
        , options = {
          hostname: 'www.xxx.com',
          port: 80,
          path: postUrl,
          method: 'POST',
          headers: {
            'Content-Type': 'application/x-www-form-urlencoded',
            'Content-Length': Buffer.byteLength(postData)
          }
        }
        , newReq = http.request(options, newRes => {
          let newData = "";

          newRes.setEncoding('utf8');
          newRes.on('data', chunk => {
            newData += chunk;
          });
          newRes.on('end', () => {
            res.send(newData);
          });
        });

      newReq.on('error', (e) => {
        res.end();
      });

      // write data to request body
      newReq.write(postData);
      newReq.end();
    }

    b.add('./src/index.js')
      .transform(vueify)
      .plugin(watchify)
      .on('update', hotBundle); // 热更新

    hotBundle();

    // web server
    app.use(express.static('public')); // 配置静态文件夹，里面的文件会被浏览器缓存
    app.use(bodyParser.json()); // for parsing application/json
    app.use(bodyParser.urlencoded({ extended: true })); // for parsing application/x-www-form-urlencoded

    // router
    app.post('/api/:reqUrl', postCall); // post请求

    app.listen(8818, () => {
      console.log('listening on port 8818!');
    });

#### 生产环境

cfg.build.js文件：

    const fs = require("fs")
        , browserify = require('browserify')
        , vueify = require('vueify')
        , vueify_extract_css = require('vueify/plugins/extract-css')
        , cleanCSS = require('clean-css');

    let b = browserify();

    function creatBundle() {
      b.bundle().pipe(fs.createWriteStream('./public/dist/bundle.js'));
    }

    function creatCSS(css_vue) {
      const miniObj = new cleanCSS().minify(css_vue);
	        , buf_vue = Buffer.from(miniObj.styles);

      fs.createWriteStream('./public/dist/bundle.min.css').end(buf_vue);
    }

    b.add('./src/index.js')
      .transform(vueify)
      .plugin(vueify_extract_css, { out: {write: creatCSS, end: function(){}} });

    creatBundle();

#### 自动化命令

    npm run dev // 开发命令
    npm run build // 生产命令