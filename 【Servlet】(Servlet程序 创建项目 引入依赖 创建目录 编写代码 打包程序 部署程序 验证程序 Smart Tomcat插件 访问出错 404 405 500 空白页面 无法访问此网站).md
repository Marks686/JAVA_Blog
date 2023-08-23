# Servlet

Servlet是Tomcat给java提供的原生的进行web开发的api.Servlet 是一种实现动态页面的技术.

静态页面就只是单纯的html,动态页面则是html + 数据.

## 第一个 Servlet 程序
先写个`hello world`,预期写个servlet程序,部署到Tomcat上,通过浏览器访问,得到hello world字符串.(史上最难hello world)
### 创建项目
此处要创建一个maven项目.maven是个"工程管理"工具.
1. 规范目录结构
2. 管理依赖(使用了啥第三方库,都给处理好)
3. 构建
4. 打包
5. 测试


![在这里插入图片描述](https://img-blog.csdnimg.cn/e7ac3672afe448e4a283d7406e9ba3d0.png)
如果首次使用maven,项目创建好后,会在下面出现读条,需要联网从中央仓库加载一些maven依赖.因为maven仓库在国外,网络不一定稳定,这里的读条可能会比较久.(长的可能会1个小时).如果报错失败,过两天再试试.

![在这里插入图片描述](https://img-blog.csdnimg.cn/343b0c9b214a48abb8f282faa672ab0d.png)
如下就是创建成功了.

![在这里插入图片描述](https://img-blog.csdnimg.cn/696418c5fd9044338da2901f371775f1.png)




> 注意此处的目录结构:

![在这里插入图片描述](https://img-blog.csdnimg.cn/9fdc2c9f3fae4cf580f82887627e049b.png)
`main`放业务代码.
`java`放Java代码.
`resources`放程序依赖文件(配置文件,数据文件,图片,图标,声音......)
`test`放测试代码.
`pom.xml`是一个maven项目总的配置文件.
### 引入依赖


引入ser

![在这里插入图片描述](https://img-blog.csdnimg.cn/c809ec8c3b1a4db299240845af70318a.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/5ff8c45881d144d3b51435ed916925b0.png)
3.1.0和Tomcat8是匹配的,点击3.1.0,将下代码赋值粘贴到pom.xml
![在这里插入图片描述](https://img-blog.csdnimg.cn/635c7b5a81da4997a71d3f32fd36537d.png)
下面代码有可能标红,是因为依赖还没引入成功,也有可能是网络的问题,刷新试试就可以了.

![在这里插入图片描述](https://img-blog.csdnimg.cn/ef917890800d4321b869b21af1b1b350.png)

###  创建目录
虽然maven已经帮我们自动创建了一些目录,但是还不够,此处需要maven开发一个web程序,还需要别的目录.

1. 在main目录下(和Java,resources 并列),创建一个`webapp`目录.
![在这里插入图片描述](https://img-blog.csdnimg.cn/47c7c280dad542b0af0e0b0b0710f739.png)

2. 在webapp下创建`WEB-INF`目录
![在这里插入图片描述](https://img-blog.csdnimg.cn/8f42318a66c9463a843cf03e318eddbb.png)
3. 再在WEB-INF 目录下创建一个`web.xml`文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/34a733e1517f41329a919ff015c68284.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b95c587fb14c40ffb523b1427981181f.png)


4. 给`web.xml`写入下代码即可
```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app>
    <display-name>Archetype Created Web Application</display-name>
</web-app>
```

但是我们发现下面有一段代码标红,其实这里不一定是错的,idea只是针对java代码能够进行比较准确的分析和判定.idea里的其他代码:包括不限于html,css,js,xml,json,sql...... 如果标红,是否是错的,是不好说的.判定代码是否正确的唯一标准就是看运行结果.
![在这里插入图片描述](https://img-blog.csdnimg.cn/b560b500a002422abf6d994f32b8fb73.png)





### 编写代码
在main目录下的java中创建新的类`HelloServlet`

![在这里插入图片描述](https://img-blog.csdnimg.cn/e66af84ea08e43fabdeeb9ea10858706.png)

让`HelloServlet`继承`HttpServlet`,`HttpServlet`是servlet api里提供的现成的类,写servlet代码一般都是继承`HttpServlet`这个类.
![在这里插入图片描述](https://img-blog.csdnimg.cn/4311f2dc536344fdbb90561a445b0ac8.png)
重写doGet方法:`HttpServlet`里已经有doGet方法了,此处是希望用子类的版本代替父类的.

![在这里插入图片描述](https://img-blog.csdnimg.cn/79808e933f19438fb30cdb6201ef0ae6.png)

`HttpServletRequest`这个参数表示一个HTTP请求.`HttpServletResponse`这个参数表示一个HTTP响应.
我们写的doGet方法不需要我们自己手动调用,而是交给Tomcat来调用,Tomcat收到get请求,就会触发doGet方法.Tomcat会构造好两个参数,req(TCP socket中读出来的字符串,按照HTTP协议解析)和resp(空的对象),我们就需要在doGet根据请求req,结合自己的业务逻辑构造出一个resp对象.




```java
//下面的注解把当前类和一个HTTP请求的路径关联起来
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
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

### 打包程序
把程序编译好(得到一些.clss文件),再把这些.class打成压缩包.此处打的是war包,它是Tomcat专属的用来描述webapp的程序.

借助maven点击即可:

![在这里插入图片描述](https://img-blog.csdnimg.cn/bb450dc1c60241cea100d591b5ae4a49.png)
双击package运行,如下就成功了:
![在这里插入图片描述](https://img-blog.csdnimg.cn/71316386fa604e2f9d87973709f626b6.png)
打包完毕,包会生成在target目录下.
![在这里插入图片描述](https://img-blog.csdnimg.cn/89979840de144af08d6903333e2eb85f.png)
默认情况下maven打的是jar包,此处我们需要微调一下.在`pom.xml`里进行修改:

![在这里插入图片描述](https://img-blog.csdnimg.cn/2195330c034e41fdb8c6b3e8da1cdd80.png)
然后继续打开maven打包就会出现war包.然后右键`open in`继续  `explore`
![在这里插入图片描述](https://img-blog.csdnimg.cn/5656ae13b64d4d40b9d3bda7ff663072.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/50195365a1194e22aad00ed98e5ff991.png)
接下来,就可以进行部署操作.
### 部署程序
把刚才打包好的war拷贝到tomcat的webapps目录中即可.

![在这里插入图片描述](https://img-blog.csdnimg.cn/ad91862be8a2481cae613aeb0eaf2f21.png)

然后启动Tomcat,启动成功,说明部署成功了.
![在这里插入图片描述](https://img-blog.csdnimg.cn/8adf005005974456aa9891389e8edbe2.png)

### 验证程序

打开浏览器输入URL(`127.0.0.1:8080/hello_servlet/hello`),访问写好的这个代码.



`hello_servlet`和`/hello`分别在如下路径找:

第一级路径对应我们打的war包的目录名,第一级路径也叫做`context path` / `application path` (这个目录就代表一个webapp网站)
![在这里插入图片描述](https://img-blog.csdnimg.cn/98bd442a068f4ff3be1342777f8beab5.png)


第二级路径如下,它也叫做`servlet path`
![在这里插入图片描述](https://img-blog.csdnimg.cn/ac67b8dc3ada4035ac1a24e5178bb150.png)





访问成功的页面如下:




![在这里插入图片描述](https://img-blog.csdnimg.cn/5de1eaf4aabf4460bc81e9b3f067cabc.png)
同时在服务器代码里也可以看到一个日志:
![在这里插入图片描述](https://img-blog.csdnimg.cn/2cc90a235a7f4e4e966e5eb337023770.png)



> 小结:
> 我们在浏览器地址栏中输入URL之后,浏览器就构造了一个对应的HTTP GET请求,发给了Tomcat,Tomcat就根据第一级路径,确定了具体的webapp,再根据第二级路径确定了具体调用哪个类,再通过GET/POST方法确定调用HelloServlet的哪个方法(doGet,doPost),此时Tomcat就执行对应的代码完成对应工作.



上述步骤是使用Servlet最朴素的步骤,当然也可以通过一些操作来简化上述过程.

打包和部署程序我们可以使用IDEA的Tomcat插件,把Tomcat集成到IDEA中,就省去手动打包,手动部署的过程,只需要按一下运行就可以自动打包部署.

## 安装 Smart Tomcat 插件


插件的位置:点击`File` 然后`Settings`点击`Plugins`
![在这里插入图片描述](https://img-blog.csdnimg.cn/e2a1a5722fe1460bb652b61acbb82046.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/1bbf06342da6443dae1333c466374bc1.png)
 搜索 `Smart Tomcat` 插件安装即可:
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/bb65831ce4e34c8d8afb3d38f717f18c.png)
安装完成后首次使用`Smart Tomcat`需要配置:
新增一个运行配置,点击新增配置,双击选择`Smart Tomcat`这个选项.




![在这里插入图片描述](https://img-blog.csdnimg.cn/2c38da3c7301413fac7d62b82ad449de.png)
然后设置Tomcat所在的路径,其他的默认,最后运行就行了.
![在这里插入图片描述](https://img-blog.csdnimg.cn/f6f4c06ab02443bda02745f950fb5ee4.png)
如果我们发现运行报错了,原因可能是13868号进程(Tomcat)占用了这个端口.

![在这里插入图片描述](https://img-blog.csdnimg.cn/f7a4ebfc7d764e1f812f780f0e1e9f71.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/309c919f36684e2a970acab9ee5fc40d.png)
将Tomcat关了,13868号进程自然就没了.
![在这里插入图片描述](https://img-blog.csdnimg.cn/8754bd7955c947d0a8903c4b616239ee.png)
如果没有出现报错就直接在网址栏输入:`127.0.0.1:8080/hello_servlet/hello`访问即可.这样我们的打包和部署操作就简化了.

`Smart Tomcat`工作原理不是自动把war包拷贝了,它是让Tomcat加载单个webapp运行.(让IDEA直接调用Tomcat,让Tomcat加载当前项目中的目录,这个过程其实没有打war包,也没有拷贝和解压缩的过程)


上述一套写代码方式很麻烦,后面学习了Spring,Spring Boot,Spring MVC再来实现类似的功能就方便了.



## 访问出错


### 404

1. 少写了 Context Path
2. 少写了 Servlet Path
3. Servlet Path写的和URL不匹配
4. web.xml 写错了

![在这里插入图片描述](https://img-blog.csdnimg.cn/16229fdb87ce4fa884735069f2a331a3.png)
### 405

405 表示对应的 HTTP 请求方法没有实现.
1. 没有实现 doGet 方法
![在这里插入图片描述](https://img-blog.csdnimg.cn/4807f36f692a43f1bcb470287062de74.png)

2. super.doGet没有删掉

![在这里插入图片描述](https://img-blog.csdnimg.cn/8a384c177c924c77ac69d8ac5278fe00.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2cd646b2a1674b6087eb9576b63cd359.png)

### 500


本质上就是代码抛出异常了,出现500的时候日志中会明确告诉异常调用栈,告诉是哪一行代码出现的异常.

![在这里插入图片描述](https://img-blog.csdnimg.cn/c7625db441ce4109bb140088d26615c5.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/1a299e5adf5744d89c5490fd72ce834d.png)


### 出现空白页面
![在这里插入图片描述](https://img-blog.csdnimg.cn/f213aaabf1d749d4bcc4c8edcb7ac4a9.png)




代码没写:

```java
resp.getWriter().write();
```


### 出现"无法访问此网站"

Tomcat没启动.
![在这里插入图片描述](https://img-blog.csdnimg.cn/fd2b85d53f9c457986dcd0cadc91ef3e.png)
