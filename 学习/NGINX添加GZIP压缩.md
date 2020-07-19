网页压缩是一项由 WEB 服务器和浏览器之间共同遵守的协议，也就是说 WEB 服务器和浏览器都必须支持该技术，所幸的是现在流行的浏览器都是支持的，包括 IE、FireFox、Opera 等；服务器有 Apache 和 IIS 等。双方的协商过程如下：
1. 首先浏览器请求某个 URL 地址，并在请求的头 (head) 中设置属性 accept-encoding 值为 gzip, deflate，表明浏览器支持 gzip 和 deflate 这两种压缩方式WEB    
2. 服务器接收到请求后判断浏览器是否支持压缩，如果支持就传送压缩后的响应内容，否则传送不经过压缩的内容；    
3. 浏览器获取响应内容后，判断内容是否被压缩，如果是则解压缩，然后显示响应页面的内容。     

在实际的应用中我们发现压缩的比率往往在 3 到 10 倍，也就是本来 50k 大小的页面，采用压缩后实际传输的内容大小只有 5 至 15k 大小，这可以大大节省服务器的网络带宽，同时如果应用程序的响应足够快时，网站的速度瓶颈就转到了网络的传输速度上，因此内容压缩后就可以大大的提升页面的浏览速度。
    
我们的前端项目基于vue.js。开启压缩，需要前端代码和nginx同时配置。
# 前端配置
前端配置需要做以下几个步骤：
1. 修改config.js，将build中的gzip开启。

```
productionGzip: true,
```

2. 安装gzip插件。

```
npm install --save-dev compression-webpack-plugin
```

3. 正常编译，可以看到gzip已经开启。
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/4-gzip-vue.jpg)

# NGINX配置
nginx配置信息，只需在nginx配置文件nginx.conf中添加以下配置信息，然后重启nginx即可。

```
gzip on;
gzip_min_length  5k;
gzip_buffers     4 16k;
#gzip_http_version 1.0;
gzip_comp_level 3;
gzip_types       text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
gzip_vary on;

```
gzip 相关配置可放在 http{} 或 server{} 或 location{} 层级，若不同层级有重复设置优先级为 location{} > server{} > http{}

1. 开启 gzip压缩    
gzip on;
1. gzip http 版本    
gzip_http_version 1.0;
1. 禁止IE6进行gzip压缩（当然现在已经基本没有人使用IE6了）    
gzip_disable "MSIE [1-6]";
1. 压缩级别（1~9，一般为平衡文件大小和CPU使用，5是常用值，当然跟实际机器的情况有关）   
gzip_comp_level 5;
1. 最小压缩临界值（默认的是20字节）    
gzip_min_length 20;    
1. 压缩通过代理链接的客户端数据    
gzip_proxied any;    
1. 采用http协议版本 默认是1.1 ，对于1.0的请求不会压缩，如果设置成1.0，表示http1.0以上 的版本都会压缩。(如果使用了proxy_pass 进行反向代理，那么nginx和后端的 upstream server之间默认是用 HTTP/1.0协议通信的。)    
gzip_http_version 1.0;    
1. 代理缓存压缩和原始版本资源，避免客户端因Accept-Encoding不支持gzip而发生错误的现象（现在一般都采用gzip）   
gzip_vary on;
1. 压缩文件类型（默认总是压缩 text/html类型，其中特别说明的是application/javascript和text/javascript最好都加上，若页面script标签的type不同则有可能发生部分js文件不会压缩，默认type为application/javascript）
```
gzip_types application/atom+xml application/javascript application/json application/rss+xml application/vnd.ms-fontobject application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/svg+xml image/x-icon text/css text/plain text/javascript text/x-component;
```  
10. 如果有已经压缩的(.gz)或者提供静态文件服务，可以设置为on。如果不是这样，最好设置为off，因为这会造成额外的I/O开销。一个比较好的处理方式是放在location{}或server{}级别单独处理。   
gzip_static on;    
1. 压缩结果数据流存储所用空间，下面表示以8k为单位，按照原始数据大小以8k为单位的16倍申请内存。默认值是申请跟原始数据相同大小的内存空间去存储gzip压缩结果。    
gzip_buffers 16 8k;

# 结论
一切配置好之后，重启nginx，comnand+shift+r强制刷新浏览器，可以看到gzip已经添加。    

![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/4-gzip-chrome.jpg)

> 参考文档：    
脚本之家：https://www.jb51.net/article/120718.htm    
OpenCountry：https://www.cnblogs.com/yingsong/p/6047311.html
