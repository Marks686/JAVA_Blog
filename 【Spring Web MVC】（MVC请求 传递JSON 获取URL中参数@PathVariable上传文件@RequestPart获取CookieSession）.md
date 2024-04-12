# MVC

## 请求


### 传递JSON

JSON：JavaScript Object Notation 【JavaScript 对象表⽰法】
JSON是⼀种轻量级的数据交互格式. 它基于 ECMAScript (欧洲计算机协会制定的js规范)的⼀个⼦集，采⽤完全独⽴于编程语⾔的⽂本格式来存储和表⽰数据。
简单来说：JSON就是⼀种数据格式, 有⾃⼰的格式和语法, 使⽤⽂本表⽰⼀个对象或数组的信息, 因此**JSON本质是字符串**. 主要负责在不同的语⾔中数据传递和交换.


```java
public class JSONUtils {
 	private static ObjectMapper objectMapper = new ObjectMapper();
	 public static void main(String[] args) throws JsonProcessingException {
		 Person person = new Person();
		 person.setId(5);
		 person.setName("zhangsan");
		 person.setPassword("123456");
		 //对象转为JSON字符串
		 String jsonStr = objectMapper.writeValueAsString(person);
		 System.out.println("JSON字符串为:"+jsonStr);
		 //JSON字符串转为对象
		 Person p = objectMapper.readValue(jsonStr,Person.class);
		 System.out.println("转换的对象id:"+p.getId()+",name:"+p.getName()+",passwo
	 }
}
```
使⽤ObjectMapper 对象提供的两个⽅法, 可以完成对象和JSON字符串的互转。
writeValueAsString: 把对象转为JSON字符串。
readValue: 把字符串转为对象。



JSON优点：
1. 简单易⽤: 语法简单，易于理解和编写，可以快速地进⾏数据交换
2. 跨平台⽀持: JSON可以被多种编程语⾔解析和⽣成, 可以在不同的平台和语⾔之间进⾏数据交换和
传输
3. 轻量级: 相较于XML格式, JSON数据格式更加轻量级, 传输数据时占⽤带宽较⼩, 可以提⾼数据传输
速度
4. 易于扩展: JSON的数据结构灵活，⽀持嵌套对象和数组等复杂的数据结构，便于扩展和使⽤。、
5. 安全性: JSON数据格式是⼀种纯⽂本格式，不包含可执⾏代码, 不会执⾏恶意代码，因此具有较⾼
的安全性。



接收JSON对象，需要使用`@RequestBody`注解



```java
@RequestMapping("/m8")
    public String M8(@RequestBody Person person){
        return "接收的数据person：" + person.toString();
    }
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6fb1d68d206b4cc69c62cffc9303f38a.png)
### 获取URL中参数@PathVariable



```java
@RequestMapping("/m9/{userId}")
    public String m9(@PathVariable Integer userId){
        return "userId: " + userId;
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/48cabd407ea74396bd5a5911cf799636.png)

格式要满足：

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/620a6b70fd614d3f8c24374bfdaf6e7d.png)
传递多个参数：

```java
@RequestMapping("/m9/{userId}/{name}")
    public String m9(@PathVariable Integer userId,@PathVariable String name){
        return "userId: " + userId + "name: " + name;
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/9eb8578d794c4e9291b87c0237faa549.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/18563ca849a14b189bc82d08b91e28e6.png)

### 上传文件@RequestPart



```java
@RequestMapping("/m10")
    public String m10(@RequestPart MultipartFile file){
        System.out.println(file.getOriginalFilename());
        return "success";
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/404dd28831e24c1dab05eb0db3b743ce.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/82ff2d2776ef49d8a120c838fcc46eb6.png)

上传图片：

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d22c85e9c963461dbddce12d5aeaf84c.png)



### 获取Cookie/Session




Http是无状态的，http没有记忆功能，现在请求和过一会请求，同样的请求参数，得到的结果是一样的。


Cookie 客户端机制
Session 服务端机制





**Session**

会话


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/96ee9d19527d49bb8345913b1b613d6f.png)


Cookie 和 Session的区别
1. Cookie 是客⼾端保存⽤⼾信息的⼀种机制. Session 是服务器端保存⽤⼾信息的⼀种机制.
2. Cookie 和 Session之间主要是通过 SessionId 关联起来的, SessionId 是 Cookie 和 Session 之间的
桥梁
3. Cookie 和 Session 经常会在⼀起配合使⽤. 但是不是必须配合.
完全可以⽤ Cookie 来保存⼀些数据在客⼾端. 这些数据不⼀定是⽤⼾⾝份信息, 也不⼀定是SessionId。
Session 中的sessionId 也不需要⾮得通过 Cookie/Set-Cookie 传递, ⽐如通过URL传递.。


之前获取cookie：

```java
@RequestMapping("/getCookie")
    public String getCookie(HttpServletRequest request){
        Cookie[] cookies = request.getCookies();
        if(cookies != null){
            Arrays.stream(cookies).forEach(cookie -> {
                System.out.println(cookie.getName()+ ":" + cookie.getValue());
            });
        }
        return "获取cookie成功";
    }
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d92c16ef75df4645a99e2fc405de7e0a.png)
使用注解获取cookie：

```java
@RequestMapping("/getCookie2")
    public String getCookie2(@CookieValue String bite){
        return "cookie存取的值bite：" + bite;
    }
```

使用注解只能一个一个cookie去拿。


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1832edee95294c5e9972e5807d40591b.png)


### 获取Session

理解session：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1a603799c7984d5bb7c03132e52496ab.png)
之前获取session的方式：


```java
@RequestMapping("/getSession")
    public String getSession(HttpServletRequest request){
        HttpSession session = request.getSession(false);
        if (session != null){
            String username = (String) session.getAttribute("username");
            return "登录用户" + username;
        }
        return "session 为空";
    }
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/285130442f14436c9cb67907db51dbe8.png)


添加session：

```java
@RequestMapping("/setSession")
    public String setSession(HttpServletRequest request){
        HttpSession session = request.getSession();
        session.setAttribute("username","zhangsamn");
        return "success";
    }
```
观察cookie的变化：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d8e2c2d367ba4f1c82ecd6ccd788dd6a.png)
此时再去getSession：


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/2e313ee749174f269c5ff49c65ddc2ad.png)


注解方式获取session：


```java
@RequestMapping("/getSession2")
    public String getSession2(@SessionAttribute String username){
        return "username:" + username;
    }
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/bd668799a3e74b4e995bfcfcfe56af97.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/8583987df9424042bb60b26c06b1ba43.png)
这是一个Spring框架中的网络请求日志。在2024年3月31日的晚上7点25分16秒，一个名为“nio-8080-exec-2”的线程或会话遇到了一个关于缺少会话属性的警告。具体来说，请求中缺少了一个名为“username”的字符串类型属性，导致了RequestBindingException异常。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f92e49dfddd24cfb834af202b8aeec93.png)

修改代码：

```java
@RequestMapping("/getSession2")
    public String getSession2(@SessionAttribute(required = false) String username){
        return "username:" + username;
    }
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1772abf9fed14c5282dcf333827ee31f.png)
此时我们去设置session：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a4eda599dabb4b7fa0b93fd045e6175a.png)
成功了之后，我们再获取session:


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b0e43e0c1d584d7082a3d5fb0634fcaa.png)

还有第三种方式来获取session：



```java
@RequestMapping("getSession3")
    public String getSession3(HttpSession session){
        String username = (String) session.getAttribute("username");
        return "登录用户：" + username;
    }
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7cc6ff4d6174430d95f15f9b5b7119df.png)




![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ba4189fb1d7c42af8a78eb95fdc34295.png)


### 获取Header

之前的方式：

```java
@RequestMapping("/getHeader")
    public String getHeader(HttpServletRequest request){
        String userAgent = request.getHeader("User-Agent");
        return "userAgent: "+ userAgent;
    }
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/5267aa9b28ad4f539af2b4bc40d6e4dc.png)
此时就拿到了浏览器的信息。


注解方式：



```java
@RequestMapping("/getHeader2")
    public String getHeader2(@RequestHeader("User-Agent")String userAgent){
        return "userAgent: "+ userAgent;
    }
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/35bc351de68c405c9b99ed472174bb05.png)

