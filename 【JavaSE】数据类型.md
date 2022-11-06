# 一、字面常量
当程序在运行的时候

```c
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("hello");
    }
}
```
输出的 “hello”就是字面常量

> **常量的概念**：程序运行期间，固定不变的量称为常量。

**字面常量分类：**
1.字符串长量: 由双引号括起来的。

```c
System.out.println("hello");
```

2.整型常量：程序中直接写的数字（没有小数点）。

```c
System.out.println(10);
```

3.浮点数常量：程序中直接写的小数。

```c
System.out.println(10.5);
```

4.字符常量：由单引号括起来的单个字符，例如：‘1’ ‘A’。

```c
System.out.println('A');
```

5.布尔常量：两种（true 和 false）。

```c
System.out.println(true);
```

6.空常量：null。

这些常量没有赋值给谁，所以这些常量是字面常量
> Java中的三种打印方式:

```c
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("hello");
        System.out.println(10);
        System.out.println(10.5);
        System.out.printf("%s\n","likaixuan");
        System.out.printf("%d\n",10);

        System.out.print(10);
        System.out.println("likaixuan");
    }
```
打印出的结果：
```c
hello
10
10.5
likaixuan
10
10likaixuan
```
很容易发现println是更方便的。
# 二、数据类型

> Java中的数据类型：1.基本数据类型 2.引用数据类型

基本数据类型：
![在这里插入图片描述](https://img-blog.csdnimg.cn/8965ca1d77ce42918d5a8fe46951a9b0.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/05750e01e8324d1abefb3d6a2d2c5a72.png)
字符串属于引用数据类型。

> 与c语言不同

1.在java中不管在32还是64位操作系统，int 都占4个字节 long都占8个字节。
2.整型和浮点型都是有符号的。
3.整型默认为int 型  浮点型默认为 double 型。
# 三、变量
## 1.变量概念
在java程序中经常改变的内容，比如 数学函数、计算结果等等，这些被称为变量，数据类型就是用来定义不同种类的变量的。
## 2.语法格式
定义变量的语法格式：

```c
数据类型 变量名 = 初始值;
   int    b    =   50;
   //定义整型变量b，b是变量名也是标识符
```



## 3.整型变量

### 1.整型变量 
1.在java中，int 不论是在64位还是32位操作系统下都是4个字节。
2.设置变量初始值的时候，值不能超过int 所表示范围，否则导致溢出。
![在这里插入图片描述](https://img-blog.csdnimg.cn/0bcccc9c06e6466fb6d65774781334c5.png)
报错了，当你赋的字面值大于这个变量能够保存的最大值的时候程序会编译报错。



3.变量在使用前必须赋初值。
![在这里插入图片描述](https://img-blog.csdnimg.cn/9330da48ed1a4a71b1cdc638978065ac.png)
如果你定义的局部变量没有初始化，那么在使用之前一定要初始化。
4.int的包装类型为Integer
```c
    public static void main(String[] args) {
        int a = 10;
        System.out.println(a);
        System.out.println(Integer.MAX_VALUE);
        //Integer->把他当成一个int的plus版本
        //专业术语：包装类
    }
```

```c
10
2147483647
```
除了输出10 还输出了int的最大值

### 2.长整型变量

```c
  public static void main(String[] args) {
        long a = 10;//long类型是八个字节
        //64个比特位 有一位是符号位
        //数值位 是63个比特位
        //取值范围 -2^63~(2^63)-1
        System.out.println(a);
        System.out.println(Long.MIN_VALUE);
        System.out.println(Long.MAX_VALUE);
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/3b7109174f6d426d85f23401e31c81a5.png)
1.长整型的初始值后加L.
2.长整型不论在哪个系统下都占8个字节。
3.长整型的表示范围 如上图。
4.long的包装类位Long。
### 3.短整型变量

```c
    public static void main(String[] args) {
        short sh = 10;
        //短整型 2个字节 ->16个比特位
        //范围 -2^15~(2^15)-1
        System.out.println(sh);
        System.out.println(Short.MIN_VALUE);
        System.out.println(Short.MAX_VALUE);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/796f0acdca73434182b67e0e3d6830b1.png)
1.short在任何系统下都占2个字节
2.short的表示范围为：-32768 ~ 32767
3.short的包装类型为Short。
### 4.字节型变量

```c
   public static void main(String[] args) {
        byte b = 10;
        //一个字节  8个比特位、
        //范围： -2^7~(2^7)-1
        System.out.println(b);
        System.out.println(Byte.MIN_VALUE);
        System.out.println(Byte.MAX_VALUE);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b9abfd14c2df4e8aa0bfb090117d7bc8.png)
1.byte在任何系统下都占1个字节
2.byte的范围是：-128 ~ 127
3.字节的包装类型为Byte
## 4.浮点型变量
### 1.双精度浮点型

```c
    public static void main(String[] args) {
        double d = 12.5;
        //双精度浮点型 8个字节
        System.out.println(d);
        System.out.println(Double.MIN_VALUE);
        System.out.println(Double.MAX_VALUE);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b6e18fb7f0314a0f8db573cacf81b596.png)
1.double在任何系统下都占8个字节
2.double的包装类型为Double
注意：
```c
    public static void main(String[] args) {
        double a = 1.1;
        double b =1.1;
        System.out.println(a * b);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/995a6f24feaa4aeb984a3172d1faff17.png)
为什么后面还有很多的0？
这个和浮点数的存储是有关系的。
精度：例如float精确到小数点后6位，再往后的就不一定精确，double 类型的内存布局遵守 IEEE 754 标准(和C语言一样), 尝试使用有限的内存空间表示可能无限的小数, 势必会存在一定的精度误差，因此浮点数是个近似值，并不是精确值。

### 2.单精度浮点型

```c
    public static void main(String[] args) {
        float f = 10.5f;
        System.out.println(f);
        System.out.println(Float.MIN_VALUE);
        System.out.println(Float.MAX_VALUE);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/debca99348ba4917bc7c7707ea3b9896.png)
书写时要注意的问题：
![在这里插入图片描述](https://img-blog.csdnimg.cn/ed6df4fd70a74e88a48815564afb69cc.png)
所以要这样写：

```c
        float f = 12.5f;
        float f = (float)12.5;
```
float 数据精度范围比较小 所以一般优先考虑double 
它的包装类位Float。
## 5.字符型变量

```c
    public static void main(String[] args) {
        char ch = 'a';
        char ch1 = 97;
        //字符型 2个字节 16个比特位 没有负数
        // 0~65535
        System.out.println(ch);
        System.out.println(ch1);
        System.out.println(Character.MIN_VALUE);
        System.out.println(Character.MAX_VALUE);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/cba9444965de4af8a3d8599373f29871.png)
输出最大和最小字符时，idea无法正常解析出来
## 6.布尔型变量

```c
    public static void main(String[] args) {
        boolean b = true;
        System.out.println(b);
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/835815e5b2d042f5af7151075593c9b4.png)

> 注意：

1.站在c语言角度考虑 ：0是假 非0是真。
2，站在java角度： 真：true 假：false。
3.所以在赋值的时候不能赋值1 只能是true或者false。
4.布尔类型没有明确大小。
5.Java的boolean类型和int不能互相转换。
6. boolean的包装类型为Boolean。
##  7.类型转换
Java作为一个强类型转换的编程语言，当不同类型之间的变量相互赋值的时候，对类型的检查比较严格。
### 1.自动类型转换（隐式）
代码不需要处理，编译器会处理。
特点：数据范围小转数据范围大的时会自动进行

```c
System.Out.println(1024); 
// 整型默认情况下是int
System.Out.println(3.14);
 // 浮点型默认情况下是double
```

```c
public class HelloWorld {
    public static void main(String[] args) {
        int a = 100;
        long b = 10L;
        b = a;//会自动将a提升为long类型，然后赋值
        System.out.println(b);
    }
```

### 1.强制类型转换（显示）

```c
   public static void main(String[] args) {
        int a =10;
        long b = 100L;
        //a = b;大赋值给小
        //不能进行直接赋值
        //需要强制类型转换
        a = (int)b;
        System.out.println(a);
    }
```

> 注意 ：

1.把范围大的类型赋值给范围小的，需要强制类型转换但是精度可能会丢失。
2.强制类型转换不一定成功，不相干的类型不能强制类型转换。
![在这里插入图片描述](https://img-blog.csdnimg.cn/b5dc645827ab4a08a0570228f7ce2294.png)

##  8.类型提升
Java中类型提升和c语言区别不大
小类型提升到大类型

> int 与long之间:int 会被提升为long

```c
    public static void main(String[] args) {
        int a = 10;
        long b = 20;
        int c = (int)(a+b);
    }
```

```c
    public static void main(String[] args) {
        int a = 10;
        long b = 20；
        long c = a+b;
    }
```

> byte与byte之间的运算

```c
    public static void main(String[] args) {
        byte a = 10;
        byte b = 20;
        //当你参与运算的数据 小于4个字节
        //会进行提升
        //不建议这样写 byte c =(byte)(a+b)
        int c = a+b;//这样写比较好
        System.out.println(c);
    }
```

# 四、字符串类型
Java中的字符串不存在以\0结尾
在Java中使用String类定义字符串类型：

```c
    public static void main(String[] args) {
        int a = 10;
        int b = 20;
        String s1 = "hello";
        String s2 = " world";
        System.out.println(a+b);
        System.out.println("a:"+a+" b:"+b);
        System.out.println("a:"+a+b);
        System.out.println(a+b+ "likaixuan");
        System.out.println(s1+s2);
    }
```
输出结果：
```c
30
a:10 b:20
a:1020
30likaixuan
hello world
```
当字符串和整数在一起使用“+”的时候，就是一个拼接的意思。

> 1.int 转成 String

```c
    public static void main(String[] args) {
        int a = 10;
        //想把a变成字符串
        //1,
        String str = a + "";
        System.out.println(str);
        //2.借助方法
        String ret = String.valueOf(10);
        System.out.println(ret);
    }
```

```c
10
10
```

> 2.String 转成 int

```c
    public static void main(String[] args) {
        String str = "1234";
        int a = Integer.parseInt(str);
        System.out.println(a+1);
    }
```

```c
1235
```
