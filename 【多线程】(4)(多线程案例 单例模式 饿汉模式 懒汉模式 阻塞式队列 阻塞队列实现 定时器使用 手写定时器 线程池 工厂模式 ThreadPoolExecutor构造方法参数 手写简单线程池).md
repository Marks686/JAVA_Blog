# 多线程案例


## 单例模式

单例模式是设计模式里的一种.

设计模式类似于象棋,围棋,五子棋的棋谱.计算机这个圈子,水平参差不齐,一些大佬为了让我们程序员写代码的效率更高也就发明了一组"棋谱",称为设计模式(针对典型场景给出了典型的解决方案).

单例模式:单个实例(对象).在有些场景中,有的特定的类,只能创建出一个实例.不应该创建多个实例.单利模式就是针对上述的需求场景进行了个更强制的保证.通过巧用java的现有语法达成了某个类只能被创建出一个实例这样的效果.(当程序员不小心创建了多个实例,就会编译出错)

### 饿汉模式

```java
//饿汉模式的  单例模式 实现
//此处保证 Singleton 这个类只能创建出一个实例
class Singleton{
    //在此处,先把这个实例创建出来
    private static Singleton instance = new Singleton();
    //如果需要使用这个唯一实例,统一通过 Singleton.getInstance() 方式来获取
    public static Singleton getInstance(){
        return instance;
    }
    //为了避免 Singleton 类不小心被复制出多份
    //把构造方法设为 private. 在类外面就无法通过 new 的方式来创建这个 Singleton 实例了
    private Singleton(){}
}
public class ThreadDemo19 {
    public static void main(String[] args) {
        Singleton s = Singleton.getInstance();
        Singleton s2 = Singleton.getInstance();
        System.out.println(s == s2);
    }
}

```


> 保证实例唯一:


1. static这个操作是让instance属性是类属性.类属性是长在类对象上的.类对象又是唯一实例的(只是在类加载阶段被创建出一个实例)
2. 构造方法是设为private.外面的代码无法new

### 懒汉模式

```java
class SingletonLazy{
    private static SingletonLazy instance = null;

    public static SingletonLazy getInstance(){
        if(instance == null){
            instance = new SingletonLazy();
        }
        return instance;
    }

    private SingletonLazy(){}
}
public class ThreadDemo20 {
    public static void main(String[] args) {
        SingletonLazy s = SingletonLazy.getInstance();
        SingletonLazy s2 = SingletonLazy.getInstance();
        System.out.println(s == s2);
    }
}
```
这个实例并非是类加载的时候创建,而是真正第一次使用的时候才去创建.(如果不用,就不创建了)




上述写的饿汉模式 和 懒汉模式,如果在多线程环境下调用getInstance,是否安全?

饿汉模式这里线程调用只是设计"读操作".懒汉模式(不安全)既有"读"又有"写".

![在这里插入图片描述](https://img-blog.csdnimg.cn/c261cff84e2149f580aff202e60ecf11.png)
为了让上述懒汉模式能够线程安全,我们可以加锁.

```java
class SingletonLazy{
    private static SingletonLazy instance = null;

    public static SingletonLazy getInstance(){
        synchronized (SingletonLazy.class) {
            if(instance == null){
                instance = new SingletonLazy();
            }
        }
        return instance;
    }

    private SingletonLazy(){}
}
```
加锁后就能保证读操作 和 修改操作是一个整体.

![在这里插入图片描述](https://img-blog.csdnimg.cn/60d515a3278a4734b1837cebee1f0ff3.png)
写到这里当前的代码还存在问题:
上面的代码导致每次getInstance都需要加锁.加锁是有开销的,会使程序变慢,仔细想想,这里的加锁只是在new出对象之前加上是有必要的,一旦对象new完了,后续代用getInstance,此时instance的值一定是非空的,因此就会直接触发return.相当于一个是比较操作,一个是返回操作,这俩操作不加锁也没事.基于上述讨论我们可以给代码加个判定:如果对象还没创建,才加锁,如果对象已经创建过了,就不加锁.


```java 
class SingletonLazy{
    private static SingletonLazy instance = null;
    public static SingletonLazy getInstance(){
        if(instance == null){
            synchronized (SingletonLazy.class) {
                if(instance == null){
                    instance = new SingletonLazy();
                }
            }
        }
        return instance;
    }
    private SingletonLazy(){}
}
```

此时就不是无脑加锁了,而是满足特定条件之后才真正加锁.

此时上述代码还存在问题:**内存可见性.**


假设有很多线程都去进行getInstance,这个时候会有被优化的风险.(只有第一次读才是真正读了内存,后续都是读寄存器 /cache),另外还会涉及到指令重排序问题(编译器为了提供程序效率,调整代码执行顺序).
![在这里插入图片描述](https://img-blog.csdnimg.cn/fcefdf9c2b0749859e50cfc309c39c3f.png)
正常情况下按照1 2 3 执行.编译器如果进行指令重排序的话,1 2 3 顺序就可能变成1 3 2.(如果是单线程,1 2 3 和 1 3 2没有本质区别),多线程环境下就会有问题,假设t1是按照1 3 2 的步骤执行,t1 执行到1 3 之后,执行2之前,被切出cpu,t2来执行.(当t1执行完3 之后 t2 此处引用就非空了,此时t2就相当于直接返回了instance引用,并且可能会尝试使用引用中的属性).由于t1中的2操作还没执行完呢,t2拿到的是空的对象(非法的对象).继续优化:

![在这里插入图片描述](https://img-blog.csdnimg.cn/53fc38f61e884957be2c1fe2ea2f968c.png)
优化:
![在这里插入图片描述](https://img-blog.csdnimg.cn/20ac643cfd4443eabf7af8c8e1f4e58d.png)

上面的例子都是我们在日常开发中经常遇见的情况,如果再进一步深挖,还有问题:考虑单例模式在序列化情况下,在反射情况下也保证单例......


##  阻塞式队列

阻塞队列也是一个队列,先进先出.实际上还有一些特殊的队列,不一定非得遵守先进先出;优先队列(PriorityQueue).
阻塞队列,也是特殊的队列,虽然也是先进先出的,但是带有特殊的功能:

> 阻塞:


1. 如果队列为空,执行出队列,就会阻塞,阻塞到另一个线程往队列里添加元素(队列不为空)为止.
2. 如果队列满了,执行入队列操作,也会阻塞,阻塞到另一个线程从队列取走元素为止.(队列不满)


消息队列也是特殊的队列,相当于是在阻塞队列的基础上加上了"消息的类型",按照指定类别进行先进先出.

> **生产者消费模型给我们的程序带来了两个非常重要的好处:**

1. 实现了发送方和接收方之间的"解耦"(降低耦合的过程)
开发中典型的场景:服务器之间相互调用

![在这里插入图片描述](https://img-blog.csdnimg.cn/d0db5126136a4798b3b49e8359c596fb.png)

上述场景中,A和B之间的耦合是比较高的,A要调用B,A务必知道B的存在,如果B挂了,很容易引起A的bug.另外如果要再加C服务器,要对A重新修改代码,重新测试,重新发布,重新部署.
![在这里插入图片描述](https://img-blog.csdnimg.cn/21c7828af72444f39b07f392112bc8a7.png)
针对上述场景,使用生产者消费者模型,就可以有效的降低耦合.
![在这里插入图片描述](https://img-blog.csdnimg.cn/f823c7389d544a13b588af580331eb8d.png)
2. 生产者消费者第二个好处,可以做到"削峰填谷",保证系统的稳定性. 




### 阻塞队列实现

阻塞队列主要方法:(带有阻塞功能)
1. 入队列,put
2. 出队列,take



```java
public class ThreadDemo21 {
    public static void main(String[] args) throws InterruptedException {
        BlockingDeque<String> blockingDeque = new LinkedBlockingDeque<>();
        blockingDeque.put("hello");
        String res = blockingDeque.take();
        System.out.println(res);
        res = blockingDeque.take();
        System.out.println(res);
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/456fee8c12184c5480799e1869abb419.png)
接下来就可以基于标准库的阻塞队列写一个生产者消费者模型的代码.

```java
public class ThreadDemo22 {
    public static void main(String[] args) {
        BlockingDeque<Integer> blockingDeque = new LinkedBlockingDeque<>();
        //创建两个线程来作为生产者和消费者

        Thread customer = new Thread(()->{
            while(true){
                try {
                    Integer result = blockingDeque.take();
                    System.out.println("消费元素: " + result);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        customer.start();

        Thread producer = new Thread(()->{
            int count = 0;
            while (true){
                try {
                    blockingDeque.put(count);
                    System.out.println("生产元素: " + count);
                    count++;
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        producer.start();
    }
}
```



> **编写阻塞队列**:


```java
class MyBlockingQueue{
    private int[] items = new int[1000];
    private int head = 0;
    private int tail = 0;
    private int size = 0;

    //入队列
    public void put(int value) throws InterruptedException {
        synchronized (this) {
            while(size == items.length){
                //队列满了,不能继续插入,此时产生阻塞
                //return;
                this.wait();
            }
            items[tail] = value;
            tail++;
            //对tail处理
            //1)
            //tail = tail % items.length;
            //2)
            if(tail >= items.length){
                tail = 0;
            }
            size++;

            //这个 notify 唤醒 take 中的 wait
            this.notify();
        }
    }
    //出队列
    public Integer take() throws InterruptedException {
        int result = 0;
        synchronized (this) {
            while(size == 0){
                //队列空 也应该进行阻塞
                this.wait();
            }
            result = items[head];
            head++;
            if(head >= items.length){
                head = 0;
            }
            size--;

            //唤醒 put中的wait
            this.notify();
        }
        return result;
    }
}
public class ThreadDemo23 {
    public static void main(String[] args) throws InterruptedException {
        MyBlockingQueue queue = new MyBlockingQueue();
        Thread customer = new Thread(()->{
            while (true){
                try {
                    int result = queue.take();
                    System.out.println("消费: " + result);
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        customer.start();

        Thread producer = new Thread(()->{
            int count = 0;
            while (true){
                try {
                    System.out.println("生产: " + count);
                    queue.put(count);
                    count++;
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        producer.start();
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/004299dc436649b0a75467b7eba97993.png)
通过上述代码我们就可以看到阻塞队列的效果.


## 定时器

类似于定闹钟,平时闹钟有两种风格:
1. 指定特定时刻,提醒.
2. 指定特定时间段之后,提醒.

咱们这里的定时器并非是提醒,而是执行一个准备好的方法/代码.这是咱们在开发中一个常用的组件,尤其是网络编程的时候.我们在网络通信等待的时候,等待不可能等待无限长,等待一段时间发现结果还没来,我们就可以通过定时器来去做一些操作来取消现在的等待.标准库给我们提供了定时器.


### 定时器使用

```java
public class ThreadDemo24 {
    public static void main(String[] args) {
        System.out.println("程序启动!");
        //这个 Timer 类就是标准库的定时器
        Timer timer = new Timer();
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                System.out.println("运行定时器任务1");
            }
        },3000);
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                System.out.println("运行定时器任务2");
            }
        },2000);
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                System.out.println("运行定时器任务3");
            }
        },1000);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/c4c158a6b0244292a26643d96a00b592.png)




![在这里插入图片描述](https://img-blog.csdnimg.cn/73243725f07a49dfaf9f63f2df5f8643.png)


### 手写定时器

1. 让被注册的任务,能在指定的时间,被执行.
2. 一个定时器是可以注册N个任务的.(这N个任务会按照最初约定的时间,按顺序执行)


为了完成第一点我们可以单独在定时器内部,搞个线程,让这个线程周期性扫描(N个任务),判定任务是否是到时间了,如果到时间了,就执行,没到时间就再等等.

为了达到可以注册N个任务的目的,我们可以使用数据结构来保存.

> **定时器里的核心**:


1. 有一个扫描线程,负责判定时间到/执行任务.
2. 一个数据结构(优先级队列),来保存所有被注册的任务.



> 优先级队列:按照时间小的,作为优先级高.此时队首元素就是整个队列中,最先要执行的任务.

此时扫描线程只需要扫描队首元素即可,不必遍历整个队列.



![在这里插入图片描述](https://img-blog.csdnimg.cn/d454419ebb634306ba472efae7e3fd1e.png)

```java
//表示定时器中的任务
class MyTask implements Comparable<MyTask> {

    //要执行的任务内容
    private Runnable runnable;
    //任务在什么时候执行(使用毫秒时间戳表示)
    private long time;
    public MyTask(Runnable runnable, long time) {
        this.runnable = runnable;
        this.time = time;
    }
    //获取当前任务时间
    public long getTime() {
        return time;
    }
    //执行任务
    public void run(){
        runnable.run();
    }
    @Override
    public int compareTo(MyTask o) {
        //返回小于0 大于0 ==0
        //this比o小 返回 < 0
        //this比o大 返回 > 0
        //this和o相同 返回 0
        //当前需要实现:队首元素是时间最小的任务
        return (int)(this.time - o.time);
    }
}
//自己写的定时器
class MyTimer{
    //扫描线程
    private Thread t = null;
    //阻塞优先级队列 保存任务
    private PriorityBlockingQueue<MyTask> queue = new PriorityBlockingQueue<>();
    public MyTimer(){
        t = new Thread(()->{
            while (true){
                try {
                    //取出对手元素,检查队首元素任务是否到时间了
                    //如果时间没到,就把任务塞回队列里去
                    //如果时间到了,就把任务进行执行
                    synchronized (this){
                        MyTask myTask = queue.take();//阻塞队列只能先把元素出队列,才好判定,不满足还得塞回去.
                        long curTime = System.currentTimeMillis();
                        if(curTime < myTask.getTime()){
                            //还没到点,先不必执行
                            queue.put(myTask);
                            //进行wait
                            this.wait(myTask.getTime() - curTime);
                        }else{
                            //时间到了 执行任务
                            myTask.run();
                        }
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        t.start();
    }
    //指定两个参数
    //第一个参数是 任务 内容
    //第二个参数是 任务 在多少毫秒之后执行 形如1000
    public void schedule(Runnable runnable,long after){
        //注意这里时间上的换算
        MyTask task = new MyTask(runnable,System.currentTimeMillis()  + after);
        queue.put(task);
        synchronized (this) {
            this.notify();
        }
    }
}
public class TestDemo25 {
    public static void main(String[] args) {
        MyTimer myTimer = new MyTimer();
        myTimer.schedule(new Runnable() {
            @Override
            public void run() {
                System.out.println("任务1");
            }
        },1000);
        myTimer.schedule(new Runnable() {
            @Override
            public void run() {
                System.out.println("任务2");
            }
        },2000);
    }
}
```



## 线程池

> **线程存在的意义**：

使用进程来实现并发编程，太重了,此时就引入了线程.线程是"轻量级进程",创建线程比创建进程更高效,销毁线程比销毁进程更高效.调度线程比调度进程更高效.所以使用多线程就可以在很多时候代替进程来实现并发编程.随着并发程度的提高,随着我们对于性能要求标准的提高,我们发现好像线程创建也没有那么轻量.当我们需要频繁创建销毁线程的时候,就发现开销还是挺大的,若想要进一步提高效率,有两种方法:
1. 搞一个"轻量级线程"----(协成/纤程).但是这个目前还没有被加入到java标准库.
2. 使用线程池(事先把需要使用的线程创建号,放到"池"中,后面需要使用的时候,直接从"池"里获取,如果用完了,也还给"池"),来降低创建/销毁线程的开销.


> 高效的原因:

创建线程/销毁线程,是交由操作系统内核完成的,从池子里获取/还给池,是咱们自己用户代码就能实现的,不必交给内核操作.相比于内核来说,用户态,程序执行的行为是可控的.要想做某个工作,就会非常干净利落的完成(从池子里取/还给池子).如果要是通过内核,从系统这里创建个线程,就需要通过系统调用,让内核来执行了(内核不只是给你一个应用程序服务,它是给所有的程序都要提供服务的).因此当使用系统调用执行内核代码的时候,无法确定内核都要做哪些工作,整体过程是"不可控"的.

### Java标准库中的线程池
Java标准库中也提供了现成的线程池,我们可以直接使用.

```java
//创建了一个线程池,池子里线程数目固定是10
ExecutorService pool = Executors.newFixedThreadPool(10);
```
上述操作使用某个类的某个静态方法,直接构造出一个对象来.(相当于把new操作给隐藏到这样的方法后面了),像这样的方法就称为"工厂方法",提供这个工厂方法的类,也就称为"工厂类".此时代码就使用了"工厂模式"这种设计模式.


#### 工厂模式

表示普通方法,来代替构造方法(只构造一种对象,好办,如果构造多种不同情况的对象,就难搞了),创建对象.

![在这里插入图片描述](https://img-blog.csdnimg.cn/a3c1c2abda8d4f0fb16a628b2a69d641.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/0704550705074d298fb70eafde6fed03.png)

为了解决上述问题我们就可以用工厂模式:

```java
class PointFactory{
	public static Point makePointByXY(double x,double y){}
	public static Point makePointByRA(double r,double a)()
}
```
普通方法,方法名没有限制,因此有多种方式构造,就可以直接使用不同的方法名即可.此时方法的参数是否要区分就不重要了.



构造完10个线程池就可以随时安排这些线程帮我们干活了.

```java
//使用标准库中的线程池
public class ThreadDemo26 {
    public static void main(String[] args) {
        ExecutorService pool = Executors.newFixedThreadPool(10);
        for (int i = 0; i < 1000; i++) {
            int n = i;
            pool.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.println("hello " + n);
                }
            });
        }
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/82d0888eac8b4ac297d36038de80ecee.png)
不同的程序特点不同,此时要设置的线程数也是不同的:
考虑两个极端情况:
1. CPU密集型,每个线程要执行的任务都是狂转CPU(进行一系列算数运算),此时线程池线程数,最多也不应该超过CPU核数.
2. IO密集型.每个线程的工作就是等待IO(读写硬盘,读写网卡,等待用户输入......),不吃CPU,此时这样的线程处于阻塞状态,不参与CPU调度.

在实际开发中没有程序符合上述的理想模型,真是的程序,往往一部分要吃CPU,一部分要等待IO.实践中确定线程数量很简单,通过测试/实验的方式

#### ThreadPoolExecutor构造方法参数

`corePoolSize` 核心线程数
`maximumPoolSize`最大线程数
`long keepAliveTime`和`TimeUnit unit` 描述了线程摸鱼的最大时间.
`BlockingQueue<Runnable> workQueue ` 线程池的任务队列.此处使用阻塞队列,每个工作线程都是不停尝试take的,如果有任务,就take成功,没有,就阻塞.
`ThreadFactor threadFactory`用于创建线程.线程池是需要创建线程的.
`RejectedExecutionHandler handler`描述了线程池的"拒绝策略",也是一个特殊的对象,描述了说当线程池任务队列满了,如果继续添加任务会有啥样的行为.如下:
![在这里插入图片描述](https://img-blog.csdnimg.cn/6ec0fa85eb274e2ebfa7e963443440f8.png)
1. 直接抛异常.
2. 多出来的任务,谁加的,谁负责.
3. 丢弃最早的任务.
4. 丢弃最新的任务.


### 手写简单线程池


> 实现固定数量线程的线程池:



```java
class MyThreadPool{
    //不涉及"时间",此处只有任务 直接使用Runnable即可
    private BlockingDeque<Runnable> queue = new LinkedBlockingDeque<>();
    //n表示线程的数量
    public MyThreadPool(int n){
        //创建出线程
        for (int i = 0; i < n; i++) {
            Thread t = new Thread(()->{
                while (true){
                    try {
                        Runnable runnable = queue.take();
                        runnable.run();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
            t.start();
        }
    }
    //注册任务给线程池
    public void submit(Runnable runnable){
        try {
            queue.put(runnable);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
public class ThreadDemo27 {
    public static void main(String[] args) {
        MyThreadPool pool = new MyThreadPool(10);
        for (int i = 0; i < 1000; i++) {
            int n = i;
            pool.submit(new Runnable() {
                @Override
                public void run() {
                    System.out.println("hello " + n);
                }
            });
        }
    }
}

```
