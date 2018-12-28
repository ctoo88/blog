## 前端打包工具Browserify与Rollup简单比较

2018/05/17

### 关键字

>[Browserify](http://browserify.org/) [Rollup](https://rollupjs.org/)

### 概述

前端打包工具很多，如webpack非常适用于开发复杂单页应用。不过平时写写小项目个人还是喜欢比较轻量的工具如Browserify、Rollup等。然而两种工具也有很大的不同：

### 准备工作

简单的目录结构：

    ├─node_modules/
    ├─src/
    │  ├─dist/ 编译打包后的文件
    │  │  └─bundle.js
    │  ├─sum.js
    │  └─index.js
    ├─cfg.js 配置文件
    └─package.json

sum.js文件：

    export default (a,b) => a + b;

index.js文件：

    import sum from './sum.js';
    console.log(sum(8,8));

接下来分别用Browserify和Rollup打包成bundle.js文件

### Browserify

Browserify实现了在浏览器端方便使用模块功能（注：默认不支持es6模块语法，需要安装babel，最新安装方法参照[官网](http://babeljs.io/docs/setup/#installation)）

cfg.js文件：

    const fs = require("fs");
    const browserify = require('browserify');
    const babelify = require("babelify");
    let b = browserify();

    b.transform(babelify);
    b.add('./src/index.js');
    b.bundle().pipe(fs.createWriteStream('./src/dist/bundle.js'));

CLI

    node cfg.js

打包成的bundle.js文件：

    (function(){function r(e,n,t){function o(i,f){if(!n[i]){if(!e[i]){var c="function"==typeof require&&require;if(!f&&c)return c(i,!0);if(u)return u(i,!0);var a=new Error("Cannot find module '"+i+"'");throw a.code="MODULE_NOT_FOUND",a}var p=n[i]={exports:{}};e[i][0].call(p.exports,function(r){var n=e[i][1][r];return o(n||r)},p,p.exports,r,e,n,t)}return n[i].exports}for(var u="function"==typeof require&&require,i=0;i<t.length;i++)o(t[i]);return o}return r})()({1:[function(require,module,exports){
    'use strict';

    var _sum = require('./sum.js');

    var _sum2 = _interopRequireDefault(_sum);

    function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

    console.log((0, _sum2.default)(8, 8));

    },{"./sum.js":2}],2:[function(require,module,exports){
    "use strict";

    Object.defineProperty(exports, "__esModule", {
      value: true
    });

    exports.default = function (a, b) {
      return a + b;
    };

    },{}]},{},[1]);

### Rollup

Rollup 对代码模块使用新的标准化格式，完全符合es6标准。使用了Tree-shaking技术，静态分析代码中的 import，排除任何未实际使用的代码。

cfg.js文件：

    const rollup = require('rollup');

    async function build() {
      let bundle = await rollup.rollup({
        input: './src/index.js'
      });
      
      bundle.write({
        file: './src/dist/bundle2.js',
        format: 'cjs'
      });
    }

    build();

CLI

    node cfg.js
    
打包成的bundle.js文件：

    'use strict';

    var sum = (a,b) => a + b;
    console.log(sum(8,8));

看到这里，两者的区别就很明显了。
