# Java的数据库编程：JDBC

真正在工作中操作数据库,99.9%都是通过代码来操作,很少会手动在客户端里输入sql语句.各种数据库,MySQL,Oracle,SQL Server 在开发的时候,就会提供一组编程接口(API)Application Programming Interface.

API:大概意思就是给你一个软件,你能使用代码对他进行操作,基于他给你提供的这些功能,就可以写一些其他代码.对于程序来说API往往就是以"函数""类"的形式来提供的.不同的的数据库提供的API不同,程序员学习的成本就高了,Java就提出了接口规范,各厂商实现对应API,这就是JDBC.

![在这里插入图片描述](https://img-blog.csdnimg.cn/81c68fde7643427b8b7d9cab7b582dd7.png)
如果我们想要进行数据库开发就需要在项目中导入对应数据库的驱动包,才能编写代码.
![在这里插入图片描述](https://img-blog.csdnimg.cn/d5656e66c6a244d9912a599a2b6091b4.png)
# 导入驱动包

> 1.创建一个项目

![在这里插入图片描述](https://img-blog.csdnimg.cn/d7890aca7fce4ee9804d55dcc6da6ca6.png)

> 2.获取驱动包:

驱动包是数据库厂商提供的.我们可以从:
(1)mysql的官方网站获取.
(2)github.
(3)还maven的中央仓库,中央仓库可以理解为服务器,它上面就托管了各种软件包.maven就相当于应用商店,通过应用商店就可以访问到软件包然后下载下来.
![在这里插入图片描述](https://img-blog.csdnimg.cn/f0fab6f87a194098a10a7ab1b666696c.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/6b8d5a2949e549218defc9de8a634cc6.png)
## MySQL Connector Java
MySQL Connector/J is a JDBC Type 4 driver, which means that it is pure Java implementation of the MySQL protocol and does not rely on the MySQL client libraries. This driver supports auto-registration with the Driver Manager, standardized validity checks, categorized SQLExceptions, support for large update counts, support for local and offset date-time variants from the java.time package, support for JDBC-4.x XML processing, support for per connection client information and support for the NCHAR, NVARCHAR ...
![在这里插入图片描述](https://img-blog.csdnimg.cn/53e1db4281764afaa5e3607a849bb733.png)


使用的版本大版本要和数据库服务器匹配.我的数据库服务器是8.0的,所以我就用8.0系列.
![在这里插入图片描述](https://img-blog.csdnimg.cn/2ccbbc0db3ae4ffbaaf692cfa4d07ab4.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0b95bebe20384112b692c912e4294f5e.png)

找到匹配的驱动包然后点进去然后下载:
![在这里插入图片描述](https://img-blog.csdnimg.cn/4ebd0efb880740c8b0bb6b039598874f.png)
下载完成如下:

![在这里插入图片描述](https://img-blog.csdnimg.cn/3d9d02619b54430ab081de06687756ac.png)
**jar包**: 是java发布程序的典型方式,java是通过.java源文件编译成.class文件,jvm来解释执行.class,所以我们要想发布一个程序给别人使用,只需要把程序.class文件拷贝给对方让他用他的jvm来解释执行就可以了.但是每个.java都一 一对应一个.class,如果代码里.java非常多,我们就把一大堆的.class打成压缩包(类似于.rar  .zip),我们这块的压缩包就是.jar.此时把jar拷贝给对方,对方就可以使用jvm来运行了.


此处,mysql驱动包的这个jar不是单独运行的jar,我们可以把它导入到咱们的项目,然后就可以调用其中的方法和类来实现编程了.

> 3.导入驱动包

1. 创建一个新的目录:

![在这里插入图片描述](https://img-blog.csdnimg.cn/3759c5278fb94fa8a700d0b5d7da1bbc.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b7bf67bea1164633828f037eaf084c1d.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/d7d8faca93084179a80130655f95a4d5.png)

2. 把这个目录标记成项目的库:

![在这里插入图片描述](https://img-blog.csdnimg.cn/c736786a9c23452492d9efa4613800f5.png)
`Add as Library` 就是把这样的目录标记成库,idea就能识别这个目录里的jar包,从而就可以调用里面的类来写代码了.



# 编写JDBC代码


## 插入操作

> 3.编写JDBC代码需要下面的五个步骤:


1. 创建并初始化一个数据源.

```java
 //1.创建并初始化一个数据源
DataSource dataSource = new MysqlDataSource();
((MysqlDataSource)dataSource).setUrl("jdbc:mysql://127.0.0.1:3306/java107?characterEncoding=utf8&useSSL=false");
//setURl()这个方法子类有,父类没有,想使用就得把父类引用转回子类引用.
((MysqlDataSource)dataSource).setUser("root");
((MysqlDataSource)dataSource).setPassword("123456");
```

通过DataSourse接口来描述数据源.
setURl()这个方法子类有,父类没有,想使用就得把父类引用转回子类引用.
URL是计算机里面的常见术语,叫做唯一资源定位符,描述网络上的某个资源所在的位置.
![在这里插入图片描述](https://img-blog.csdnimg.cn/64279fcf12114720b4b2424d12167099.png)



2.和数据库服务器建立连接.

```java
Connection connection = dataSource.getConnection();
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/9dacad65207f4e6f92f30323447f7481.png)


3.构造 SQL 语句.

```java
String sql = "insert into student value(1,'张三')";
PreparedStatement statement = connection.prepareStatement(sql);
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/90f97e110a54461c865873dcab319398.png)

即使咱们使用代码来构建数据库,还是靠sql语句,只不过换成用代码来构建sql,前面的sql的各种语法仍然有效.


![在这里插入图片描述](https://img-blog.csdnimg.cn/ab3d5d71b2514bf8b430eb514c9004e1.png)


4.执行 SQL 语句.

```java
//4.执行 SQL 语句
int ret = statement.executeUpdate();
System.out.println("ret = " + ret);
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/1cf1d71405564332ae987af0c0880efd.png)


5.释放必要的资源.

```java
//5. 释放必要的资源
statement.close();
connection.close();
//后创建的先释放
```

数据库客户端和服务器之间进行通信的时候,会消耗一定的系统资源(包括不限于CPU,内存,硬盘,宽带),对于服务器来说同一时刻要处理很多个客户端,需要消耗很多资源,此时客户端就应该省着点用资源即不用资源的时候就赶紧释放.




```java
public class JDBCInset {
    public static void main(String[] args) throws SQLException {
        Scanner scanner = new Scanner(System.in);

        //JDBC 需要以下步骤来完成开发.

        //1.创建并初始化一个数据源
        DataSource dataSource = new MysqlDataSource();
        ((MysqlDataSource)dataSource).setUrl("jdbc:mysql://127.0.0.1:3306/java107?characterEncoding=utf8&useSSL=false");
        //setURl()这个方法子类有,父类没有,想使用就得把父类引用转回子类引用.
        ((MysqlDataSource)dataSource).setUser("root");
        ((MysqlDataSource)dataSource).setPassword("123456");
        //2.和数据库服务器建立连接
        Connection connection = dataSource.getConnection();
        //3.从控制塔读取用户的输入内容
        System.out.println("请输入学生姓名:");
        String name = scanner.next();
        System.out.println("请输入学号:");
        int id = scanner.nextInt();
        //4.构造 SQL 语句
        String sql = "insert into student value(?,?)";
        PreparedStatement statement = connection.prepareStatement(sql);
        statement.setInt(1,id);
        statement.setString(2,name);
        //这个打印需要加到拼接数据之后
        System.out.println(statement);
        //5.执行 SQL 语句
        int ret = statement.executeUpdate();
        System.out.println("ret = " + ret);
        //6. 释放必要的资源
        statement.close();
        connection.close();
    }
}
```
DataSourse内置了数据库连接池(字符串常量池),可以复用连接,提高连接服务器的效率. 

## 查询操作

查询来说,返回结果不是单纯的int了,而是ResultSet对象.

```java
public class JDBCSelect {
    public static void main(String[] args) throws SQLException {
        //1.创建并初始化数据源
        DataSource dataSource = new MysqlDataSource();
        ((MysqlDataSource)dataSource).setUrl("jdbc:mysql://127.0.0.1:3306/java107?characterEncoding=utf8&useSSL=false");
        ((MysqlDataSource)dataSource).setUser("root");
        ((MysqlDataSource)dataSource).setPassword("123456");
        //2.建立连接
        Connection connection = dataSource.getConnection();
        //3.构造SQL
        String sql = "select * from student";
        PreparedStatement statement = connection.prepareStatement(sql);
        //4.执行SQL
        ResultSet resultSet = statement.executeQuery();
        //5.遍历结果集合
        while (resultSet.next()){
            //把resultSet想象成一个表格,同时表格这里有个光标,初始情况下光标指向表的最上面.
            //每次调用next光标往下走一行  当光标把整个表遍历完next返回false
            //当光标指向某一行的时候,就可以通过getXXX来获取当前行的数据
            int id = resultSet.getInt("id");
            String name = resultSet.getString("name");
            System.out.println("id = "+ id + ", name = " + name);
        }
        //6.释放资源
        resultSet.close();//查询结果的临时表
        statement.close();
        connection.close();
    }
}
```
