# HTTP协议

HTTP属于是应用层协议最广泛使用的协议之一.浏览器获取到网页就是基于http.HTTP就是浏览器和服务器之间的交互桥梁.HTTP 往往是基于传输层的 TCP 协议实现的. (HTTP1.0, HTTP1.1, HTTP2.0 均为TCP, HTTP3 基于 UDP实现)



我们平时打开一个网站, 就是通过 HTTP 协议来传输数据的.
![在这里插入图片描述](https://img-blog.csdnimg.cn/f590462f4cc546c484b432f4f18114f4.png)


HTTP协议的交互详细过程可以借助第三方的工具来看到的,称为"抓包"工具.

fiddler本质是一个代理程序,使用的时候需注意:
1. 可能和别的代理程序冲突.使用的时候要关闭其他的代理程序(包括一些浏览器插件)
2. 要想正确抓包,还需要开启https功能.https是基于http搞出来的进化版协议.当下互联网绝大部分的服务器都是https的.fiddler默认不能抓https的包,需要手动启用https并且安装证书.
![在这里插入图片描述](https://img-blog.csdnimg.cn/5bde22687e494e5c9b4912a39c06f53d.png)
下面的请求是我们最需要关注的,在请求百度的首页页面,其他的请求都是基于这个请求产生的.(百度主页里,其他代码又产生了别的请求)


![在这里插入图片描述](https://img-blog.csdnimg.cn/e5ff9630c8e841b4b998ae761a749618.png)
双击左侧请求列表就能够查看请求的详细情况:
![在这里插入图片描述](https://img-blog.csdnimg.cn/7b13c42cb7bb4036bba6b8fdb9feae02.png)
观察抓包结果当前http请求 是个行文本格式的数据.

查看响应:
![在这里插入图片描述](https://img-blog.csdnimg.cn/45ee81de6197456aaada11e1ad75f333.png)
响应数据本来也是文本,但是有的服务器会对响应进行压缩.(变成二进制了),压缩是为了节省带宽.

## HTTP 请求 (Request)

### URL
"网址" 其实就是说的 URL (Uniform Resource Locator 统一资源定位符).互联网上的每个文件都有一个唯一的URL，它包含的信息指出文件的位置以及浏览器应该怎么处理它.URL 的详细规则由 因特网标准RFC1738 进行了约定
![在这里插入图片描述](https://img-blog.csdnimg.cn/10a132e6a46b4abca987690827360c61.png)
`https` : 协议方案名. 常见的有 http 和 https, 也有其他的类型. (例如访问 mysql 时用的jdbc:mysql )
`user:pass` : 登陆信息. 现在的网站进行身份认证一般不再通过 URL 进行了. 一般都会省略
`www.example.jp` : 服务器地址. 此处是一个 "域名", 域名会通过 DNS 系统解析成一个具体的 IP 地址.(通过 ping 命令可以看到, www.example.jp 的真实 IP 地址 )
`端口号`: 端口号描述的是哪个程序
`/dir/index.html?`: 带层次的文件路径.找到程序管辖下的哪个文件.
`uid=1` : 查询字符串(query string). 本质是一个键值对结构. 键值对之间使用 & 分隔. 键和值之间使用 = 分隔.就是获取资源的时候带的参数.
`片段标识`: 此 URL 中省略了片段标识. 片段标识主要用于页面内跳转. (例如 Vue 官方文档:https://cn.vuejs.org/v2/guide/#%E8%B5%B7%E6%AD%A5, 通过不同的片段标识跳转到文档的不同章节)

URL最关键的四个部分:
1. 域名/IP
2. 端口号
3. 带层次的路径
4. 查询字符串

![在这里插入图片描述](https://img-blog.csdnimg.cn/2838bcdcdbd6478bb8e7f0249c5a4025.png)
一个URL这些部分有的是可以省略的.
`https://www.baidu.com`省略了端口,省略端口的时候,浏览器会提供默认端口.对于http来说默认端口80;对于https来说默认端口是443.
`/`也是路径,没省略只是有点短,代表"根目录",是HTTP服务器的根目录,HTTP服务器是系统上的一个进程,就委托这个服务器管理系统上的一个特定的目录即这个目录里的资源可以让外面进行访问.

###  "方法" (method)
![在这里插入图片描述](https://img-blog.csdnimg.cn/bcdbd2485f5546ada7d26cd2fc1a5b3d.png)
**GET 方法**
GET 是最常用的 HTTP 方法. 常用于获取服务器上的某个资源.在浏览器中直接输入 URL, 此时浏览器就会发送出一个 GET 请求.另外, HTML 中的 link, img, script 等标签, 也会触发 GET 请求



> 触发GET请求:

1. 在浏览器地址栏直接输入URL
2. html里的link,script,img,a......
3. 通过js来构造get请求


**POST方法**

典型就是登录跳转的时候会涉及到POST,还有就是上传文件.


HTTP请求可以认为是分成四个部分:
1. 首行
2. 请求头(header)
3. 空行
4. 正文(body)
如果是GET请求,没有body.如果是POST请求,一般有body.

在抓包结果中,post的body是程序员自定义的内容.

> GET和POST之间的典型区别:

1. GET也可以给服务器传递一些信息,GET传递的信息一般都是放在query string.POST传递消息则是通过body.
2. GET请求一般是用于从服务器获取数据,POST一半是用于个服务器提交数据.
3. GET通常会被设计成幂等的,POST不要求幂等.
4. GET可以被缓存,POST则一般不能不能被缓存.(把请求的结果保存下来,下次请求就不必请求了,直接取缓存结果)

### 请求"报头"(header)

如下就是一堆键值对,每一行是一个键值对,键和值之间使用:分割
![在这里插入图片描述](https://img-blog.csdnimg.cn/1bd697122b904c3595640436549c2fff.png)
上面的键值对都是HTTP实现定义好的,有特定的含义.
**Host**
描述了服务器所在的地址和端口.Host这里的地址和端口用来描述我们最终要访问的目标,这个内容大概率和URL中是一样的.
**Content-Length**
表示body中的数据长度

**Content-Type**
表示请求的 body 中的数据格式

**User-Agent**
User-Agent描述了浏览器和操作系统的版本.
如:
`Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Chrome/91.0.4472.77 Safari/537.36`

其中 Windows NT 10.0; Win64; x64 表示操作系统信息
AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36 表示浏览器信息.

**Referer**

当前页面的"来源"

**Cookie**
本质是浏览器给网页提供的本地存储数据的机制.网页默认是不允许访问你计算机的硬盘的.(保证安全).Cookie针对浏览器访问硬盘做出了明确的限制,Cookie就是通过键值对的方式来组织数据的.


Cookie中的数据是来自于服务器,HTTP响应的报头部分(Set-Cookie字段)把服务器要返回的数据放到Cookie里.服务器来决定浏览器的Cookie要存的内容.

Cookie可以认为是存在于浏览器中或者是存在硬盘中,Cookie在存的时候是按照浏览器+域名来细分的.不同的浏览器各自存各自的Cookie,同一个浏览器不同的域名对应不同的Cookie.Cookie里的内容不光是键值对,同时还有过期时间.

客户端同一时刻是有很多的,通过Cookie可以保存当前用户使用中间状态,当客户端访问浏览器的时候就会自动把Cookie的内容带入到请求中,服务器就能够知道现在客户端是什么样子了.

### 请求"正文"(body)

正文中的内容格式和 header 中的 Content-Type 密切相关.

1) application/x-www-form-urlencoded

2) multipart/form-data


3) application/json


## HTTP 响应详解
![在这里插入图片描述](https://img-blog.csdnimg.cn/cfa0436ca318413f8efea04f1a4c3016.png)
响应也是由四个部分组成;
1. 首行
2. header
3. 空行,表示header结束标记
4. 正文
### "状态码" (status code)
描述了这次响应的结果(成功?失败?)
![在这里插入图片描述](https://img-blog.csdnimg.cn/c8c5b84dca1c4f8198e17b9c2a26ce94.png)

`200`成功了
`404 Not Found`访问的资源不存在
`403 Forbidden`访问被拒绝(没有权限)
`302 Moved temporarily`重定向(类似于呼叫转移)
![在这里插入图片描述](https://img-blog.csdnimg.cn/499fc49816744c2c9b93a56d042e308e.png)
`500`服务器内部错误(服务器代码抛异常)
`504 gateway timeout`请求超时(响应时间太久,浏览器等不及了)

![在这里插入图片描述](https://img-blog.csdnimg.cn/59bd2405c03e4792a1c7837473ee0b86.png)


> HTTP协议里可以自定义的:

1. URL中的路径
2. URL中的query string
3. header中的键值对
4. header中的cookie的键值对
5. body

### 响应"报头"(header)

响应报头的基本格式和请求报头的格式基本一致.类似于 Content-Type , Content-Length 等属性的含义也和请求中的含义一致.


`text/html` : body 数据格式是 HTML
`text/css` : body 数据格式是 CSS
`application/javascript` : body 数据格式是 JavaScript
`application/json` : body 数据格式是 JSON
![在这里插入图片描述](https://img-blog.csdnimg.cn/891e1711faab49f1b22b4e7f50f33d18.png)
[关于Content-Type的详细文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types)

HTTP协议报文格式
![在这里插入图片描述](https://img-blog.csdnimg.cn/ed3fb0e333814119bbae7e22fbdd1c1c.png)

## 构造 HTTP 请求
### 通过 form 表单构造 HTTP 请求

form (表单) 是 HTML 中的一个常用标签. 可以用于给服务器发送 GET 或者 POST 请求
#### form 发送 GET 请求


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <!-- 表单标签 允许用户和服务器之间交互数据 -->
    <!-- 要求提交的数据以键值对的结构来组织 -->
    <form action="https://www.sogou.com" method="get">
        <input type="text" name="studentName ">
        <!-- <input type="submit" 构造了一个特殊的提交按钮 value属性描述了按钮中的文本 -->
        <!-- 点击这个按钮就会触发改form表单的 "提交操作" 也就是构造http请求发给服务器-->
        <input type="submit" value="提交">
    </form>
</body>
</html>
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/828d0408e61a431484258a7063eecad7.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/f9c536f2d94b41f19ac2dbaee0468a84.png)



#### form 发送 POST 请求


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <!-- 表单标签 允许用户和服务器之间交互数据 -->
    <!-- 要求提交的数据以键值对的结构来组织 -->
    <form action="https://www.sogou.com" method="post">
        <input type="text" name="studentName ">
        <!-- <input type="submit" 构造了一个特殊的提交按钮 value属性描述了按钮中的文本 -->
        <!-- 点击这个按钮就会触发改form表单的 "提交操作" 也就是构造http请求发给服务器-->
        <input type="submit" value="提交">
    </form>
</body>
</html>
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/fa27c861deeb4eb0af4e2d1e710b8533.png)

### 通过  ajax表单构造 HTTP 请求
ajax 全称 Asynchronous Javascript And XML, 是 2005 年提出的一种 JavaScript 给服务器发送HTTP 请求的方式.
特点是可以不需要 刷新页面/页面跳转 就能进行数据传输
#### 发送 GET 请求


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <!-- 引入 jquery -->
    <script src="https://code.jquery.com/jquery-3.6.3.min.js"></script>
    <script>
        $.ajax({
            type:'get',
            url: 'https://www.sogou.com?studentName=zhangsan',
            // 此处success 就声明了一个回调函数 就会在服务器
            // 响应返回到浏览器的时候触发该回调
            // 正是此处的回调体现了 "异步"
            success: function(data){
                // data则是响应的正文部分
                console.log("当服务器返回的响应到达浏览器之后,浏览器触发该回调,通知到咱们的代码中")
            }
        });
        console.log("浏览器立即往下执行后续代码");
    </script>
</body>
</html>
```


# HTTPS

HTTP + 安全层(SSL),SSL是用来加密的协议.在网络世界中存在运营商劫持的问题,所以就需要加密协议来解决此类问题.
![在这里插入图片描述](https://img-blog.csdnimg.cn/2564bc2d3d23423abb4faeeac67b7fed.png)
网络上如果明文传输数据是非常危险的,就需要加密才能保证安全.
## 加密
加密就是把明文(要传输的信息)进行一系列变换,生成密文.解密就是把密文再进行一系列变换,还原成明文.在这个加密和解密的过程中, 往往需要一个或者多个中间的数据, 辅助进行这个过程, 这样的数据称为密钥.


### 对称加密



> 加密中最简单有效的一种方法:"对称加密"

a(明文) + key = b(密文) 加密过程
a(密文) + key = b(明文) 解密过程
同一个秘钥,既可以用来加密也可以用来解密就叫做堆成秘钥.对称加密的安全性的前提是,秘钥不能被黑客知道.

![在这里插入图片描述](https://img-blog.csdnimg.cn/102038fc627a4254a0ebf915824a56e9.png)
黑客不知道秘钥是什么,即使黑客截获了加密的数据也不知道是什么意思.


服务器同一时刻其实是给很多客户端提供服务的. 这么多客户端, 每个人用的秘钥都必须是不同的(如果是相同那密钥就太容易扩散了, 黑客就也能拿到了). 因此服务器就需要维护每个客户端和每个密钥之间的关联关系.


在客户端和服务器建立连接的时候, 双方协商确定这次的密钥是什么.

![在这里插入图片描述](https://img-blog.csdnimg.cn/e8fd23f370d44044b68a081fe7513b37.png)
客户端生成秘钥,发给服务器.此时由于秘钥刚刚生成,服务器还不知道,此处的秘钥只能明文传输.秘钥可能就被黑客给截获了.

因此密钥的传输也必须加密传输,但是要想对密钥进行对称加密, 就仍然需要先协商确定一个 "密钥的密钥". 此时密钥的传输再用对称加密就行不通了,就需要引入非对称加密. 


### 非对称加密

生成一对秘钥,明文 + 公钥 = 密文(使用公钥加密); 密文 + 私钥 = 明文(使用私钥解密).也可以反着用.公钥可以公开,私钥是私藏的.

![在这里插入图片描述](https://img-blog.csdnimg.cn/2d610559932248ca81f694c15d2946f3.png)

1. 客户端在本地生成对称密钥, 通过公钥加密, 发送给服务器.
2. 由于中间的网络设备没有私钥, 即使截获了数据, 也无法还原出内部的原文, 也就无法获取到对称密钥
3. 服务器通过私钥解密, 还原出客户端发送的对称密钥. 并且使用这个对称密钥加密给客户端返回的响应数据.
4. 后续客户端和服务器的通信都只用对称加密即可. 由于该密钥只有客户端和服务器两个主机知道,其他主机/设备不知道密钥即使截获数据也没有意义. 

客户端为了确定公钥不是黑客伪造的,引入了证书的概念.

## 证书

为解决中间人攻击的关键,在于让客户端能够辨别当前响应(公钥)是服务器真实的公钥.引入一个"证书"(本质上是引入第三方的公证机构)

服务器(网站)在设立之初,就需要去专门的认证机构申请证书,审核通过就会给其颁发证书,服务器生成的公钥也就包含在这个证书中了,客户端像服务器请求公钥的时候,此时就不是请求单单一个公钥,而是把整个证书都请求过来.客户端拿到证书后,就可以对证书进行校验.如果发现证书是无效的,浏览器就会直接弹框警告.

![在这里插入图片描述](https://img-blog.csdnimg.cn/047765d0d6384e35acaeb12a2e04cca5.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/09f196d2d35d4bb38de28f01d7bd8012.png)
