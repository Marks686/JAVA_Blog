# 传输层

### TCP内部工作机制
![在这里插入图片描述](https://img-blog.csdnimg.cn/a8aaa2498bd74fa7b2d46058e7ca9288.png)

#### 连接管理


连接(Connection)

![在这里插入图片描述](https://img-blog.csdnimg.cn/923f79ba68194d13963d0753fd8356c2.png)

当这两部分信息都被维护好之后,此时连接就有了,此时也把保存这部分信息的这个空间(数据结构)也称为连接.当A和B把自己存储的连接信息(数据结构)删了,连接就是断开了.

> TCP的建立连接过程(**三次握手**)
> 断开连接过程(**四次挥手**)



##### TCP建立连接(三次握手)

通信双方各自要记录对方的信息,彼此之间要相互认同.过程中的每次通信都称为是一次"握手"
![在这里插入图片描述](https://img-blog.csdnimg.cn/0e76b5adea6c4e4d8f651f586cbff3d5.png)


> 小结:所谓的三次握手,本质上是"四次"交互,通信双方,各自要向对方发起一个"建立连接"的请求.同时,再各自向对方回应一个ACK("好啊好啊").所以这里其实是一共有四次信息交互,但是中间两次交互是可以合并成一次交互的.因此就构成了"三次握手".



为什么要把中间这两次合并?
封装分用两次一定比封装分用一次成本更高.


三次握手另外一个重要的作用:验证通信双方各自的发送能力和接收能力是否正常.三次握手,也是一定程度的保证了TCP传输的可靠性.(起到的不是关键作用,是辅助作用).

![在这里插入图片描述](https://img-blog.csdnimg.cn/084961c1e5114b1c8e97dcd44fac10e7.png)

> 三次握手的意义:

1. 让通信双方各自建立对对方的"认同"
2. 验证通信双方各自的发送能力和接收能力是否ok
3. 在握手的过程中,双方来协商一些重要的参数.TCP通信过程中有些数据通信双方要相互同步,此时就需要有这样的交互过程,切好可以利用三次握手的机会来完成数据的同步.



真正的三次握手四次挥手,中间传输过程:
![在这里插入图片描述](https://img-blog.csdnimg.cn/93bc4c3ada14471b95c62453d0a2639d.png)

客户端主动给服务器发起的建立连接请求,称为"SYN"(synchronize),同步报文段.
![在这里插入图片描述](https://img-blog.csdnimg.cn/51b0b58f3a4542018168e29da6afe7a7.png)
"SYN"发出去后,对方发回"SYN"和"ACK",即这俩报文都是1.客户端再发回"ACK".此过程就完成了三次握手.

> TCP的状态:

![在这里插入图片描述](https://img-blog.csdnimg.cn/1b68b357deb040b49e4f8ad925499d58.png)

> 建立连接阶段,主要认识两个状态:

1. LISTEN 服务器的状态.表示服务器已经准备就绪,随时可以有客户端来建立连接了.
2. ESTABLISHED 客户端和服务器都有的状态.表示连接建立完成,接下来就可以正常通信了.




![在这里插入图片描述](https://img-blog.csdnimg.cn/0de581d2a03949218e8966b8d1d4e188.png)
##### TCP断开连接(四次挥手)


"挥手"和"握手"都是形象的叫法,都是客户端服务器之间的数据交互.四次挥手和三次握手非常类似,都是通信双方各自向对方发起一个断开连接的请求,再各自给对方一个回应.
![在这里插入图片描述](https://img-blog.csdnimg.cn/90228a258bbb4dfeabba061fa237aa30.png)
这里不能合并,两个数据发送的时机相同才能合并,如果是不同的时机,就合并不了.
![在这里插入图片描述](https://img-blog.csdnimg.cn/055bf7bc552b4947935e70394b30c61c.png)
FIN的发起不是由内核控制的,而是由应用程序,调用soket的close方法(或者进程退出)才会触发FIN.ACK则是由内核控制的是收到FIN之后,立即返回ACK.两者之间隔着时间.所以就不能合并.

> 四次挥手中涉及到的两个重要的TCP状态:

1. CLOSE_WAIT 就是等待关闭.(等待调用close方法关闭socket).出现在被动发起断开连接的一方.建立连接一定是客户端主动发起请求,断开连接可能是客户端主动发起,也可能是服务器主动发起.
2. TIME_WAIT 出现在主动发起断开连接的一方,此时TIME_WAIT要保持当前的TCP连接状态不要立即释放.这里不立即将连接释放的原因是在三次握手和四次挥手过程中,同样存在超时重传,如果是最后一个ACK丢包了,站在服务器的视角来看,服务器是不知道是因为ACK丢了,还是自己发的FIN丢了,所以统一视为FIN丢了,统一进行重传操作.既然服务器可能要重传FIN,客户端就需要能够针对这个重传的FIN进行ACK响应,如果刚才彻底把连接释放了,这样的ACK就无法进行了.因此使用TIME_WAIT状态保留一定的时间,就是为了能够处理最后一个ACK丢包的情况,就能够在收到重传的FIN之后进行ACK响应.
![在这里插入图片描述](https://img-blog.csdnimg.cn/80181da56f394b798f3f346411dcc0cb.png)
具体TIME_WAIT保持的时间是2倍的MSL(MSL指的是互联网上两个节点之间数据传输消耗的最大时间).如果A经历了2MSL这个时间还没收到重传的FIN,就认为上个ACK就正常到达了.(认为对方没有重传FIN)

> 小结:TCP作为一个有连接的协议,就需要建立连接和断开连接.其中建立连接的过程是三次握手,断开连接的过程是四次挥手.






#### 滑动窗口

之前了解到的确认应答,超时重传,连接管理,都是个TCP的可靠性提供的支持.引入了可靠性,就会影响到数据的传输效率.可靠和效率是冲突的.因此UDP没有可靠性,但是传输效率比TCP高.TCP也在竭尽可能的提高传输效率.(本质上就是补救措施)滑动窗口就能完成这个功能.

我们在进行IO操作的时候,时间成本主要是两个部分:(1)等(2)数据传输(数据拷贝).大多是情况,IO花的的时间成本都是在等.

滑动窗口本质上就是降低了确认应答,等待ACK消耗的时间.具体怎么缩短?


批量发送,批量等待,把多份等待时间,合并成一份.

![在这里插入图片描述](https://img-blog.csdnimg.cn/78117294321848549a3dfa32cd4f19a0.png)
滑动窗口的本质就是不等待的批量发送一组数据,然后使用一份时间来等待着一组数据的多个ACK了.把不需要等待就能直接发送的数据的最大的量称为"窗口大小".上图的窗口大小就是4000.当批量发送了窗口大小这些数据之后,发送方就要等待ACK了.注意:不是等所有的ACK到达了才继续往下发,而是到一个ACK,就继续往下发一条.这样就可以让我们等待的ACK始终都是4条.
滑动窗口:
![在这里插入图片描述](https://img-blog.csdnimg.cn/fc24deade74e449bbc6e310a72c9e33b.png)
上述情况下丢包了怎么办?
1. ACK丢了
![在这里插入图片描述](https://img-blog.csdnimg.cn/e22c5eb10b10423681c746f819a700d2.png)这种情况不做任何处理,2001的ACK到了说明2001之前的数据都已经确认到达了,所以1001的ACK丢了是没关系的(2001这个ACK涵盖了上一个1001的ACK信息).





2. 数据丢了

![在这里插入图片描述](https://img-blog.csdnimg.cn/98132bd720f14ff9a23b5c7e76f6b5a5.png)
上述丢包重传的方式叫做"快速重传"(重传操作只重传丢失的数据),可以视为是超时重传机制在滑动窗口下的变形.




#### 流量控制


流量控制是一种干预发送的窗口大小的机制.滑动窗口越大,传输效率就越高,但是窗口也不能无限大.并且发送速度太快,接收方处理不过来,发了也白发.接受方的能力就是一个很重要的约束依据,发送方的速度不能超过接收方的处理能力.
流量控制要做的工作就是**根据接收方的处理能力来协调发送方的发送速率**.



> 如果衡量接收方的处理能力?

根据接收方接收缓冲区的剩余大小来衡量.我们可以把缓冲区想象成蓄水池,一边进水,一边出水.

![在这里插入图片描述](https://img-blog.csdnimg.cn/937ba25c9b43444796a2f02b1295a2b5.png)
每次A给B发了个数据,B就需要算一下水池里剩余空间,然后把这个值,通过ACK报文返回给A,A就根据这个值来决定接下来的发送速率.(窗口大小)
![在这里插入图片描述](https://img-blog.csdnimg.cn/849cc41a2cba40d18a18e2177ed8a20c.png)
所以发送方窗口大小不是固定值,也不是配置的,而是随着传输过程的进行,动态调整.
![在这里插入图片描述](https://img-blog.csdnimg.cn/4e8f5a9d57c44f5b8939c8cf994acbef.png)


#### 拥塞控制

流量控制和拥塞控制共同决定发送方的窗口大小是多少.流量控制考虑的是接收方的处理能力.拥塞控制描述的是传输过程中,中间节点的处理能力.
![在这里插入图片描述](https://img-blog.csdnimg.cn/b13739e7b136422a96f41c20482550ad.png)
接收方,处理能力好量化,但是中间节点的处理能力不好量化(如上图).既然不好直接量化,那么就通过"实验"的方式来测试出一个合适的值(把中间的节点看成一个节点).

拥塞控制本质就是**通过实验的方式,来逐渐找一个合适的窗口大小**.(合适的发送速率)

![在这里插入图片描述](https://img-blog.csdnimg.cn/1ca2f7382abe42dabe8309a59fcb256e.png)
拥塞窗口不是固定数值而是一直动态变化,随着时间的推移,逐渐达到一个动态平衡的过程.

> 总结:流量控制和拥塞控制共同决定发送方的窗口大小(拥塞窗口和流量控制窗口的较小值).




#### 延时应答

延时应答也是提升效率的机制.延时:收到数据之后不是立即返回ACK,而是稍微等会再返回.等待的时间里接收方的应用程序就能够把接收缓冲区的数据消费一部分,此时剩余的空间就更大了.

实际上延时应答采取的方式就是在滑动窗口下,ACK不再每一条数据都返回了,比如隔一条返回一个ACK.如下:
![在这里插入图片描述](https://img-blog.csdnimg.cn/41b7a6af63024cb0be316dc729d572aa.png)
实际上剩余空间大小变化是一个复杂的过程,既取决于发送方也取决于接收方的处理.



#### 捎带应答

捎带应答也是提高效率的方式.服务器客户端程序最典型的模型就是"一问一答".
![在这里插入图片描述](https://img-blog.csdnimg.cn/08f7f4e8364a46928a040390ecba79a2.png)


#### 面向字节流



面向字节流引入粘包问题.由于TCP是字节流的,一次读1个字节,读N个字节都是可以的.这就导致一次独到的数据可能是半个应用层数据报,可能是一个应用层数据报,也可能是多个应用层数据报.在TCP层次,没有socket api告诉应该读几个字节,具体怎么读由程序员负责.我们期望读到的是整个应用层数据报,后续才好处理.

> 解决方案:在应用层约定好应用层协议,尤其是明确应用层数据报和数据报之间的边界.

1. 约定好分隔符
2. 约定每个数据报的长度



#### 异常情况


传输过程中出现了不可抗力.
1. 进程崩溃了
2. 主机关机(正常流程关机)
3. 主机掉电
4. 网线断开



进程崩溃是进程没了,对应的PCB就没了,对应的文件描述符表就释放了,相当于socket.close(),此时内核会继续完成四次挥手此时仍然是一个正常断开的流程.

主机关机是要先杀进程,然后才正式关机,杀死进程的过程和上面的一样触发四次挥手.

主机掉电,瞬间机器就关了,来不及进行任何挥手操作.网线断开和他一样.
![在这里插入图片描述](https://img-blog.csdnimg.cn/4c5a936c89b340218a7d53e2a30b9122.png)
