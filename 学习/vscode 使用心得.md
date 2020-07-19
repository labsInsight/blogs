# 一、前言

作为一个前端程序员，相信你肯定有自己非常喜欢和熟悉的代码编辑器，比如Webstorm、Sublime、HBuilder等。

Webstorm的功能也非常完善，但是它是收费的，虽然网上可以找到破解版，但是破解起来还是比较麻烦的。

在公司的前端组，我们统一使用的是微软出品的VScode。所以为了以后更好地进行团队的写作，你要慢慢熟悉和适应这款优秀的代码编辑器。
为了让不熟悉VScode的你更快地适应这款编辑器，我根据自己的使用经验和体会，写下这篇文章，希望能帮助到你。


# 二、优势

其实我之前写代码的时候，一直是使用webstorm这款编辑器，感觉非常的舒适。但是在接触VScode之后，才发现这款轻量级的编辑器，也能给自己非一般的编程体验。那么接下来，就给简单介绍一下，我印象中VScode的优势。

1. 轻量级编辑器
VScode是一款轻量级的编辑器，安装包非常小，而且启动速度非常快。虽然这对实际的项目没什么大的帮助，但是可以在无形间提高我们的用户体验。
2. 丰富的插件系统
VScode有着非常丰富的插件系统，无论你是编辑HTML、CSS、JS、TS、Vue、React等前端代码，还是JAVA、Python等后端代码。我们都可以找到相对应的插件，让我们如虎添翼，更快速地完成项目。
3. 代码跟踪功能
我们是一个团队，项目中往往都是协作开发，绝大多数情况下要使用Git来管理我们的代码，这个时候VScode会跟踪我们的代码，非常明显地为我们标注：你更改了那些文件，文件中你修改可第几行的代码，让我们对自己编写的代码一目了然。

# 三、VScode的插件推荐
1. Chinese (Simplified) Language Pack for Visual Studio Code，
这是一款汉化VScode的插件，如果你的英文不是很好，不习惯英文的编程环境，那么你就可以安装这款插件。需要提醒一下，插件安装好之后，他会提示你是否更改自己的语言，你要选择是，如果没有成功，可以查看后面的具体汉化教程。

2. Auto Close Tag，标签自动补全功能

3. Auto Rename Tag，标签自动补全功能

4. Bracket Pair Colorizer 2，用于对匹配括号上色

5. Debugger for Chrome，在Chrome浏览器或支持Chrome调试器协议的任何其他目标中调试JavaScript代码

6. filesize，在状态栏中显示当前文件大小

7. Gitconfig Syntax，添加语法突出显示对Git配置文件的支持

8. HTML CSS Support，支持HTML文档的CSS

9. HTMLHint

10. Path Intellisense，路径感知

11. PostCSS syntax，postcss语言支持

12. postcss-sugarss-language，postcss扩展

13. Prettier - Code formatter，代码格式化

14. Quokka.js，JavaScript的实时草稿

15. Settings Sync，vscode配置同步

16. Vetur，vue工具

17. vscode-icons，图标工具

18. TSLint，代码检查插件，让我们的代码更加的规范和正确。

19. Vue 2 Snippets，Vue扩展插件，如果你要进行编写Vue代码，进行安装就行了。

20. Vue Snippets(vue-ls)，Vue扩展插件，如果你要进行编写Vue代码，进行安装就行了。


# 四、环境配置

打开：首选项→用户设置（settings.json）
建议配置如下

    // 将设置放入此文件中以覆盖默认设置
    {
        "editor.fontSize": 16
        ,"editor.fontFamily": "PingFang SC"
        //,"editor.fontFamily": "Microsoft Yahei"
        // 有苹方字体用苹方，没有就用雅黑，mac下字体建议设置为14
    }

# 五、中文设置

解决办法：F1 -> Configure Language（可以只输入 language） ，打开 locale.json 进行修改

    {
        // Defines VSCode's display language.
        // See https://go.microsoft.com/fwlink/?LinkId=761051 for a list of supported languages.
        // Changing the value requires restarting VSCode.
        // 想体验原味的话，设置为：en ，中文的话就设置为：zh-CN
        "locale":"zh-CN"
    }

# 六、设置VScode的快捷键

每个人的操作习惯不同，使用的快捷键也不同，在VScode中，我们可以根据自己的习惯来设置自己的快捷键。设置非常简单，首先是打开快捷键设置面板：点击左下方的锯齿图标，选择"键盘快捷方式"即可打开。

# 七、配置同步

1. Settings Sync是vscode中同步设置和安装插件的小工具，在扩展商店中搜索并安装它 
2. 登陆Github>Your profile> settings>Developer settings>personal access tokens>generate new token，输入名称，勾选Gist，提交 
3. 保存Github Access Token 
4. 打开vscode，Ctrl+Shift+P打开命令框，输入sync，找到update/upload settings，输入Token，上传成功后会返回Gist ID，保存此Gist ID. 
5. 若需在其他机器上DownLoad插件的话，同样，Ctrl+Shift+P打开命令框，输入sync，找到Download settings，会跳转到Github的Token编辑界面，点Edit，regenerate token，保存新生成的token，在vscode命令框中输入此Token，回车，再输入之前的Gist ID，即可同步插件和设置。


详细图文教程请看：https://www.cnblogs.com/kenz520/p/7416836.html

# 八、vscode调试

### 最基本的launch模式

这是最简单的配置，使用 `launch` 模式调试 Node.js，实际场景用的不多。

`.vscode/launch.json`

    {
      "version": "0.2.0",
      "configurations": [
        {
          "type": "node",
          "request": "launch",
          "name": "Debug Node App",
          "program": "${workspaceRoot}/app.js"
        }
      ]
    }

### 最基本的attach模式

`attach` 模式的最简单配置，`attach` 模式用来调试某个已启动的程序，这里使用 `npm srcript` 启动一个 node 服务

`package.json`

    {
      "scripts": {
        "dev": "node --nolazy --inspect app.js"
      }
    }
    
`.vscode/launch.json`

    {
      "version": "0.2.0",
      "configurations": [
        {
          "type": "node",
          "request": "attach",
          "name": "Attach",
          "port": 9229
        }
      ]
    }

### 使用npm

`package.json`

    {
      "scripts": {
        "dev": "node --nolazy --inspect app.js"
      }
    }
    
`.vscode/launch.json`

    {
      "version": "0.2.0",
      "configurations": [
        {
          "type": "node",
          "request": "launch",
          "name": "use npm",
          "runtimeExecutable": "npm",
          "runtimeArgs": ["run-script", "dev"],
          "console": "integratedTerminal",
          "port": 9229
        }
      ]
    }
    
### 使用nodemon

`.vscode/launch.json`

    {
       "version": "0.2.0",
      "configurations": [
        {
          "type": "node",
          "request": "launch",
          "name": "nodemon 启动",
          "runtimeExecutable": "nodemon",
          "program": "${workspaceFolder}/app.js",
          "restart": true,
          "console": "integratedTerminal",
          "internalConsoleOptions": "neverOpen"
        }
      ]
    }
