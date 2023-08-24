# Servlet API

Servlet中常用的API


## HttpServlet

![在这里插入图片描述](https://img-blog.csdnimg.cn/36d05ead5ebd45eea769113949696d78.png)
实际开发的时候主要重写 doXXX 方法, 很少会重写 init / destory / service



`destory` 服务器终止的时候会调用.

```java
//下面的注解把当前类和一个HTTP请求的路径关联起来
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    @Override
    public void init() throws ServletException {
        //重写init 插入自己"初始化"相关的逻辑
        System.out.println("init");
    }
    @Override
    public void destroy() {
        System.out.println("destroy");
    }
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //这个只是在服务器的控制台打印
        System.out.println("hello world");
        //要想把hello world返回到客户端,需要使用下面的代码
        //getWriter 会得到一个Writer对象
        resp.getWriter().write("hello world");
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/aaf410618dee49a69df9a03b57c61aa5.png)
上面的destroy能不能被执行到有待商榷,如果是通过Smart Tomcat 的停止按钮,这个操作本质上是通过Tomcat的8005端口,主动停止,可以触发destroy.如果是直接杀进程,此时可能就来不及执行destroy就没了.因此不太推荐使用destroy.


`service`每次收到http请求就出触发(路径匹配)

```java
@Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.service(req, resp);
    }
```


init / destory / service 这三个方法是HttpServlet中最关键的三个方法.


> Servlet的声明周期是怎么回事?

1. 开始的时候执行init
2. 每次收到请求执行service
3. 销毁之前执行destroy


### 处理请求

```java
@WebServlet("/method")
public class MethodServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doGet");
        resp.getWriter().write("doGet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doPost");
        resp.getWriter().write("doPost");
    }

    @Override
    protected void doPut(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doPut");
        resp.getWriter().write("doPut");
    }

    @Override
    protected void doDelete(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("doDelete");
        resp.getWriter().write("doDelete");
    }
}

```
我们刷新页面只有doGet请求,想要获得别的请求可以利用postman这个软件来实现:

![在这里插入图片描述](https://img-blog.csdnimg.cn/fcf89eb799b549de84cb50fb39f2432d.png)
也可以通过ajax来构造请求:先在webapp目录下创建test.html.
![在这里插入图片描述](https://img-blog.csdnimg.cn/8f26a8db52e74c3dbbe76c244374a76e.png)

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <!-- 使用这个页面来构造ajax请求 -->
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.4/jquery.min.js"></script>
    <script>
        $.ajax({
            type: 'get',
            url: 'method',
            success: function(body, status){
                console.log(body);
            }
        });
    </script>
</body>
</html>
```
然后重启服务器,在网址栏输入:`http://127.0.0.1:8080/hello_servlet/test.html`然后刷新在控制台就能看到doGet请求了.想要获取别的请求,直接在代码中改就可以了,需要注意的是我们在编写Servlet代码的时候,每次修改代码,要及得重新启动服务器.
![在这里插入图片描述](https://img-blog.csdnimg.cn/8042cbe271624f809a98318c1b061bbd.png)

此处写的路径是相对路径,相对路径的基准目录是该html所在的路径.此处写method就相当于在`http://127.0.0.1:8080/hello_servlet`的基础上,再拼上一个`method`
![在这里插入图片描述](https://img-blog.csdnimg.cn/90d4f4bd1950403697b125f6dcf0abd1.png)

绝对路径的写法:

![在这里插入图片描述](https://img-blog.csdnimg.cn/a89fc1c9aa62400eb6e811f3bb848776.png)


## HttpServletRequest

这个类表示的是HTTP请求.这个对象是Tomcat自动构造的,Tomcat会实现监听端口,接受连接,解析请求,构造请求对象等一系列工作.
下面的表格就是一些典型的方法:



|方法  | 描述 |
|--|--|
| String getProtocol() | 返回请求协议的名称和版本
 String getMethod()|返回请求的 HTTP 方法的名称，例如，GET、POST 或 PUT
 | String getRequestURI() | 从协议名称直到 HTTP 请求的第一行的查询字符串中，返回该请求的 URL 的一部分
|String getContextPath()  | 返回指示请求上下文的请求 URI 部分 |
|String getQueryString()  | 返回包含在路径后的请求 URL 中的查询字符串 |
|Enumeration getParameterNames()  | 返回一个 String 对象的枚举，包含在该请求中包含的参数的名称  |
|String getParameter(Stringname)  | 以字符串形式返回请求参数的值，或者如果参数不存在则返回null |
| String[] getParameterValues(String name) | 返回一个字符串对象的数组，包含所有给定的请求参数的值，如果参数不存在则返回 null |
|Enumeration getHeaderNames()  | 返回一个枚举，包含在该请求中包含的所有的头名 |
| String getHeader(String name) | 以字符串形式返回指定的请求头的值 |
| String getCharacterEncoding() | 返回请求主体中使用的字符编码的名称 |
|String getContentType()  | 返回请求主体的 MIME 类型，如果不知道类型则返回 null |
| int getContentLength() | 以字节为单位返回请求主体的长度，并提供输入流，或者如果长度未知则返回 -1 |
| InputStream getInputStream() | 用于读取请求的 body 内容. 返回一个 InputStream 对象 |



### 打印请求信息

 

```java
@WebServlet("/showRequest")
public class ShowRequestServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //设置响应的 content-type 告诉浏览器 响应的body里的格式是啥样的
        resp.setContentType("text/html");
        //搞个 StringBuilder 把这些api的结果拼起来,统一写回到响应中
        StringBuilder stringBuilder = new StringBuilder();
        stringBuilder.append(req.getProtocol());
        stringBuilder.append("<br>");
        stringBuilder.append(req.getMethod());
        stringBuilder.append("<br>");
        stringBuilder.append(req.getRequestURI());
        stringBuilder.append("<br>");
        stringBuilder.append(req.getContextPath());
        stringBuilder.append("<br>");
        stringBuilder.append(req.getQueryString());
        stringBuilder.append("<br>");

        //获取到 header 中所有的键值对
        Enumeration<String> headerNames = req.getHeaderNames();
        while (headerNames.hasMoreElements()){
            String headerName = headerNames.nextElement();
            stringBuilder.append(headerName + ": " + req.getHeader(headerName));
            stringBuilder.append("<br>");
        }

        resp.getWriter().write(stringBuilder.toString());
    }
}
```
在浏览器通过 URL `http://127.0.0.1:8080/hello_servlet/showRequest` 访问, 可以看到:
![在这里插入图片描述](https://img-blog.csdnimg.cn/3283239d1aa448c585f31ac56da2b3cf.png)

### 前端给后端传参

**1.通过GET里的query string**

在前端给后端传两个数字,一个是同学的studentId,一个是classId

```java
@WebServlet("/getParameter")
public class GetParameterServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //预期浏览器会发一个形如 /getParameter?studentId=10&classId=20 这样的请求
        // 借助req 里的 getParameter方法就能拿到 query string 中的键值对内容
        //getParameter 得到的是 String 类型的结果
        String studentId = req.getParameter("studentId");
        String classId = req.getParameter("classId");
        resp.setContentType("text/html");
        resp.getWriter().write("studentId = " + studentId + " classId = " + classId);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/55b3b3ab54ed43cab5ab8e8977d3156b.png)
如果key在query string中不存在,此时就返回值就是null



**2.通过POST,借助form表单的方式**
对于前端是form表单这样格式的数据(也是键值对,和query string的格式一样,只是这部分内容在body中),后端还是使用getParameter来获取.
> 前端代码:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <form action="postParameter" method="post">
        <input type="text" name="studentId">
        <input type="text" name="classId">
        <input type="submit" value="提交">
    </form>
</body>
</html>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/9afd9c73f8bb46d2a6450dc1738543e4.png)

> 后端代码:

```java
@WebServlet("/postParameter")
public class PostParameterServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String studentId = req.getParameter("studentId");
        String classId = req.getParameter("classId");
        resp.setContentType("text/html");
        resp.getWriter().write("studentId = " + studentId + " classId = " + classId);
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ff5a6e9394f24df0a9b15777e9707dad.png)

使用getParameter 既可以获取到query string 中的键值对,也可以获取到form表单构造的body中的键值对.

![在这里插入图片描述](https://img-blog.csdnimg.cn/2bdb3b0db1134ee0bdf655bc94add6c7.png)



**3.POST里的json**

json是一种非常主流的数据格式,也是键值对结构.
![在这里插入图片描述](https://img-blog.csdnimg.cn/67e46e82ea6a4468ac0ef1d30f6d7690.png)
我们可以把body按照这个格式来组织.前端可以通过ajax的方式来构造出这个内容,更简单的方法使用postman直接构造.


![在这里插入图片描述](https://img-blog.csdnimg.cn/7daa9e41674743df95218f2ed9eb5953.png)
>后端代码:

```java
@WebServlet("/postParameter2")
public class PostParameter2Servlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //通过这个方法来处理 body 为 json 格式的数据
        //直接把 req 对象里 body 完整的读取出来
        //getInputStream
        // 在流对象中读多少字节取决于Content-Length
        int length = req.getContentLength();
        byte[] buffer = new byte[length];

        InputStream inputStream = req.getInputStream();
        inputStream.read(buffer);
        //把这个字节数组构造成 String 打印出来
        String body = new String(buffer,0,length,"utf8");
        System.out.println("body =" + body);
        resp.getWriter().write(body);
    }
}
```


然后在postman那块点击Send请求,服务器也相对应打印出了从body中读出的数据.
![在这里插入图片描述](https://img-blog.csdnimg.cn/9fce21eec8934cc2b2c7afd3f8fab39f.png)

> 总结:借助postman构造出post请求,body就是json数据,请求到达Tomcat,Tomcat解析成req对象,再通过req.getInputStream读取body内容,把读出的结果构造成响应往回写,最后postman客户端收到了对应的结果.


当前通过json传递数据,但是服务器这边只是把整个body读出来,没有按照键值对的方式来处理,还不能根据key获取value,为了解决这个问题,我们可以使用jackson第三方库.

通过maven引入第三方库:

![在这里插入图片描述](https://img-blog.csdnimg.cn/bfc1809670c04d64a261631de004deb0.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/82d5d66cae344c9d8043aec19ff4ff41.png)



将所选的内容粘贴到`pom.xml`当中:

![在这里插入图片描述](https://img-blog.csdnimg.cn/bd82b7794c1445b3833fbc624d603fe3.png)

然后上面的后端代码就需要改变:


```java
class Student{
    public int studentId;
    public int classId;
}
@WebServlet("/postParameter2")
public class PostParameter2Servlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        Student student = objectMapper.readValue(req.getInputStream(),Student.class);
        System.out.println(student.studentId + ", " + student.classId);
    }
}
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/afa1be5e1f8e478dbb950cf90f437fec.png)
