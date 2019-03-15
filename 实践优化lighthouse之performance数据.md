性能优化是考验前端工程师能力的一个重要方向，也是除了兼容性之外，前端工程师需要耗费巨大精力的地方。在完成了业务需求之后的间隙时间，我们也针对性能优化进行了细致的工作，首先我们初步利用lighthouse对网站性能进行分析，然后根据分析项进去逐步优化。本文逐步记录了我们的优化措施和优化结果。
# 初步分析
利用lighthouse初步分析我们的页面性能，数据并不是特别理想（我们已经在测试环境开启了gzip压缩，未开启cdn）
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/10_lighthouse_1.png) 
# 优化步骤
## 1，修改css提取方式
**webpack ExtractTextPlugin** 它会将所有 required 的 *.css 模块抽取到分离的 CSS 文件。 所以你的样式将不会内联到 JS bundle，而是在一个单独的 CSS 文件。如果你的样式文件很大，这样会提速，因为 CSS bundle 和 JS bundle 是平行加载的；当然劣抛就是增加了额外的http请求。    
**这个优化的方式要根据实际项目而定。**

```
new ExtractTextPlugin({
      filename: utils.assetsPath('css/[name].[contenthash].css'),
      https://github.com/vuejs-templates/webpack/issues/1110
      allChunks: true, // 将此值从false修改为true
    }),
```

修改后我们发现，数据的改善不是特别大，因为我们的CSS文件本身就不大，性能的屏颈不在这。

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/10_lighthouse_2.png) 
## 2，去掉main.js中非必要的库
析vender.js的是我们最大的一个js文件。造成过大的原因是因为在main.js导入第三库太多时,webpack合并js时生成了vendor.js(我们习惯把第三方库放在vendor里面)造成的。通过分析，我们发现mock.js占据了较大的空间。于是，我们决定不采用mock.js前端 mock方案，切换为swagger的api自动生成方案。修改后，数据有了一定的改善：    

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/10_lighthouse_3.png) 
## 3，优化JS
1，vue-router使用异步加载； （https://router.vuejs.org/zh/guide/advanced/lazy-loading.html#%E6%8A%8A%E7%BB%84%E4%BB%B6%E6%8C%89%E7%BB%84%E5%88%86%E5%9D%97）   
2，json资源引入，从require改为import异步加载；    
3，<head>中的<script>引入，可以合适使用async和defer属性；
   
这几个步骤能够带来比较显著的成果：   
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/10_lighthouse_4.png) 
## 4，根据lighthouse提示进去逐项优化
1，在nginx中，为文件添加cache缓存

```
location / {
        ##客户端缓存7天,有修改向服务器请求最新文件
        add_header Cache-Control max-age=604800;
    }
```

2，使用**purifycss-webpack**来实现Tree Shaking（https://blog.csdn.net/u010982507/article/details/81437090）     
3，使用**critical**来实现首屏CSS同步加载和非首屏CSS异步加载，减少CSS的阻塞渲染（https://www.cnblogs.com/ikcamp/p/7649623.html） 

```
new HtmlCriticalWebpackPlugin({
      base: path.resolve(__dirname, '../dist'),
      src: 'index.html',
      dest: 'index.html',
      inline: true,
      minify: true,
      extract: true,
      width: 375,
      height: 565,
      penthouse: {
        blockJSRequests: false,
      }
    })
```

4，开启DNS预获取（https://blog.csdn.net/jl244981288/article/details/80975527?utm_source=blogxgwz1）

```
<!-- 开启DNS预获取 -->
<meta http-equiv="x-dns-prefetch-control" content="on">
<!-- 设置DNS预获取的域名 -->
<link rel="dns-prefetch" href="http://wechat.jinbaochuang.com" />
```
最后针对代码的实际情况进行优化    

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/10_lighthouse_5.png) 




> 参考文档：    
性能优化：https://segmentfault.com/a/1190000012923143    
2019前端性能优化年度总结：https://juejin.im/post/5c4418006fb9a049c043545e    
浅谈http中的Cache-Control：https://blog.csdn.net/u012375924/article/details/82806617


