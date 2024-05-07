# 什么是MyBatis

MyBatis是一种优秀的持久层框架，用于简化JDBC的开发



持久层：指的就是持久化操作的层, 通常指数据访问层(dao), 是⽤来操作数据库的。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c872cda5030d4377b77f1b0501c422d0.png)
# MyBatis的写法

1. xml
2. 注解


## 注解

1.引入MyBatis依赖，引入对应数据库的依赖，比如mysql
2.配置数据库相关信息
3.定义Java对象 
4.写实现





```java
package com.example.demo.mapper;
import com.example.demo.model.UserInfo;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;
import java.util.List;

@Mapper
public interface UserInfoMapper {
    @Select("select * from userinfo")
    List<UserInfo> selectAll();
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7516ce7a925045839ca89396b2c85f78.png)
逻辑删除和物理删除：

逻辑删除：从逻辑上删除（推荐）
物理删除：从硬盘中进行数据删除 `delete`


### 单元测试


在创建出来的SpringBoot工程中，在src下的test⽬录下，已经⾃动帮我们创建好了测试类 ，我们可以直接使⽤这个测试类来进⾏测试。


在需要写单元测试的类中右键点击`generate`，选择`Test`
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/194d23c195a248ef87e642be51a15ad6.png)

勾选需要的东西，生成类：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6f2f56d3954644ef8fedb16d70ebfcad.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/225094a3bcd941ab97b519283642f1f0.png)



然后编写测试方法：

```java
package com.example.demo.mapper;

import com.example.demo.model.UserInfo;
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import java.util.List;
import static org.junit.jupiter.api.Assertions.*;

@Slf4j
@SpringBootTest
class UserInfoMapperTest {

    @Autowired
    private UserInfoMapper userInfoMapper;

    @BeforeEach
    void setUp() {
        log.info("setUp");
    }

    @AfterEach
    void tearDown() {
        log.info("After");
    }

    @Test
    void selectAll() {
        List<UserInfo> list = userInfoMapper.selectAll();
        log.info(list.toString());
    }
}
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/86bc71a7bc334b68823254865fddd297.png)


### 使用MyBatis可能会遇到的问题



1. 没有配置数据库相关信息
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/dda6361d53d84e6ca62df5f35d7f14e1.png)
2. 账号密码错误


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/fe06004de244464ca693229f78a9a9f9.png)



3. 数据库错误


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4c75829fb7344718b538f1f3fc12c164.png)

4. 表不存在
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/cc8ccbf0c2f54009ab8e2bb54ac10dac.png)


5. 字段错误
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/47a82d95c14d4fde961485d8b1080be8.png)


### MyBatis基础操作
MyBatis的增, 删, 改操作
#### 打印日志

**配置yml**：

```java
mybatis:
  configuration: # 配置打印 MyBatis⽇志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b7b0e7050c4d4ef7b48400e8f5898bbf.png)




#### 参数传递

`查询id=1的用户`

```java
 @Select("select * from userinfo where id= 1")
 UserInfo selectOne();
```
这样的话, 只能查找id=1 的数据, 所以SQL语句中的id值不能写成固定数值，需要变为动态的数值解决⽅案：在queryById⽅法中添加⼀个参数(id)，将⽅法中的参数，传给SQL语句使⽤ #{} 的⽅式获取⽅法中的参数。



```java
@Select("select * from userinfo where id= #{id}")
UserInfo selectOne(Integer id);
```


**添加测试用例**：

```java
@Test
void selectOne() {
    log.info(userInfoMapper.selectOne(1).toString());
}
```

运行结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/72cc6c8c8eb14c8c8aab7a3df209d6f5.png)
使⽤ `@Param`设置别名, `#{...}`⾥⾯的属性名必须和
`@Param` 设置的⼀样。

```java
@Select("select * from userinfo where id= #{userId}")
UserInfo selectOne2(@Param("userId") Integer id);
```


#### 增(Insert)

`增加一个用户`

```java
@Insert("insert into userinfo (username,password,age,gender,phone)" +
        "values(#{username},#{password},#{age},#{gender},#{phone})")
Integer insert(UserInfo userInfo);
```
**添加测试用例**：


```java
@Test
void insert() {
    UserInfo userInfo = new UserInfo();
    userInfo.setUsername("zhaoliu");
    userInfo.setPassword("zhaoliu");
    userInfo.setAge(6);
    userInfo.setGender(0);
    userInfo.setPhone("123456789");

    Integer result = userInfoMapper.insert(userInfo);
    log.info("insert 方法，执行结果：{}",result);
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/da706440be7549058826d35488d0ed63.png)


获取自增id

```java
    //返回自增id
    @Options(useGeneratedKeys = true, keyProperty = "id")
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/edfeb0ae02ee482296fcce36a62ba9e2.png)
对Insert的参数进行重命名

参数为对象时，对参数重命名

```java
    @Options(useGeneratedKeys = true, keyProperty = "id")
    @Insert("insert into userinfo (username,password,age,gender,phone)" +
            "values(#{userInfo.username},#{userInfo.password},#{userInfo.age},#{userInfo.gender},#{userInfo.phone})")
    Integer insert2(@Param("userInfo") UserInfo userInfo);
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ac947464521b4902b8ff8b51988c0e1f.png)


#### 删(Delete)

`删除id为5的用户`

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/cf9a62d40e7d4f368c8bc659773cc9da.png)

```java
@Select("delete from userinfo where id = #{id}")
Integer delete(Integer id);
```

测试：

```java
@Test
void delete() {
    userInfoMapper.delete(5);
}
```
执行成功了
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/cdfcdaafddd74d70af4e756965bf73c8.png)


数据库中id为5的用户已经删了
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/af02f977181a4729be132094834ba9ae.png)


#### 改(Update)
`将id等于4的年龄改为16`
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/fc6edee4016f45d1a5754eacf1ae8271.png)



```java
@Update("update userinfo set age = #{age} where id = #{id}")
Integer update(Integer id);
```
测试：

```java
@Test
void update() {
    UserInfo userInfo = new UserInfo();
    userInfo.setAge(16);
    userInfo.setId(4);
    Integer result = userInfoMapper.update(userInfo);
    if (result > 0){
        log.info("数据修改成功");
    }
}
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d6e14ba4692b420196202fe5c1a8aaf4.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/853dfe227e6040dea323070e1756e328.png)


#### 查(Select)

结果映射，MyBatis会自动的根据数据库的字段名和Java对象的属性名，进行映射，如果名称一样就进行赋值。

1. 对mysql查询结果进行重命名

```java
/**
 * 结果映射
 * 方法一：对字段进行重命名
 * @return
 */
@Select("select id, username, password, age, gender, phone," +
        " delete_flag as deleteFlag, create_time as createTime, update_time as updateTime" +
        " from userinfo")
List<UserInfo> selectAll();
```



测试:


```java
@Test
void selectAll() {
    List<UserInfo> list = userInfoMapper.selectAll();
    log.info(list.toString());
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4f6f507864034f3c967eb924c10f299d.png)


2. 使用`@Results`

```java
    /**
     * 结果映射
     * 方法二：使用注解的方式
     * @return
     */
    @Results({
            @Result(column = "delete_flag",property = "deleteFlag"),
            @Result(column = "create_time",property = "createTime"),
            @Result(column = "update_time",property = "updateTime"),
    })
    @Select("select * from userinfo")
    List<UserInfo> selectAll2();
```


测试 ：


```java
@Test
void selectAll2() {
    List<UserInfo> list = userInfoMapper.selectAll2();
    log.info(list.toString());
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/700d0af8c1774a9f9f61d51c4dc58015.png)

如何复用`@Results`的定义

```java
/**
 * 结果映射
 * 方法二：使用注解的方式
 * @return
 */
@Results(id = "BaseMap",value = {
        @Result(column = "delete_flag",property = "deleteFlag"),
        @Result(column = "create_time",property = "createTime"),
        @Result(column = "update_time",property = "updateTime"),
})
@Select("select * from userinfo")
List<UserInfo> selectAll2();


@ResultMap(value = "BaseMap")
@Select("select * from userinfo where id= #{id}")
UserInfo selectOne(Integer id);
```




![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/8ac92e0b989b4248a5d7f631476fba7b.png)



3. 使用配置的方式，自动转驼峰


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4c3c91029c394994860f5dbb138d8ad8.png)


```java
mybatis:
  configuration: # 配置打印 MyBatis⽇志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    map-underscore-to-camel-case: true #配置驼峰自动转换
```





```java
/**
   * 结果映射
   * 方法三：使用配置的方式，自动转驼峰
   * @return
   */
  @Select("select * from userinfo")
  List<UserInfo> selectAll3();
```





运行测试：



```java
@Test
void selectAll3() {
    List<UserInfo> list = userInfoMapper.selectAll3();
    log.info(list.toString());
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/25e3eb2070284579aeb8e2b37dfd5129.png)
## XML


1. 配置数据库


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/11fc00816e4b4da8b5dbded7657419b8.png)

```java
spring:
  datasource:
    url: jdbc:mysql://127.0.0.1:3306/mybatis_test?characterEncoding=utf8&useSSL=false
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
```

2. 指明xml的路径

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/2966a4d845e34beda3f1fbe601cb8f09.png)


3. 写xml的实现
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/fc6761bd0fe94b1da82c47db38b2fe33.png)




```java
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.demo.mapper.UserInfoXMLMapper">
    
    <select id="selectAll" resultType="com.example.demo.model.UserInfo">
        select  * from userinfo;
    </select>

</mapper>
```


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/93fde10ec89947f79cf129f23c00f27a.png)


```java
@Mapper
public interface UserInfoXMLMapper {
    List<UserInfo> selectAll();
}

```


测试:




![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/984f7889c021421d8b3be99222d90446.png)



```java
@Slf4j
@SpringBootTest
class UserInfoXMLMapperTest {
    @Autowired
    private UserInfoXMLMapper userInfoXMLMapper;
    @Test
    void selectAll() {
        List<UserInfo> userInfos = userInfoXMLMapper.selectAll();
        log.info(userInfos.toString());
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1774913b6b1e49ffb60e57f1f24b6a33.png)


### 常见问题

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/6407a2e6dace4cb2b64a5cd980917837.png)
可能原因：
1. `xml`和接口定义的方法名不一致
2. `mapper`的路径配置和`xml`的路径不一样
3. `xml` `namespace`写错了
