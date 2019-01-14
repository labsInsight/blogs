# 安装 acme.sh  
我们需要把 acme.sh 安装到你的 home 目录下，并自动你创建 cronjob, 每天 0:00 点自动检测所有的证书, 如果快过期了, 需要更新, 则会自动更新证书. （因为Let's Encrypt的有效期只有3个月）  

1，升级系统SSL加密方式（根据系统决定）：

```
sudo yum -y update nss
```

2，进入用户目录：

```
/home/appusr
```
3，安装脚本：

```
curl https://get.acme.sh | sh
```
可以看到/home/appusr/.acme.sh目录下已经生成了对应的文件。     
    
4，创建 一个 bash 的 alias, 方便使用：

```
alias acme.sh=~/.acme.sh/acme.sh
```

# 生成证书
acme.sh 实现了 acme 协议支持的所有验证协议. 一般有两种方式验证: http 和 dns 验证。    
我们以域名activity.jinbaochuang.com和代码目录/data/www为例，讲解如何生成证书：    

1，http 方式需要在你的网站根目录下放置一个文件, 来验证你的域名所有权,完成验证. 然后就可以生成证书了

```
./acme.sh  --issue  -d activity.jinbaochuang.com  --webroot  /data/www/
```
2，dns 方式    
可参考官方文档，我们暂不介绍。
# 安装使用证书
1，拷贝证书    

```
acme.sh  --installcert  -d  activity.jinbaochuang.com --key-file  /home/appusr/nginx/nginx-1.13.7/conf/activity.jinbaochuang.key --fullchain-file /home/appusr/nginx/nginx-1.13.7/conf/fullchain.cer
```
2，配置nginx：

```
server {
        listen       443 ssl http2 default_server;
        listen       [::]:443 ssl http2 default_server;
        listen       80;
        server_name  www.test.ainnovating.com;
        root         /data/www;

        ssl_certificate "/etc/nginx/fullchain.cer";
        ssl_certificate_key "/etc/nginx/activity.jinbaochuang.key";
        ssl_session_cache shared:SSL:1m;
        ssl_session_timeout  10m;
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }
    }

```
完成配置之后，记得要重启nginx


> 参考文档： 
acme使用文档：https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E
