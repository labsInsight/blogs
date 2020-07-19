Webpack 是一个打包模块化 JavaScript 的工具，在 Webpack 里一切文件皆模块，通过 Loader 转换文件，通过 Plugin 注入钩子，最后输出由多个模块组合成的文件。Webpack 专注于构建模块化项目。JavaScript、CSS、SCSS、图片、模板，在 Webpack 眼中都是一个个模块，这样的好处是能清晰的描述出各个模块之间的依赖关系，以方便 Webpack 对模块进行组合和打包。 经过 Webpack 的处理，最终会输出浏览器能使用的静态资源。
# 构建工具简介
所谓构建就是把源代码转换成发布到线上的可执行 JavaScrip、CSS、HTML 代码，包括如下内容：    
1，代码转换：TypeScript 编译成 JavaScript、SCSS 编译成 CSS 等。    
2，文件优化：压缩 JavaScript、CSS、HTML 代码，压缩合并图片等。    
3，代码分割：提取多个页面的公共代码、提取首屏不需要执行部分的代码让其异步加载。    
4，模块合并：在采用模块化的项目里会有很多个模块和文件，需要构建功能把模块分类合并成一个文件。    
5，自动刷新：监听本地源代码的变化，自动重新构建、刷新浏览器。    
6，代码校验：在代码被提交到仓库前需要校验代码是否符合规范，以及单元测试是否通过。    
7，自动发布：更新完代码后，自动构建出线上发布代码并传输给发布系统。  
# webpack简介
经过多年的发展， Webpack 已经成为构建工具中的首选，这是有原因的：    
1，大多数团队在开发新项目时会采用紧跟时代的技术，这些技术几乎都会采用“模块化+新语言+新框架；   
2，Webpack 可以为这些新项目提供一站式的解决方案；    
3，Webpack 有良好的生态链和维护团队，能提供良好的开发体验和保证质量；    
4，Webpack 被全世界的大量 Web 开发者使用和验证，能找到各个层面所需的教程和经验分享。    

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/11_webpack_01.png) 
Webpack的优点是：    
1，专注于处理模块化的项目，能做到开箱即用一步到位；    
2，通过 Plugin 扩展，完整好用又不失灵活；    
3，使用场景不仅限于 Web 开发；    
4，社区庞大活跃，经常引入紧跟时代发展的新特性，能为大多数场景找到已有的开源扩展；    
5，良好的开发体验。
# webpack核心概念
webpack主要包括6个核心概念：    
1，Entry：入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。    
2，Module：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块。    
3，Chunk：代码块，一个 Chunk 由多个模块组合而成的抽象概念，用于代码合并与分割。    
4，Loader：模块转换器，用于把模块原内容按照需求转换成新内容。    
5，Plugin：扩展插件，在 Webpack 构建流程中的特定时机注入扩展逻辑来改变构建结果或做你想要的事情。    
6，Output：输出结果，在 Webpack 经过一系列处理并得出最终想要的代码后输出结果。        

Webpack 启动后会，从 Entry 里配置的 Module 开始递归解析 Entry 依赖的所有 Module；
每找到一个 Module， 就会根据配置的 Loader 去找出对应的转换规则，对 Module 进行转换后，再解析出当前 Module 依赖的 Module；
 这些模块会以 Entry 为单位进行分组，一个 Entry 和其所有依赖的 Module 被分到一个组也就是一个 Chunk；
最后 Webpack 会把所有 Chunk 转换成文件输出。 在整个流程中 Webpack 会在恰当的时机执行 Plugin 里定义的逻辑。
## Entry
entry是配置模块的入口，可抽象成输入，Webpack 执行构建的第一步将从入口开始搜寻及递归解析出所有入口依赖的模块。    
Entry支持一个入口，也可以支持多个入口，更可以支持由函数动态写入。    
Webpack 在寻找相对路径的文件时会以 context 为根目录，context 默认为执行启动 Webpack 时所在的当前工作目录。 如果想改变 context 的默认配置，则可以在配置文件里这样设置它：
## Module
Module，通过rules 配置模块的读取和解析规则，通常用来配置 Loader。其类型是一个数组，数组里每一项都描述了如何去处理部分文件。 配置一项 rules 时大致通过以下方式：    
1，条件匹配：通过 test 、 include 、 exclude 三个配置项来命中 Loader 要应用规则的文件。   
2，应用规则：对选中后的文件通过 use 配置项来应用 Loader，可以只应用一个 Loader 或者按照从后往前的顺序应用一组 Loader，同时还可以分别给 Loader 传入参数。
## Loader
Loader 可以看作具有文件转换功能的翻译员，配置里的 module.rules 数组配置了一组规则，告诉 Webpack 在遇到哪些文件时使用哪些 Loader 去加载和转换。 如上配置告诉 Webpack 在遇到以 .css 结尾的文件时先使用 css-loader 读取 CSS 文件，再交给 style-loader 把 CSS 内容注入到 JavaScript 里。
## Plugin
Plugin 是用来扩展 Webpack 功能的，通过在构建流程里注入钩子实现，它给 Webpack 带来了很大的灵活性。    
使用 Plugin 的难点在于掌握 Plugin 本身提供的配置项，而不是如何在 Webpack 中接入 Plugin。
几乎所有 Webpack 无法直接实现的功能都能在社区找到开源的 Plugin 去解决，你需要善于使用搜索引擎去寻找解决问题的方法。
## Output
output 配置如何输出最终想要的代码。output 是一个 object，里面包含一系列配置项。例如：    
1，output.filename ：配置输出文件的名称，为string 类型。   
2，Path： output.path 配置输出文件存放在本地的目录，必须是 string 类型的绝对路径    
3，publicPath：在复杂的项目里可能会有一些构建出的资源需要异步加载，加载这些异步资源需要对应的 URL 地址。
# 实践
下面我们利用webpack手动搭建一个vue单页面应用。
1. 创建项目： mkdir vue-raw;  cd vue-raw;     npm init; 
2. 参考vue-cli，创建项目出的基础结构；
3. 安装webpack以及一些其他的依赖包： npm i -D html-webpack-plugin webpack webpack-cli webpack-dev-server； npm i css-loader style-loader vue vue-loader；
4. 配置webpack.prod.config.js：
5. 在 package.json 里面添加 打包命令，添加 --config 指向 webpack.prod.config.js。在命令面板中输入 npm run build，会在项目中生成一个 dist文件夹
6. 引入 html-webpack-plugin 插件，让webpack把html也打包进去
7. 参考webpack.prod.config.js，在webpack.dev.config.js 中配置 dev-server 构建本地node服务器，添加热部署功能
8. package.json 中,添加 babel-loader babel-core babel-preset-env 依赖包，支持 es6,添加 server 指令
9. package.json 中,添加 babel-loader babel-core babel-preset-env 依赖包，支持 es6,添加 server 指令
10. 在你的index上添加内容 ，在终端输入 npm run server 浏览器自动打开页面
11. 在package.json 中install vue依赖相关的package，重启服务

> 参考文档：    
官方文档：http://webpack.wuhaolin.cn/    
实践演练：https://segmentfault.com/a/1190000015132838



