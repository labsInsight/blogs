&emsp;&emsp;团队初期，h5系统的布署，都是本地构建完成之后，通过ssh直接上传到web服务器对应目录进行文件的替换。这样的操作有不少问题，就是容易误操作。譬如一不小心敲错地址，就布署到错误的目录了，如果是布到一个不存在的目录那还好，要是一不小心覆盖了已经存在的重要的目录，那问题就严重了。就算是有自信的程序员，认为自己永不犯错，但效率不高，却也是不争的事实。    
&emsp;&emsp;为此，我们通过jenkins引入了自动化部署平台。至于什么是jenkins有啥用，我们就不多说了，同学们可以自行搜索。下面，我们直接进入平台的搭建过程。
1. 如果是全新的机器，需要先安装java开发环境。可以通过***yum install java*** 命令直接安装即可。如果已经安装过java，直接跳到第2步。
2. 安装jenkins稳定版本。
```
一，添加源：sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo    
二，导入key：sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key    
三，yum安装 jenkins：sudo yum install jenkins
```    
3. 启动jenkins。(修改端口号不是必选步骤，除非系统端口8080受限)
```
一，修改端口号。通过命令vi /etc/sysconfig/jenkins进入配置文件，查找JENKINS_PORT，修改JENKINS_PORT为需要启动的端口号。
二，输入service jenkins restart启动jenkins。
三，启动无误后，在浏览器中输入http://+ip+端口号即可正常访问。
```
4. 到jenkins中心安装以下插件。
```
1.Publish Over SSH
2.GitLab Plugin
3.Email Extension Plugin
```
5. 在配置了jenkins的服务器中，安装git工具；并在***全局工具配置***中配置git命令。     
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-git.jpg)
6. 在***全局工具配置***中配置SSH目录信息，也就是你要布署的web服务器的域名、目录这些信息。    
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-ssh.jpg)
7. 新建一个job，开始我们的构建任务。    
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-job.jpg)
8. 进入job的配置，开始配置git的相关信息。    
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-gitlab.jpg)
9. 在job配置中，继续配置SSH信息，包括我们要从git的哪个source目录，拷贝到web服务器的dest目录。    
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-buildssh.jpg)
10. 另外，如果有需要对git的代码进行编译之类的操作，可以在***构建***配置上进行脚本编译；如果有需要在web服务器上进行操作，例如备份，则可以在***构建后操作***配置上进行脚本编译。
11. 最后，直接点击job进行构建，即可查看构建结果：如果显示灯为绿色，说明构建成功，如果红色，则可以点击***console output***查看失败原因。



