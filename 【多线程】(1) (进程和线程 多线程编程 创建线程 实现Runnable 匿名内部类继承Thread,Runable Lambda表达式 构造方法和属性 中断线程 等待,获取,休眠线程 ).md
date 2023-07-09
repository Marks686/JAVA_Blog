# 线程
线程是解决并发编程的前提下,让创建,销毁,调度的速度更快点,它是一个"轻量级进程".线程"轻"的原因是:它把申请资源/释放资源的操作给省下了.多进程是再调用一些新的空间和成本,多线程可以做到公用资源.


## 进程和线程
线程和进程的关系是**进程包含线程**,一个进程可以包含一个线程,也可以包含多个线程(不能没有),只有第一个线程启动的时候开销是比较大的,后续线程就省事了.



同一个进程里的多个线程之间是共用了进程的同一份资源:主要是内存(线程1 new的对象可以在线程2,3,4里直接使用)和文件描述表(在线程1打开的文件,在线程2,3,4里都可以直接使用).

操作系统实际调度的时候是以线程为单位进行调度,如果每个进程有多个线程了,每个线程是独立在CPU上调度的(线程是系统调度执行的基本单位).每个线程也都有自己的执行逻辑(执行流).操作系统调度的时候其实不关心进程,而是关心线程.

一个线程也是通过PCB来描述的,一个进程里面可能是对应一个PCB也可能是对应多个.同一个进程里的PCB之间,pid是一样的,内存指针和文件描述符表是一样的.谈到"调度"的时候已经和进程没什么关系了,进程专门负责资源分配,线程来接管和调度相关的一切内容.


如果一个线程抛异常,如果处理不好,很可能就把整个进程都给带走了,其他线程就挂了.

## 在Java中进行多线程编程

本身关于线程的操作,是操作系统提供的API.Java是一个跨平台的语言,很多操作系统的提供的功能都被JVM给封装好了.
Java操作多线程,最核心的类`Thread`.我们使用`Thread`类,不需要`import`别的包,因为它在`java.lang`下面,除此以外还有`String,StringBuilder,StringBuffer`.

### 继承Thread,重写run

我们创建一个线程是希望线程称为一个独立的执行流(执行一段代码).
```java
class MyThread extends Thread{
    @Override
    public void run() {
        System.out.println("hello world");
    }
}
public class ThreadDemo1 {
    public static void main(String[] args) {
        Thread t = new MyThread();
        t.start();
    }
}
```

`start();`是线程中的特殊方法:启动线程.
![在这里插入图片描述](https://img-blog.csdnimg.cn/0d42bcd2e8ee4877b491f005d28ca7b3.png)
创建一个新线程就是调用操作系统API,通过操作系统内核创建新线程的PCB,并且把要执行的指令交给这个PCB.当PCB被调度到CPU上执行的时候,也就执行到了线程`run()`方法中的代码了.`run`方法执行完毕,新的线程就自然销毁了.



#### 多线程体现并发执行效果

上代码:

```java
class MyThread extends Thread{
    @Override
    public void run() {
        while(true){
            System.out.println("hello thread");
        }
    }
}
public class ThreadDemo1 {
    public static void main(String[] args) {
        Thread t = new MyThread();
        t.start();

        while(true){
            System.out.println("hello main");
        }
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/bd3bfb301caf4286a2b6a1253b9bfb47.png)

虽然两个循环都是死循环但是我们有两个线程在同时干活,所以两个会交替同时打印,main中的和thread中的代码不一定谁先谁后,所以**操作系统在调度线程的时候是"抢占式执行",具体哪个线程先上,哪个线程后上取决于操作系统调度器具体实现的策略**.虽然存在优先级,但是在应用程序层面上无法修改,从应用程序(代码)的角度来看,就好像是线程之间的调度顺序是"随机"的一样,但是在内核里并不是随机的,干预的因素太多并且应用程序这一层也无法感知到细节,就只能认为是随机的.通过一些API只能进行有限度的干预.因为抢占式执行和随机调度的存在,所以就会有一些线程的安全问题.

#### start和run区别
start是真正创建了一个线程(从系统这里创建的),线程是独立的执行流.
run只是描述了线程要干的活是啥,如果直接在main中调用run,此时没有创建新线程,全是main线程在干活.
我们可以使用JDK自带的应用来查看进程的情况:`jconsole`

![在这里插入图片描述](https://img-blog.csdnimg.cn/26f8e3a0e33b4d45b8b59c2339affcce.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/ef7a2ef921ec4b05a314045f3afddcc5.png)
点进去连接上后:
![在这里插入图片描述](https://img-blog.csdnimg.cn/32b7500ea885434b931c997c15b14f0b.png)
剩余的其他线程都是JVM自带的.

## 创建线程

### 实现Runnable接口

`Runnable`作用是描述一个"要执行的任务",run方法就是任务的执行细节.

```java
class MyRunnable implements Runnable{
    @Override
    public void run() {
        System.out.println("hello thread");
    }
}
public class ThreadDemo2 {
    public static void main(String[] args) {
        Runnable runnable = new MyRunnable();
        Thread t = new Thread(runnable);
        t.start();
    }
}
```

解耦合,目的就是为了让线程和线程要干的活之间分离开.如果未来要改代码,不用多线程,使用多进程或者线程池或者协程......此时代码改动比较小.

### 使用匿名内部类继承Thread

```java
public class ThreadDemo3 {
    public static void main(String[] args) {
        Thread t = new Thread(){
            @Override
            public void run() {
                System.out.println("hello");
            }
        };
        t.start();
    }
}
```
1. 创建了一个Thread的子类(子类没有名字),所以才叫做"匿名".
2. 创建了子类的实例,并让t引用指向改实例.
### 使用匿名内部类实现Runable

```java
public class ThreadDemo4 {
    public static void main(String[] args) {
        Thread t = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("hello");
            }
        });
        t.start();
    }
}
```
这个写法和上一个写法一样只不过把实现Runnable任务交给匿名内部类的语法.此处是创建了一个类,实现Runnable,同时创建了类的实例并且传给了Thread的构造方法.


### 使用Lambda表达式

最简单,推荐写法:

```java
public class ThreadDemo5 {
    public static void main(String[] args) {
        Thread t = new Thread(()->{
            System.out.println("hello");
        });
        t.start();
    }
}
```
把任务用lambda表达式描述,直接把lambda传给Thread构造方法.


上述方法只是语法规则不同,本质上是一样的,这些方法创建出来的线程都是一样的.

# Thread类及常见方法
可以在官方文档多看:

![在这里插入图片描述](https://img-blog.csdnimg.cn/9898de4e2d434103a634b2464bc6e0b1.png)


##  Thread的常见构造方法



![在这里插入图片描述](https://img-blog.csdnimg.cn/7082b20f539442589303a7439be491e0.png)


`Thread(Runnable target, String name)`使用 Runnable 对象创建线程对象，并命名.

```java
public class ThreadDemo6 {
    public static void main(String[] args) {
        Thread t = new Thread(new Runnable() {
            @Override
            public void run() {
                while(true){
                    System.out.println("hello");
                }
            }
        },"mythread");
        t.start();
    }
}
```


## Thread 的几个常见属性

`getId()`获取ID
ID 是线程的唯一标识，不同线程不会重复
名称是各种调试工具用到





`getName()`获取名称,就是构造方法里面起的名字.
名称会在各种调试工具用到.

`getState()`获取线程状态,表示线程当前所处的一个情况(Java里线程的状态要比操作系统原生的状态更丰富一些)

`getPriority()` 获取优先级,也可以设置,但是设置了没什么用.优先级高的线程理论上来说更容易被调度到关于后台线程，需要记住一点：JVM会在一个进程的所有非后台线程结束后，才会结束运行。

`isDaemon()` 是否是守护线程,是否是"后台线程",前台线程会阻止进程结束,前台线程的工作没做完,进程是不能结束的.后台线程不会阻止进程结束,后台线程的工作没做完,进程是可以结束的.代码里手动创建的线程默认都是前台的.包括main默认也是前台的.其他的JVM自带的线程都是后台线程,是后台线程就是守护线程.

`isAlive()` 指内核里的pcb是否存在,如果是存在的话,说明线程正在干活,如果是false的话说明线程还没创建或者已经销毁.系统线程是否存活,简单的理解为 run 方法是否运行结束了.
![在这里插入图片描述](https://img-blog.csdnimg.cn/7ebdcd5bfbb24af28f0f4ff12a72d382.png)
在真正调用start之前,调用t.isAlive就是false.调用start之后,isAlive就是true.isAlive是在判断当前系统里面的这个线程是不是真的存在.如果线程把run运行结束了,此时线程销毁pcb随之释放.但是Thread t这个对象还不一定被释放.此时isAlive也是false.

`isInterrupted()` 是否中断


## 中断一个线程
中断的意思不是让线程立刻停止而是通知线程将要停止,是否真的停止,取决于线程这里具体的代码写法.


> 1.使用线程的标志位来控制线程是否停止.


在主线程里可以随时通过 flag 变量的取值,来操作 t 线程是否结束.


```java
public class ThreadDemo8 {
    private static boolean flag = true;

    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(()->{
            while (flag){
                System.out.println("hello thread");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        });
        t.start();
        Thread.sleep(3000);
        flag = false;
    }
}
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/964f66a191d34065a1b6d219d438fc08.png)

这个代码之所以能起到修改 flag , t 线程就结束,完全取决于 t 线程内部的代码,代码通过flag来控制循环.




> 使用Thread自带的标志位来进行判定


这个能够唤醒上面 sleep 这样的方法.

```java
public class ThreadDemo9 {
    public static void main(String[] args) throws InterruptedException {
        Thread t = new Thread(()->{
            while (!Thread.currentThread().isInterrupted()){
                System.out.println("hello thread");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
        });
        t.start();
        Thread.sleep(3000);
        t.interrupt();
    }
}
```

`currentThread()`这是Thread类的静态方法.通过这个方法可以获取到当前线程,哪个线程调用这个方法,就是得到哪个线程的对象引用,类似于this,这个方法是在`t.run`中被调用的,所以此处获取的线程就是 t 线程.

`isInterrupted()` 结果为true表示被终止,为false表示未被终止.

`interrupt()` 可以终止线程(`t.interrupt()`就是终止 t 线程).

![在这里插入图片描述](https://img-blog.csdnimg.cn/fd1e6ecd87614631b4765b9bee579980.png)
如果线程在sleep中休眠,此时调用`interrupt`会把 t 线程唤醒.从sleep中提前返回了,换句话说就会触发异常,`interrupt`会触发sleep内部的异常,导致sleep提前返回.
![在这里插入图片描述](https://img-blog.csdnimg.cn/2204e35ea18e4ba1a8e65465f6cc2b73.png)
前面三次是 t 正常执行,中间的是异常是调用`interrupt`触发了异常.后面发现 t 还在执行.
`interrupt`会做两件事:
1.把线程内部的标志位(boolean)给设置成true.
2.如果线程在进行sleep,就会触发异常,把sleep唤醒,在唤醒的同时还会把刚才设置的标志位,再设置回false(清空了标志位).



这就导致,当sleep的异常被catch完了之后,循环还会继续执行.


所以下面的写法可以认为是线程 t 忽略了终止请求.
![在这里插入图片描述](https://img-blog.csdnimg.cn/61f51784bb664120b778a035196d18ea.png)


下面写法就会立即响应终止请求.
![在这里插入图片描述](https://img-blog.csdnimg.cn/2e163a8dd3aa497581450ec79bf4f11e.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/05c5394920484664a3244e32598bc40b.png)
这样写就可以达到稍后终止线程的目的:
![在这里插入图片描述](https://img-blog.csdnimg.cn/8d303c86fa3a43dc91108c685322961c.png)
## 等待线程

线程是一个随机调度的过程.等待线程就是控制两个线程的结束顺序.

```java
public class ThreadDomo10 {
    public static void main(String[] args){
        Thread t = new Thread(()->{
            for (int i = 0; i < 3; i++) {
                System.out.println("hello thread");
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        t.start();
        System.out.println("join 之前");
        try {
            t.join();//此处的join就是让当前main线程来等待t线程执行结束.
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("join 之后");
    }
}
```

本身执行完 start 之后, t 线程和main线程就并发执行分头行动,main会继续往下执行, t 也会继续往下执行.当main遇到`t.join`就会发生阻塞,一直阻塞到 t 线程执行结束,main线程才会从join中恢复回来,才能继续往下执行,使用 t 线程肯定是比main先结束的.

![在这里插入图片描述](https://img-blog.csdnimg.cn/175494113b504680a649ac474426856f.png)
我们就从上面可以看到主线程,等待 t 线程执行完毕后,才继续往下执行了.

如果执行join的时候 t 已经结束了,此时join不会阻塞,就会立即返回.如下;
![在这里插入图片描述](https://img-blog.csdnimg.cn/3321e99f405c435d95d5176c81a85626.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/53b2d5f2b3b14c9a92edf57414ab15a1.png)

多线程是实现并发编程的基础方式.比较接近系统底层,使用起来不太友好.
![在这里插入图片描述](https://img-blog.csdnimg.cn/aa89ba1aeddd4451a82eedf5ed6255ae.png)

## 获取当前线程引用

`public static Thread currentThread();`返回当前线程对象的引用.在哪个线程中调用就能获取到哪个线程的实例.

```java
public class ThreadDemo {
    public static void main(String[] args) {
        Thread thread = Thread.currentThread();
        System.out.println(thread.getName());
   }
}
```

## 休眠当前线程

`public static void sleep(long millis)` 让线程休眠,本质上就是让各个线程不参与调度.(不去CPU上执行)----休眠当前线程 millis毫秒.
![在这里插入图片描述](https://img-blog.csdnimg.cn/2f95b8a8b0334abaacf6413bf44dfa8b.png)

```java
public class ThreadDemo {
    public static void main(String[] args) throws InterruptedException {
        System.out.println(System.currentTimeMillis());
        Thread.sleep(3 * 1000);
        System.out.println(System.currentTimeMillis());
   }
}
```


PCB使用链表来组织的.实际的情况并不是一个简单的链表,这是一系列以链表为核心的数据结构.

线程A调用 sleep ,A就会进入休眠状态,就会把A从链表(就绪队列)里拎出来放到另一个链表(阻塞队列),这个链表的PCB都是"阻塞状态",暂时不参与CPU的调度执行,一旦线程进入阻塞状态,对应PCB就进入阻塞队列了,此时就暂时无法参与调度了.比如:调用sleep(1000),对应的线程PCB就要再阻塞队列中待1000ms这么久.我们想让线程等待1000ms,但实际上考虑到调度的开销,对应的线程是无法在唤醒之后立即就执行的,实际上的时间间隔大概率要大于1000ms.
