

### 网络基础

为了理解HTTP,我们有必要事先了解一下TCP/IP协议族，通常我们使用的网络（包括互联网）是在TCP/IP协议族的基础上运作的，我们的HTTP属于它内部的一个子集。

#### TCP/IP协议族
计算机与网络设备要相互通信，双方就必须基于相同的方法。

#### TCP/IP分层管理
TCP/IP协议族按层次分为以下四层：
- 应用层
- 传输层
- 网络层
- 数据链路层

> 这样的分层是有好处的。比如，如果互联网只由一个协议统筹，某个地方需要改变设计时，就需要吧所有的部分整体替换掉。而分层的话只需要把变动的层替换掉就好了。把各层之间的接口部分规划好之后，每个层次的内部设计就能够自由改动了

各层简介：
- 应用层：
    - 应用层决定了向用户提供应用服务时通信的活动
    - TCP/IP协议族内预存了各类通用的应用服务。比如FTP、DNS，当然，HTTP协议也处于该层。
- 传输层：
    - 传输层对上层应用层，提供处于网络连接中的两台计算机之间的数据传输。
    - 在传输层有两个性质不同的协议：TCP(传输控制协议)、UDP(用户数据协议)
- 网络层（又名网络互连层）
- 链路层（又名数据链路层，网络接口层）


#### TCP/IP通信传输流
利用TCP/IP协议族进行网络通信时，会通过分层书序与对方进行通信。发送端是从应用层往下走，接收端则从链路层网上走。

比如说一次HTTP请求：
1. 首先作为发送端的客户端在应用层（HTTP协议）发出一个想看某个Web页面的HTTP请求
2. 接着，为了传输方便，在传输层（TCP协议）把从应用层处收到的数据（HTTP请求报文）进行分割，并在各个报文上打上标记序号及端口号后转发给网络层。
3. 在网络层（IP协议），增加作为通信目的的MAC地址后转发给链路层。这样一来，发往网络的通信请求就准备齐全了。
4. 接收端的服务器在链路层就收到数据，按序往上层发送，一直到应用层。当传输到应用层，才能算真正接收到由客户端发送来的HTTP请求。

> 发送端在层与层之间传输数据时，每经过一层时必定会被打上一个该层所属的首部信息。反之，接收端在层与层传输数据时，每经过一层时会把对应的首部消去。

#### 与HTTP关系密切的协议：IP、TCP和DNS
IP协议位于网络层，作用是把各种数据包传输给对方。

TCP位于传输层，提供可靠的字节流服务，对大数据分割成报文段为单位的数据包进行管理，且TCP协议能够确认数据最终是否发送给对方

DNS服务于HTTP协议同样位于应用层，它提供域名到IP地址之间的解析服务。

#### URI和URL
URI是统一资源标识符，通常为请求报文中method后的路径
URL是统一资源定位符，就是浏览器地址栏输入的内容

#### HTTP报文
请求报文由以下内容构成
- 请求方法
- 请求URI
- 协议版本
- 可选的请求首部字段和内容实体

响应报文由以下内容构成
- 协议版本
- 状态码（标识请求成功或失败的数字代码）
- 用以解释状态码的原因短语
- 可选的响应首部字段以及实体主体

#### HTTP协议的特点
- HTTP是一种不保存状态，即无状态协议，虽然HTTP是无状态的，但是为了实现保持状态的功能引入了Cookie技术
- HTTP协议规定，请求从客户端发出，最后服务器端响应该请求并返回

#### 告知服务器意图的HTTP方法
- GET：获取资源
- POST: 传输实体主体
- PUT: 传输文件
- HEAD: 获得报文首部，与GET类似，但是只返回头，用于确认URI的有效性及资源的更新日期
- DELETE: 用来删除文件，与PUT相反
- OPTIONS: 询问支持的方法，用来查询针对URI指定资源支持的方法
- TRACE: 追踪路径
- CONNECT: 要求用隧道协议连接代理，主要使用SSL和TLS协议把通信内容加密后经网络隧道传输

#### 持久连接
在最初的版本中，每一次请求都需要断开一次TCP连接，但是由于传输的内容很多，持续进行TCP连接会耗费很多时间，为了解决这个问题，HTTP/1.1和一部分的HTTP/1.0想出了持久连接，也称为HTTP keep-alive的方法。

持久连接的特点就是只要任意一端没有明确提出断开连接，连接就会保持

#### 无状态的HTTP如何维持状态
服务端响应头中加入Set-Cookie

#### 返回结果的HTTP状态码
- 2XX 成功
    - 200 成功了
    - 204 成功了，但是没有返回值
    - 206 进行了范围请求，响应报文中Content-Range指定范围的实体内容
- 3XX 重定向
    - 301 永久重定向，表示请求的资源已被分配了新的URI
    - 302 临时性重定向
    - 303 303与302类似，但是303明确要求用户使用GET方式获取数据
    - 304 缓存，服务器资源没有改变
    - 307 与302类似，但是强制使用POST
- 4XX 客户端错误
    - 400 表示请求的报文中可能存在语法错误
    - 401 表示发送的请求需要有通过HTT认证的认证信息
    - 403 没有权限访问资源
    - 404 资源没找到，服务器上没有资源
- 5XX 服务器错误
    - 501 服务器端在执行请求时发生了错误
    - 503 服务器发生超载或停机维护

#### 确保安全的HTTPS
HTTP的缺点
- 通信使用明文（不加密），内容可能被窃听
    - 通信的加密: HTTP协议和SSL组合使用，就被称作HTTPS
    - 内容的加密: 直接加密传输内容，服务端和客户端均有加密解密功能，但仍然有被篡改的风险
- 不验证通信方的身份，因此可能遭遇伪装
    - 有可能WEB服务器是伪装的，返回的可能并不是服务器的真实意图
    - 有可能客户端是伪装的，想要攻击服务器
    - 无法确认客户端是否有权限，有些资源可能只想给特定的人群展示
    - 无法判断请求来自何方
    - 即使无意义的请求也照单全收，无法阻止海量的DOS
- 无法证明报文的完整性，所以有可能已遭篡改

SSL不但提供了通信加密，还提供了一种被称为证书的手段，通常证书又第三方值得信赖的机构颁发，用于证明服务器和客户端都是真实存在的

HTTP + 加密 + 认证 + 完整性保护 = HTTPS


#### WEB的攻击技术
其实简单的HTTP协议本身并不存在安全性问题，因为协议本身几乎不会成为攻击的对象。应用HTTP协议的服务器和客户端，以及运行在服务器上的Web应用等资源才是攻击的目标。

##### 针对WEB应用的攻击
- 以服务器为主的主动攻击：攻击者通过直接访问Web应用，把攻击代码传入，由于该模式是直接针对服务器上的资源进行攻击，因此攻击者需要能够访问到那些资源。
> 在主动攻击中比较有代表性的就是SQL注入攻击
- 以服务器为目标的被动攻击
    - 攻击者诱使用户触发已设置好的陷阱，陷阱会自动发送已经嵌入攻击代码的HTTP请求
    - 当用户不知不觉中招后，用户的浏览器就会触发这个陷阱
    - 中招后用户浏览器会把含有攻击代码的HTTP请求发送给作为攻击者目前的Web应用，运行攻击代码
    - 执行完攻击代码，存在安全漏洞的Web应用会成为攻击者的跳板，可能会导致Cookie等个人信息被窃取，登录状态中的用户权限遭到恶意滥用等后果
> 被动攻击模式中具有代表性的攻击就是跨站脚本攻击和跨站点请求伪造

##### 因输出值转义不完全引发的安全漏洞
- 客户端验证: 通常客户端的验证只是为了尽早的预防和辨识输入错误，提升用户体验
- Web应用端（服务器端）的验证
    - 输入值验证
    - 输出值验证

因输出值转义不完全常见的攻击
- 跨站脚本攻击
    - 利用虚假输入表单骗取用户个人信息
    - 利用脚本窃取用户的Cookie
    - 在被害者不知情情况下发送恶意请求
    - 显示伪造的文章或图片




#★web应用攻击技术

对web应用的攻击模式分为主动攻击和被动攻击。

##1.以服务器为目标的主动攻击
主动攻击是攻击者通过直接访问web应用，把攻击代码注入的攻击模式。由于该模式是直接针对服务器上的资源进行攻击，因此攻击者需要能够访问到那些资源。
主动攻击模式里具有代表性的就是SQL注入攻击和OS命令注入攻击

##2.以服务器为目标的被动攻击
被动攻击是指利用圈套策略执行攻击代码的攻击模式。在被动攻击过程中，攻击者不直接对web应用访问发起攻击。

被动攻击通常的攻击模式为：
步骤①：攻击者诱使用户触发已设置好的陷阱，而陷阱会自动发送已嵌入攻击代码的HTTP请求
步骤②：当用户不知不觉中招以后，用户的浏览器就会触发这个陷阱
步骤③：用户触发陷阱后的浏览器会把含有攻击代码的HTTP请求发送到作为攻击目标的web应用，执行攻击代码。
步骤④：执行完攻击代码后，存在安全漏洞的web应用会成为攻击者的跳板，可能导致用户所持的cookie等个人信息被窃取、登录状态中的用户权限被滥用的后果。

被动攻击里具有代表性的就是跨站脚本攻击（XSS）和跨站点请求伪造（CSRF）

**a.跨站脚本攻击（XSS）**
跨站脚本攻击是指存在安全漏洞的web应用在用户的浏览器上执行非法的HTML标签或者JavaScript脚本的一种攻击。动态创建的HTML部分可能隐藏着安全漏洞，这样攻击者就有可能编写脚本设下陷阱，用户在自己的浏览器上运行时一不小心就会受到攻击。
跨站脚本攻击可能存在以下影响：

利用虚假输入表单骗取用户个人信息。
利用脚本窃取用户的cookie值，在被害者不知情的情况下帮助攻击者发送恶意请求。
显示伪造的文章或图片。
**b.跨站点请求伪造（CSRF）**
跨站点请求伪造（CSRF）是一种被动的网络攻击方式。也就是攻击者伪造被攻击者的请求，而且被攻击者还没有察觉到。
举个例子说明：
A在银行要转一笔钱给朋友B，A可以通过一个HTTP（URL地址后面是for=B）请求把钱转给B。通常情况下，该请求到达网站后，服务器会验证请求是否来自一个合法的session，并且该session的用户A已经登录。攻击者C也在该银行有账户，于是他伪造了一个请求（URL地址后面是for=C），但是如果直接访问，服务器肯定会识别出当前登录用户是C而不是A，不能接受请求。于是通过CSRF攻击方式，将此链接伪造在广告下，诱使A自己点这个链接，那么请求就会随着A浏览的cookiie一起发送到银行，而此时A正在登录状态或者刚刚登陆不久session还没有过期，当服务器发现session中有A的信息，接收请求，攻击就成功了。

##<font color=red size=5>现在防御CSRF的策略</font>
1.验证Referer
referer携带请求来源，从示例可以看出，受害者发送非法请求肯定不是在银行的界面，所以在服务器通过验证Referer是不是bank.example开始就可以了，这个方法简单粗暴。
2.在请求参数中加token验证
要抵御跨站点请求伪造就要设置一个黑客伪造不了的东西。我们可以在请求参数中加一个随机token，在服务器验证这个token，通过即销毁重设。
参考别人的实现：
首先定义token为key-value结构，因为很多情况会从不同的地方访问同一个请求，如果是单一的数据结构，第一个请求生成token后还没来得及发送请求，第二个又请求生成token就会把第一个冲掉，从而导致连续的验证失败。所以，我们要通过请求源将token隔离起来。这里我将请求地址摘要后作为token的key,用GUID作为token的value。
3.在HTTP请求头中自定义属性并验证
这个方法和上面那个类似，也是设置token，只是把token设置为HTTP请求头中的自定义属性。

通过XMLHttpRequest可以一次性给所有该类请求的HTTP头加上token 属性，但是XMLHttpRequest请求通常用于Ajax方法对局部页面的异步刷新，比较有局限性；而且通过XMLHttpRequest请求的地址不会被记录到浏览器的地址栏，一方面不会通过Referer泄露token，另一方面会导致前进，后退，刷新，收藏等操作失效，所以还是慎用。

虽然上面介绍了几种方法，但现在还没有一种完美的解决方案，但是通过Referer和Token方案结合起来使用，也能很得有效CSRF攻击。




##### TCP和UDP的区别是什么
TCP：面向连接、传输可靠(保证数据正确性,保证数据顺序)、用于传输大量数据(流模式)、速度慢，建立连接需要开销较多(时间，系统资源)
UDP：面向非连接、传输不可靠、用于传输少量数据(数据包模式)、速度快
TCP和UDP协议的一些应用例子：
TCP一般用于文件传输（FTP HTTP 对数据准确性要求高，速度可以相对慢），发送或接收邮件（POP IMAP SMTP 对数据准确性要求高，非紧急应用），远程登录（TELNET SSH 对数据准确性有一定要求，有连接的概念）等等；UDP一般用于即时通信（QQ聊天 对数据准确性和丢包要求比较低，但速度必须快），在线视频（RTSP 速度一定要快，保证视频连续，但是偶尔花了一个图像帧，人们还是能接受的），网络语音电话（VoIP 语音数据包一般比较小，需要高速发送，偶尔断音或串音也没有问题）等等。


##### TCP和UDP属于计算机网络中的哪一层
网际层协议：IP协议、ICMP协议、ARP协议、RARP协议
传输层协议：TCP协议、UDP协议
应用层协议：FTP、Telnet、SMTP、HTTP、RIP、NFS、DNS

##### HTTPS是什么
HTTPS即加密的HTTP，HTTPS并不是一个新协议，而是HTTP+SSL（TLS）。原本HTTP先和TCP（假定传输层是TCP协议）直接通信，而加了SSL后，就变成HTTP先和SSL通信，再由SSL和TCP通信，相当于SSL被嵌在了HTTP和TCP之间
