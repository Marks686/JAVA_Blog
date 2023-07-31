# 网络编程

## TCP流套接字编程
TCP提供的API主要是两个类:ServerSocket 和 Socket .
TCP不需要一个类来表示"TCP数据报"因为TCP不是以数据报为单位进行传输的.是以字节的方式,流式传输
### ServerSocket API

ServerSocket 是专门给服务器使用的Socket对象.

> ServerSocket 构造方法：


`ServerSocket(int port)` 创建一个服务端流套接字Socket,并绑定到指定端口.

> ServerSocket 方法：



`Socketaccept()`开始监听指定端口（创建时绑定的端口），有客户端连接后，返回一个服务端Socket对象，并基于Socket建立与客户端的连接，否则阻塞等待.

![在这里插入图片描述](https://img-blog.csdnimg.cn/9576aedb311245369d4e6d520c26577e.png)


`voidclose()` 关闭此套接字

### Socket API

Socket是既会给客户端使用,也会给服务器使用.

> Socket 构造方法：在服务器这边是有accept返回的.在客户端这边,在代码里构造的时候制定一个IP和端口号.(此处的IP和端口是服务器IP和端口)有了这个信息就能和服务器进行连接了.


`Socket(String host, intport)`创建一个客户端流套接字Socket，并与对应IP的主机上，对应端口的进程建立连接.
![在这里插入图片描述](https://img-blog.csdnimg.cn/099fbe3a32b146ac8b019a66b181b97e.png)


> Socket 方法：

进一步通过Socket对象获取内部的流对象,借助流对象来进行发送/接收.


`InetAddress getInetAddress()` 返回套接字所连接的地址
`InputStream getInputStream()` 返回此套接字的输入流
`OutputStream getOutputStream()` 返回此套接字的输出流

### TCP中的长短连接

在TCP有连接的场景下,针对连接这个概念,有两种典型的表现形式.
1. 短连接:客户端每次给服务器发消息,先建立连接,发送请求;下次再发送,则重新建立连接.


2. 长连接:客户端建立连接后,连接先不断开,然后再次发送请求,读取响应;再发送请求,读取响应;若干轮之后,客户端确实短时间之内不再需要使用这个连接了,此时再断开.


> 两者区别:

1. 建立连接、关闭连接的耗时：短连接每次请求、响应都需要建立连接，关闭连接；而长连接只需要第一次建立连接，之后的请求、响应都可以直接传输。相对来说建立连接，关闭连接也是要耗时的，长连接效率更高。
2. 主动发送请求不同：短连接一般是客户端主动向服务端发送请求；而长连接可以是客户端主动发送请求，也可以是服务端主动发。
3. 两者的使用场景有不同：短连接适用于客户端请求频率不高的场景，如浏览网页等。长连接适用于客户端与服务端通信频繁的场景，如聊天室，实时游戏等。

拓展了解:
![在这里插入图片描述](https://img-blog.csdnimg.cn/47b265de1f9c4b28a7c4e2965f6548ed.png)



### 手写TCP版本的回显服务器

![在这里插入图片描述](https://img-blog.csdnimg.cn/ee1d4a3bda3a4e7ca9e7c2149939eaf6.png)



> TCP服务端

```java
public class TcpEchoServer {
    private ServerSocket serverSocket = null;

    public TcpEchoServer(int port) throws IOException {
        serverSocket = new ServerSocket(port);
    }
    public void start() throws IOException {
        System.out.println("启动服务器!");
        while (true) {
            //使用这个clientSocket和具体的客户端进行交流
            Socket clinentSocket = serverSocket.accept();
            processConnection(clinentSocket);
        }
    }
    //使用这个方法处理一个连接 一个连接对应一个客户端
    //可能涉及到多次交互
    private void processConnection(Socket clinentSocket) {
        //获得客户端IP和端口
        System.out.printf("[%s:%d] 客户端上线!\n",clinentSocket.getInetAddress().toString(),clinentSocket.getPort());
        // 基于上述socket对象和客户端进行通信
        try(InputStream inputStream = clinentSocket.getInputStream();
            OutputStream outputStream = clinentSocket.getOutputStream()){
            //由于要处理多个请求和响应,也是使用循环
            while (true){
                //1. 读取请求
                Scanner scanner = new Scanner(inputStream);
                if(!scanner.hasNext()){
                    //没有下一个数据 说明读完了 (客户端关闭了连接)
                    System.out.printf("[%s:%d] 客户端下线!\n",clinentSocket.getInetAddress().toString(),clinentSocket.getPort());
                    break;
                }
                //此处使用next是一直读取到换行符/空格/其他空白符结束
                //但是结果不包含上述空白符
                String request = scanner.next();
                //2. 根据请求响应
                String response = process(request);
                //3. 返回响应结果
                //outputStream没有write String这样的功能 可以把String里的字节数组拿出来 进行写入
                //也可以用字符流来转换一下
                PrintWriter printWriter = new PrintWriter(outputStream);
                //此处使用println来写入 让结果中带有一个\n 换行 方便对端来接收解析
                printWriter.println(response);
                //flush用来刷新缓冲区 保证当前写入的数据 确实是发送出去了
                printWriter.flush();
                System.out.printf("[%s:%d] req: %s; resp: %s \n",clinentSocket.getInetAddress().toString(),clinentSocket.getPort(),
                        request,response);
            }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            try {
                clinentSocket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    public String process(String request) {
        return request;
    }
    public static void main(String[] args) throws IOException {
        TcpEchoServer server = new TcpEchoServer(9090);
        server.start();
    }
}
```


> TCP客户端

```java
public class TcpEchoClient {
    private Socket socket = null;

    public TcpEchoClient(String serverIp,int serverPort) throws IOException {
        //Socket 构造方法能够识别 电分十进制格式的IP地址 比DatagramPacket 更方便
        //new 这个对象的同时,就会进行 TCP 连接操作
        socket = new Socket(serverIp,serverPort);
    }

    public void start(){
        System.out.println("客户端启动!");
        Scanner scanner = new Scanner(System.in);
        try (InputStream inputStream = socket.getInputStream();
             OutputStream outputStream = socket.getOutputStream()){
            while (true){
                //1. 先从键盘上读取用户输入的内容
                System.out.println("> ");
                String request = scanner.next();
                if(request.equals("exit")){
                    System.out.println("goodbye");
                    break;
                }
                //2.  把读到的内容构造成请求 发送给服务器
                PrintWriter printWriter = new PrintWriter(outputStream);
                printWriter.println(request);
                //加flush保证数据确实发送出去了
                printWriter.flush();
                //3. 读取服务器响应
                Scanner respScanner = new Scanner(inputStream);
                String response = respScanner.next();
                //4. 把响应显示到界面上
                System.out.println(response);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) throws IOException {
        TcpEchoClient tcpEchoClient = new TcpEchoClient("127.0.0.1",9090);
        tcpEchoClient.start();
    }
}
```


**问题**:

> 当前代码里使用的是println来发送数据,println会在发送的数据后面自动带上\n换行.如果不适用println,而是使用print(不带\n换行) 上面的代码是否能正确运行?

不能正确运行,没有\n是不行的.TCP协议是面向字节流的协议(字节流特性:一次读多少个字节都行).接收方无法知道我们一次要多多少字节,这就需要我们在数据传输中进行明确的约定.此处代码中,隐式约定了使用\n来作为当前代码的请求/响应分割约定.
![在这里插入图片描述](https://img-blog.csdnimg.cn/45a4f77d178a438f8567ec981dfc3e05.png)
但是我们发现上面的代码还是存在一些问题的:
![在这里插入图片描述](https://img-blog.csdnimg.cn/c030a66403fc45c3bfaf789b4732a069.png)
为解决上面的问题,我们使用多线程,主线程负责进行accept.每次接收到一个连接,创建新线程 ,由这个新的线程负责处理这个新的客户端.每个线程是独立的执行流.每个独立的执行流是各自执行各自的逻辑,彼此之间是并发关系.不会出现一边阻塞而影响到另一边执行的情况.

如果服务器,客户端特别多,很多客户端频繁建立连接就需要频繁创建/销毁线程了, 此时单纯的多线程的处理方法也不行了,所以我们就得用线程池来进行处理.

![在这里插入图片描述](https://img-blog.csdnimg.cn/32f5dc0c5e1645ce8e9a1906a34100eb.png)


如果客户端非常多而且客户端连接都迟迟不断开,就会导致机器上有很多线程.如果一个服务器有几千个客户端就得是几千个线程.这个事情对机器来说,是一个很大的负担.这个时候为了解决单机支持更大量客户端的问题即C10M问题.就想办法让一个线程,处理多个客户端连接.为了解决这个问题操作系统底层提出了IO多路复用,IO多路转接.就是利用充分等待时间,做别的事情.我们给这个线程安排个集合,这个集合就放了一堆连接.这个线程就负责监听这个集合,哪个连接有数据来了,线程就来处理哪个连接,虽然连接有很多,总还是有先有后的.操作系统提供了一些原生API ,select,poll,epoll.在Java中,提供了一组NIO这样类,就封装了上述多路复用的API.




> 改进后:TCP服务器代码

```java
public class TcpEchoServer {
    private ServerSocket serverSocket = null;

    public TcpEchoServer(int port) throws IOException {
        serverSocket = new ServerSocket(port);
    }
    public void start() throws IOException {
        System.out.println("启动服务器!");
        //此处使用CachedThreadPool ,使用FixedThreadPool不太合适(线程数不太应该是固定的 )
        ExecutorService threadPool = Executors.newCachedThreadPool();
        while (true) {
            //使用这个clientSocket和具体的客户端进行交流
            Socket clinentSocket = serverSocket.accept();
            //此处使用多线程处理
            /*Thread t = new Thread(()->{
                processConnection(clinentSocket);
            });*/
            //使用线程池
            threadPool.submit(()->{
                processConnection(clinentSocket);
            });
        }
    }
    //使用这个方法处理一个连接 一个连接对应一个客户端
    //可能涉及到多次交互
    private void processConnection(Socket clinentSocket) {
        //获得客户端IP和端口
        System.out.printf("[%s:%d] 客户端上线!\n",clinentSocket.getInetAddress().toString(),clinentSocket.getPort());
        // 基于上述socket对象和客户端进行通信
        try(InputStream inputStream = clinentSocket.getInputStream();
            OutputStream outputStream = clinentSocket.getOutputStream()){
            //由于要处理多个请求和响应,也是使用循环
            while (true){
                //1. 读取请求
                Scanner scanner = new Scanner(inputStream);
                if(!scanner.hasNext()){
                    //没有下一个数据 说明读完了 (客户端关闭了连接)
                    System.out.printf("[%s:%d] 客户端下线!\n",clinentSocket.getInetAddress().toString(),clinentSocket.getPort());
                    break;
                }
                //此处使用next是一直读取到换行符/空格/其他空白符结束
                //但是结果不包含上述空白符
                String request = scanner.next();
                //2. 根据请求响应
                String response = process(request);
                //3. 返回响应结果
                //outputStream没有write String这样的功能 可以把String里的字节数组拿出来 进行写入
                //也可以用字符流来转换一下
                PrintWriter printWriter = new PrintWriter(outputStream);
                //此处使用println来写入 让结果中带有一个\n 换行 方便对端来接收解析
                printWriter.println(response);
                //flush用来刷新缓冲区 保证当前写入的数据 确实是发送出去了
                printWriter.flush();
                System.out.printf("[%s:%d] req: %s; resp: %s \n",clinentSocket.getInetAddress().toString(),clinentSocket.getPort(),
                        request,response);
            }
        }catch (IOException e){
            e.printStackTrace();
        }finally {
            try {
                clinentSocket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
    public String process(String request) {
        return request;
    }
    public static void main(String[] args) throws IOException {
        TcpEchoServer server = new TcpEchoServer(9090);
        server.start();
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6f76a7249041444f9d7316d0d12fe780.png)


