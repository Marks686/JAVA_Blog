# 1.什么是运算符？
计算机通常会执行数学运算，在执行数学运算过程中就会用到运算符。

> 运算符：对操作数进行操作时的符号，不同运算符操作含义不同。


## 2. 算术运算符
### (1) 基本四则运算符

```c
+ - * / %
```
我们主要看除法运算符：

```java
    public static void main(String[] args) {
        System.out.println(5/2);//2
        System.out.println(5.0/2);//2.5
        System.out.println(5/2.0);//2.5
        System.out.println((float) 5/2);//2.5
        System.out.println(5/(float)2);//2.5
        System.out.println((float) (5/2));//2.0
    }
```

通过结果我们可以发现它的运算法则。
求余数：

```java
    public static void main(String[] args) {
        System.out.println(20%3);//2
        System.out.println(-20%3);//-2
        System.out.println(20%-3);//2
        System.out.println(-20%-3);//-2
    }
```
我们发现只要是被除数是负的那么余数也是负的，这是我通过观察得出的一个小规律，当然用笔算也是很简单的，细心一点是不会出错的。
小数和小数之间也是可以求余数的：

> 在Java里面%左右两边可以是小数，也就是说可以对小数进行求余。

```java
    public static void main(String[] args) {
        System.out.println(12.5 % 10.5);
        System.out.println(11 % 10.5);
        
    }
```

```java
2.0
0.5
```
 

>  右操作数为0的话会报错

 

```java
    public static void main(String[] args) {
        System.out.println(10 / 0);
    }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/eb32fbe497cc4fddbe8275b2c6018811.png)

> 两侧操作数类型不一样时，向类型大的提升

```java
    public static void main(String[] args) {
        System.out.println( 2 + 3.5);//5.5
    }
```

### (2) 增量运算符

```java
+=  -=  *=  %=
```

> 运算符操作完成后，将结果赋值给左操作数

```java
    public static void main(String[] args) {
        int a = 9;
        a += 4;//相当于 a = a + 4
        System.out.println(a);//13
    }
```

> 增量运算符可以自动帮我们进行类型的转换

```java
    public static void main(String[] args) {
        double d = 11.5;
        int a = 10;
        //a = a +d;错误警告
        a += d;//自动转换成int型
        System.out.println(a);//21
    }
```

### (3) 自增/自减运算符

> 直接上代码：

```java
    public static void main(String[] args) {
        int b = 1;
        b++;//b加1 此时 b = 2
        System.out.println(b++);//此时是后置++ 表达式先使用 再++ 所以 输出2 此时 b = 3
        ++b;//前置++给b加1  b = 4
        System.out.println(++b);//前置++ 先++ 再使用 所以此时输出5   b = 5
        System.out.println(b);//输出5
    }
```
注意：只有变量才能使用自增/自减运算符，常量不能使用，因为常量不能被修改。
## 3.关系运算符

```java
==  !=  <  >  <=  >=
```

> 关系运算符的运算的结果是 true 或者 false

```java
    public static void main(String[] args) {
        int a = 13;
        int b = 20;
        System.out.println(a == b);//false
        System.out.println(a != b);//true
        System.out.println(a > b);//false
        System.out.println(a < b);//true
        System.out.println(a <= b);//true
        System.out.println(a >= b);//false
    }
```
注意：
```java
   public static void main(String[] args) {
        int a = 8;
        if(6 < a < 9){
        }
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/13044f6625504d1db021f6caee335c39.png)

## 4.逻辑运算符
### (1) 逻辑与 &&

> 要求：
> 1.都是布尔表达式 
> 2.如果第一个表达式为假，那么就不会执行第二个表达式【短路】


![在这里插入图片描述](https://img-blog.csdnimg.cn/10d8e84a7fc14aa48555791aa4b12936.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/dbbc530bcaad4c09a5dd18ed1d7d5480.png)

### (2) 逻辑或 ||
![在这里插入图片描述](https://img-blog.csdnimg.cn/b4d1e6012677408095d98c62cb0e3ea6.png)

### (3) 逻辑非 ！
！后面一定是一个布尔表达式

```java
    public static void main(String[] args) {
        int a = 4;
        System.out.println(!(a<5));//false
        System.out.println(!(a == 4));//false
        System.out.println(!(a != 4));//true
    }
```
## 5.位运算符
### (1) 按位与 &

> 对应位上如果有 0,结果就是 0

```java
1011011101
1011010110 按位与操作&、
---------- 
1011010100
```

> 用途：寻找 这一位是不是都是1

### (2) 按位或 |

>  只要这一位是1，结果就是1，求并集

```java
1011011101
1011010110 按位或操作 ||
---------- 
1011011111     
```

### (3) 按位取反 ~

> 1变成0,0变成1

```java
1011011101
---------- 按位取反
0100100010   

```

### (4) 按位异或 ^

> 不一样的进行或，一样的是0

```java
1011011101
1011010110 按位异或操作 ^
---------- 
0000001011                 
```

## 6.移位运算符
### (1) 左移 <<

> 每左移一下，夸大2倍

```java
0000 0100  4
0000 0100 << 1  -----> 0000 1000   8
0000 0100 << 2  -----> 0001 0000   16
```

### (2) 右移 >>



>每右移一下，缩小2倍

```java
0001 0000 16
0001 0000   >> 1 -----------> 0000 1000  8 
0001 0000   >> 2 -----------> 0000 0100  4
```

### (3) 无符号右移 >>>

> 最右侧位不要了, 最左侧补 0，右移之后不管符号是0还是1，都补0

```java
    public static void main(String[] args) {
        System.out.println(-1>>>1);//最大值：2147483647
    }
```

```java
1111 1111 >>> 1 -----> 0111 1111
```

> 思考：有没有无符号左移？？？

 不存在无符号左移，最左边无符号位
## 7.条件运算符

> 布尔表达式1 ？表达式2 ：表达式3

表达式1为真，执行表达式2，否则执行表达式3

> 注意：c 的类型要同时满足表达式2和3

```java
   public static void main(String[] args) {
        int a = 10;
        int b = 20;
        //int c = a>b? 1 : 2.0;  这种写法是错误的 c的类型要同时满足表达式2和3
        double c = a>b ? 1 : 2。0
        
    }
```

## 8.运算符的优先级

>     *和 / 的优先级要高于 +和 -
>      +的优先级高于 >>
>    注意：运算符之间是有优先级的. 具体的规则我们不必记忆. 在可能存在歧义的代码中加上括号即可
> 


求a 和 b的平均值：

```java
    public static void main(String[] args) {
        int a = 10;
        int b = 20;
        int c = a + ((b - a) >> 1);
        System.out.println(c);//15
    }
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/4cf262771354489cb3b00208ca5275d8.png)
对于位操作相关的运算符稍微注意加括号。
