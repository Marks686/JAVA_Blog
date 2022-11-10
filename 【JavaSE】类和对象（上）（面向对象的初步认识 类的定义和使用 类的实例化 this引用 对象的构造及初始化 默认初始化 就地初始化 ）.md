# 一、面向对象的初步认识
## 1.1什么是面向对象
Java是一门纯面向对象的语言(OOP),Java里面一切皆对象。
面向对象是解决问题的一种思想，主要依靠对象之间的交互完成一件事情。
## 1.2面向对象与面向过程
拿洗衣服举例子：
之前我们洗衣服的流程，注重的是每一个过程我们是怎么做的，
![在这里插入图片描述](https://img-blog.csdnimg.cn/59f5db812213434886c854b839268a6f.png)

而面向对象是我们抽象出来很多对象，比如我们现在洗衣服：
![在这里插入图片描述](https://img-blog.csdnimg.cn/eec515c3a6b74d3e919750411dfc7937.png)
我们需要通过若干对象与对象之间相互协作，然后完成这些事情。具体衣服是怎么洗的跟我们没关系。
如何描述对象呢？


# 二、类的定义和使用

## 2.1简单认识类

> 类是用来对一个实体对象来进行描述的，主要描述实体的属性、功能，描述完计算机识别就行了。

类的实质是一种引用数据类型，类似于byte、short、int(char)、long、float、double等基本数据类型，不同的是它是一种复杂的数据类型。因为它的本质是数据类型，而不是数据，所以不存在于内存中，不能被直接操作，只有被实例化为对象时，才会变得可操作。


## 2.2类的定义格式
定义类：用到class关键字

```java
//创建类
//class-->大驼峰
class WashMachine{
    //属性【字段】-->成员属性

    //行为【方法】-->成员方法
}
```
class为定义类的关键字，WashMachine为类的名字、{}中是类的主体。
定义一个洗衣机类：

```java
//创建类
//class-->大驼峰
class WashMachine{
    //属性【字段】-->成员属性
    public String brand; // 品牌
    public String type; // 型号
    public double weight; // 重量
    public double length; // 长
    public double width; // 宽
    public double height; // 高
    public String color; // 颜色
    //行为【方法】-->成员方法
    public void washClothes(){ // 洗衣服
        System.out.println("洗衣功能");
    }
    public void dryClothes(){ // 脱水
        System.out.println("脱水功能");
    }
    public void setTime(){ // 定时
        System.out.println("定时功能");
    }
}
```

> **注意事项
1.类名注意采用大驼峰
2.成员前写法统一为public
3.此处写的方法不带static关键字**

定义一个狗类：

```java
class Dog{
    public String name;
    public String color;

    public void barks(){
        System.out.println(name+"汪汪叫！");
    }
    public void wag(){
        System.out.println(name+"摇尾巴！");
    }
}
```

> 注意：
> 1.一般情况下一个java文件是一个类
>2. main方法所在的类一般要使用public修饰(注意：Eclipse默认会在public修饰的类中找main方法)
>3. public修饰的类必须要和文件名相同
>4. 不要轻易去修改public修饰的类的名称，如果要修改，通过开发工具修改

若想要改类名，如下操作
![在这里插入图片描述](https://img-blog.csdnimg.cn/89eee3a8302f4082b973e788dc44685c.png)
用idea全局帮你修改，只要用到的都会被改。

# 三、类的实例化
## 3.1什么是类的实例化
上面定义了类，那么我们定义的洗衣机、狗真的就是洗衣机和狗吗？
其实我们上面的只是一个类似模板的图纸一样的。
定义了一个类，就相当于在计算机中定义了一种新的类型，是用户自定义了一个新的类型，有了这些自定义的类型之后，就可以使用这些类来定义实例。
**用类型创建对象的过程，称为类的实例化**，在java中采用new关键字，配合类名来实例化对象。
例如：

```java
class Dog{
    public String name;
    public String color;

    public void barks(){
        System.out.println(name+"汪汪叫！");
    }
    public void wag(){
        System.out.println(name+"摇尾巴！");
    }
}
public class Test {
    public static void main(String[] args) {
        //实例化一个Dog对象
        Dog dog = new Dog();
        dog.name = "来福";
        dog.color = "黑色";

        System.out.println(dog.name);
        System.out.println(dog.color);

        dog.barks();
        dog.wag();
        
        Dog dog2 = new Dog();
        dog2.name = "小白";
        dog2.color = "白色";

        Dog dog3 = new Dog();

        Dog dog4 = new Dog();
        //通过一个类可以实例化无数个对象
    }
}
```

```java
来福
黑色
来福汪汪叫！
来福摇尾巴！
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ae95de3fde1e46b8b22a64ec25cd56b4.png)

1.我们访问成员属性和成员方法都用 点 来访问。
2.new关键字用于创建一个对象的实例。
3.同一个类可以创建多个实例


## 3.2类和对象的说明

 1.类是一种自定义的类型。
 2.一个类可以实例化多个对象，实例化出的对象占用实际的物理空间，存储类成员变量。
 

```java
Dog dog = new Dog();
Dog dog2 = new Dog();
Dog dog3 = new Dog();
Dog dog4 = new Dog();
```

3.类实例化出的对象好比用建筑设计图纸建造出房子，类就是设计图纸。

```java
class Dog{
    public String name;
    public String color;

    public void barks(){
        System.out.println(name+"汪汪叫！");
    }
    public void wag(){
        System.out.println(name+"摇尾巴！");
    }
}
public class Test {
    public static void main(String[] args) {
        //实例化一个Dog对象
        Dog dog = new Dog();
        System.out.println(dog.name);
        System.out.println(dog.color);
        dog.barks();
        dog.wag();
    }
}
```

```java
null
null
null汪汪叫！
null摇尾巴！
```

> **当成员变量没有初始化的时候，默认值就是对应的初始值。**
引用类型-->null
int --> 0
float--> 0.0f
boolean -->false
char --> '\u0000'

# 四、this引用
## 4.1为什么要有this引用
 引入一个日期类的例子：
 

```java
public class TestDate {
    public int year;
    public int month;
    public int day;

    public void setDay(int y, int m, int d){
        year = y;
        month = m;
        day = d;
    }

    public void printDate(){
        System.out.println(year+"-"+month+"-"+day);
    }

    public static void main(String[] args) {
//构建三个日期类型的对象testDate1、testDate2、testDate3
        TestDate testDate1 = new TestDate();
        TestDate testDate2 = new TestDate();
        TestDate testDate3 = new TestDate();

//对testDate1、testDate2、testDate3进行日期设置
        testDate1.setDay(2022,8,16);
        testDate2.setDay(2022,8,17);
        testDate3.setDay(2022,8,18);

//打印日期中的内容
        testDate1.printDate();
        testDate2.printDate();
        testDate3.printDate();
    }
}
```
上面代码定义了一个日期类，代码整体逻辑没有问题，细思之下有以下两个疑问：

> 1.形参名不小心与成员变量名相同

```java
    public void setDay(int year, int month, int day){
        year = year;
        month = month;
        day = day;
    }
```

这样的话函数体中是谁给谁赋值？成员变量给成员变量？参数给参数？参数给成员变量？成员变量给参数？此时非常的混乱。

> 2.三个对象都在调用setDate和printDate函数，但是这两个函数中没有任何有关对象的说明，setDate和printDate函数怎么知道打印的是哪个对象的数据呢？

上述问题我们都需要用this来解决！

## 4.2什么是this引用

> this引用指向当前对象(成员方法运行时调用该成员方法的对象)，在成员方法中所有成员变量的操作，都是通过该
引用去访问。只不过所有的操作对用户是透明的，即用户不需要来传递，编译器自动完成。


![在这里插入图片描述](https://img-blog.csdnimg.cn/5967f24be3134459965ea4ce92b0817a.png)

注意：**this引用的是调用成员方法的对象**

```java
public class TestDate {
    public int year;
    public int month;
    public int day;
   
    public void setDay(int year, int month, int day){
        this.year = year;
        this.month = month;
        this.day = day;
    }
    public void printDate(){
        System.out.println(year+"-"+month+"-"+day);
    }
     public static void main(String[] args) {
        TestDate testDate1 = new TestDate();
        testDate1.setDay(2022,8,18);
        testDate1.printDate();
    }
}
```


## 4.3 this引用的特性

> 1.this的类型：对应类型引用，即哪个对象调用就是哪个对象的引用类型。
> 2.this只能在“成员方法中使用。
3.在“成员方法”中，this只能引用当前对象，不能再引用其他对象。

![在这里插入图片描述](https://img-blog.csdnimg.cn/1d10f60b3af84da38cc35ed4fbedb4c2.png)
例：写一个学生类并使用到this引用

```java
public class Student {
    public String name;
    public int age;

    public void setStudent(String name,int age){
        this.name = name;
        this.age = age;
    }
    public void printStudent(){
        System.out.println(this.name+"-"+this.age);
    }
    public static void main(String[] args) {
        Student student = new Student();
        student.setStudent("lihua",19);
        student.printStudent();
    }
}

```

# 五、对象的构造及初始化
## 5.1 初始化对象
我们知道在Java方法内部定义一个局部变量时，必须要初始化，否则会编译失败，但是如果是对象的话不去赋值是可以编译的，因为他是一个引用变量。
## 5.2 构造方法
### 5.2.1 概念

> **构造方法：名字必须与类名相同，在创建对象时，由编译器自动调用，并且在整个对象的生命周期内只调用一次。**
构造方法是对对象中的成员进行初始化，并不负责给对象开辟空间。

![在这里插入图片描述](https://img-blog.csdnimg.cn/1663b823fd5b4d14a6f1b3a21098b9c6.png)
实例：

```java
    public Student(){
        System.out.println("不带参数的构造方法");
    }

    public Student(String name, int age){
        System.out.println("带两个参数的构造方法");
        this.name = name;
        this.age = age;
    }
    public static void main(String[] args) {
        Student student = new Student();
        Student student2 = new Student("小白",19);
    }
```
输出如下:
```java
不带参数的构造方法
带两个参数的构造方法
```
当我们运行的时候发现不带参数的构造方法和带两个参数的构造方法都被打印，说明在**实例化对象的时候会调用构造方法**，而且调用构造方法的时候可以传参对你对象中的成员属性进行赋值。
换句话说，**要想实例化对象一定得调用构造方法。所以当构造方法调用完成之后，那么对象实际上才真正的产生了。**
还有很重要的一点是**构造方法是可以被重载的**

### 5.2.2 特性

> 1.名字必须与类名相同
2.没有返回值类型，设置为void也不行
3.创建对象时由编译器自动调用，并且在对象的生命周期内只调用一次(相当于人的出生，每个人只能出生一次)
4.构造方法可以重载(用户根据自己的需求提供不同参数的构造方法)















**我们可以通过this()访问构造方法：**

>   this( )
> 必须放在构造方法里面，并且必须是第一行。
> 不能循环调用。

![在这里插入图片描述](https://img-blog.csdnimg.cn/3773d56d94af4134a23133a102dcc970.png)
程序先走第二个构造方法，在走第一个构造方法。
输出如下：
```java
带两个参数的构造方法
不带参数的构造方法
```

## 5.3 默认初始化

> 初始化所分配的空间：对象空间被申请好之后，对象中包含的成员已经设置好了初始值。

![在这里插入图片描述](https://img-blog.csdnimg.cn/5f996fd152d64b5ea5e376df9125285c.png)

如何快速生成构造方法：
![在这里插入图片描述](https://img-blog.csdnimg.cn/d241f4b0a9d147c6a6186b9918e77495.png)

## 5.4 就地初始化
在声明成员变量时，就直接给出了初始值。

```java
public class Date {
    public int year = 1900;
    public int month = 1;
    public int day = 1;
    public Date(){
    }
    public Date(int year, int month, int day) {
    }
    public static void main(String[] args) {
        Date d1 = new Date(2021,6,9);
        Date d2 = new Date();
    }
}
```
注意：代码编译完成后，编译器会将所有给成员初始化的这些语句添加到各个构造函数中

> 解析字节码插件：

![在这里插入图片描述](https://img-blog.csdnimg.cn/9e22b6715240485daa800ad70c91f5f0.png)
