### HTML规范

##### 尽量不使用无内容标签

Don't introduce a specific HTML structure just to solve some visual design problems

Not recommended

    <span class="item">
      <i class="icon"></i>
    </span>

Recommended

    <span class="item"></span>
    
    /* 对应的css */
    .item::before {
      content: '';
      display: inline-block;
      /* 其他icon属性 */
    }
    
##### 使用双引号

When quoting attributes values, use double quotation marks. Use double `"` rather than single quotation marks `'` around attribute values.

Not recommended

    <div class='item'></div>
    
Recommended

    <div class="item"></div>

### CSS规范

##### 最后一个值也以分号结尾

通常在大括号结束前的值可以省略分号，但是这样做会对修改、添加和维护工作带来不必要的失误和麻烦。

##### 省略值为0时的单位

为节省不必要的字节同时也使阅读方便，我们将0px、0em、0%等值缩写为0。

    .content {
      margin: 0 10px;
      background-position: 50% 0;
    }
    
##### 去掉小数点前的0

    .item {
      opacity: .5;
    }
    
##### 使用单引号

省略url引用中的引号，其他需要引号的地方使用单引号。

    .item {
      background: url(bg.png);
    }
    .item::after {
      content: '.';
    }

##### Shorthand Properties

CSS offers a variety of shorthand properties (like font) that should be used whenever possible, even in cases where only one value is explicitly set.

Not recommended

    border-top-style: none;
    font-family: palatino, georgia, serif;
    font-size: 100%;
    padding-bottom: 2em;
    padding-left: 1em;
    padding-right: 1em;
    padding-top: 0;

Recommended

    border-top: 0;
    font: 100% palatino, georgia, serif;
    padding: 0 1em 2em;

##### 根据属性的重要性按顺序书写

先显示定位布局类属性，后盒模型等自身属性，最后是文本类及修饰类属性。

|定位布局类|自身属性|文本修饰类饰|
|--|--|--|--|
|`display`|`overflow`|`font`|
|`visibility`|`min-width`|`text-align`|
|`float`|`width`|`text-decoration`|
|`clear`|`height`|`vertical-align`|
|`list-style`|`margin`|`white-space`|
|`position`|`padding`|`color`|
|`top`|`border`|`background`|

### JS规范

##### 定义变量

Not recommended

    var x = 10;
    var y = 100;
    
Recommended

    var x = 10,
        y = 100;

##### Don't use switch

switch is a very error prone control statement in every programming language. Use if else if instead.

##### String

字符串使用单引号`''`

Not recommended

    var name = "xu";
    
Recommended

    var name = 'xu';
    
##### 模块

import使用单引号`''`

    import React from 'react';

##### 属性

使用`.`来访问对象的属性

    var user = {
      name: 'xu',
      coin: 0,
    };


Not recommended

    var name = user['name'];
    
Recommended

    var name = user.name;

##### 增加结尾的逗号

Not recommended

    var user = {
      name: 'xu',
      coin: 0
    };
    
    var users = [
      'student',
      'teacher'
    ];

Recommended

    var user = {
      name: 'xu',
      coin: 0,
    };
    
    var users = [
      'student',
      'teacher',
    ];
