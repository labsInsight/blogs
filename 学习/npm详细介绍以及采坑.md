# 什么是NPM？

npm 之于 Node.js ，就像 pip 之于 Python， gem 之于 Ruby，
pear 之于 PHP

npm 是 Node.js 官方提供的包管理工具，他已经成了 Node.js 包的标准发布平台，用于 Node.js 包的发布、传播、依赖控制。npm 提供了命令行工具，使你可以方便地下载、安装、升级、删除包，也可以让你作为开发者发布并维护包。

# 为什么使用npm

npm 是随同 Node.js 一起安装的包管理工具，能解决 Node.js 代码部署上的很多问题，常见的场景有以下几种：

1、允许用户从 npm 服务器下载别人编写的第三方包到本地使用。
2、允许用户从 npm 服务器下载并安装别人编写的命令行程序到本地使用。
3、允许用户将自己编写的包或命令行程序上传到 npm 服务器供别人使用。

npm 的背后，是基于 couchdb 的一个数据库，详细记录了每个包的信息，包括作者、版本、依赖、授权信息等。

它的一个很重要的作用就是：将开发者从繁琐的包管理工作（版本、依赖等）中解放出来，更加专注于功能的开发。

# 如何使用npm

1、安装

npm 不需要单独安装。在安装 Node 的时候，会连带一起安装 npm 。但是，Node 附带的 npm 可能不是最新版本，最后用下面的命令，更新到最新版本。

    $ sudo npm install npm@latest –g

如果是 Window 系统使用以下命令即可：

    npm install npm –g

也就是使用 npm 安装自己。之所以可以这样，是因为 npm 本身与 Node 的其他模块没有区别。

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-1.png)

2、使用

使用命令 npm init 用来初始化生成一个新的 package.json 文件。

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-2.png)

3、Npm install

使用 npm 安装包的命令格式为：

    npm [install/i] [package_name]

本地安装 

    npm install <package name>

全局安装 

    sudo npm install -global <package name> 
    sudo npm install -g <package name>

安装不同版本

    npm install sun-rice@latest
    npm install sun-rice @0.1.1
        npm install sun-rice @">=0.1.0 <0.2.0”

install 命令可以使用不同参数，指定所安装的模块属于哪一种性质的依赖关系，即出现在 packages.json 文件的哪一项中。

    –save：模块名将被添加到 dependencies，可以简化为参数-S。
    –save-dev：模块名将被添加到 devDependencies，可以简化为参数-D

4、Dependencies 依赖

这个可以说是我们 npm 核心一项内容，依赖管理，这个对象里面的内容就是我们这个项目所依赖的 js 模块包。下面这段代码表示我们依赖了 markdown-it 这个包，版本是 ^8.1.0 ，代表最小依赖版本是 8.1.0 ，如果这个包有更新，那么当我们使用 npm install 命令的时候，npm 会帮我们下载最新的包。当别人引用我们这个包的时候，包内的依赖包也会被下载下来

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-3.png)

5、devDependencies 依赖

在我们开发的时候会用到的一些包，只是在开发环境中需要用到，但是在别人引用我们包的时候，不会用到这些内容，放在 devDependencies 的包，在别人引用的时候不会被 npm 下载

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-4.png)

npm install默认会安装 dependencies 字段和 devDependencies 字段中的所有模块，如果使用--production参数，可以只安装 dependencies 字段的模块

6、npm run

npm 不仅可以用于模块管理，还可以用于执行脚本。package.json 文件有一个 scripts 字段，可以用于指定脚本命令，供 npm 直接调用。

Scripts 脚本

顾名思义，就是一些脚本代码，可以通过 npm run script-key 来调用，例如在这个 package.json 的文件夹下使用 npm run dev 就相当于运行了 node build/dev-server.js 这一段代码。使用 scripts 的目的就是为了把一些要执行的代码合并到一起，使用 npm run 来快速的运行，方便省事。

npm run 是 npm run-script 的缩写，一般都使用前者，但是后者可以更好的反应这个命令的本质。

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-5.png)

# 创建于发布包

1、创建包

包是在模块基础上更深一步的抽象，Node.js 的包类似于 C/C++ 的函数库或者 Java、.Net 的类库。它将某个独立的功能封装起来，用于发布、更新、依赖管理和版本控制。Node.js 根据 CommonJS 规范实现了包机制，开发了 npm 来解决包的发布和获取需求。

Node.js 的包是一个目录，其中包含了一个 JSON 格式的包说明文件 package.json。严格符合 CommonJS 规范的包应该具备以下特征：

    1、package.json 必须在包的顶层目录下；
    
    2、二进制文件应该在 bin 目录下；
    
    3、JavaScript 代码应该在 lib 目录下；
    
    4、文档应该在 doc 目录下；
    
    5、单元测试应该在 test 目录下。

Node.js 对包的要求并没有这么严格，只要顶层目录下有 package.json，并符合一些规范即可。当然为了提高兼容性，我们还是建议你在制作包的时候，严格遵守 CommonJS 规范。

Node.js 在调用某个包时，会首先检查包中 packgage.json 文件的 main 字段，将其作为包的接口模块，如果 package.json 或 main 字段不存在，会尝试寻找 index.js 或 index.node 作为包的接口。
package.json 是 CommonJS 规定的用来描述包的文件，完全符合规范的 package.json 文件应该含有以下字段：


    name: 包的名字，必须是唯一的，由小写英文字母、数字和下划线组成，不能包含空格。
    
    description: 包的简要说明。
    
    version: 符合语义化版本识别规范的版本字符串。
    
    keywords: 关键字数组，通常用于搜索。
    
    maintainers: 维护者数组，每个元素要包含 name 、email(可选)、web(可选)字段。
    
    contributors: 贡献者数组，格式与 maintainers 相同。包的作者应该是贡献者数组的第一个元素。
    
    bugs: 提交 bug 的地址，可以是网址或者电子邮件地址。
    
    licenses: 许可证数组，每个元素要包含 type（许可证的名称）和 url（链接到许可证文本的地址）字段。
    
    repositories: 仓库托管地址数组，每个元素要包含 type（仓库的类型，如 git）、URL（仓库的地址）和 path（相对于仓库的路径，可选）字段。
    
    dependencies: 包的依赖，一个关联数组，由包名称和版本号组成。

2、发布包

通过使用 npm init 可以根据交互式回答产生一个符合标准的 package.json。创建一个 index.js 作为包的接口,一个简单的包就制作完成了。

在发布前,我们还需要获得一个账号用于今后维护自己的包,使用 npm adduser 根据提示完成账号的创建。

    完成后可以使用 npm whoami 检测是否已经取得了账号。

接下来，在 package.json 所在目录下运行 npm publish，稍等片刻就可以完成发布了，打开浏览器，
访问 http://search.npmjs.org/ 就可以找到自己刚刚发布的包了。

    现在我们可以在世界的任意一台计算机上使用 npm install neveryumodule 命令来安装它。

如果你的包将来有更新,只需要在 package.json 文件中修改 version 字段，然后重新使用 npm publish 命令就行了。

    如果你对已发布的包不满意，可以使用 npm unpublish 命令来取消发布。

# Sun组件库发布实践

1、打包组件库

在一般情况下，我们使用 npm run build 命令打包出来的项目，是一个完整的项目，其中包含了html、js、css以及一些其他的静态资源，例如图片等。

但由于我们需要的是一个组件库，所以其实我们并不需要将项目中的demo、部分静态资源打包。我们只需要其中的组件代码就可以了。

因此我们需要在webpack中单独配置一个entry。然后将所有的组件 import 到这个文件中，再 export 出去。如下，具体请参照代码。

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-6.png)

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-7.png)

2、安装并使用包
在使用npm publish将包发布以后，我们就可以在任何一个项目中安装并使用它了。

    使用 npm install sun-rice 命令来安装它

Import 之后就可以在项目中使用了

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-8.png)

3、vue版本冲突

大多数情况下，我们的组件都可以正常使用，但是在一些全局注册的组件中，可能会报错。

原因在于：组件中的vue版本与项目中使用的vue版本不一致，导致实际实用时，存在两个不同的vue实例。从而引起了冲突。

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-9.png)

为此，我们需要在打包组件时，将vue等公用的依赖剔除，不参与打包。这样，项目会使用module中依赖的vue，从而避免了冲突。在webpack的 externals 字段可以做相关配置

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-10.png)

4、js与css文件拆分

经过一系列配置以后，我们使用 npm run build 命令打包出来的组件库，是一个单独的 js 文件，它会将 js 与 css 代码全部打包到一起。正常情况下，这个包已经可以使用了。

但是，这个单独的 js 文件，体积太大了，多达 800kb

于是我们需要将 js 与 css 打包成独立的两个文件。

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-11.png)

5、css依赖报错

将 js 与 css 拆分后，我们需要在项目中单独引入css文件

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-12.png)

但是这样以后，我们的项目却报错了。原因是 import 的时候，webpack会去读取module中所有的代码，但是其实我们不需要那么多。

所以在发布包的时候，我们应该只需要发布dist目录中的代码就可以了

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-13.png)

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/npm-share-14.png)