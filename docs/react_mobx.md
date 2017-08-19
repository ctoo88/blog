## react+mobx初探

2017/08/19

### 关键字

>[mobx](https://github.com/mobxjs/mobx) [react](https://github.com/facebook/react) [webpack](https://github.com/webpack/webpack) [babel](https://github.com/babel/babel)

### 概述

主要解决react兄弟组件间的通信问题，由mobx来存储和更新状态。

### 工程介绍

#### 第一步：新建一个简单的react工程

    ├─node_modules/
    ├─dist/ 编译打包后的文件
    ├─src/
    │  ├─components/
    │  │  ├─app.jsx
    │  │  └─one.jsx
    │  └─index.jsx
    ├─.babelrc babel配置文件
    ├─index.html
    ├─package.json
    ├─webpack.config.js webpack配置文件
    └─yarn.lock

生成package.json文件：

    npm init

安装依赖包：

    yarn add react
    yarn add react-dom
    yarn add react-router@3.0.0
    yarn add babel-core --dev
    yarn add babel-loader --dev
    yarn add babel-preset-es2015 --dev
    yarn add babel-preset-react --dev
    yarn add webpack --dev

.babelrc 配置文件

    {
       "presets": ["es2015", "react"]
    }

webpack.config.js 配置文件

    module.exports = {
      entry: [
        './src/index.jsx'
      ],
      output: {
        path: __dirname + '/dist',
        publicPath: '/dist',
        filename: 'bundle.js'
      },
      module: {
        loaders: [{
          test: /\.js[x]?$/,
          exclude: /node_modules/,
          loader: 'babel-loader'
        }]
      },
      resolve: {
        extensions: ['.js', '.jsx']
      }
    }

index.jsx 文件内容

    import React from 'react';
    import ReactDOM from 'react-dom';
    import { Router, Route, hashHistory } from 'react-router';

    import App from './components/app';
    import One from './components/one';

    ReactDOM.render(
        <Router history={hashHistory}>
          <Route path="/" component={App} />
          <Route path="/one" component={One} />
        </Router>
      document.getElementById('root')
    );

app.jsx 文件内容

    import React from 'react';

    const Page = () => {
      return (
        <div>
          <h1>hello react</h1>
          <a href="#/one">page one</a>
        </div>
      );
    }

    export default Page;

one.jsx 文件内容

    import React from 'react';

    const Page = () => {
      return (
        <div>
          <p>test content</p>
          <a href="#/">back</a>
        </div>
      );
    }

    export default Page;

#### 第二步：用mobx管理状态

安装依赖包：

    yarn add mobx --dev
    yarn add mobx-react --dev

新建stores文件夹和相关store文件

    ├─src/
    │  ├─components/
    │  │  ├─app.jsx
    │  │  └─one.jsx
    │  ├─stores
    │  │  ├─userstore.jsx
    │  │  └─todostore.jsx
    │  └─index.jsx

userstore.jsx 文件内容

    import { extendObservable } from 'mobx';

    class Store {
      constructor() {
        extendObservable(this, {
          name: "ctoo"
        });
      }
    }

    export default new Store();

todostore.jsx 文件内容

    import { extendObservable } from 'mobx';

    class Store {
      constructor() {
        extendObservable(this, {
          content: "learn mobx"
        });
      }
    }

    export default new Store();

index.jsx 文件更新

    import React from 'react';
    import ReactDOM from 'react-dom';
    import { Router, Route, hashHistory } from 'react-router';
    import { Provider } from 'mobx-react'; // 新增

    import App from './app';
    import One from './one';

    import usestore from './stores/usestore'; // 新增
    import todostore from './stores/todostore'; // 新增

    const myStore = window.myStore = { usestore, todostore };

    ReactDOM.render(
      <Provider {...myStore}>
        <Router history={hashHistory}>
          <Route path="/" component={App} />
          <Route path="/one" component={One} />
        </Router>,
      </Provider>,
      document.getElementById('root')
    );

app.jsx 文件更新(es6版无状态组件的写法)

    import React from 'react';
    import { observer, inject } from 'mobx-react'; // 新增

    const Page = inject('usestore')(observer(({usestore}) => {
      return (
        <div>
          <h1>{'hello ' + usestore.name}</h1>
          <a href="#/one">page one</a>
        </div>
      );
    }));

    export default Page;

one.jsx 文件更新

    import React from 'react';
    import { observer, inject } from 'mobx-react'; // 新增

    const Page = inject('todostore')(observer(({todostore}) => {
      return (
        <div>
          <p>{'task: ' + todostore.content}</p>
          <a href="#/">back</a>
        </div>
      );
    }));

    export default Page;

测试

    在控制台输入：
    myStore.usestore.name = "xu";
    myStore.todostore.content = "running 5km";
    看到状态变化说明成功了。

#### 第三步：用es6新特性装饰器

安装依赖包：

    yarn add babel-plugin-transform-decorators-legacy --dev
    yarn add babel-preset-stage-1 --dev

.babelrc 文件更新

    {
      "presets": ["es2015", "stage-1", "react"],
      "plugins": ["transform-decorators-legacy"]
    }

usestore.jsx 文件更新

    import { observable  } from 'mobx';

    class Store {
      @observable name = "ctoo"; // 简化了很多
    }

    export default new Store();

app.jsx 文件更新(es6版react组件的写法)

    import React from 'react';
    import { observer, inject } from 'mobx-react';

    @inject('usestore')
    @observer
    class Page extends React.Component {
      render() {
        const {usestore} = this.props;

        return (
          <div>
            <h1>{'hello ' + usestore.name}</h1>
            <a href="#/one">page one</a>
          </div>
        );
      }
    }

    export default Page;
