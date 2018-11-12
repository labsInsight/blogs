一说起js的调试，相信很多人第一个想到的就是console.log()，貌似除了这个打印，就没有其它更好的方法。实际上，打印方法确实方便快捷，并且也可以解决80%甚至 90%的问题；但剩下那10%的难啃的骨头，是不是有更好的方法来处理呢？本文将带你探索一些可行的调试方法。
# console对象
其实console并不等于打印，利用好console对象，可以帮我们处理好很多事情。譬如，利用console.table可以将对象打印成表格从而有更好的视觉效果；结合console.console.profile和console.profileEnd，可以让我们做好性能分析；调用console.trace，我们可以清晰知道当前的调用堆栈：     

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/8_debug_console.png)    

在此，我们建议所有喜欢console.log()的同学，可以详细了解console对象：[Console API 参考](https://developers.google.cn/web/tools/chrome-devtools/console/console-reference)
# 断点调试
断点调试的方便自不必说。但前端断点调试不是特别受欢迎，主要是因为前端编辑器不能直接设置断点。在这我们推荐两个方法：
1. 建议可使用vscode或者webstorm这些集成功能较好的IDE，可以直接将调试模式集成到chrome中；
2. 对于不喜欢庞大IDE的同学，可以在代码中需要断点的地方加入debugger，程序运行到对应的代码行便会断住，这时可以选择“进入”或者“跳过”后面的一个函数调用。（对于匿名函数，需要在控制台中输入 debug(car.funcY)，脚本会在调试模式下，进入 car.funcY 的时候停止运行）
    
断点调试是每一个程序的必备技能，在此不做赘述。
# vue调试
我们的主要框架是vue，在此介绍一下vue调试神器vue-devtools。首先，介绍一下怎么安装vue-devtools：
1. 下载编译vue-devtools
```
git clone https://github.com/vuejs/vue-devtools.git // 找到vue-devtools的github项目，并将其clone到本地. vue-devtools
cd vue-devtools // 进入工程目录
npm install // 如果太慢的话，可以安装一个cnpm, 然后命令换成 cnpm install
npm run build // 编译项目文件
```
2. 加载vue-devtools到浏览器
游览器输入地址“chrome://extensions/”进入扩展程序页面，将编译好之后的shells/chrome添加到扩展程序：
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/8_debug_vue.png)
3. 点击右上角的vue按钮，可以看到详细的调试信息：
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/8_debug_vue_chrome.png)
# 模拟设备调试
由于浏览器无法模拟微信公众号的js-api功能，所以我们一般从事微信公众号的开发，都是在微信开发者工具上。在此我们讲一下微信开发者工具，如何与手机真机进行联动。
1. 选择调试方式为远程设备**Remote devices**：   

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/8_debug_device.png)
2. 选择通过USB或者网络进行联接：    

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/8_debug_device_set.png)    

浏览器的真机调试与此类似，也是可以支持真机调试的。真机调试的目的，是方便我们查看一些在真机上容易出现，而模拟器难以出现的bug。
# 生产调试
生产环境的调试和找bug，我们推荐一个平台：[fundebug](https://www.fundebug.com/)。    
这个平台可以做到非常好的生产监控，以及生产问题的定位。主要缺陷是平台收费较高，且不提供免费的入口级功能；**==如果大家有其它免费的平台或者框架，也非常欢迎推荐我们==**，在此预先感谢。      


> 参考文档：    
如何用浏览器调试器代替console.log() ：https://mp.weixin.qq.com/s/uHtEI_spD2slM-ddVQ9reA        
14 个你可能不知道的 JavaScript 调试技巧：https://www.cnblogs.com/wangdan0915/p/7866949.html    
专业的应用错误监控平台：https://www.fundebug.com/