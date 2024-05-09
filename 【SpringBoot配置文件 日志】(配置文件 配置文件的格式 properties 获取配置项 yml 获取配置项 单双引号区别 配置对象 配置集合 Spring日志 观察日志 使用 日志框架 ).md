# 配置文件

SpringBoot的配置文件，有三种格式

1.`properties`
2.`yaml`
3.`yml`（yaml的简写）


事实是：SpringBoot只支持3个文件
1.`application.prperties`
2.`application.yaml`
3.`application.yml`


如果项目中同时存在`properties`和`yml`配置文件，`properties`的优先级更高，同时存在时，两个文件都生效，如果两个文件中都包含同一个配置，以`properties`为主。
# 配置文件的格式


## properties

`properties`:key value的形式，以=分割，key的格式建议是小写，单词之间使用`.`分割
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/715e8cf0260d421388a6df320ca80c54.png)
### 获取配置项

```java
# 自定义配置
demo.key1 = hello,properties
```





```java
@RestController
public class PropertiesController {
    // 读取配置文件
    @Value("${demo.key1}")
    private String key1;
    @RequestMapping("/readKey")
    public String readKey(){
        return "读取到的配置项key1: "+key1;
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6f7f1a674ca344f0a2ec22268b5eb1ea.png)

## yml

冒号后面一定要加空格，值的前面的空格不可以省略。

```java
server:
  port: 9090
```


`yml`连接数据库

```java
# 数据库相关配置
spring:
  datasource:
    url: jdbc:mysql://127.0.0.1/mycnblog?characterEncoding=utf8
    username: root
    password: 123456
```




### 获取配置项



```java
demo:
  key1: hello,yml
```



```java
@RestController
public class YmlController {
    @Value("${demo.key1}")
    public String key1;
    @RequestMapping("/readYml")
    public String readYml(){
        return key1;
    }
}
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/5883d30867864186b0dff46329953875.png)

### 单双引号区别

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/667f358db34d405396955376beadd356.png)
### 配置对象

```java
student:
  id: 18
  name: zhangsan
  age: 12
```

```java
@RestController
public class YmlController {
    @PostConstruct
    public void init(){
        System.out.println("student: " + student);
    }
    @Autowired
    public Student student;
}
```

```java
@Component
@ConfigurationProperties(prefix = "student")
@Data
public class Student {
    private Integer id;
    private String name;
    private Integer age;
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/83eae0280a344647adca2aebacbfdcb3.png)


### 配置集合


```java
dbtypes:
  name:
    - mysql
    - sqlserver
    - db2
```


```java
@RestController
public class YmlController {
    @PostConstruct
    public void init(){
        System.out.println("dbtype:" + dbType);
    }
    @Autowired
    public DBType dbType;
}

```

```java
@Component
@ConfigurationProperties(prefix = "dbtypes")
@Data
public class DBType {
    private List<String> name;
}

```




![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/67a0f4f9f2464b9fb2853baa780a034e.png)

> `yml`优缺点：

优点：

1. 可读性⾼，写法简单, 易于理解。
2. ⽀持更多的数据类型, 可以简单表达对象, 数组, List，Map等数据形态。
3. ⽀持更多的编程语⾔, 不⽌是Java中可以使⽤, 在Golang, Python, Ruby, JavaScript中也可以使⽤。



缺点:
4. 不适合写复杂的配置⽂件。
5. 对格式有较强的要求。

# Spring日志


日志作用：
1. 定为和发现问题
2. 系统监控
3. 数据采集
4. 日志审计



## 观察日志
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e4c9b68a53e8407fa88037b174f2bd57.png)

Spring帮我们集成了日志框架




## 使用


打印日志步骤：
1. 定义日志对象
2. 打印日志


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/47c35a4b6dc642b69ccc861c5a433373.png)

```java
package com.example.demo.ioc.controller;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.RestController;
import javax.annotation.PostConstruct;

@RestController
public class LoggerController {
    private static Logger logger = LoggerFactory.getLogger(LoggerController.class);

    @PostConstruct
    public void print(){
        System.out.println("打印日志");
        logger.info("我是日志框架打印的日志");
    }
}
```



查看打印日志的区别：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/13bcc0c5ab654169b9c2d21f32c10033.png)

## 日志框架


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d93e1e2ec3fd4eadb7412eaf4928a079.png)


slf4j并不是一个真实的日志实现，而是日志门面，具体实现是log4j/2 logback 等



### 门面模式

slf4j是门面模式的典型应用

门面模式（外观模式），提供了一个统一的接口，用来访问子系统的一群接口，主要特征是定义了一个高层接口，让子系统更容易使用。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ee22a99c1cf04cb4979701e3627b90b9.png)


> 门面模式主要包含2种角色:

外观角色(Facade): 也称门面角色，系统对外的统⼀接⼝.
子系统角色(SubSystem): 可以同时有⼀个或多个 `SubSystem`. 每个 `SubSytem` 都不是⼀个单独的类,而是⼀个类的集合. `SubSystem` 并不知道` Facade` 的存在, 对于 `SubSystem` 而言, `Facade` 只是另⼀个客⼾端⽽已(即 `Facade `对 `SubSystem` 透明)




比如去医院看病，可能要去挂号, 门诊, 化验, 取药, 让患者或患者家属觉得很复杂, 如果有提供接待⼈员, 只让接待⼈员来处理, 就很⽅便.


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/26d6db0a09354bf7ad89cf59b5fcb5b0.png)

通过代码学习门面模式：


模拟回家开灯的情况：

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/49375ef2801a417f926dc0923582363a.png)

> 定义一个灯的接口：

```java
package com.example.demo.facade;

public interface Light {
    void on();
    void off();
}
```



> 餐厅的灯：

```java
package com.example.demo.facade;

public class DiningLight implements Light{
    @Override
    public void on() {
        System.out.println("打开餐厅灯");
    }

    @Override
    public void off() {
        System.out.println("关闭餐厅灯");
    }
}

```

> 走廊灯：

```java
package com.example.demo.facade;

public class HallLight implements Light{

    @Override
    public void on() {
        System.out.println("打开走廊的灯");
    }

    @Override
    public void off() {
        System.out.println("关闭走廊得灯");
    }
}

```


> 客厅灯：

```java
package com.example.demo.facade;

public class LivingLight implements Light{
    @Override
    public void on() {
        System.out.println("打开客厅的灯");
    }

    @Override
    public void off() {
        System.out.println("关闭客厅的灯");
    }
}

```

> main方法：

```java
public class Main {
    public static void main(String[] args) {
        HallLight hallLight = new HallLight();
        hallLight.on();
        LivingLight livingLight = new LivingLight();
        livingLight.on();
        DiningLight diningLight = new DiningLight();
        diningLight.on();
    }
}
```

运行main方法：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b0a12d2d93ba4371932cd8be0401c744.png)

现在我们想一键开关灯，是不是就很方便了。那么我们现在定义一个门面：`FacadePattern`，由它帮我们完成开关所有灯

```java
package com.example.demo.facade;

public class FacadePattern {
    public void lightOn(){
        HallLight hallLight = new HallLight();
        hallLight.on();
        LivingLight livingLight = new LivingLight();
        livingLight.on();
        DiningLight diningLight = new DiningLight();
        diningLight.on();
    }
}

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/71aae383e82f428fba3ab79b3648583f.png)

```java
package com.example.demo.facade;

public class Main {
    public static void main(String[] args) {
        FacadePattern facadePattern = new FacadePattern();
        facadePattern.lightOn();
    }
}

```
`mian`方法只需要通过调用`FacadePattern `就可以实现全部灯的开关。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b0aaa5d160bb48178af37c7bf44576e3.png)
门面模式的优点主要包括**减少系统间依赖、提高灵活性和安全性等**。具体如下：

1. **减少依赖性**：门面模式通过提供一个统一的接口来访问多个子系统中的不同接口，从而降低了客户端与子系统之间的耦合度。这样，即使子系统的内部实现发生变化，也不会影响到调用它的客户端。
2. **提高灵活性**：门面模式简化了客户端对子系统的使用难度。客户端无需关心子系统的具体实现方式，只需与门面对象交互即可。这使得客户端可以更加灵活地使用子系统的功能。
3. **增强安全性**：门面模式可以灵活设定访问权限，不在门面对象中开通的方法将无法被访问。这为系统提供了一种安全机制，防止了未授权的访问和操作。

总的来说，门面模式是一种非常有用的设计模式，它通过提供一个简单的统一接口来封装复杂的子系统，从而简化了客户端的使用，提高了系统的可维护性和安全性。在实际应用中，门面模式可以应用于多种场景，如数据库连接池、图形界面库、操作系统接口和电商系统订单处理等。




### SLF4J框架


SLF4J 就是其他⽇志框架的⻔⾯. SLF4J 可以理解为是提供⽇志服务的统⼀API接⼝, 并不涉及到具体的⽇志逻辑实现。



## 日志格式

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e4c9b68a53e8407fa88037b174f2bd57.png)
打印日志的类：

`lisi`日志对象的名称。
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0fb7401b3e7b4a4789c6da10d7d93e4c.png)


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f3cc1f9f727e457184883fdfb0414e70.png)
通常情况下是当前类。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/81caf6535f7f4d44a072ece0acbb4dea.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b43b3926dc7145af8329e20c0ae0de78.png)

## 日志级别
⽇志级别代表着⽇志信息对应问题的严重性, 为了更快的筛选符合⽬标的⽇志信息.


⽇志的级别从⾼到低依次为: 
`FATAL`、`ERROR`、`WARN`、`INFO`、`DEBUG`、`TRACE`
• `FATAL`: 致命信息，表⽰需要⽴即被处理的系统级错误。
• `ERROR`: 错误信息, 级别较⾼的错误⽇志信息, 但仍然不影响系统的继续运⾏。
• `WARN`: 警告信息, 不影响使⽤, 但需要注意的问题。
• `INFO`: 普通信息, ⽤于记录应⽤程序正常运⾏时的⼀些信息, 例如系统启动完成、请求处理完成等。
• `DEBUG`: 调试信息, 需要调试时候的关键信息打印。
• `TRACE`: 追踪信息, ⽐DEBUG更细粒度的信息事件(除⾮有特殊⽤意，否则请使⽤DEBUG级别替代)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/84c9eb4fb48c4f49863742f4f00cbb1c.png)



## 日志配置

打印日志我们发现他只打印了前三种，原因是Spring默认的日志级别是`info`级别
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f48786d74a8e419c84e4af8f8d6bccd8.png)
可以通过配置文件修改默认的日志级别：

```java
logging:
  level:
    root: debug
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/072c056fb9d74b58a2841e5144a5de5c.png)




我们可以发现出现了非常多的`debug`信息


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/137797b6e5394fbda74abcf7e951fa13.png)


### 日志持久化

数据保存在数据库中，是一种持久化的方式，日志保存在文件中，也是一种持久化的方式。


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/feac6496d16a45088a91989c65393526.png)

如果没有加路径，默认放在当前目录下。


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/72bb3b1f548744c9b69804b0b181ced3.png)

### 日志分割
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/541ebed8950a462b9f0628dbb21f5cae.png)
第一条是日志文件分割的名称定义规则。


```java
  # 日志分割
  logback:
    rollingpolicy:
      max-file-size: 1KB
```
不是达到1KB就立即分割，以行来分割，不是以字符分割。

第二条是日志最大文件。




### 修改日志格式



![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/fa75ba31db5b4446ad067f8da4643668.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b524b9cc9c734d01a333656fb2102c5e.png)




```java
  # 修改日志格式
  pattern:
    console:
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/375b4d6e0f66472296654d13d1ff61bb.png)



## 更简单的日志输出




**添加 lombok 依赖**


```java
<dependency>
 <groupId>org.projectlombok</groupId>
 <artifactId>lombok</artifactId>
 <optional>true</optional>
</dependency>
```



**输出日志**



```java
package com.example.demo.ioc.controller;
import lombok.extern.slf4j.Slf4j;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.RestController;
import javax.annotation.PostConstruct;
@Slf4j
@RestController
public class LoggerController {
    @PostConstruct
    public void print(){
        System.out.println("打印日志");
        log.info("我是日志框架打印的日志");
        log.error("error");
        log.warn("warn");
        log.debug("debug");
        log.trace("trace");
    }
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/87ed419f33e942be9f3634ee09eac26b.png)
