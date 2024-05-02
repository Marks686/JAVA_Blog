## 动态SQL
在MyBatis框架中，动态SQL是一个强大的特性。它允许我们根据不同的条件动态的组装SQL语句，能够灵活的发挥SQL强大的功能，方便解决一些其他方法难以解决或解决方法繁琐的问题。

在MyBatis中，我们可以使用特定的标签来实现这些功能，如< if >用于条件判断，< choose >、< when >和< otherwise >用于多条件选择，< trim >、< where >和< set >用于处理SQL语句的前缀和后缀，以及< foreach >用于迭代处理。






###  < if > 标签

**注解写法**
1. 把全部的sql放在script标签下
2. 使用if标签








> 性别为空：


```java
/**
 * 性别字段为空
 * @param userInfo
 * @return
 */
@Insert("<script>" +
        "insert into userinfo (username,password,age," +
        "<if test='gender!=null'>gender,</if>" +
        "phone)" +
        "values(#{username},#{password},#{age}," +
        "<if test='gender!=null'>#{gender},</if>" +
        "#{phone})" +
        "</script>")
Integer insert(UserInfo userInfo);
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f483e721053e468da3e3a52a4f57e396.png)

```java
@Test
void insert() {
    UserInfo userInfo = new UserInfo();
    userInfo.setUsername("libai-2024-1");
    userInfo.setPassword("liibaiiili");
    userInfo.setAge(14);
    userInfo.setPhone("10290192");
    userInfo2Mapper.insert(userInfo);
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1cf23be8540244c6ab6866f8ed72798b.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a4282d58383a46a38955c5f835568163.png)
**XML写法：**

```java
<insert id="insertByXML">
    insert into userinfo(username,password,age,
    <if test="gender!=null">
        gender,
    </if>
    phone)
    values(#{username},#{password},#{age},
    <if test="gender!=null">
        #{gender},
    </if>
    #{phone})
</insert>
```


```java
@Test
void insertByXML() {
    UserInfo userInfo = new UserInfo();
    userInfo.setUsername("libai-2024-2");
    userInfo.setPassword("liibaiiili");
    userInfo.setAge(18);
    userInfo.setPhone("10290192");
    userInfo2Mapper.insertByXML(userInfo);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/39a21989444a4e7190b90d901b09f6ae.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c890f8b39b784089af7ad7d02532fe08.png)


###  < trim> 标签
帮助我们去除多余的字符


`prefixOverrides`：去除trim标签代码块指定的字符
`prefix`：在trim标签代码块最前面添加制定的字符 
`suffix`：在trim标签代码块最后面添加制定的字符 
`suffixOverrides`：去除trim标签代码块最后面指定的字符
**XML：**

> 去除最后一个符号，去除括号：


```java
Integer insertByXML(UserInfo userInfo);
```

```java
 <insert id="insertByXML">
    insert into userinfo
    <trim suffixOverrides="," prefix="(" suffix=")">
        <if test="username!=null">
            username,
        </if>
        <if test="password!=null">
            password,
        </if>
        <if test="age!=null">
            age,
        </if>
        <if test="gender!=null">
            gender,
        </if>
        <if test="phone!=null">
            phone
        </if>
    </trim>
    values
    <trim suffixOverrides="," prefix="(" suffix=")">
        <if test="username!=null">
            #{username},
        </if>
        <if test="password!=null">
            #{password},
        </if>
        <if test="age!=null">
            #{age},
        </if>
        <if test="gender!=null">
            #{gender},
        </if>
        <if test="phone!=null">
            #{phone}
        </if>
    </trim>
</insert>
```

```java
@Test
void insertByXML() {
    UserInfo userInfo = new UserInfo();
    userInfo.setUsername("libai-2024-3");
    userInfo.setPassword("liibaiiili");
    userInfo.setAge(18);
//        userInfo.setGender(1);
//        userInfo.setPhone("10290192");
    userInfo2Mapper.insertByXML(userInfo);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7d89abf9a2814be9b066849e5d097c65.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/da3ffbfbea7c4a38a9b7dce8ca251384.png)

> 查询年龄为14的用户


```java
List<UserInfo> selectByCondition(UserInfo userInfo);
```

```java
<select id="selectByCondition" resultType="com.example.demo.model.UserInfo">
    select * from userinfo
    where
    <trim prefixOverrides="and">
        <if test="username!=null">
            username = #{username}
        </if>
        <if test="age!=null">
            and age = #{age}
        </if>
        <if test="gender!=null">
            and gender = #{gender}
        </if>
    </trim>
</select>
```

```java
@Test
void selectByCondition() {
    UserInfo userInfo = new UserInfo();
//        userInfo.setUsername("libai");
//        userInfo.setGender(0);
    userInfo.setAge(14);
    userInfo2Mapper.selectByCondition(userInfo);
    log.info(userInfo.toString());
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1629880e04c24a1c957bd9781782aa99.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/244e8427e1b7458f8f34397cf7de5700.png)


如果是查询来说，还有更简单的方式，就是使用`where`标签，`where`标签可以替代上面`trim`标签的功能
###  < where> 标签
它会把前面的and自动去掉
```java
<select id="selectByCondition" resultType="com.example.demo.model.UserInfo">
        select * from userinfo
        <where>
            <if test="username!=null">
                username = #{username}
            </if>
            <if test="age!=null">
                and age = #{age}
            </if>
            <if test="gender!=null">
                and gender = #{gender}
            </if>
        </where>    
    </select>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/dbe242ee33b145a2bbf9cde155a73f09.png)

**使用`trim`存在的问题，当所有条件都为空时，SQL语句会变成`select * from userinfo where`，如果查询条件都为空，`where`标签会自动去去除`where`关键字，还会帮我们去除最前面的and字符**。

问题的解决方式：`where 1=1`
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/009f46bc66c74fb2a78f67a0babe015a.png)

**注解写法：**


```java
@Select("<script>" +
        "select * from userinfo" +
        "        <where>" +
        "            <if test=\"username!=null\">" +
        "                username = #{username}" +
        "            </if>" +
        "            <if test=\"age!=null\">" +
        "                and age = #{age}" +
        "            </if>" +
        "            <if test=\"gender!=null\">" +
        "                and gender = #{gender}" +
        "            </if>" +
        "        </where>" +
        "</script>")
List<UserInfo> selectByCondition3(UserInfo userInfo);
```

```java
    @Test
    void selectByCondition3() {
        UserInfo userInfo = new UserInfo();
        userInfo.setUsername("libai");
//        userInfo.setGender(0);
//        userInfo.setAge(14);
        userInfo2Mapper.selectByCondition3(userInfo);
        log.info(userInfo.toString());
    }
```

![](https://img-blog.csdnimg.cn/direct/6815c385d48f42d3bfdab8b8f818bf8d.png)


###  < set> 标签
修改id为 6的用户的信息
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/67a9f87794c54af5a4e0d7741b212483.png)
根据前面学的知识我们可以这样写：

**XML写法：**

`Integer updateByCondition(UserInfo userInfo);`（使用trim标签）

```java
<update id="updateByCondition">
    update userinfo
    set
    <trim suffixOverrides=",">
        <if test="username!=null">
            username = #{username},
        </if>
        <if test="age!=null">
            age = #{age},
        </if>
        <if test="gender!=null">
            gender = #{gender}
        </if>
    </trim>
    where id = 6
</update>
```

上面的写法我们可以用 < set > 标签来代替：

```java
/**
 * 使用set标签
 * @param userInfo
 * @return
 */
Integer updateByCondition2(UserInfo userInfo);
```



```java
<update id="updateByCondition2">
    update userinfo
    <set>
        <if test="username!=null">
            username = #{username},
        </if>
        <if test="age!=null">
            age = #{age},
        </if>
        <if test="gender!=null">
            gender = #{gender}
        </if>
    </set>
    where id = 6
</update>
```

```java
@Test
void updateByCondition2() {
    UserInfo userInfo = new UserInfo();
    userInfo.setUsername("libai2024-01");
//        userInfo.setGender(0);
//        userInfo.setAge(14);
    userInfo2Mapper.updateByCondition2(userInfo);
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ea2d6623c23d45e5bca19f9e624ff698.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/dd13de785bf2420c98682b54b2ee147c.png)




###  < foreach> 标签

对集合进⾏遍历时可以使⽤该标签。标签有如下属性：
• collection：绑定⽅法参数中的集合，如 List，Set，Map或数组对象
• item：遍历时的每⼀个对象
• open：语句块开头的字符串
• close：语句块结束的字符串
• separator：每次遍历之间间隔的字符串


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f2ca06e5ccb54664bc6f087466d0be43.png)

删除一个集合的数据

> 删除`id`为`7 8 9` 的数据

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/eee93376f1ed423bbc58c9d107e8d7c9.png)

```java
Integer batchDelete(@Param("ids") List<Integer> ids);
```




```java
<delete id="batchDelete">
     delete from userinfo
     where id in
     <foreach collection="ids" separator="," item="id" open="(" close=")">
        #{id}
     </foreach>
</delete>
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/7aa1bade08e94fefb13d44f0b37dfdb2.png)


```java
@Test
void batchDelete() {
    userInfo2Mapper.batchDelete(Arrays.asList(7,8,9));
}
```
删除成功：

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/9c81357f1e91442da3949182d1ebebf4.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/63aeadaddd1b41bfb2feb37e4835f3bc.png)

###  < include> 标签

在xml映射文件中配置的SQL，有时可能会存在很多重复的片段，此时就会存在很多冗余的代码。

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/da999d36a413474d9921cdaf586fe228.png)
可以对重复的`sql`段进⾏抽取，通过 `< sql >` 标签封装到⼀个`SQL`片段，然后再通过`<include>`标签进行引用。
• `< include >` ：标签进⾏引用。
• `< sql >` ：定义可重⽤的SQL片段
• `< include >` ：通过属性refid，指定包含的SQL片段



```java
<sql id="allColumn">
	id, username, age, gender, phone, delete_flag, create_time, update_time
</sql>
```
通过 `<include>` 标签在原来抽取的地⽅进⾏引⽤。操作如下：

```java
<select id="queryAllUser" resultMap="BaseMap">
	 select
	 <include refid="allColumn"></include>
	 from userinfo
</select>
```

```java
<select id="queryById" resultType="com.example.demo.model.UserInfo">
	 select
	 <include refid="allColumn"></include>
	 from userinfo where id= #{id}
</select>
```

