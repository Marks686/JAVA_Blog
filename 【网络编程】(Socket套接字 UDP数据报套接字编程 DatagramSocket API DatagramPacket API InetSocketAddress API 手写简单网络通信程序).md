# 网络编程



## Socket套接字
这里的核心就是，Socket(套接字) ,就是操作系统给应用程序提供的网络编程的API.换句话说SocketAPI就是站在传输层的角度来去和应用层交互.在传输层里,提供了两个最核心的协议,UDP TCP.因此socket api也提供了两种风格(UDP TCP).

> 简单认识UDP和TCP:
> UDP 无连接 不可靠传输 面向数据报 全双工.
> TCP 有连接 可靠传输 面向字节流 全双工.


1. 有无连接: 打电话就是有连接的(对方可以选择接通),需要连接建立了才能通信.发短信/微信就是无连接的.
2. 可靠和不可靠传输: 网络环境天然是复杂的.不可能保证传输的数据100%就能到达."可靠传输"就是发送方能知道自己的消息发过去了.打电话也是可靠传输,发微信/短信是不可靠传输.
3. 面向字节流:数据传输就和文件读写类似是"流式"的.面向数据报:数据传输则是以一个个的"数据报"为基本单位.(一个数据报可能是若干个字节,带有一定格式的)
4. 全双工:一个通信通道,可以双向传输.(既可以发送,也可以接收)




## UDP数据报套接字编程


### DatagramSocket API
使用这个类表示一个socket对象.在操作系统中,把这个socket对象当成一个文件来处理.相当于是文件描述符表上的一项.普通文件对应的硬件设备是硬盘.socket文件对应的硬件设备是网卡.一个socket对象就可以和另外一台主机进行通信了.如果要和多个不同的主机通信,则需要创建多个socket.

`DatagramSocket` 是UDP Socket，用于发送和接收UDP数据报。

> `DatagramSocket` 构造方法：

![在这里插入图片描述](https://img-blog.csdnimg.cn/5b38d03779c241eba20c3645c4bb55e4.png)

> DatagramSocket 方法：

`void receive(DatagramPacket p)` 从此套接字接收数据报（如果没有接收到数据报，该方法会阻塞等待）

`void send(DatagramPacket p)`从此套接字发送数据报包（不会阻塞等待，直接发送）

`void close()` 关闭此数据报套接字

### DatagramPacket API
表示UDP中传输的一个报文,构造这个对象,可以指定一些具体的数据进去.

`atagramPacket`是UDP Socket发送和接收的数据报。

> `DatagramPacket` 构造方法：

`DatagramPacket(byte[] buf, int length)`构造一个DatagramPacket以用来接收数据报，接收的数据保存在字节数组（第一个参数buf）中，接收指定长度（第二个参数length）


`DatagramPacket(byte[] buf, int offset, int length,SocketAddress address)`构造一个DatagramPacket以用来发送数据报，发送的数据为字节数组（第一个参数buf）中，从0到指定长度（第二个参数length）。address指定目的主机的IP和端口号




> `DatagramPacket` 方法：

![在这里插入图片描述](https://img-blog.csdnimg.cn/b249fd82c73141869e605d4bb81aa6f9.png)


### InetSocketAddress API

> `InetSocketAddress`（ SocketAddress 的子类 ）构造方法：

![在这里插入图片描述](https://img-blog.csdnimg.cn/1d6d9910d2034aaaa0b3b76316bc937a.png)
### 手写简单网络通信程序

基于UDP来编写一个简单的客户端服务器的网络通信程序:



> UDP 版本的回显服务器

```java
public class UdpEchoServer {
    //网络编程本质操作网卡 操作系统内核中使用"socket"这样的文件抽象表示网卡
    //先创建socket对象
    private DatagramSocket socket = null;

    //对于服务器来说 创建socket对象的同时 要让它绑定一个具体的端口号
    //服务器一定要关联上一个具体的端口
    //服务器是网络传输中,被动的一方,如果是操作系统随机分配的端口,
    // 此时客户端就不知道这个端口是啥,也就无法进行通信
    public UdpEchoServer(int prot) throws SocketException {
        socket = new DatagramSocket(prot);
    }
    public void start() throws IOException {
        System.out.println("服务器启动!");
        //服务器不是只给一个客户端服务 它需要给很多客户端提供服务
        while (true){
            //只要有客户端过来 就提供服务
            //1.读取客户端发来的请求
            //   receive 方法的参数是一个输出型参数,需要先构造好个空白的 DatagramPacket对象
            //交给receive来进行填充
            DatagramPacket requestPacket = new DatagramPacket(new byte[4096],4096);
            socket.receive(requestPacket);
            //此时DatagramPacket是一个特殊的对象 并不方便直接进行处理 我们可以把这里包含的数据拿出来
            //构造成一个字符串
            String request = new String(requestPacket.getData(),0,requestPacket.getLength());
            // 2. 根据请求计算响应 此处是回显服务器 响应和请求相同
            String response = process(request);
            //3. 把响应程序写回客户端 send的参数也是DatagramPacket 需要把这个Packet对象构造好
            // 此处构造的响应对象 不能用空的字节数组构造,要用响应数据来构造
            DatagramPacket responsePacket = new DatagramPacket(response.getBytes(),response.getBytes().length,
                    requestPacket.getSocketAddress());
            socket.send(responsePacket);
            // 4.打印这次请求响应的处理中间结果
            System.out.printf("[%s:%d] req: %s; resp:%s \n", requestPacket.getAddress().toString(),
                    responsePacket.getPort(),request,response);
        }
    }
    //"根据请求计算响应"
    public String process(String request){
        return request;
    }

    public static void main(String[] args) throws IOException {
        //端口号 1024--65536 之间随便指定
        UdpEchoServer server = new UdpEchoServer(9090);
        server.start();
    }
}
```


> UDP 版本的回显客户端

 

```java
public class UdpEchoClient {
    private DatagramSocket socket = null;

    private String serverIp = null;
    private int serverPort = 0;
    //让操作系统自动分配端口 不需要显式绑定一个端口
    //一次通信需要两个IP 两个端口
    //客户端的ip是 127.0.0.1已知
    //客户端的 port是系统自动分配的
    //服务器的ip 和 端口 也需要告诉客户端 才能顺利发送消息给服务器
    public UdpEchoClient(String serverIp,int serverPort) throws SocketException {
        socket = new DatagramSocket();
        this.serverIp = serverIp;
        this.serverPort = serverPort;
    }
    public void start() throws IOException {
        System.out.println("客户端启动!");
        Scanner scanner = new Scanner(System.in);

        while (true){
            //1. 从控制台读取要发送的数据
            System.out.print("> ");
            String request = scanner.next();
            if(request.equals("exit")){
                System.out.println("goodbye");
                break;
            }
            //2. 构造UDP请求并发送
            //构造Packet 的时候需要把serverIp和port都传入过来,IP地址需要填写32位整数形式
            //上述的IP地址是一个字符串 所以需要用InetAddress.getByName(serverIp)转换
            DatagramPacket requestPacket = new DatagramPacket(request.getBytes(),request.getBytes().length,
                    InetAddress.getByName(serverIp),serverPort);
            socket.send(requestPacket);
            //3. 读取服务器UDP响应 并解析
            DatagramPacket responsePacket = new DatagramPacket(new byte[4096],4096);
            socket.receive(responsePacket);
            String response = new String(responsePacket.getData(),0,responsePacket.getLength());
            //4. 把解析好的结果显示出来
            System.out.println(response);
        }
    }
    public static void main(String[] args) throws IOException {
        UdpEchoClient client = new UdpEchoClient("127.0.0.1",9090);
        client.start();
    }
}
```

回显服务器,缺少业务逻辑.我们就在上述代码的基础上稍作调整,实现一个"查词典"的服务器(英文单词翻译成中文解释)

修改服务器代码:

```java
//对于UdpDictSever来说 大部分东西和UdpEchoServer一样
//主要是 "根据请求计算响应"这个步骤不太一样
public class UdpDictSever extends UdpEchoServer{
    private Map<String,String> dict = new HashMap<>();

    public UdpDictSever(int prot) throws SocketException {
        super(prot);
        //给dict设置内容
        dict.put("cat","小猫");
        dict.put("dog","小狗");
        dict.put("fuck","卧槽");
        //这里可以无限多的设置键值对
    }
    @Override
    public String process(String request){
        //查词典的过程
        return dict.getOrDefault(request,"当前单词没有查到结果!");
    }
    public static void main(String[] args) throws IOException {
        UdpDictSever sever = new UdpDictSever(9090);
        sever.start();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/722966b9f9a541d18f96cd69d8aebb1b.png)
如果两个服务器用一个端口,程序就会抛异常,一个端口只能被一个进程使用.
`Address already in use: Cannot bind`这个异常以后会反复出现,说明我们绑定的端口已经被别人占用了.
