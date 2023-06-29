
# 异常
## 概念

在程序运行过程中，可能会存在一些奇怪问题，例如：网络不畅，内存报警等等。所以在java中，将程序执行过程中发生的不正常的行为称为异常。
例如：
1.
```java
public class Test {
    public static void main(String[] args) {
        System.out.println(10/0);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/82bbe39d6fea4519b99864cc008e9913.png)
抛出的异常就是一个类:

![在这里插入图片描述](https://img-blog.csdnimg.cn/114017007a294c55ba6196167b3dcef2.png)
 2.
 
```java
public class Test {
    public static void main(String[] args) {
        int[] array = null;
        System.out.println(array.length);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/e13f4bea0308473f967118561cc3d2d7.png)
异常的种类非常多，常见的我们罗列出来。
在没有处理异常的时候，抛出异常之后，后面的内容不能打印。

3.
```java
public class Test {
    public static void main(String[] args) {
        int[] array = {1,2,3};
        System.out.println(array[10]);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/f30f9695ec6744e0aa869f783eb6478b.png)
## 异常结构

1. Throwable：是异常体系的顶层类，其派生出两个重要的子类, Error 和 Exception
2. Error：指的是Java虚拟机无法解决的严重问题，比如：JVM的内部错误、资源耗尽等，典型代表：
StackOverflowError和OutOfMemoryError，一旦发生回力乏术。
3. Exception：异常产生后程序员可以通过代码进行处理，使程序继续执行。比如：感冒、发烧。我们平时所说
的异常就是Exception。

```java
public class Test {
    public static void func(){
        func();
    }
    public static void main(String[] args) {
        func();
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/027f4f7f85c844d38c53b162939c2782.png)
异常还分为运行时异常和编译时异常，所有的运行时异常都继承了`RuntimeException`这个类，对于编译时异常来说，必须把异常处理掉。
![在这里插入图片描述](https://img-blog.csdnimg.cn/5032e7e9ff5140feb464e2b568735a61.png)

# 异常处理

1. `LBYL`: Look Before You Leap. 在操作之前就做充分的检查。

```java
boolean ret = false;
ret = 进入安全系统();
if (!ret) {
进入安全系统错误;
return;
}
```
在下一步操作的时候，必须保证上一步操作成功。
缺陷：正常流程和错误处理流程代码混在一起。

 2. `EAFP`: It's Easier to Ask Forgiveness than Permission 就是先操作, 遇到问题再处理。
 

```java
try{
   ...
}catch(){

}catch(){

}
```


异常处理主要的5个关键字：`throw、try、catch、final、throws`。
## 异常抛出
手动抛异常：

```java
public class Test {
    public static void test1(int[] a){
        if(a == null){
            throw new NullPointerException();
        }
    }
    public static void main(String[] args) {
        test1(null);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/36be8eb2151f474cb4bb2a6173efb1d5.png)
上面提示18和22行出错，18行报错就是最直接抛出异常的地方。一般最上面的是我们需要解决的。当解决这个异常之后，下面的异常就没有问题了。
**上面的语法一般用在自己自定义的异常**。

> **注意**：

1. throw必须写在方法体内部。
2. 抛出的对象必须是Exception 或者 Exception 的子类对象。
3. 如果抛出的是 RunTimeException 或者 RunTimeException 的子类，则可以不用处理，直接交给JVM来处理。
4. 如果抛出的是编译时异常，用户必须处理，否则无法通过编译。
5. 异常一旦抛出，其后的代码就不会执行。


## 异常的捕获
主要有两种：异常声明throws 和 try-catch。

### 异常声明throws

```java
public class Test {
    public static void test1(int[] a) throws CloneNotSupportedException {
        if(a == null){
            throw new CloneNotSupportedException();
        }
    }
    public static void main(String[] args) throws CloneNotSupportedException {
        test1(null);
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/cb34c9ee39274b6788c3af7db2c114ad.png)
当我们再次运行的时候还会报错，而且后面的代码不会执行了。此时不让编译报错了，但是异常没有真正的处理掉，它把异常交给JVM了。
**当我们没有解决这个异常的时候，我们就会把这个异常交给JVM处理，一旦交给JVM程序就崩溃了**。
> **注意**：

1. throws必须跟在方法的参数列表之后。
2. 声明的异常必须是 Exception 或者 Exception 的子类。
3. 方法内部如果抛出了多个异常，throws之后必须跟多个异常类型，之间用逗号隔开，如果抛出多个异常类型具有父子关系，直接声明父类即可。




### try-catch捕获并处理

```java
public static void main(String[] args){
    try{
        //存放可能抛出异常的代码
        test1(null);
    }catch (CloneNotSupportedException e){

    }
    System.out.println("正常的逻辑");
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/12ab7c7f80804c1386d8575a644061bd.png)
![](https://img-blog.csdnimg.cn/197386c708e44dbaa24e86e278e6bc13.png)

对于受查异常来说，当try中没有抛出catch这个受查异常的时候，检测不到就会报错，但是运行时去抛出的异常来说不会检测的很严格。所以受查异常或者编译时异常我们在使用的时候要注意。
![在这里插入图片描述](https://img-blog.csdnimg.cn/9376d74437b64d0a9891817d6cb868f6.png)
注意：Exception作为所有异常的父类，它能捕获所有的异常，如果把它放到catch的第一个，那么后续的异常没有任何作用了！


当try当中存在多个异常的时候，从上往下执行，谁先抛出异常就捕获哪个异常。catch中程序的书写不影响异常的捕获。
![在这里插入图片描述](https://img-blog.csdnimg.cn/b22842922b2f4620a9b0b4e6198a0529.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/b2be14cd3ae24686a9a9c0f1943e0719.png)
当catch当中没有指定的异常，程序就会交给JVM处理，只要交给JVM处理，程序就会终止。
![在这里插入图片描述](https://img-blog.csdnimg.cn/5bebb7035abe4444b0625c137f12b387.png)


### finally
有些特定的代码，不论程序是否发生异常，都需要执行，比如程序中打开的资源：网络连接、数据库连接、IO流等，在程序正常或者异常退出时，必须要对资源进进行回收。另外，因为异常会引发程序的跳转，可能导致有些语句执行不到，finally就是用来解决这个问题的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/eeca4e2f47bc46d68dc36742c03d0663.png)
通过上面的实验我们明白了：**不管是否在try中抛出异常，finally里面的代码终将被执行**。所以finally一般用于资源的释放。



**【异常处理流程总结】**：
程序先执行 try 中的代码， 如果 try 中的代码出现异常, 就会结束 try 中的代码, 看和 catch 中的异常类型是否匹配，如果找到匹配的异常类型, 就会执行 catch 中的代码，如果没有找到匹配的异常类型, 就会将异常向上传递到上层调用者，无论是否找到匹配的异常类型, finally 中的代码都会被执行到(在该方法结束之前执行)，如果上层调用者也没有处理的了异常, 就继续向上传递，一直到 main 方法也没有合适的代码处理异常, 就会交给 JVM 来进行处理, 此时程序就会异常终止。

## 自定义异常

> PassWordException

```java
public class PassWordException extends RuntimeException{
    public PassWordException(){
        super();
    }
    public PassWordException(String s){
        super(s);
    }
}
```

> UserNameException


```java
public class UserNameException extends RuntimeException {
    public UserNameException(){
        super();
    }
    public UserNameException(String s){
        super(s);
    }
}
```

> 自定义异常

```java
public class Test2 {
    public String name ="lkx";
    public String pssword = "123456";
    public  void login(String name,String pssword){
        if(!this.name.equals(name)){
            System.out.println("用户名错误！");
            throw new UserNameException("你的用户名错了");
        }
        if(!this.pssword.equals(pssword)) {
            System.out.println("密码错误！");
            throw new PassWordException("你的密码错了");
        }
    }
    public static void main(String[] args) {
        Test2 test2 = new Test2();
        try{
            test2.login("lk","12345");
        }catch (UserNameException e){
            e.printStackTrace();
            System.out.println("用户名异常！");
        }catch (PassWordException passWordException){
            passWordException.printStackTrace();
            System.out.println("密码异常！");
        }finally {

        }
    }
}
```
1. 自定义异常通常会继承自 Exception 或RuntimeException。
2. 继承自 Exception 的异常默认是受查异常。
3. 继承自 RuntimeException 的异常默认是非受查异常。
