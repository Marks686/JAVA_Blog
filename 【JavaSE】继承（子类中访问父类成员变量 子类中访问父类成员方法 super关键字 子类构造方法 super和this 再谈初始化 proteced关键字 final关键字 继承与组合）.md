# 继承
## 为什么需要继承
在面向对象的语言中存在继承的思想，我们看下面的代码：

```java
class Dog{
    public String name;
    public int age;

    public void eat()
    {
        System.out.println(name+" 正在吃饭！");
    }
    public void wangwang(){
        System.out.println(name+" 正在wangwang叫！");
    }
}
class Cat{
    public String name;
    public int age;

    public void eat()
    {
        System.out.println(name+" 正在吃饭！");
    }
    public void miaomiao(){
        System.out.println(name+" 正在miaomiao叫！");
    }
}
public class Test {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.name = "小黄";
        dog.eat();
        dog.wangwang();

        Cat cat = new Cat();
        cat.name = "小花";
        cat.eat();
        cat.miaomiao();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/4ea77846241943a48f36183066f2c898.png)
观察我们写的代码，写了一个狗类，一个猫类，不管是狗还是猫，它都有很多共同的属性
![在这里插入图片描述](https://img-blog.csdnimg.cn/cda1af04f76d41888c4a66bcd03e31ca.png)
假如我们能将目前这两个类当中共同的属性进行抽取，放到一个类当中，需要的时候去那个类拿就可以了。比如我们抽象一个A类出来，

```java
class A{
    public String name;
    public int age;

    public void eat()
    {
        System.out.println(name+" 正在吃饭！");
    }
}
class Dog extends A{
    public void wangwang(){
        System.out.println(name+" 正在wangwang叫！");
    }
}
class Cat extends A{
    public void miaomiao(){
        System.out.println(name+" 正在miaomiao叫！");
    }
}
public class Test {
    public static void main(String[] args) {
        Dog dog = new Dog();
        dog.name = "小黄";
        dog.eat();
        dog.wangwang();

        Cat cat = new Cat();
        cat.name = "小花";
        cat.eat();
        cat.miaomiao();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/3d4d1fdfb4bc4996ba1157ea4f5d0523.png)

继承最大的意义是：对代码可以进行复用。


![在这里插入图片描述](https://img-blog.csdnimg.cn/ed1db65b022d4e84a0d2661b77f715dc.png)

`Dog extends A`就是Dog继承了A这个类，我们把Dog叫做子类或者派生类，把A叫做父类、基类或者超类。
`extends`就是继承中的一个关键字。
**继承解决的问题就是：共性的抽取从而实现代码的复用。**

注意：
1. 子类会将父类中的成员变量或者成员方法继承到子类中了。
2. 子类继承父类之后，必须要新添加自己特有的成员，体现出与基类的不同，否则就没有必要继承了。




## 父类成员访问

### 子类中访问父类成员变量

```java
class Base{
    public int a;
    public int b;
}
class Derived extends Base{
    public int c;
    public void func(){
        System.out.println(a);
        System.out.println(b);
        System.out.println(c);
    }
}
public class Test2 {
    public static void main(String[] args) {
        Derived derived = new Derived();
        derived.func();
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/c453e2da63174849b4983aef0f39faaa.png)

#### 子类和父类成员变量同名


```java
class Base{
    public int a;
    public int b;

    public int c = 100;
}
class Derived extends Base{
    public int c = 10;
    public void func(){
        System.out.println(a);
        System.out.println(b);
        System.out.println(c);
    }
}
public class Test2 {
    public static void main(String[] args) {
        Derived derived = new Derived();
        derived.func();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/408c152c901b43be843d606c73dc18bd.png)

如果父类和子类有同名的成员变量，那么优先访问子类自己的。

注意：
1. 如果访问的成员变量子类中有，优先访问自己的成员变量。
2. 如果访问的成员变量子类中无，则访问父类继承下来的，如果父类也没有定义，则编译报错。
3. 如果访问的成员变量与父类中成员变量同名，则优先访问自己的。

### 子类中访问父类成员方法

#### 成员方法名不同

```java
class Base {
    public void methodA(){
        System.out.println("Base中的methodA()");
    }
}
class Derived extends Base{
    public void methodB(){
        System.out.println("Derived中的methodB()方法");
    }
    public void methodC(){
        methodB(); // 访问子类自己的methodB()
        methodA(); // 访问父类继承的methodA()
        // methodD(); // 编译失败，在整个继承体系中没有发现方法methodD()
    }
}
public class Test2 {
    public static void main(String[] args) {
        Derived derived = new Derived();
        derived.methodC();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ff6e09befd384fbab8aff9d63f2cf4b8.png)

#### 成员方法名字相同

```java
class Base {
    public void methodA(){
        System.out.println("Base中的methodA()");
    }
    public void methodB(){
        System.out.println("Base中的methodB()");
    }
}
class Derived extends Base{
    public void methodA(int a) {
        System.out.println("Derived中的method(int)方法");
    }
    public void methodB(){
        System.out.println("Derived中的methodB()方法");
    }
    public void methodC(){
        methodA(); // 没有传参，访问父类中的methodA()
        methodA(20); // 传递int参数，访问子类中的methodA(int)
        methodB(); // 直接访问，则永远访问到的都是子类中的methodB()，基类的无法访问到
    }
}
public class Test2 {
    public static void main(String[] args) {
        Derived derived = new Derived();
        derived.methodC();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b1756e0e0c574057b01451a1ae5cac3d.png)






**注意：**
1. 通过子类对象访问父类与子类中不同名方法时，优先在子类中找，找到则访问，否则在父类中找，找到则访问，否则编译报错。
2. 通过派生类对象访问父类与子类同名方法时，如果父类和子类同名方法的参数列表不同(重载)，根据调用方法适传递的参数选择合适的方法访问，如果没有则报错。


如果子类中存在与父类中相同的成员时，在子类中访问父类相同名称的成员要用super。
## super关键字
如果要在子类方法中访问父类同名成员时，Java提供了super关键字。该关键字主要作用：**在子类方法中访问父类的成员**。
1.`super.data` 在子类中访问父类的成员变量。
2.`super.func()` 在子类中访问父类的成员方法。

super就只是一个关键字，它可以增加我们代码的可读性。

注意：
1. 只能在非静态方法中使用。
2.  在子类方法中，访问父类的成员变量和方法。

## 子类构造方法
在继承关系上，在我们构造子类的时候，一定要先帮助父类进行构造。 

```java
class Animal{
    public String name;
    public int age;
    public Animal(){

    }
    public void eat(){
        System.out.println(name + " 正在吃饭！");
    }
    public Animal(String name,int age){
        this.name = name;
        this.age = age;
        System.out.println("带2个参数的构造方法！");
    }
}
class Bird extends Animal{
    public Bird(){
        super();
    }
}
class Dog extends Animal{
    /*public Dog(){
        super("二蛋",10);
    }*/
    public Dog(){
        super();
    }
    public Dog(String name,int age){
        super(name,age);
    }
    public void wangwang(){
        System.out.println(name + " wangwang叫!");
    }
}
class Cat extends Animal{
    public Cat(){
        super();
    }
    public int size;
    /*public Cat(){
        super("咪咪",11);
    }*/
    public Cat(String name,int age,int size){
        super(name,age);
        this.size = size;
    }
    public void miaomiao(){
        System.out.println(name+" miaomiao叫！");
    }
}
```
子类构造方法中默认会调用基类的无参构造方法：super(), 用户没有写时,编译器会自动添加，而且super()必须是子类构造方法中第一条语句，并且只能出现一次。
`super(name,age);`并没有帮我们构造一个父类对象，而是帮我们对继承过来的属性进行初始化。
`super()`只能在子类的构造方法中用。
![在这里插入图片描述](https://img-blog.csdnimg.cn/c485be163372437aa040422a6810fb0a.png)


## super和this
super和this都可以在成员方法中用来访问：成员变量和调用其他的成员函数，都可以作为构造方法的第一条语句。

**相同点:**
super和this都可以在成员方法中用来访问：成员变量和调用其他的成员函数，都可以作为构造方法的第一条语句。


**不同点:**
1. this是当前对象的引用，当前对象即调用实例方法的对象，super相当于是子类对象中从父类继承下来部分成员的引用。
2. 在非静态成员方法中，this用来访问本类的方法和属性，super用来访问父类继承下来的方法和属性。
3. 在构造方法中：this(...)用于调用本类构造方法，super(...)用于调用父类构造方法，两种调用不能同时在构造方法中出现。
4. 构造方法中一定会存在super(...)的调用，用户没有写编译器也会增加，但是this(...)用户不写则没有。
## 再谈初始化

```java
class Animal{
    public String name;
    public int age;
    static {
        System.out.println("Animal static()--静态代码块");
    }

    {
        System.out.println("Animal {}--实例代码块");
    }

    public Animal(){
        System.out.println("Animal()--不带参数的构造方法");
    }
    public Animal(String name,int age){
        this.name = name;
        this.age = age;
        System.out.println("Animal(String name,int age)带2个参数的构造方法！");
    }
    public void eat(){
        System.out.println(name + " 正在吃饭！");
    }
}
class Dog extends Animal{
    static {
        System.out.println("Dog static()--静态代码块");
    }

    {
        System.out.println("Dog {}--实例代码块");
    }
    public Dog(){
        System.out.println("DOG()--不带参数的构造方法");
    }
    public Dog(String name,int age){
        super(name,age);
    }
    public void wangwang(){
        System.out.println(name + " wangwang叫!");
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Dog dog = new Dog();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/8c002a92881e4d0ca4fff21ff7405010.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/0796ebc8c26845708b16e98a9b9f71ff.png)
当我们再new一个dog对象：

```java
public class Test {
    public static void main(String[] args) {
        Dog dog = new Dog();
        System.out.println("===========");
        Dog dog2 = new Dog();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/5f2597681a844d19b9622a929c625778.png)

第二次运行的时候发现没有静态的了，也就是说静态的只执行一次。

注意：
1. 静态代码块先执行，并且只执行一次，在类加载阶段执行。
2. 当有对象创建时，才会执行实例代码块，实例代码块执行完成后，最后构造方法执行。




> 结论： 
> 1、父类静态代码块优先于子类静态代码块执行，且是最早执行。
2、父类实例代码块和父类构造方法紧接着执行。
3、子类的实例代码块和子类构造方法紧接着再执行。
4、第二次实例化子类对象时，父类和子类的静态代码块都将不会再执行。


## proteced关键字

受保护的，同一个包里可以用，不同包里也可以用，但是不同包里用有一个前提：必须是子类。这里就体现在了继承上了。
1.
![在这里插入图片描述](https://img-blog.csdnimg.cn/2ef36483d3014de1a24ccf7ea58c4fd8.png)
2.
![在这里插入图片描述](https://img-blog.csdnimg.cn/70240fd23c524918be86b0c180eef3fb.png)

3.
![在这里插入图片描述](https://img-blog.csdnimg.cn/c9f81a7973284a4fadd27312a7a994d4.png)

正确的如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/e4607c628f074067bb2aae0156284ca0.png)

private修饰的成员，被继承了，只不过不能访问，
![在这里插入图片描述](https://img-blog.csdnimg.cn/bad7ac7e45bf4c29ae5b8cdec3bdc8b8.png)
上图报错的原因不是没有继承，而是权限是只能在Animal这个类里面用，出了Animal这个类用不了。


## final关键字
`final class Animal{}`
一个类前面加了final后，这个类就不能被继承了。这个类叫做密封类。
`final int A = 10`
此时A变为常量了。不可以被修改了。（final修饰常量，变量名大写）
`public final String name = "yyx"`
如果是成员变量定义为final语法规定，必须同时给定一个初始值。
## 继承与组合

组合：是一种代码的实现方式，设计思想。


```java
class Student{

}
class Teacher{

}
class School{
    //成员变量
    public Student[] students = new Student[3];
    public Teacher[] teachers = new Teacher[3];
}
public class Test {
    public static void main(String[] args) {
        School school = new School();
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/44ff9e40214f4b6f8272d7528f07b505.png)
