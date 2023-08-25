# Servlet API
## HttpServletResponse


|方法  |描述  |
|--|--|
|void setStatus(int sc)  |为该响应设置状态码  |
|void setHeader(String name, String value)  | 设置一个带有给定的名称和值的 header. 如果 name 已经存在,则覆盖旧的值 |
| void addHeader(String name, String value) | 添加一个带有给定的名称和值的 header. 如果 name 已经存在,不覆盖旧的值, 并列添加新的键值对  |
|void setContentType(String type)  |设置被发送到客户端的响应的内容类型  |
|void setCharacterEncoding(String charset)  | 设置被发送到客户端的响应的字符编码（MIME 字符集）例如，UTF-8 |
|void sendRedirect(String location)  | 使用指定的重定向位置 URL 发送临时重定向响应到客户端 |
| PrintWriter getWriter() | 用于往 body 中写入文本格式数据 |
| OutputStream getOutputStream() |用于往 body 中写入二进制格式数据  | 


###  重定向

`void sendRedirect(String location)`

>后端代码:
```java
@WebServlet("/redirect")
public class RedirectServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.sendRedirect("https://www.sogou.com");
    }
}
```


网址栏输入:`http://127.0.0.1:8080/hello_servlet/redirect`,刷新页面如下:
![在这里插入图片描述](https://img-blog.csdnimg.cn/01c0cfd98cc84fe4812cf6b26069adf3.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/f1fad125cb6242ac85e0268c986f527d.png)
上述代码也可以这样写:

```java
@WebServlet("/redirect")
public class RedirectServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //resp.sendRedirect("https://www.sogou.com");
        resp.setStatus(302);
        resp.setHeader("Location","https://www.sogou.com");
    }
}
```


## 服务器版表白墙

**表白墙前端页面:**

>messageWall.html:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>表白墙</title>
    <style>
        /* 通配符选择器 选中页面所有的元素 */
        *{
            /* 消除浏览器的默认样式 */
            margin:0;
            padding:0;
            box-sizing: border-box;
        }
        .container{
            width: 600px;
            margin:20px auto;
        }
        h1{
            text-align: center;
        }
        p{
            text-align: center;
            margin:20px auto;
            color: #666;
        }
        .row{
            /* 开启弹性布局 */
            display: flex;
            height: 40px;
            /* 水平居中 */
            justify-content: center;
            /* 垂直居中 */
            align-items: center;
        }
        .row span{
            width: 80px;
        }
        .row input{
            width: 200px;
            height: 25px;
        }
        .row button{
            width: 280px;
            height: 30px;
            color: white;
            background-color: orange;
            /* 去掉边框 */
            border: none;
            border-radius: 5px;
        }
        /* 点击的时候有个反馈 */
        .row button:active{
            background-color: gray;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>表白墙</h1>
        <p>输入内容后点击提交,信息会显示到下方表格中</p>
        <div class="row">
            <span>谁:</span>
            <input type="text">
        </div>

        <div class="row">
            <span>对谁:</span> 
            <input type="text">
        </div>

        <div class="row">
            <span>说:</span>
            <input type="text">
        </div>
        <div class="row">
            <button id = "submit">提交</button>
        </div>

        <div class="row">
            <button id = "revert">撤销</button>
        </div>
    </div>

    <script>
        // 实现提交操作 点击提交 就把用户输入的提交到页面显示
        // 点击时 获取三个输入框中的文本内容
        // 创建新的div.row 把内容构造到这个div中即可
        let containerDir = document.querySelector('.container');
        let inputs = document.querySelectorAll('input');
        let button = document.querySelector('#submit');
        button.onclick = function(){
            //1. 获取内容
            let from = inputs[0].value;
            let to = inputs[1].value;
            let msg = inputs[2].value;
            // 2.构造新 div
            if(from == '' || to == '' || msg == ''){
                return;
            }
            let rowDiv = document.createElement('div');
            rowDiv.className = 'row';
            rowDiv.innerHTML = from + ' 对 ' + to + ' 说: ' + msg;
            containerDir.appendChild(rowDiv);
            // 3.清空之前的输入框操作
            for(let input of inputs){
                input.value = '';
            }
        }

        let revertButton = document.querySelector('#revert');
        revertButton.onclick = function(){
            //删除最后一条消息
            //选中所有的row 找出最后一个row删除即可
            let rows = document.querySelectorAll('.row');
            containerDir.removeChild(rows[rows.length - 1]);
        }
    </script>
</body>
</html>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ec43ecf97b5542dab0b15f305e5dd0fb.png)

上面的表白墙页面有非常严重的问题:
1. 如果我们刷新页面/关闭页面重开,之前输入的消息就不见了.
2. 如果一个机器上输入了数据,第二个机器上是看不到的(这些数据都是在本地浏览器中).

针对上面的问题解决思路:让服务器来存储用户提交的数据,由服务器保存,当有新的浏览器打开页面的时候,就从服务器获取数据.

### 准备工作 

1) 创建 maven 项目.
2) 创建必要的目录 webapp, WEB-INF, web.xml
![在这里插入图片描述](https://img-blog.csdnimg.cn/0a889ce5341f44d0924169616fdb0a89.png)
>web.xml代码:

```xml
<!DOCTYPE web-app PUBLIC
        "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
        "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app>
    <display-name>Archetype Created Web Application</display-name>
</web-app>
```

3) 调整 pom.xml,引入依赖生成 war 包, 以及 war 包名字

![在这里插入图片描述](https://img-blog.csdnimg.cn/b0e4b385b44a4222a39aaec2c0783857.png)




>pom.xml代码:
```html
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>MessageWall</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- https://mvnrepository.com/artifact/javax.servlet/javax.servlet-api -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.14.2</version>
        </dependency>
        
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
    </dependencies>

</project>
```


4) 把实现的表白墙前端页面拷贝到 webapp 目录中

![在这里插入图片描述](https://img-blog.csdnimg.cn/120741c7cecb4fd5b4bdd139ccfbfe3a.png)



### 约定前后端交互接口

**1.点击提交.浏览器把表白信息发到服务器**

>请求:POST /message

```
POST /message
{
    from: "黑猫",
    to: "白猫",
    message: "喵"
}
```

>响应: HTTP/1.1 200 OK


**2.页面加载.浏览器从服务器获取到表白信息**

>请求:GET/message



>响应:HTTP/1.1 200 OK

```
[
   {
        from: "黑猫",
        to: "白猫",
        message: "喵"
   },
   {
 from: "黑狗",
        to: "白狗",
        message: "汪"
   },
]
```


约定没有固定的强制要求,只要保证能够实现必要的需求即可,此处目的就是为了前端代码和后端代码能对上号.
### 实现服务器端代码

```java
class Message {
    public String from;
    public String to;
    public String message;
}
@WebServlet("/message")
public class MessageServlet extends HttpServlet {
    //使用这个 List 变量保存所有消息
    private List<Message> messageList = new ArrayList<>();

    private ObjectMapper objectMapper = new ObjectMapper();
    
    //向服务器提交数据
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 把 body 中的内容读取出来了, 解析成了个Message对象
        Message message = objectMapper.readValue(req.getInputStream(),Message.class);
        // 保存
        messageList.add(message);
        //设定状态码,不设置默认也是200
        resp.setStatus(200);
    }

    //从服务器获取数据
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 显式告诉浏览器 数据是json格式 字符集是utf8的
        resp.setContentType("application/json; charset=utf8");
        objectMapper.writeValue(resp.getWriter(),messageList);
    }
}
```

### 调整前端页面代码


![在这里插入图片描述](https://img-blog.csdnimg.cn/ed2e76f96ec444a3bec92c6370c7773c.png)



post是点击提交按钮的时候发起,get是页面加载的时候发起.

引入jQuery:

```html
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.4/jquery.min.js"></script>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/a0beb523e5de4aedb6539db738455dda.png)

>[新增] 给服务器发起 post 请求, 把上述数据提交到服务器这边.

```
let body ={
    "from": from,
    "to": to,
    "message": msg
}
let strBody = JSON.stringify(body);
console.log("strBody: " + strBody);
$.ajax({
    type: 'post',
    url: 'message',
    data: strBody,
    contentType: "application/json; charset=utf8",
    success: function(body){
        console.log("数据发布成功");
    }
});
```
重启服务器,打开fiddler然后测试可以看到下面的信息:
![在这里插入图片描述](https://img-blog.csdnimg.cn/3ef1e587f34e44b799ebd8ae2db25fab.png)

>[新增] 在页面加载的时候, 发送 GET 请求,从服务器获取到数据并添加到页面中.

```
$.ajax({
    type: 'get',
    url: 'message',
    success: function(body){
        //此处拿到的 body 就是一个 js 的对象数组
        //本来服务器返回的是一个 json 格式的字符串 但是 jquery 的 ajax 可以自动识别
        //自动帮我们把 json 字符串转成 js 对象数组
        //接下来遍历数组, 把元素取出来 构造到页面中即可
        let containerDiv = document.querySelector('.container')
        for (let message of body){
            // 针对每个元素构造一个 div
            let rowDiv = document.createElement('div');
            rowDiv.className = 'row message';
            rowDiv.innerHTML = message.from + ' 对 ' + message.to + ' 说: ' + message.message;
            containerDir.appendChild(rowDiv);
        }
    }
})
```
增加完代码后提交数据,重新加载页面,数据还可以显示.
![在这里插入图片描述](https://img-blog.csdnimg.cn/45d85f83d58e44d88e837fde578d2e70.png)
此时数据是保存在服务器内存中的,重启页面数据还在,重启服务器数据就丢失了,再下步我们就需要让服务器把数据存储到数据库中.


### 数据存入数据库
**1) 在 pom.xml 中引入 mysql 的依赖**


```
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.49</version>
</dependency>
```


**2) 创建 messages 表**
```
create table message (`from` varchar(20), `to` varchar(20), message varchar(1024));
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ea9ad0b037dc4f60b63f8a2bc07e12a2.png)


**3) 创建 DBUtil 类**



通过这个类,把数据库连接过程封装一下,此处把这个类作为一个工具类,提供static方法 供其他代码来调用.

```java
//通过这个类,把数据库连接过程封装一下,此处把这个类作为一个工具类,提供static方法 供其他代码来调用.
public class DBUtil {
    //静态成员跟随类对象 类对象在整个进程中只有唯一一份
    //静态成员相当于也是唯一的实例 (单例模式 饿汉模式)
    private static DataSource dataSource = new MysqlDataSource();

    static {
        //使用静态代码块 针对dataSourse初始化
        ((MysqlDataSource)dataSource).setURL("jdbc:mysql://127.0.0.1:3306/java106?characterEncoding=utf8&useSSL=false");
        ((MysqlDataSource)dataSource).setUser("root");
        ((MysqlDataSource)dataSource).setPassword("123456");
    }
    //通过这个方法建立连接
    public static Connection getConnection() throws SQLException {
        return dataSource.getConnection();
    }
    //通过这个方法断开连接,释放资源
    public static void close(Connection connection, PreparedStatement statement, ResultSet resultSet){
        //此处 try catch 分开写更好 避免前面的异常导致后面的代码不能被执行
        if (resultSet != null){
            try {
                resultSet.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (statement != null){
            try {
                statement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (connection != null){
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```


**4) 修改 load 和 save 方法, 改成操作数据库**


>save 方法代码:




```java
// 提供一对方法
// 往数据库中寸一条消息
private void save(Message message){
    //JDBC 操作
    Connection connection = null;
    PreparedStatement statement = null;
    try {
        // 1.建立连接
        connection = DBUtil.getConnection();
        // 2.构造 SQL 语句
        String sql = "insert into message values(?,?,?)";
        statement = connection.prepareStatement(sql);
        statement.setString(1,message.from);
        statement.setString(2,message.to);
        statement.setString(3,message.message);
        // 3.执行sql
        statement.executeUpdate();
    } catch (SQLException e) {
        e.printStackTrace();
    } finally {
        // 4. 关闭连接
        DBUtil.close(connection,statement,null);
    }
}
```







> load 方法代码:

```java
// 从数据库取所有消息
private List<Message> load(){
    List<Message> messageList = new ArrayList<>();
    Connection connection = null;
    PreparedStatement statement = null;
    ResultSet resultSet = null;
    try {
        // 1.和数据库建立连接
        connection = DBUtil.getConnection();
        // 2.构造sql
        String sql = "select * from message";
        statement = connection.prepareStatement(sql);
        // 3.执行 sql
        resultSet = statement.executeQuery();
        // 4.遍历结果集合
        while (resultSet.next()){
            Message message = new Message();
            message.from = resultSet.getString("from");
            message.to = resultSet.getString("to");
            message.message = resultSet.getString("message");
            messageList.add(message);
        }
    } catch (SQLException e){
        e.printStackTrace();
    } finally {
        // 5. 释放资源 断开连接
        DBUtil.close(connection,statement,resultSet);
    }
    return messageList;
}
```


> 整体代码:

```java
class Message {
    public String from;
    public String to;
    public String message;
}
@WebServlet("/message")
public class MessageServlet extends HttpServlet {
    //使用这个 List 变量保存所有消息
    //private List<Message> messageList = new ArrayList<>();

    private ObjectMapper objectMapper = new ObjectMapper();
    //向服务器提交数据
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 把 body 中的内容读取出来了, 解析成了个Message对象
        Message message = objectMapper.readValue(req.getInputStream(),Message.class);
        // 保存
        //messageList.add(message);
        save(message);
        //设定状态码,不设置默认也是200
        resp.setStatus(200);
    }
    //从服务器获取数据
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 显式告诉浏览器 数据是json格式 字符集是utf8的
        resp.setContentType("application/json; charset=utf8");
        //objectMapper.writeValue(resp.getWriter(),messageList);
        List<Message> messageList = load();
        String jsonResp = objectMapper.writeValueAsString(messageList);
        System.out.println("jsonResp: " + jsonResp);
        resp.getWriter().write(jsonResp);
    }
    // 提供一对方法
    // 往数据库中寸一条消息
    private void save(Message message){
        //JDBC 操作
        Connection connection = null;
        PreparedStatement statement = null;
        try {
            // 1.建立连接
            connection = DBUtil.getConnection();
            // 2.构造 SQL 语句
            String sql = "insert into message values(?,?,?)";
            statement = connection.prepareStatement(sql);
            statement.setString(1,message.from);
            statement.setString(2,message.to);
            statement.setString(3,message.message);
            // 3.执行sql
            statement.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // 4. 关闭连接
            DBUtil.close(connection,statement,null);
        }
    }
    // 从数据库取所有消息
    private List<Message> load(){
        List<Message> messageList = new ArrayList<>();
        Connection connection = null;
        PreparedStatement statement = null;
        ResultSet resultSet = null;
        try {
            // 1.和数据库建立连接
            connection = DBUtil.getConnection();
            // 2.构造sql
            String sql = "select * from message";
            statement = connection.prepareStatement(sql);
            // 3.执行 sql
            resultSet = statement.executeQuery();
            // 4.遍历结果集合
            while (resultSet.next()){
                Message message = new Message();
                message.from = resultSet.getString("from");
                message.to = resultSet.getString("to");
                message.message = resultSet.getString("message");
                messageList.add(message);
            }
        } catch (SQLException e){
            e.printStackTrace();
        } finally {
            // 5. 释放资源 断开连接
            DBUtil.close(connection,statement,resultSet);
        }
        return messageList;
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/866f8d7cec16484f9fbcb6eff9ab6bdb.png)
