# Callable 接口

类似于Runnable用来描述一个任务,Runnable描述的任务没有返回值,但是Callable描述的任务是有返回值的.
如果我们需要使用一个线程单独的计算出某个结果,此时使用Callable是比较合适的.


# JUC的常见类
java.util.concurrent这个包里放了并发编程(多线程)相关的组件.多线程是实现并发编程的一种具体方式.(同时也是Java中提供的默认的方式)除了这种方式外,还有其他的并发编程模型.
 
 

> 创建线程计算 1 + 2 + 3 + ... + 1000



```java
public class ThreadDemo29 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        Callable<Integer> callable = new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                int sum = 0;
                for (int i = 0; i < 1000; i++) {
                    sum += i;
                }
                return sum;
            }
        };
        FutureTask<Integer> futureTask = new FutureTask<>(callable);
        Thread t = new Thread(futureTask);
        t.start();
        Integer result = futureTask.get();
        System.out.println(result);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/d426343a03d74c8a9e519fd60a43057b.png)



![在这里插入图片描述](https://img-blog.csdnimg.cn/a3d947549bdf41b3abbe5b5a2f82a6a1.png)
总结:

1. Callable 和 Runnable 相对, 都是描述一个 "任务". Callable 描述的是带有返回值的任务,Runnable 描述的是不带返回值的任务.
2. Callable 通常需要搭配 FutureTask 来使用. FutureTask 用来保存 Callable 的返回结果. 因为Callable 往往是在另一个线程中执行的, 啥时候执行完并不确定.
4. FutureTask 就可以负责这个等待结果出来的工作.

## ReentrantLock

可重入锁,这里ReentrantLock是标准库给我们提供的另一种锁,顾名思义也是"可重入的".synchronized是基于代码块的方式来进行加锁解锁的.ReentrantLock更传统,它使用lock和unlock方法进行加锁解锁.

```java
public class ThreadDemo30 {
    public static void main(String[] args) {
        ReentrantLock reentrantLock = new ReentrantLock();
        reentrantLock.lock();
        try{
            //...
        }finally {
            reentrantLock.unlock();
        }
    }
}
```


ReentrantLock 和 synchronized 的区别:
1. synchronized 是一个关键字, 是 JVM 内部实现的(大概率是基于 C++ 实现). ReentrantLock 是标准库的一个类, 在 JVM 外实现的(基于 Java 实现).
2. synchronized 使用时不需要手动释放锁. ReentrantLock 使用时需要手动释放. 使用起来更灵活,但是也容易遗漏 unlock.
3. synchronized 在申请锁失败时, 会死等. ReentrantLock 可以通过 trylock 的方式等待一段时间就放弃.

```java
public class ThreadDemo30 {
    public static void main(String[] args) {
        ReentrantLock reentrantLock = new ReentrantLock(true);
        boolean result = reentrantLock.tryLock();
        try{
            if(result){
                //做需要考虑线程安全的操作
            }else{
                // 啥都不做
            }
        }finally {
            if(result){
                reentrantLock.unlock();
            }
        }
    }
}
```

4. synchronized 是非公平锁, ReentrantLock 默认是非公平锁. 可以通过构造方法传入一个 true 开启公平锁模式.
5. 更强大的唤醒机制. synchronized 是通过 Object 的 wait / notify 实现等待-唤醒. 每次唤醒的是一个随机等待的线程. ReentrantLock 搭配 Condition 类实现等待-唤醒, 可以更精确控制唤醒某个指定的线程.


## 原子类
原子类:
`AtomicBoolean`
`AtomicInteger`
`AtomicIntegerArray`
`AtomicLong`
`AtomicReference`
`AtomicStampedReference`


 AtomicInteger 举例，常见方法:
 

```java
addAndGet(int delta); i += delta;
decrementAndGet(); --i;
getAndDecrement(); i--;
incrementAndGet(); ++i;
getAndIncrement(); i++;
```
基于CAS确实更高效的解决了线程安全问题,但是CAS不能代替锁.CAS使用范围是有限的,不像锁适用范围广.


## 信号量 Semaphore
信号量, 用来表示 "可用资源的个数". 本质上就是一个计数器.此处的信号量是Java把操作系统原生的信号量封装了一下.

> 可以把信号量想象成是停车场的展示牌: 当前有车位 100 个. 表示有 100 个可用资源.当有车开进去的时候, 就相当于申请一个可用资源, 可用车位就 -1 (这个称为信号量的 P 操作)当有车开出来的时候, 就相当于释放一个可用资源, 可用车位就 +1 (这个称为信号量的 V 操作)如果计数器的值已经为 0 了, 还尝试申请资源, 就会阻塞等待, 直到有其他线程释放资源.



Semaphore 的 PV 操作中的加减计数器操作都是原子的, 可以在多线程环境下直接使用.


```java
public class ThreadDemo31 {
    public static void main(String[] args) throws InterruptedException {
        Semaphore semaphore = new Semaphore(4);
        semaphore.acquire();
        System.out.println("执行一次 P 操作");
        semaphore.acquire();
        System.out.println("执行一次 P 操作");
        semaphore.acquire();
        System.out.println("执行一次 P 操作");
        semaphore.acquire();
        System.out.println("执行一次 P 操作");
        semaphore.release();
    }
}

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/6fc45aa5eab54299b1816ee2922cad04.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/01d797af54004cf1a180a83b5899dae4.png)

## CountDownLatch

同时等待 N 个任务执行结束. 类似于跑步比赛，10个选手依次就位，哨声响才同时出发；所有选手都通过终点，才能公布成绩。


为了等待这个比赛的结束,就引入了CountDownLatch.
主要是两个方法:
1. await(wait是等待,a=> all,a前缀的术语很多表示的是"异步",这里a是all的意思)主线程来调用这个方法.
2. countDown 表示选手冲过了终点线.countDownLatch在构造的时候指定一个计数(选手的个数)

例如:指定四个选手比赛,初始情况下,调用await就会阻塞,每个选手都冲过终点,都会调用countDown方法.前三次调用countDown,await没有任何影响,但是第四次调用countDown,await就会被唤醒,返回(解除阻塞).此时就可以认为是整个比赛都结束了.


在实际开发中CountDownlatch有很多使用场景,例如下载器:迅雷 steam idm fdm qBittorrent 这些下载器可以给我们提供多线程下载.我们可以使用CountDownLatch来进行区分是不是整体下载完了.

# 线程安全的集合类

Java标准库里大部分集合类都是"线程不安全"的.Vector.StackHashTable这几个类关键方法带有synchronized,是少数的线程安全的集合类.


## 多线程环境使用ArrayList

1. 自己加锁,使用synchronized或者ReentrantLock.
2. 标准库提供了Collections.synchronizedList 这里会提供一些ArrayList相关方法,同时是带锁的,使用这个方法把集合类套一层.
3. CopuOnWriteArrayList,简称为COW,也叫做"写时拷贝".如果我们针对ArrayList进行读操作,不做任何额外的工作.如果进行写操作,则拷贝一份新的ArrayList,针对新的进行修改,修改过程中如果有读操作,就继续读旧的这份数据.当修改完毕了,使用新的替换旧的(本质就是一个引用之间的赋值,操作是原子的).这种方式优点是不需要加锁;缺点则是要求这个ArrayList不能太大,只适用于这种数组比较小的情况下.

## 多线程环境使用哈希表

多线程环境使用HashMap线程是不安全的.另一个选择是HashTable是线程安全的.给关键方法加了synchronized,更推荐的是使用ConcurrentHashMap.这是一个更优化的线程安全哈希表.

> ConcurrentHashMap进行了哪些优化?和HashTable之间区别?

1. 最大的优化之处:ConcurrentHashMap相比于HashTable大大缩小了锁冲突的概率.把一把大锁转成多把小锁了.HashTable是直接在方法上加synchronized,等于是给this加锁.只要操作哈希表上的任意元素,都会产生加锁也就可能都会发生锁冲突.但实际上基于哈希表的结构特定,有些元素在进行并发操作的时候是不会产生线程安全问题的,也就不需要使用锁控制.ConcurrentHashMap此时把锁的粒度变小了,每个链表有各自的锁.每个链表的头结点,作为锁对象.上述的情况是针对JDK1.8及其以后的情况,在1.7和之前,ConcurrentHashMap使用的是"分段锁",即几个链表使用一把锁(本质一样,缩小锁的范围从而降低锁冲突的概率).
2. ConcurrentHashMap做了一个激进的操作,针对读操作不加锁,只针对写操作加锁.之前我们了解过那种操作会有一下情况:读和读没冲突,写和写之间有冲突,读和写之间也没有冲突---可能会发生脏读.ConcurrentHashMap虽然针对读操作不加锁,只针对写操作加锁,但是它的写操作是原子的,使得读和写之间没有脏读.(volatile + 原子的写操作)
3. ConcurrentHashMap内部充分使用了CAS,通过这个进一步削减加锁操作的数目.比如:维护元素个数.
4. 针对扩容,采取了"化整为零"的方式.
![在这里插入图片描述](https://img-blog.csdnimg.cn/b263c14e521c4126972f58a93a043550.png)
ConcurrentHashMap中,扩容采取的是每次搬运一小部分元素的方式.创建新的数组,旧的数组也保留.每次put操作都往新数组上添加,同时进行一部分搬运(把一小部分旧的元素搬运到新数组上),每次get的时候,则旧数组和新数组都查询,每次remove的时候,只是把元素删了就行了,经过一定时间后,所有元素都搬运好了最终释放数组.



