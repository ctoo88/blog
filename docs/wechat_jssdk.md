## 微信公众号JS-SDK开发，服务端采用Node.js

2017/06/23

### 概述

>微信JS-SDK是微信公众平台面向网页开发者提供的基于微信内的网页开发工具包。

### JSSDK使用步骤

#### 步骤一：绑定域名

登录“微信公众平台-设置-公众号设置-功能设置-JS接口安全域名”进行绑定。

#### 步骤二：引入JS文件

在需要调用JS接口的页面引入如下JS文件：http://res.wx.qq.com/open/js/jweixin-1.2.0.js

#### 步骤三：通过config接口注入权限验证配置

所有需要使用JS-SDK的页面必须先注入配置信息，否则将无法调用。

    wx.config({
        debug: true, // 开启调试模式
        appId: '', // 必填，公众号的唯一标识
        timestamp: , // 必填，生成签名的时间戳
        nonceStr: '', // 必填，生成签名的随机串
        signature: '',// 必填，权限签名，见附录
        jsApiList: [] // 必填，所有JS接口列表，见附录
    });

#### 步骤四：通过ready接口处理成功验证

    wx.ready(function(){
        // config信息验证后会执行ready方法。
    });

#### 步骤五：通过error接口处理失败验证

    wx.error(function(res){
        // config信息验证失败会执行error函数。
    });

#### 接口调用说明

所有接口通过wx对象来调用，参数是一个对象，除了每个接口本身需要传的参数之外，还有以下通用参数：
1.success：接口调用成功时执行的回调函数。
2.fail：接口调用失败时执行的回调函数。
3.complete：接口调用完成时执行的回调函数，无论成功或失败都会执行。
4.cancel：用户点击取消时的回调函数，仅部分有用户取消操作的api才会用到。
5.trigger: 监听Menu中的按钮点击时触发的方法，该方法仅支持Menu中的相关接口。

#### 分享接口

获取“分享到朋友圈”按钮点击状态及自定义分享内容接口

    wx.onMenuShareTimeline({
        title: '', // 分享标题
        link: '', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
        imgUrl: '', // 分享图标
        success: function () { 
            // 用户确认分享后执行的回调函数
        },
        cancel: function () { 
            // 用户取消分享后执行的回调函数
        }
    });

### 附录

#### JS-SDK使用权限签名算法

##### access_token

access_token是公众号的全局唯一接口调用凭据，公众号调用各接口时都需使用access_token。开发者需要进行妥善保存。access_token的存储至少要保留512个字符空间。access_token的有效期目前为2个小时，需定时刷新，重复获取将导致上次获取的access_token失效。

公众号可以使用AppID和AppSecret调用本接口来获取access_token。AppID和AppSecret可在“微信公众平台-开发-基本配置”页中获得（需要已经成为开发者，且帐号没有异常状态）。

    https请求方式: GET
    https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=AppID&secret=AppSecret

    成功返回如下JSON数据：
    {
      "access_token": "ACCESS_TOKEN",
      "expires_in": 7200
    }

##### jsapi_ticket

jsapi_ticket是公众号用于调用微信JS接口的临时票据。正常情况下，jsapi_ticket的有效期为7200秒，通过access_token来获取。由于获取jsapi_ticket的api调用次数非常有限，频繁刷新jsapi_ticket会导致api调用受限，影响自身业务，开发者必须在自己的服务全局缓存jsapi_ticket 。

    https请求方式: GET
    https://api.weixin.qq.com/cgi-bin/ticket/getticket?access_token=ACCESS_TOKEN&type=jsapi

    成功返回如下JSON数据：
    {
        "errcode": 0,
        "errmsg": "ok",
        "ticket": "JSAPI_TICKET",
        "expires_in": 7200
    }

获得jsapi_ticket之后，就可以生成JS-SDK权限验证的签名了。

##### 签名算法

签名生成规则如下：参与签名的字段包括有效的jsapi_ticket, noncestr（随机字符串）, timestamp（时间戳）, url（当前网页的URL，不包含#及其后面部分）。对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1。这里需要注意的是所有参数名均为小写字符。对string1作sha1加密，字段名和字段值都采用原始值，不进行URL 转义。

即signature = sha1(string1)。 示例：

    jsapi_ticket = JSAPI_TICKET;
    noncestr = Math.random().toString(36).substr(2, 15);
    timestamp = parseInt(new Date().getTime() / 1000);
    url = http://h5.ctoo.io;

步骤1. 对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1：

    jsapi_ticket=JSAPI_TICKET&noncestr=Wm3WZYTPz0wzccnW&timestamp=1414587457&url=http://h5.ctoo.io

步骤2. 对string1进行sha1签名，得到signature：

    4a126064a2d39c1bbffba2538dc35da1

##### 注意事项

1. 签名用的noncestr和timestamp必须与wx.config中的nonceStr和timestamp相同。
2. 签名用的url必须是调用JS接口页面的完整URL。
3. 出于安全考虑，开发者必须在服务器端实现签名的逻辑。

##### 服务端node.js代码：

    'use strict'
    function createNonceStr() {
      return Math.random().toString(36).substr(2, 15);
    };

    function createTimestamp() {
      return parseInt(new Date().getTime() / 1000);
    };

    function raw(args) {
      let keys = Object.keys(args)
        , newArgs = {}
        , string = '';

      keys = keys.sort()
      keys.forEach( key => {
        newArgs[key.toLowerCase()] = args[key];
      });

      for (let k in newArgs) {
        string += '&' + k + '=' + newArgs[k];
      }

      string = string.substr(1);
      return string;
    };

    /**
    * @synopsis 签名算法 
    *
    * @param jsapi_ticket 用于签名的 jsapi_ticket
    * @param url 用于签名的 url ，注意必须动态获取，不能 hardcode
    *
    * @returns
    */
    function sign(jsapi_ticket, url) {
      let ret = {
            jsapi_ticket: jsapi_ticket,
            nonceStr: createNonceStr(),
            timestamp: createTimestamp(),
            url: url
          }
        , string = raw(ret)
        , jsSHA = require('jssha') // jssha.js
        , shaObj = new jsSHA(string, 'TEXT');

      ret.signature = shaObj.getHash('SHA-1', 'HEX');

      return ret;
    };

    module.exports = sign;

#### 所有JS接口列表

    onMenuShareTimeline // 分享到朋友圈
    onMenuShareAppMessage // 分享给好友
    onMenuShareQQ // 分享到QQ
    onMenuShareWeibo // 分享到微博
    onMenuShareQZone // 分享到QQ空间
    startRecord
    stopRecord
    onVoiceRecordEnd
    playVoice
    pauseVoice
    stopVoice
    onVoicePlayEnd
    uploadVoice
    downloadVoice
    chooseImage
    previewImage
    uploadImage
    downloadImage
    translateVoice
    getNetworkType
    openLocation
    getLocation
    hideOptionMenu
    showOptionMenu
    hideMenuItems
    showMenuItems
    hideAllNonBaseMenuItem
    showAllNonBaseMenuItem
    closeWindow
    scanQRCode
    chooseWXPay
    openProductSpecificView
    addCard
    chooseCard
    openCard