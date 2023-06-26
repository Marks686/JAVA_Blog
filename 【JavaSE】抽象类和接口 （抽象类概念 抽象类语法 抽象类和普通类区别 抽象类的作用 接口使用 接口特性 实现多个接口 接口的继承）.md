# 抽象类
## 抽象类概念
在面向对象的概念中，所有的对象都是通过类来描绘的，但是反过来，并不是所有的类都是用来描绘对象的，如果一个类中没有包含足够的信息来描绘一个具体的对象，这样的类就是抽象类。

## 抽象类语法
1. 抽象类用`abstract`修饰。
2. 抽象类不能实例化。
![在这里插入图片描述](https://img-blog.csdnimg.cn/440f33380b4646348bd8ee5a7a02639d.png)
3. 抽象类里面可以有普通方法也可以有抽象方法。
![在这里插入图片描述](https://img-blog.csdnimg.cn/99d85dc675d643ae9abe6328598cae11.png)
4. 抽象方法：一个方法被`abstract`修饰，没有具体的实现，只要包含抽象方法，这个类必须是抽象类。
5. 当一个普通类继承了这个抽象类，必须重写这个抽象方法。
![在这里插入图片描述](https://img-blog.csdnimg.cn/5da6969fa85f4dd1b19200406d64464f.png)
6. 抽象类存在的最大的意义就是被继承。
7. 抽象方法不能被`private,final,static`修饰，因为抽象方法要被子类重写。
![在这里插入图片描述](https://img-blog.csdnimg.cn/46c64b33acdb4c9b94aba4b93e702703.png)
8. 当一个子类没有重写抽象的父类的方法，可以把当前子类变成`abstract`修饰。
![在这里插入图片描述](https://img-blog.csdnimg.cn/74352785a79844f6af4ee3fce3be5ded.png)
9. 抽象类必须被继承，并且继承后子类要重写父类中的抽象方法，否则子类也是抽象类，必须要使用 abstract 修饰。


![在这里插入图片描述](https://img-blog.csdnimg.cn/295c43ed3a4645418f2601ab19900525.png)
 
10. 抽象类当中不一定包含抽象方法。

![在这里插入图片描述](https://img-blog.csdnimg.cn/a93032972b6e486691f33d2fae5f9c4e.png)

## 抽象类和普通类区别

1. 抽象类不能实例化，普通类可以。
2. 抽象类中可以包含非抽象方法和抽象方法，普通类只能包含非抽象方法。



## 抽象类的作用

抽象类本身不能被实例化, 要想使用, 只能创建该抽象类的子类. 然后让子类重写抽象类中的抽象方法。

使用抽象类的场景就如上面的代码, 实际工作不应该由父类完成, 而应由子类完成. 那么此时如果不小心误用成父类了, 使用普通类编译器是不会报错的. 但是父类是抽象类就会在实例化的时候提示错误, 让我们尽早发现问题。



很多语法存在的意义都是为了 "预防出错", 例如我们曾经用过的 final 也是类似. 创建的变量用户不去修改, 不就相当于常量嘛? 但是加上 final 能够在不小心误修改的时候, 让编译器及时提醒我们。

# 接口


接口就是公共的行为规范标准，大家在实现时，只要符合规范标准，就可以通用。
在Java中，接口可以看成是：多个类的公共规范，是一种引用数据类型。

## 接口使用

```java
public class 类名称 implements 接口名称{
// ...
}
```

> 请实现笔记本电脑使用USB鼠标、USB键盘的例子：

```java
public interface IUSB {
    void openDevice();
    void closeDevice();
}
```

```java
public class Computer {
    public void open(){
        System.out.println("开机！");
    }
    public void close(){
        System.out.println("关机！");
    }

    //是否使用usb
    public void useDevice(IUSB iusb){
        iusb.openDevice();
        if(iusb instanceof Mouse){
            Mouse mouse = (Mouse)iusb;
            mouse.click();
        }else if(iusb instanceof KeyBoard){
            KeyBoard keyBoard =(KeyBoard) iusb;
            keyBoard.inPut();
        }
        iusb.closeDevice();
    }
}

```

```java
public class KeyBoard implements IUSB{

    @Override
    public void openDevice() {
        System.out.println("打开键盘！");
    }

    @Override
    public void closeDevice() {
        System.out.println("关闭键盘！");
    }
    public void inPut(){
        System.out.println("敲击键盘！");
    }
}

```

```java
public class Mouse implements IUSB{

    @Override
    public void openDevice() {
        System.out.println("打开鼠标服务！");
    }

    @Override
    public void closeDevice() {
        System.out.println("关闭鼠标服务！");
    }
    public void click(){
        System.out.println("点击鼠标！");
    }
}

```

```java
public class Test {
    public static void main(String[] args) {
        Computer computer = new Computer();
        Mouse mouse = new Mouse();
        KeyBoard keyBoard = new KeyBoard();
        computer.useDevice(mouse);
        System.out.println("==========");
        computer.useDevice(keyBoard);
    }
}

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/4436c9a34f564ed39e8732c42a898fbe.png)



##  接口特性

1. 使用关键字`interface`来定义接口。
![在这里插入图片描述](https://img-blog.csdnimg.cn/857d3ddad5214bf4971963eadeab5e0a.png)
2. 接口不能被实例化。
![在这里插入图片描述](https://img-blog.csdnimg.cn/d922b97d8e6a4fcfbc24c0136dab47c0.png)
3. 可以直接创建一个接口。
![在这里插入图片描述](https://img-blog.csdnimg.cn/db0cbb98ef2d4e1f84f537231661dddb.png)

4. 接口当中的成员，默认是`public static final`。
![在这里插入图片描述](https://img-blog.csdnimg.cn/1bc7b7245b194e16920915049bb85809.png)

5. 接口中的方法，不写也是默认都是`public abstruct`的。
6. 接口中的方法不能有具体实现，但是从JDK8开始可以写一个`default`修饰的方法。
![在这里插入图片描述](https://img-blog.csdnimg.cn/96c2641505a34d3ea7defe5930f998a5.png)
7. 接口当中不能有构造方法。
![在这里插入图片描述](https://img-blog.csdnimg.cn/b0a96022dfea4fdba03d216cb2f529b9.png)

8. 接口需要被类实现，使用关键字`implements`。
![在这里插入图片描述](https://img-blog.csdnimg.cn/ddf3719b33d74957a286e135f9ccddfb.png)
9. 接口当中也可以有`static`修饰的方法。
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/d9b90f3be3654718b49c932f13097dd7.png)

## 实现多个接口

```java
abstract class Animal {
    public String name;

    public Animal(String name) {
        this.name = name;
    }
}
interface IRunning{
    public void run();
}
interface ISwimming{
    void swim();
}
interface IFly{
    void fly();
}
class Dog extends Animal implements IRunning{

    public Dog(String name) {
        super(name);
    }
    @Override
    public void run() {
        System.out.println(name+ " 正在用四条腿跑！");
    }
}

class Fish extends Animal implements ISwimming{

    public Fish(String name) {
        super(name);
    }
    @Override
    public void swim() {
        System.out.println(name+" 正在在游泳！");
    }
}

class Bird extends Animal implements IFly{

    public Bird(String name) {
        super(name);
    }

    @Override
    public void fly() {
        System.out.println(name+" 正在飞！");
    }
}


class Duck extends Animal implements IRunning,IFly,ISwimming{
    public Duck(String name) {
        super(name);
    }

    @Override
    public void run() {
        System.out.println(name+" 正在用两条腿跑！");
    }

    @Override
    public void swim() {
        System.out.println(name+ " 正在用两条腿游泳！");
    }

    @Override
    public void fly() {
        System.out.println(name+ " 正在用翅膀飞！");
    }
}
class Robot implements IRunning{

    @Override
    public void run() {
        System.out.println("机器人在用机器腿跑步！");
    }
}
public class Test {
    public static void walk(IRunning iRunning){
        iRunning.run();
    }
    public static void swim(ISwimming iSwimming){
        iSwimming.swim();
    }
    public static void main(String[] args) {
        walk(new Dog("旺财"));
        walk(new Duck("唐老鸭"));
        walk(new Robot());
        System.out.println("====================");
        swim(new Fish("七秒"));
        swim(new Duck("唐老鸭2"));
    }
}
```

## 接口的继承
在Java中，类和类之间是单继承的，一个类可以实现多个接口，接口与接口之间可以多继承。即：用接口可以达到
多继承的目的。
接口可以继承一个接口, 达到复用的效果. 使用 extends 关键字。
![在这里插入图片描述](https://img-blog.csdnimg.cn/4244a892762c4ed7a37b2a4f5e0c6ef9.png)

```java
interface A{
    void funcA();
}
interface B{
    void funcB();
}
interface C extends A,B{
    void funcC();
}
class D implements C{
    @Override
    public void funcA() {

    }
    @Override
    public void funcB() {

    }
    @Override
    public void funcC() {

    }
}
```

总结：
1. 类可以继承一个普通类。
2. 类可以继承一个抽象类。
3. 类可以继承一个普通类/抽象类，同时实现多个接口（`implements`A，B）。
4. 接口可以通过`extends`拓展多个接口的功能。
