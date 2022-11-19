# 六、 封装
## 6.1 封装的概念
* 封装：将数据和操作数据的方法进行有机结合，隐藏对象的属性和实现细节，仅对外公开接口来和对象进行 交互。

面向对象程序三大特性：封装、继承、多态。
封装简单来说就是套壳屏蔽细节。比如说你的手机、电脑，你见到的只是一个精致的东西，它已经被封装好了，地下的电路板等等你都看不到，对内部的细节进行封装，对外提供了接口，这就是一个简单的封装，要实现封装，要修改一定的权限，权限由访问修饰限定符进行修改。





## 6.2 访问限定符
> 访问修饰限定符：`private`、`default`、 `protected`、 `public`

`default`是默认的，用的最多的是`public`。

`public`：公开的，谁都可以看得到。
`default`: 对于同一个包中不是什么秘密，对于其他来说就是隐私。
`private`：私有的，只有自己知道，别人不知道。

## 6.3 封装扩展之包
### 6.3.1 包的概念
为了更好的管理类，把多个类收集在一起称为一组，称为软件包，类似于目录，将相似的东西放在一个文件夹，也可更加细致的分类。
包是对类、接口等的封装机制的体现，是一种对类或者接口等的很好的组织方式，比如：一个包中的类不想被其他包中的类使用。包还有一个重要的作用：**在同一个工程中允许存在相同名称的类，只要处不同的包中即可**。

包：
![在这里插入图片描述](https://img-blog.csdnimg.cn/d9c3f9ae09aa4636ae9ad63e634f453e.png)

### 6.3.2 导入包中的类
使用 `java.util.Date` 导入 `java.util` 这个包中的 `Date`类。

![在这里插入图片描述](https://img-blog.csdnimg.cn/0b3537d81f5f433fac5d6257a457da13.png)


```java
public class Test {
    public static void main(String[] args) {
        java.util.Date date = new java.util.Date();
// 得到一个毫秒级别的时间戳
        System.out.println(date.getTime());
    }
}
```

 更好的方法：使用 `import`语句导入包.
 

```java
import java.util.Date;
public class Test {
    public static void main(String[] args) {
        Date date = new Date();
// 得到一个毫秒级别的时间戳
        System.out.println(date.getTime());
    }
}
```

如果需要使用 `java.util` 中的其他类, 可以使用 `import java.util.*`

```java
import java.util.*;
public class Test {
    public static void main(String[] args) {
        Date date = new Date();
// 得到一个毫秒级别的时间戳
        System.out.println(date.getTime());
    }
}
```

### 6.3.3 自定义包


如何建文件夹？
![在这里插入图片描述](https://img-blog.csdnimg.cn/1b987699fbef41edaab720982a2131e4.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/df0ddaaa02d44228abc386aaa8460e5e.png)

### 6.3.4 包的访问权限控制举例
![在这里插入图片描述](https://img-blog.csdnimg.cn/42b4a9b778a247c4986e2b2ad23709b3.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/ab21ed4a4903400da51eaf39acb04395.png)
同一个包底下，不同的类可以访问，Test和Test2是两个不同的类，但是可以访问。
例如下面，不是一个包的，所以不可以访问，
![在这里插入图片描述](https://img-blog.csdnimg.cn/22d3cbcdbe2e4b9db641f8d3626cdaf5.png)
不同包之间不能访问。但是可以实例化。
### 6.3.5 常见的包
* 1. `java.lang`:系统常用基础类(`String`、`Object`),此包从`JDK1.1`后 自动导入。
* 2. `java.lang.reflect`:java 反射编程包;
* 3. `java.net`:进行网络编程开发包。
* 4. `java.sql`:进行数据库开发的支持包。
* 5. `java.util`:是java提供的工具程序包。(集合类等) 非常重要
* 6. `java.io:I/O`编程开发包。

# 七、内部类
## 7.1 内部类

> 内部类：

* 1.实例内部类
* 2.静态内部类
* 3.局部内部类
* 4.匿名内部类

当一个事物的内部，还有一个部分需要一个完整的结构进行描述，而这个内部的完整的结构又只为外部事物提供服务，那么整个内部的完整结构最好使用内部类。在 Java 中，**可以将一个类定义在另一个类或者一个方法的内部，前者称为内部类，后者称为外部类。**内部类也是封装的一种体现。

```java
public class OutClass {
class InnerClass{
}
}
// OutClass是外部类
// InnerClass是内部类
```
* 定义在class 类名`{}`花括号外部的，即使是在一个文件里，都不能称为内部类。
*  内部类和外部类共用同一个java源文件，但是经过编译之后，内部类会形成单独的字节码文件。
### 7.1.1 实例内部类

```java
class OuterClass {
    public int date1;
    int date2;
    public static int date3;

    public void test() {
        System.out.println("OuterClass::test()");
    }

    //实例内部类`在这里插入代码片`
    class InnerClass {
        public int date4;
        int date5;

        //public static int date6;error
        public void func() {
            System.out.println("InnerClass::func()");
        }
    }
}
```
如何获取实例内部类的对象？

```java
    public static void main(String[] args) {
        OuterClass outerClass = new OuterClass();
        OuterClass.InnerClass innerClass = outerClass.new InnerClass();
    }
```

**注意**：实例内部类当中不能有静态的成员变量。


```java
    //实例内部类
    class InnerClass {
        public int date4;
        int date5;
        //public static int date6;error
        public void func() {
            System.out.println("InnerClass::func()");
        }
    }
```

它被调用的时候是要通过外部类对象的引用，有了外部类对象以后才能调用它，但是`static`是类的成员，类加载的时候它就会被执行。所以实例内部类当中不能有静态的成员变量。实例内部类比较特殊，写成下面的写法就可以了，

```java
    //实例内部类
    class InnerClass {
        public int date4;
        int date5;

        public static final int date6 = 10;
        public void func() {
            System.out.println("InnerClass::func()");
        }
    }
```
不报错的原因是加了`final`后，值相当于一个常量，常量就是在编译的时候确定`date6`就是10。`final`是用来定义常量的，常量是在程序编译的时候确定的，一旦初始化就不能再进行修改了。

```java
class OuterClass {
    public int date1 = 1;
    int date2 = 2;
    public static int date3 = 3;

    public void test() {
        System.out.println("OuterClass::test()");
    }

    //实例内部类
    class InnerClass {
        public int date4 = 4;
        int date5 = 5;

        public static final int date6 = 6;
        public void func() {
            System.out.println("InnerClass::func()");
            System.out.println(date1);
            System.out.println(date2);
            System.out.println(date3);
            System.out.println(date4);
            System.out.println(date5);
            System.out.println(date6);
        }
    }
}
public class Test {
    public static void main(String[] args) {
        OuterClass outerClass = new OuterClass();
        OuterClass.InnerClass innerClass = outerClass.new InnerClass();
        innerClass.func();
    }
}
```
如果`class InnerClass`内也有`date1`,就近原则，打印的是`class InnerClass`内的`date1`。如果一定要在实例内部类当中访问外部类中和内部类相同的成员变量，可以这样写：
第一种：
```java
OuterClass outerClass = new OuterClass();
System.out.println(outerClass.date1);
```

第二种：
```java
System.out.println(OuterClass.this.date1);
```

---

【**注意**】
1. 外部类中的任何成员都可以在实例内部类方法中直接访问。
2. 实例内部类所处的位置与外部类成员位置相同，因此也受public、private等访问限定符的约束。
3. 在实例内部类方法中访问同名的成员时，优先访问自己的，如果要访问外部类同名的成员，必须：外部类名称.this.同名成员 来访问。
4. 实例内部类对象必须在先有外部类对象前提下才能创建。
5. 实例内部类的非静态方法中包含了一个指向外部类对象的引用。
6. 外部类中，不能直接访问实例内部类中的成员，如果要访问必须先要创建内部类的对象。
### 7.1.2 静态内部类

```java
class OuterClass2{
    public int data1 = 1;
    int data2 = 2;
    public static int data3 = 3;

    public void test(){
        System.out.println("out::test()");
    }
    static class InnerClass2{
        public int data4 = 4;
        int data5 = 5;
        public static int data6 = 6;
        public void func(){
            System.out.println("out::func()");
        }
    }
}
```
如何获取静态内部类对象？

```java
OuterClass2.InnerClass2 innerClass2 = new OuterClass2.InnerClass2();
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/ed695310ffe04ab2a1db22fb1d3b2516.png)
静态内部类当中，不能访问外部类的非静态成员。外部类的非静态成员，需要通过外部类对象的引用才能访问。如果非要访问，如下：

```java
OuterClass2 outerClass = new OuterClass2();
System.out.println(outerClass.data1);
```
【**注意**】
1. 在静态内部类中只能访问外部类中的静态成员。
2. 创建静态内部类对象时，不需要先创建外部类对象。
## 7.2 局部内部类
*  局部内部类：定义在方法体内部。
* 不能被public、static等访问限定符修饰。
* 只能在该方法体内部使用，其他位置都不能用。
* 编译器也有自己独立的字节码文件，命名格式：外部类名字$数字内部类名字.class。
```java
public class Main {
    public void func(){
        //局部内部类：
        class Inner{
            public void test(){
                System.out.println("11111");
            }
        }
        Inner inner = new Inner();
        inner.test();
    }
    public static void main(String[] args) {
    }
}
```

## 7.3 匿名内部类

```java
class Person{
    public int age;
    public String name;
}
public class Test2 {
    public static void main(String[] args) {
        Person person = new Person();
        System.out.println(person.age);
        System.out.println(person.name);
        new Person();
        System.out.println(new Person().age);//匿名对象
        System.out.println(new Person().name);//匿名对象 只能用一次
       //匿名内部类
        new Person(){
        };
    }
```
后续在抽象接口再细细总结。
# 八、对象的打印
![在这里插入图片描述](https://img-blog.csdnimg.cn/be25ce6f61df48a6b8a594f669aea2a7.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/28b8454bab74482680958c6dc033ed09.png)
自动生成`toString`,方便我们打印。

```java
class Person{
    public int age;
    public String name;

    @Override
    public String toString() {
        return "Person{" +
                "age=" + age +
                ", name='" + name + '\'' +
                '}';
    }
}

public class Test2 {
    public static void main(String[] args) {
        Person person = new Person();
        System.out.println(person);
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/fb23508d4d9d41e7b3c565f16e8a4f97.png)
也可以自己写：

```java
    @Override
    public String toString() {
        return "姓名："+ name +"年龄："+age;
    }
}

```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ba5e1bee2ac942209ba1eb4c818c06fe.png)
`@Override`叫做注解，帮我们检查，我们写的对不对，充当的是一个检查的角色。`Java`当中注解有很多，我们慢慢学。
