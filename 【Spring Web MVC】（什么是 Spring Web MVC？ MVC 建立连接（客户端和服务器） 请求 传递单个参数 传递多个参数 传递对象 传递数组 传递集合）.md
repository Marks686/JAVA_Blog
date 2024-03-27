## 什么是 Spring Web MVC？

Spring Web MVC is the original web framework built on the Servlet API and has been included in the Spring Framework from the very beginning. The formal name, “Spring MVC”，comes from the name of its source module (spring-webmvc)

Spring Web MVC 是基于 Servlet API 构建的原始 Web 框架，从⼀开始就包含在 Spring 框架中。它的正式名称“Spring Web MVC”来⾃其源模块的名称(Spring-webmvc)，但它通常被称为“Spring MVC”

## MVC


MVC是 Model View Controller 的缩写，它是软件⼯程中的⼀种软件架构设计模式，它把软件系统分为模型、视图和控制器三个基本部分



MVC就是把一个项目分成了三部分

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7c925ee837844ff1857ca1a7164e05c4.png)
MVC是一种思想，Spring进行了实现，称为Spring MVC

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c908bad11a544d6bba196f82ea87491d.png)
SpringBoot是创建SpringMVC项目的一种方式。

当前阶段，MVC的概念又发生了一些变化，后端开发人员不涉及前端页面的开发，所以也就没有view层，所以view又有了一层解释，之前返回的是视图，现在返回的是视图所需要的数据。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7447e69800824a20b71a62423195aa1a.png)
### 建立连接（客户端和服务器）
@RequestMapping就是建立了路由映射




@RequestMapping可以修饰方法也可以修饰类

访问地址：类的路径+方法路径

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/8f9274e53f364d3cb85d371c7237bbf8.png)

`http://127.0.0.1:8080/hello/sayhello`
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c3017d706b494f63ad628c003bcd6e89.png)
@RequestMapping支持get和post


注解如果没有写属性名，默认是value，如下源码：

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/49dace0e7e0f49b0a9b873b54dcef0a7.png)
如果多了属性，比如我们设置必须使用get请求（限制请求方式）：

```java
@RequestMapping(value = "/sayhi",method = RequestMethod.GET)
public String sayHi(){
    return "hi,SpringBoot!!!";
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/619e39b55d4b4a7e81c45ce3098dd6c4.png)



![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6362274f6bed4eedbc64d9e2edf4cd59.png)
使用postman查看post请求：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/fb2892dd778d41c0af93fa9295c7ca30.png)

### 请求

请求就是学习如何传参（下命令）



#### 传递单个参数


```java
@RequestMapping("/param")
@RestController
public class ParamController {
    @RequestMapping("/m1")
    public String m1(String name){
        return "接收到参数name：" + name;
    }
}

```
没有传参数
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ad50a6a455e34ee6a12c668b44ef6a55.png)
传了参数

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/9fb05ced02c04143aeb8ff7132cd6392.png)

spring就接收到参数。



只需要保持名称一样，底层逻辑：从请求的参数中，获取参数名为name的值，并给name赋值



#### 传递多个参数

```java
@RequestMapping("/param")
@RestController
public class ParamController {
    @RequestMapping("/m2")
    public String m2(String name,Integer age){
        return "接收到参数name：" + name + "age: " + age;
    }
}
```




![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/283ce39b7b8e4a7db5f0ca53e50a12ad.png)
传递多个参数的时候也是直接发送，参数的顺序可以调换。
此时将Integer换成int，正常赋值，请求是正常的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4a51098aee3046129c162075a51d61b4.png)

如果将不传int这个参数，就出问题了
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a325790dc27c4ad197f2f7f5f47c2be3.png)

查看日志：age是一个int类型的，不能转化成null值，因为我们没有传值，我们只传了个name，基本类型int不能转成null值，日志建议我们使用基本类型的包装类型。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/da32a5c739d64353a48e6e14138bf449.png)

如果使用基本类型必须要传参。



#### 传递对象


传递一个对象：

```java
public class Person {
    Integer id;
    String name;
    Integer age;

    @Override
    public String toString() {
        return "Person{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0364ff0e1e8b491b97fdb1fc7c936c5d.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/5e93850dae114a31bcbf01eb482811a9.png)

`http://127.0.0.1:8080/param/m4?name=zhangsan&age=19&id=5`
传递对象Spring会帮我们进行关系的映射，开发中接口的参数通常定义为对象。



修改后端参数：

```java
@RequestMapping("/m5")
    public String m4(@RequestParam("name")String username){
        return "接收到参数name：" + username;
    }
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/29baf71fd9254c57a392b643d24537b0.png)
如果把参数改成username就没有拿到数据了


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ebd1c4a9408f4b66b2e8cd9827b19467.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/2da468f97a4044569bb8e6e218b2fb52.png)
日志信息告诉我们下面的参数是必传参数;
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e315cd8bd0b64a5fa1c59fd9fdb0b59d.png)
若不想是必须传入的参数，只需要后面加上参数required，并且值为false。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ed486a464de4473aa5c9ce7949f6854f.png)
再次测试发现值为null，
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f995bfc1ed2a4c59a3c02032acdde13b.png)

所以，如果进行了重命名，就必须要使用@RequestMapping注解里的名字。

#### 传递数组
```java
@RequestMapping("/m6")
    public String m6(String[] arrayParam){
        return "接收到参数name：" + Arrays.toString(arrayParam);
    }
```
传递数组，当我们请求中，同一个参数有多个时，浏览器就会帮我们给封装成一个数组。
`http://127.0.0.1:8080/param/m6?arrayParam=zhangsan&arrayParam=lisi&arrayParam=wangwu`

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1c53b47c770b4a16bfca976f81cf234e.png)

还可以用逗号分割：
`http://127.0.0.1:8080/param/m6?arrayParam=zhangsan,lisi,wangwu`
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/80f41f2cb25542fb81098dab2b8a241a.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a997131a3602488a84eed91542adde61.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/9a6144bec64544eb9f37d5b7b5db8aae.png)

#### 传递集合


```java
@RequestMapping("/m7")
    public String m7(List<String> listParam){
        return "接收到参数name：" + listParam + "长度：" + listParam.size();
    }
```

我们进行传递集合发现报了个服务器错误500
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/887bfbf4a03f4a4c9fa8c961442dacff.png)

查看日志：意思是默认给封装成了数组而不是list接口，如果我们想用list需要使用注解。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/597e7049d60d42f7ada7848938f29ddf.png)


```java
@RequestMapping("/m7")
    public String m7(@RequestParam List<String> listParam){
        return "接收到参数name：" + listParam + "长度：" + listParam.size();
    }
```
此时就可以了

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/cb195e6c95934f798eb21ddb4d61795a.png)


