# Tomcat
Tomcat 是一个 HTTP 服务器.HTTP 协议就是 HTTP 客户端和 HTTP 服务器之间的交互数据的格式. HTTP 服务器我们可以通过 Java Socket 来实现. 而 Tomcat 就是基于 Java 实现的一个开源免费,也是被广泛使用的 HTTP 服务器.

## 下载Tomcat
官方网站:[https://tomcat.apache.org/download-80.cgi](https://tomcat.apache.org/download-80.cgi)
![在这里插入图片描述](https://img-blog.csdnimg.cn/4036275758fc48fe9fdc0752f3e71d51.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/94993d3b325b40f180b2e547fb156e84.png)

## 启动Tomcat

在 `bin` 目录中, 双击 `startup.bat` 即可启动 Tomcat 服务器. 
![在这里插入图片描述](https://img-blog.csdnimg.cn/1f5669f7a8f34c9aa72ea9e5d9118df1.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/9f766294451041ceb0aaa4e3aef933de.png)
接下来访问Tomcat 的默认欢迎页面.Tomcat必须是运行的才能访问.网址栏输入:`127.0.0.1:8080`



![在这里插入图片描述](https://img-blog.csdnimg.cn/503ddda985084cd091f6e3e11c00b796.png)
## 简单部署

将之前写的前端代码部署到服务器上,将文件复制粘贴进`webapps`然后重启Tomcat.再在网址栏输入`http://127.0.0.1:8080/blog/blog_edit.html` 这个页面只能内网访问.
![在这里插入图片描述](https://img-blog.csdnimg.cn/4b82a9b4b91c4fc6bc7f9ce2eec51f9b.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/0ca2029e3b5441ab8e62d879a07ef1be.png)
第一级路径(blog),叫做application path / context path.


# 基于Tomcat进行网站后端开发


[https://editor.csdn.net/md/?articleId=132256218](https://editor.csdn.net/md/?articleId=132256218)
