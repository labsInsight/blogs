前端系统基于vue+webpack架构，前期系统的布署，都是本地构建完成之后，通过ssh直接上传到web服务器对应目录进行文件的替换。现通过jenkins实现实现自动化部署，以下是jenkins自动化部署的流程：  
# 安装jenkins
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
# 配置jenkins全局信息
1. 到jenkins中心安装以下插件。
```
1.Publish Over SSH
2.GitLab Plugin
3.Email Extension Plugin
```
2. 在配置了jenkins的服务器中，安装git工具；并在***全局工具配置***中配置git命令。     
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-git.jpg)
3. 在***全局工具配置***中配置SSH目录信息，也就是你要布署的web服务器的域名、目录这些信息。    
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-ssh.jpg)
# 配置job
1. 新建一个job，开始我们的构建任务。    
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-job.jpg)
2. 进入job的配置，开始配置git的相关信息。    
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-gitlab.jpg)
3. 在job配置中，继续配置SSH信息，包括我们要从git的哪个source目录，拷贝到web服务器的dest目录。    
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/1-jenkins-buildssh.jpg)
4. 进入目录**/var/lib/jenkins/workspace**可以看到从git上拉到的代码，在这里**npm install**的话需要预告安装好node.js和npm    

```
sudo yum install nodejs
sudo yum install npm
```

5. 最后，直接点击job进行构建，即可查看构建结果：如果显示灯为绿色，说明构建成功，如果红色，则可以点击***console output***查看失败原因。



