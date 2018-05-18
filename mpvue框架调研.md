# 框架简介
&emsp;&emsp;mpvue是一个使用Vue.js开发小程序的前端框架。框架基于Vue.js核心，修改了Vue.js的runtime和compiler实现，使其可以运行在小程序环境中，从而为小程序开发引入了整套Vue.js开发体验。
美团也给出了已经上线的项目，同一套代码运行的小程序和H5的对比。
（链接：http://mpvue.com/assets/20170810-022809-HD.mp4），官方文档表示同一代码在改动很少的情况下，可以在小程序和webview两个不同的宿主中运行，但是同时也声明希望在未来最理想的状态是，可以一套代码可以直接跑在多端：WEB、小程序（微信和支付宝）、Native（借助weex），说明想借助mpvue框架跑多端的时机还不够成熟。所以美团也特别强调从产品的层面，不建议这么做，各个端有自己的差异性，期望的只是开发和调试体验一致。

## 不同点
1.webpack运行/打包
&emsp;&emsp;因为mpvue和h5对于npm依赖包不同，所以要分别准备两个wepack配置文件打包和运行。

2.请求跨域
&emsp;&emsp;小程序属于nativeApp，请求数据不存在跨域问题，但是webview请求数据会因为javascript的同源策略会存在跨域问题，这个需要在webpack中利用 proxy设置代理请求。

3.路由配置
&emsp;&emsp;h5路由管理是vue-router组件，小程序是由一个特定的man.js文件进行配置。

4.DOM元素
&emsp;&emsp;小程序很多元素都有h5 dom元素的对标，比如text标签对应的是span，image标签对应的是img。尽量避免使用不必要的小程序特有标签，如 text，image。

5.语法支持不够成熟
&emsp;&emsp;mpvue的组件不支持v-html，js渲染表达式，过滤器。

6.通用api
6.1.登陆方式：
&emsp;&emsp;公众号通过wx.config()函数接口注入权限验证配置，具体参数如下：
<pre><code>
wx.config({
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，公众号的唯一标识
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名
    jsApiList: [] // 必填，需要使用的JS接口列表
});
</code></pre>
然后通过
https://open.weixin.qq.com/connect/oauth2/authorize?appid=CORPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&agentid=AGENTID&state=STATE#wechat_redirect   获取用户登录授权和 返回用户信息

&emsp;&emsp;小程序通过调用接口wx.login() 获取临时登录凭证（code），然后通过开发者服务器连接微信服务器以code换取 用户唯一标识openid 和 会话密钥session_key，之后开发者服务器可以根据用户标识来生成自定义登录态，用于后续业务逻辑中前后端交互时识别用户身份。

6.2.调用微信支付方式：
&emsp;&emsp;公众号通过wx.chooseWXPay()具体参数如下：
<pre><code>
wx.chooseWXPay({
timestamp: 0, // 支付签名时间戳，注意微信jssdk中的所有使用timestamp字段均为小写。但最新版的支付后台生成签名使用的timeStamp字段名需大写其中的S字符
nonceStr: '', // 支付签名随机串，不长于 32 位
package: '', // 统一支付接口返回的prepay_id参数值，提交格式如：prepay_id=\*\*\*}
signType: '', // 签名方式，默认为'SHA1'，使用新版支付需传入'MD5'
paySign: '', // 支付签名
success: function (res) {
// 支付成功后的回调函数
},
fail: function (res) {
// 支付失败后的回调函数
},
complete: function (res) {
// 接口调用的回调函数，成功/失败都会执行
});
</code></pre>
&emsp;&emsp;小程序通过wx.requestPayment()具体参数如下:
<pre><code>
wx.requestPayment ({
timestamp: 0, //时间戳从1970年1月1日00:00:00至今的秒数,即当前的时间
nonceStr: '', // 支付签名随机串，不长于 32 位
package: '', // 统一支付接口返回的prepay_id参数值，提交格式如：prepay_id=\*\*\*}
signType: '', // 签名方式，暂时支持md5
 paySign: '', // 支付签名
success: function (res) {
// 支付成功后的回调函数
},
fail: function (res) {
// 支付失败后的回调函数
},
complete: function (res) {
// 接口调用的回调函数，成功/失败都会执行
}
});
</code></pre>

6.3.native方法
&emsp;&emsp;小程序可以操作文件，数据缓存，系统信息，网络状态，加速度计，罗盘，拨打电话，扫码，剪切板，调用蓝牙，调节屏幕亮度，监听用户截屏事件，手机振动，获取手机联系人，调用nfc功能，调用wifi功能，。
&emsp;&emsp;公众号在数据缓存方面的替换方案为数据缓存可以用localStorage和sessionStorage。
&emsp;&emsp;公众号也可以扫码，但是调用函数名称不一样，函数名为scanQRCode，小程序为scanCode。
&emsp;&emsp;公众号拨打电话可以用a标签的href.tel作为替代方案。

6.4.界面和组件
&emsp;&emsp;小程序的界面和公共组件可以自己设置和直接调用，如果和h5匹配一样，需要自己使用vue写和小程序一样的组件，增加开发难度。其中iBeacon函数各不相同，比如：
&emsp;&emsp;小程序查找开始搜索附近的iBeacon设备：wx.startBeaconDiscovery
&emsp;&emsp;小程序停止搜索附近的iBeacon设备：wx. stopBeaconDiscovery
&emsp;&emsp;小程序获取所有已搜索到的iBeacon设备：wx.getBeacons
&emsp;&emsp;公众号查找开始搜索附近的iBeacon设备：wx. startSearchBeacons
&emsp;&emsp;公众号停止搜索附近的iBeacon设备：wx.stopSearchBeacons
&emsp;&emsp;公众号获取所有已搜索到的iBeacon设备：wx. onSearchBeacons

&7.框架生命周期
&emsp;&emsp;除了Vue本身的生命周期外，mpvue还兼容了小程序生命周期，这部分生命周期钩子的来源于微信小程序的Page，除特殊情况外，不建议使用小程序的生命周期钩子。