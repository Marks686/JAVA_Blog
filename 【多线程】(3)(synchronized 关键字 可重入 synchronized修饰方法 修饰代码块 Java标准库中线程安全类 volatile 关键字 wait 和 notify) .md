# synchronized 关键字

进入 synchronized 修饰的代码块, 相当于 加锁.
退出 synchronized 修饰的代码块, 相当于 解锁.

## synchronized 的特性
### 可重入
一个线程针对同一个对象,连续加锁两次,如果没问题,就叫做可重入.如果有问题,就叫做不可重入的.

```java
synchronized public void add(){
     synchronized (this) {
         count++;
     }
 }
```
锁对象是this,只要有线程调用add,进入add方法的时候就会先加锁(此时可以加锁成功),紧接着又遇到了代码块再次尝试加锁,站在this的视角(锁视角),它认为自己已经被另外的线程给占用了,这里的第二次加锁是否需要阻塞等待? 如果允许第二次加锁,那么这个锁就是可重入的,如果第二次加锁阻塞等待,就是不可重入的. 这个情况就导致线程死锁了.为了避免不小心死锁,java就把synchronize设定成可重入的.

## synchronized 使用示例
### 修饰方法

需要明确锁对象,针对哪个对象加锁.如果两个线程针对同一个对象加锁,就会出现锁竞争/锁冲突,一个线程能够获取到锁,另一个线程阻塞等待,等待到上一个线程解锁,它才能获取锁成功.

如果两个线程针对不同对象加锁,此时不会发生锁竞争/锁冲突.这俩线程都能获取到各自的锁.就不会阻塞等待了.

两个线程,一个线程加锁,一个线程不加锁,这个时候不会有锁竞争.
#### 修饰普通方法
![在这里插入图片描述](https://img-blog.csdnimg.cn/c878eab9f17345eeabeca14604cb19b1.png)

进入方法加锁,离开方法解锁.
#### 修饰静态方法

```java
class Counter{
    public static int count = 0;
     public synchronized static void add(){
        count++;
    }
}
```

进入方法加锁,离开方法解锁,但是和普通方法加锁的对象是不一样的.如果两个线程针对同一个对象加锁,会产生阻塞等待(锁竞争/锁冲突),如果两个线程针对不同对象加锁,不会阻塞等待(不会锁冲突/锁竞争).



### 修饰代码块

```java
class Counter{
    public static int count = 0;
     public void add(){
         synchronized (this) {
             count++; 
         }
     }
}
```

让synchronize修饰代码块,表示进入代码块就加锁,出了代码块就解锁.



## Java标准库中的线程安全类


如果多个线程操作同一个集合类,此时就需要考虑到线程安全问题.
![在这里插入图片描述](https://img-blog.csdnimg.cn/40650d9f525d4374a131febcfa8481ea.png)

如下的类是相对安全的,因为使用了synchronize锁机制来控制.
`Vector `
`HashTable` 
`ConcurrentHashMap`
`StringBuffer`


加锁是有副作用的.(额外的时间开销),还有的类虽然没有加锁,但是不涉及"修改",仍然是线程安全的.
`String`



# volatile 关键字


## volatile 能保证内存可见性

volatile和内存可见性问题密切相关.

```java
class MyCounter{
    public int flag = 0;
}
public class ThreadDemo15 {
    public static void main(String[] args) {
        MyCounter myCounter = new MyCounter();

        Thread t1 = new Thread(()->{
            while (myCounter.flag == 0){
                //这个循环体空着
            }
            System.out.println("t1 循环结束");
        });

        Thread t2 = new Thread(()->{
            Scanner scanner = new Scanner(System.in);
            System.out.println("请输入一个整数:");
            myCounter.flag = scanner.nextInt();
        });
        t1.start();
        t2.start();
    }
}
```
线程t1循环快速重复读取.线程t2进行修改.预期t2把flag改成非0的值之后,t1随之就结束循环了.

![在这里插入图片描述](https://img-blog.csdnimg.cn/8956442bb89546a3b522594aceffa12d.png)
当输入t1的时候,显然t1是非0,但是t1这个线程并没有结束循环,同时t2这个线程已经没了.这个情况就是"内存可见性"问题.

![在这里插入图片描述](https://img-blog.csdnimg.cn/27628392bb46444a85da6a1c87c32a83.png)

> 内存可见性:

一个线程针对一个变量进行读取操作,同时另一个线程针对这个变量进行修改,此时读到的值,不一定是修改之后的值.这个读线程没有感知到变量的变化.归根结底是编译器/jvm在多线程环境下优化是产生了误判.

此时就需要程序员手动干预,给flag这个变量加上volatile关键字.意思就是告诉编译器这个变量是"易变"的,希望编译器每次都读取这个变量的内存内容,不要去进行激进的优化了.

加上volatile:

```java
class MyCounter{
    volatile public int flag = 0;
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/57695070a76f4ec7ab240379036277cc.png)

> 从JMM(Java Memory Model)的角度重新表述内存可见性问题:

Java程序里，主内存，每个线程还有自己的工作内存(t1和t2的工作内存不是同一个东西)，t1线程进行读取的时候，只是读取了工作内存的值，t2线程进行修改的时候，先修改工作内存的值，然后把工作内存的内容同步到主内存中。由于编译器优化,导致t1没有重新从主内存同步数据到工作内存,读到的结果就是"修改之前"的结果.这里我们可以近似理解"主内存"为我们常说的"内存","工作内存"为"CPU寄存器",但是也不一定只是CPU的寄存器,还可能包括CPU的缓存cache.

CPU读取寄存器比读取内存速度快很多,一次就会在CPU内部引入缓存cache,寄存器存储空间小,读写速度快,贵,中间有个cache,它存储空间居中读写速度居中,成本居中,内存的存储空间大,读写速度慢,便宜.当CPU要读取一个内存数据的时候,可能是直接读内存,也可能是读cache,还可能是读寄存器.
引入cache之后硬件结构就更复杂了.工作内存(工作存储区域):CPU寄存器+CPU的cache.一方面为了表述简单,一方面为了避免涉及到硬件的细节和差异,在Java中就使用"工作内存"这一词一言蔽之了.
## volatile 不保证原子性
原子性是靠synchronize来保证的,synchronize和volatile都能保证线程安全.不能使用volatile处理两个线程并发 ++ 这样的问题.

```java
static class Counter {
    volatile public int count = 0;
    void increase() {
        count++;
   }
}
public static void main(String[] args) throws InterruptedException {
    final Counter counter = new Counter();
    Thread t1 = new Thread(() -> {
        for (int i = 0; i < 50000; i++) {
            counter.increase();
       }
   });
    Thread t2 = new Thread(() -> {
        for (int i = 0; i < 50000; i++) {
            counter.increase();
       }
   });
    t1.start();
    t2.start();
    t1.join();
    t2.join();
    System.out.println(counter.count);
}
```

最终 count 的值仍然无法保证是 100000




# wait 和 notify

线程最大的问题,是抢占式执行,随机调度.我们写代码的时候喜欢确定的东西,于是程序员发明了一些办法来控制线程之间的执行顺序.虽然线程在内核里的调度是随机的,但是可以通过一些api让线程主动阻塞,主动放弃CPU.(给别的线程让路)

比如t1,t2两个线程,希望t1先干活,等干的差不多了,再让t2来干.就可以让t2先wait(阻塞,主动放弃CPU),等t1干的差不多了,再通过notify通知t2,把t2唤醒让t2接着干.
上述场景使用join和sleep不是不行,主要是有一些限制,如果使用join,则t1要彻底执行完t2才能运行,如果希望t1先干50%的活,就让t2开始行动,join无能为力.使用sleep,指定一个休眠时间,但是t1执行的工作到底花了多少时间,不好估计.

使用wait和notify可以更好的解决上述问题.

## wait()方法

wait进行阻塞等待

某个线程调用wait方法,就会进入阻塞(无论是通过哪个对象wait的),此时就处在WAITING.
![在这里插入图片描述](https://img-blog.csdnimg.cn/469cb947dc5c4a1c8417905954190ba6.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0c27d6f521914172a5aea0674b7ebf9f.png)
为什么会有这个异常??
wait:
1. 先释放锁;
2. 进行阻塞等待;
3. 收到通知之后.重新尝试获取锁,并且在获取锁后,继续往下执行.



上面的锁状态异常就是,没有加锁就想着释放锁.因此wait操作需要搭配synchronize来使用.
![在这里插入图片描述](https://img-blog.csdnimg.cn/aa98d68d3b214cf8a874571d53f28226.png)
我们发现代码走到wait之前就停住了不会继续走了,虽然wait那块是阻塞(阻塞在synchronize代码块里),实际上,这里的阻塞是释放了锁,此时其他线程是可以获取到object这个对象的锁.此时这里的阻塞就处在WAITING状态.




## notify()方法
notify 方法是唤醒等待的线程.:
1. 方法notify()也要在同步方法或同步块中调用，该方法是用来通知那些可能等待该对象的对象锁的其它线程，对其发出通知notify，并使它们重新获取该对象的对象锁。
2. 如果有多个线程等待，则有线程调度器随机挑选出一个呈 wait 状态的线程。(并没有 "先来后到")
3. 在notify()方法后，当前线程不会马上释放该对象锁，要等到执行notify()方法的线程将程序执行
完，也就是退出同步代码块之后才会释放对象锁。


wait使用的对象得和notify使用的对象相同.

```java
public class ThreadDemo17 {
    public static void main(String[] args) throws InterruptedException {
        Object object = new Object();
        Thread t1 = new Thread(()->{
            //这个线程负责进行等待
            System.out.println("t1:wait之前");
            try {
                synchronized (object) {
                    object.wait();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("t1:wait之后");
        });

        Thread t2 = new Thread(()->{
            System.out.println("t2:notify 之前");
            //notify 务必要获取到锁,才能进行通知
            synchronized (object) {
                object.notify();
            }
            System.out.println("t2:notify 之后");
        });

        t1.start();
        //此处的 sleep 500 是大概率会让当前的 t1 先执行 wait
        Thread.sleep(500);
        t2.start();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ac84de7bd36843d4a0e9562311be889f.png)
此处先执行wait,很明显wait操作阻塞了,没有看到wait之后的打印,接下来执行到t2,t2进行了notify的时候,才会把t1的wait唤醒,t1才能继续执行.只要t2不进行notify,此时t1就会始终wait下去.
![在这里插入图片描述](https://img-blog.csdnimg.cn/a4e2cefc0fe549b29e10ada1ce68ef6a.png)
notify唤醒wait,这是不会有任何异常的.(正常的业务逻辑)
interrupt唤醒sleep则是出异常了.( 表示一个出问题了的逻辑)
![在这里插入图片描述](https://img-blog.csdnimg.cn/02721e5744494e08bfeab0f6eec17f13.png)

>   三个线程,分别打印 A,B,C. 控制三个线程固定按照 ABC 的顺序进行打印


```java
public class ThreadDemo18 {
    public static void main(String[] args) throws InterruptedException {
        Object locker1 = new Object();
        Object locker2 = new Object();

        Thread t1 = new Thread(()->{
            System.out.println("A");
            synchronized (locker1){
                locker1.notify();
            }
        });
        Thread t2 = new Thread(()->{
            synchronized (locker1){
                try {
                    locker1.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }

            System.out.println("B");

            synchronized (locker2){
                locker2.notify();
            }
        });
        Thread t3 = new Thread(()->{
            synchronized (locker2){
                try {
                    locker2.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("C");
        });
        
        t2.start();
        t3.start();
        Thread.sleep(100);
        t1.start();
    }
}
```
