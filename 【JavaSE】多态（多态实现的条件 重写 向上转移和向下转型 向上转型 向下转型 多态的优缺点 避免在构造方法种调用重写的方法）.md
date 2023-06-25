# 多态
一种事物，多种形态。
多态的概念：去完成某个行为，当不同对象去完成时会产生出不同的状态。
## 多态实现的条件

1.必须再继承体系下。
2.子类必须要对父类中的方法进行重写。
3.通过父类的引用调用重写的方法。


![在这里插入图片描述](https://img-blog.csdnimg.cn/9b548ab8d62f4ad2aaed7ed99eb8695d.png)


当我们发生向上转型之后，此时通过父类引用只能访问父类自己的成员，不能访问到子类特有的成员。

## 重写
重写需要满足三个条件：
1. 方法名称相同。
2. 参数列表相同。
3. 返回值相同。

```java
class Animal{
    public String name;
    public int age;

    public void eat(){
        System.out.println(name+" 正在吃饭！");
    }
}
class Dog extends Animal{
    public void wangwang(){
        System.out.println(name+" 正在汪汪叫！");
    }
    @Override
    public void eat(){
        System.out.println(name+" 正在吃狗粮！");
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/860b89a67f1d415088ef741a279ca8b4.png)
编译器可以生成：
![在这里插入图片描述](https://img-blog.csdnimg.cn/1d1f70ed90bb4b089700b5a93e18c225.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/6ec0b6ccf0cf406189ac06b0d0591bfe.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/5b94eac5c3fa4044800175305d717870.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/6a31ef017b2a4bcdadd22193ee021361.png)


```java
class Animal{
    public String name;
    public int age;

    public void eat(){
        System.out.println(name+" 正在吃饭！");
    }
}
class Dog extends Animal{
    public void wangwang(){
        System.out.println(name+" 正在汪汪叫！");
    }
    @Override
    public void eat(){
        System.out.println(name+" 正在吃狗粮！");
    }
}
class Bird extends Animal{
    public String wing;//翅膀
    public void fly(){
        System.out.println(name+" 正在飞！");
    }
    @Override
    public void eat(){
        System.out.println(name+" 正在吃鸟粮！");
    }
}
public static void main(String[] args) {
       Animal animal1 = new Dog();
       animal1.name = "小黄";
       animal1.eat();
	System.out.println("=================");
       Animal animal2 = new Bird();
       animal2.name = "圆圆";
       animal2.eat();
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/40964173ebaa4a14b4890b5b3909afa7.png)
**静态绑定**：也称为前期绑定(早绑定)，即在编译时，根据用户所传递实参类型就确定了具体调用那个方法。典型代表函数重载。
**动态绑定**：也称为后期绑定(晚绑定)，即在编译时，不能确定方法的行为，需要等到程序运行时，才能够确定具体调用那个类的方法。


![在这里插入图片描述](https://img-blog.csdnimg.cn/eca88d518d04468cb99dbc8b142538f1.png)


重写需要注意：
1. private修饰的方法不能被重写。
2. static修饰的方法不能被重写。
3. 子类的访问修饰限定权限要大于等于父类的权限。 `private < 默认 < protected < public`
4. 被final修饰的方法不能被重写，此时这个方法被称作密封方法。
![在这里插入图片描述](https://img-blog.csdnimg.cn/43db92c6ba8c4264b82b347be26d0a63.png)



## 向上转移和向下转型
### 向上转型
实际就是创建一个子类对象，将其当成父类对象来使用。

1. 直接赋值
```java
class Animal{
    public String name;
    public int age;

    public void eat(){
        System.out.println(name+" 正在吃饭！");
    }
}
class Dog extends Animal{
    public void wangwang(){
        System.out.println(name+" 正在汪汪叫！");
    }
}
public class Test2 {
    public static void main1(String[] args) {
        Dog dog = new Dog();
        dog.name = "小黄";
        dog.eat();
        dog.wangwang();
        Animal animal = dog;
    }
}
```
2. 方法传参
```java
class Animal{
    public String name;
    public int age;

    public void eat(){
        System.out.println(name+" 正在吃饭！");
    }
}
class Dog extends Animal{
    public void wangwang(){
        System.out.println(name+" 正在汪汪叫！");
    }
}
public class Test2 {
    public static void func(Animal animal){
    }
    public static void main(String[] args) {
        Dog dog = new Dog();
        func(dog);
    }
}
```
3.方法返回值

```java
class Animal{
    public String name;
    public int age;

    public void eat(){
        System.out.println(name+" 正在吃饭！");
    }
}
class Dog extends Animal{
    public void wangwang(){
        System.out.println(name+" 正在汪汪叫！");
    }
}
public class Test2 {
    public static Animal func2(){
        return new Dog();
    }
    public static void main(String[] args) {
        func2();
    }
}
```



优点：让代码实现更简单灵活。
缺陷：不能调用到子类特有的方法。

### 向下转型

```java
class Animal{
    public String name;
    public int age;

    public void eat(){
        System.out.println(name+" 正在吃饭！");
    }
}
class Dog extends Animal{
    public void wangwang(){
        System.out.println(name+" 正在汪汪叫！");
    }
}
public class Test2 {
    public static void main(String[] args) {
        Animal animal1 = new Dog();
        //向下转型
        Dog dog = (Dog)animal1;
        dog.name = "小黄";
        dog.wangwang();
    }
}
```

向下转型非常不安全，比如：
![在这里插入图片描述](https://img-blog.csdnimg.cn/4656ac9acd234ae2b58ee822a82e386b.png)

解决方法：Java中为了提高向下转型的安全性，引入了 instanceof ，如果该表达式为true，则可以安全转换。

```java
if(animal1 instanceof Dog){
     //向下转型
     Dog dog = (Dog)animal1;
     dog.name = "小黄";
     dog.wangwang();
}
```

可以理解为判断animal1这个引用，是不是引用Dog对象。


## 多态的优缺点

```java
class Shap{
    public  void draw(){
        System.out.println("画图形！");
    }
}
class Rect extends Shap{
    @Override
    public void draw() {
        System.out.println("画矩形！");
    }
}
class Cycle extends Shap{
    @Override
    public void draw() {
        System.out.println("画圆！");
    }
}
class Flower extends Shap{
    @Override
    public void draw() {
        System.out.println("画一朵花❀！");
    }
}
public class Test3 {
    public static void drawMap(Shap shap){
        shap.draw();
    }
    public static void main(String[] args) {
        Rect rect = new Rect();
        Cycle cycle = new Cycle();

        drawMap(rect);
        drawMap(cycle);
        drawMap(new Flower());
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/b4fc6481c9ca4206ac984308a94203aa.png)







```java
class Shap{
    public  void draw(){
        System.out.println("画图形！");
    }
}
class Rect extends Shap{
    @Override
    public void draw() {
        System.out.println("画矩形！");
    }
}
class Cycle extends Shap{
    @Override
    public void draw() {
        System.out.println("画圆！");
    }
}
class Flower extends Shap{
    @Override
    public void draw() {
        System.out.println("画一朵花❀！");
    }
}
public class Test3 {
    public static void drawMap2(){
        Rect rect = new Rect();
        Cycle cycle = new Cycle();
        Flower flower = new Flower();
        //c r c r f
        String[] shapes = {"cycle", "rect", "cycle", "rect", "flower"};
        for (String shape : shapes) {
            if (shape.equals("cycle")) {
                cycle.draw();
            } else if (shape.equals("rect")) {
                rect.draw();
            } else if (shape.equals("flower")) {
                flower.draw();
            }
        }
    }
    public static void main(String[] args) {
        drawMap2();
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/06f09e5d12d640b8be4c33dab1ea18c2.png)


多态实现：

```java
public static void drawMap3(){
  Rect rect = new Rect();
  Cycle cycle = new Cycle();
  Flower flower = new Flower();
  Shap [] shaps = {cycle,rect,cycle,rect,flower};
  for(Shap shap:shaps){
      shap.draw();
  }
}
```

> 多态好处：

1. 能够降低代码的 "圈复杂度", 避免使用大量的 if - else。
2. 可扩展能力更强如果要新增一种新的形状, 使用多态的方式代码改动成本也比较低。

> 多态缺陷：代码的运行效率降低。

1. 属性没有多态性：当父类和子类都有同名属性的时候，通过父类引用，只能引用父类自己的成员属性。
2. 构造方法没有多态性。





## 避免在构造方法种调用重写的方法

下面我们看一段有坑的代码：

```java
class B {
    public B() {
// do nothing
        func();
    }
    public void func() {
        System.out.println("B.func()");
    }
}
class D extends B {
    private int num = 1;
    @Override
    public void func() {
        System.out.println("D.func() " + num);
    }
}
public class Test4 {
    public static void main(String[] args) {
        D d = new D();
    }
}
```
当在父类的构造方法当中去调用父类和子类重写的方法的时候，此时会调用子类的。
num为0是因为父类此时还没有走完，就来到了打印阶段。

![在这里插入图片描述](https://img-blog.csdnimg.cn/41a5cd4f5fa5464c85cd82d57a0ce7d3.png)
**结论**: "用尽量简单的方式使对象进入可工作状态", 尽量不要在构造器中调用方法(如果这个方法被子类重写, 就会触发动态绑定, 但是此时子类对象还没构造完成), 可能会出现一些隐藏的但是又极难发现的问题。
