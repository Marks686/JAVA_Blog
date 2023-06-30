# CRUD


增加(Create)、查询(Retrieve)、更新(Update)、删除(Delete)四个单词的首字母缩写.
我们作为一个后端程序员以后可能工作主要就是CRUD.
## 一、Create

新增插入数据使用`insert into values(值,值...)`;
`insert into student values(1,'张三');`
![在这里插入图片描述](https://img-blog.csdnimg.cn/bec26e4950494597a26fa489a34578b4.png)
### 指定列插入

![在这里插入图片描述](https://img-blog.csdnimg.cn/21eff1e19f424aa6891072deab2abfaa.png)
我们想只插入name和gender两列数据:`insert into student(name,gender) values('张三','男');`
![在这里插入图片描述](https://img-blog.csdnimg.cn/530d9a80ded0456a8e67a39ffc87251f.png)
此时values后面的内容和values前面()的内容匹配.
想要看到结果,需要使用查询语句,最简单的查询:`select * from student;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/a7577782d5af4dc793f42597d8b6582b.png)
### 全列插入
` insert into student values(2,'李四','女'),(3,'王五','男');`
![在这里插入图片描述](https://img-blog.csdnimg.cn/dea9fd0bac1e4b3eba0a84b8a742ce04.png)
一次插入N个记录,比一次插入一个记录,分N次插入,效率更高一点.交互多次的成本要比交互一次高很多.



### 时间日期插入

插入时间是通过特定格式的字符串来表示时间日期的.
形如:`2023-02-17-21:25:00`
![在这里插入图片描述](https://img-blog.csdnimg.cn/3dc81f06200144808beb72d7caeaf1b5.png)
往表中插入信息:
`insert into homework values(1,'2023-02-17-21:25:00');`
![在这里插入图片描述](https://img-blog.csdnimg.cn/d329b27946dd48faa6d24ed210311aa3.png)
假设我们想把这个时间日期设置成当前时刻,sql提供了一个函数`now()`
`insert into homework values(2,now());`
![在这里插入图片描述](https://img-blog.csdnimg.cn/1cc0b9f659944209bf820a37aa6d74ad.png)
## 二、Retrieve
查找操作
### 全列查找

查找整个表,所有行所有列.
`select * from 表名;`
`* `表示所有列,这种特殊含义的符号,计算机中叫做"通配符".


> mysql 是一个"客户端服务器"结构的程序


![在这里插入图片描述](https://img-blog.csdnimg.cn/04ecef8173094da3ba42c4656c4ddb15.png)


结论:执行select * 操作可能会非常危险.
如果数据量非常大,几亿几十亿,进行select * 操作会出问题.
请求本身不大,请求发到服务器上,服务器要读取硬盘,由于数据量非常大,就会立刻把硬盘访问的带宽给占满.此时其他程序也想使用硬盘就会出问题.进一步部署的服务器可能就死机了.除此以外还有网络.
这个操作会瞬间吃满硬盘带宽和网络带宽....就可能导致其他程序无法使用硬盘或者使用网络.



### 指定列查询

`select 列名,列名.....from 表名;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/00a9466ad3754de1b0768c71bbfc2069.png)

`select name,gender from student;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/cb42e984d9554ffc802292a46a181e5e.png)

查询结构就只是我们想查的那两列.

### 查询字段是表达式

在查询的过程中可以做一些简单的运算(可以是列和列之间的运算)

我们先创建一个表:
`create table exam_result (id int, name varchar(20), chinese decimal(3,1), math decimal(3,1),english decimal(3,1));`
![在这里插入图片描述](https://img-blog.csdnimg.cn/4a53c65ba6574bca9b75bedb09a3270d.png)
然后插入:
`insert into exam_result values (1,'唐三藏', 67, 98, 56),
      (2,'孙悟空', 87.5, 78, 77),
      (3,'猪悟能', 88, 98.5, 90),
      (4,'曹孟德', 82, 84, 67),
      (5,'刘玄德', 55.5, 85, 45),
      (6,'孙权', 70, 73, 78.5),
      (7,'宋公明', 75, 65, 30);`
     
![在这里插入图片描述](https://img-blog.csdnimg.cn/c6a0c1f6acc04a99b8c467840c767d1e.png)

此时数据就全部插入进去了,现在我看看查询为表达式的操作:

1. 我们可以在查询的时候针对分数进行变换,比如让查询的数学成绩都在原来的基础上+10分.
`select name,math + 10 from exam_result;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/f1d40433dbc34d9bac40fc8d477406ee.png)
上述这样的查询,数据库服务器硬盘的数据是没有改变的.原因就是mysql是一个"客户端-服务器"结构的程序!!用户在客户端输入的sql,通过请求发送给服务器,服务器解析并执行sql把查询的结构从硬盘中读取出来,通过网络响应还给客户端,客户端把这些数据以"临时表"(只是在客户端显示一下的临时表,和服务器那边的硬盘上的表没啥关系)的形式展示出来.

2. 查询每个同学的总成绩
`select name,math + chinese + english from exam_result;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/f334f3cee7464fc2bfc8c7805cbc4390.png)
表达式查询,是让列和列之间进行运算而不是行和行.按照表达式查询,临时表的列名就和表达式是一样的,很多时候,表达式的含义是不直观的.

### 查询的时候指定别名
 相当起了一个小名,可以让我们更方便的理解含义.
`select name,math + chinese + english as total from exam_result;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/eeb0862c7fcf4a619a91274808f43f6d.png)
as可以省略,但是不建议!

### 去重DISTINCT

使用distinct针对指定列进行去重(把重复的行只保留一个)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2ca04921dc79485d9557e39fe1ed0006.png)
数学这一列有重复的,我们先查看math这一列:
`select math from exam_result;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/b49541154cda4533bdb976f498bb67bb.png)

然后:`select distinct math from exam_result;`此时回车我们就看到去重了.
![在这里插入图片描述](https://img-blog.csdnimg.cn/7d5771122e71477ea31f473bcb2b9f1e.png)
distinct指定多个列的时候,要求这些列的值都相同,才视为重复.

### 按照查询结果排序
使用order by子句,指定某些列进行排序,排序可能升序也可能降序.
比如我们想按照数学成绩升序排序:
`select name,math from exam_result order by math;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/ceeb2b864e264cf4a8f2f2046f1a7a99.png)
对于mysql来说,如果一个sql没有指定order by此时查询结果集的数据顺序是不可预期的. 所以在代码逻辑中不能依赖这里的查询顺序.

order by相当于针对很大数据量进行排序,有可能内存存不下,所以此时可能用的是归并排序,归并排序既可以对内存进行排序,又可以对硬盘进行高效的排序.

降序:
`select name,math from exam_result order by math desc;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/bc90976cfc9e42dcb32f5a30713c2270.png)
desc是describe的缩写.

还可以指定多个列来排序.多个列之间使用","分割.列越靠前就是关键的排序依据.先按照第一列排序,如果第一列的值相同了,再按照第二列排序......
` select * from exam_result order by math desc,chinese desc;`
咱们在排序的时候最关键的就是明确排序的列的主次关系.


### 条件查询:WHERE
在查询的时候制定筛选条件,符合条件的数据留下,不符合的直接pass,想查询需要先描述条件,sql就通过一系列的运算符来表示条件.

![在这里插入图片描述](https://img-blog.csdnimg.cn/15f85a684898411aa97f11d9b5bd684a.png)

#### 基本查询
1. 查询英语不及格的同学
`select * from exam_result where english < 60;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/fdaaf763cc5342a4b464ec7d8221c249.png)
相当于针对数据库的表进行遍历,取出每一行数据,把数据代入到条件中,看条件是否符合,如果是真,这个记录就保留,作为结果集的一部分,如果是假,这个记录就pass.
2. 查询语文成绩好于英语成绩的同学

`select * from exam_result where chinese > english;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/23bcef8592374e45924108d8225cde8d.png)



3. 查询总分在200分以下的同学
`select * from exam_result where chinese + english + math < 200;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/7ee6925160cd4685b3944acd2bf8c492.png)
还可以：
`select name,chinese+math+english from exam_result where chinese + english + math < 200;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/661c7d588a3143e98f8ebd33389ba22e.png)
`select name,chinese+math+english as total from exam_result where chinese + english + math < 200;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/bb18c9a0c1654da9b758d4e2bb26fceb.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/cff49f30848644ee87b0d498cad0b08b.png)

#### AND与OR

1. 查询语文成绩大于80分，且英语成绩大于80分的同学
`select * from exam_result where chinese > 80 and english > 80;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/ea3c379e3eb34937b3afc48eeae2346d.png)
2. 查询语文成绩大于80分或者英语成绩大于80分的同学
`select * from exam_result where chinese > 80 or english > 80;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/eea5334a3413470ebb5d57704079fd6c.png)
如果一个where中即存在and又存在or，先执行and后执行or.
#### 范围查询

1. between...and... 约定的是一个前闭后闭区间.
查询语文成绩在 [80, 90] 分的同学及语文成绩.
 ` select * from exam_result where chinese >= 80 and chinese <= 90;`
`select * from exam_result where chinese between 80 and 90;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/775882c3736a4ed49dfb1d11b583c472.png)
2. in,查询数学成绩是 58 或者 59 或者 98 或者 99 分的同学及数学成绩
` select * from exam_result where math = 58 or math = 59 or math = 98 or math = 99;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/d55676f94fa4406ab7d92eef20b5f717.png)
` select * from exam_result where math in(58,59,98,99);`
![在这里插入图片描述](https://img-blog.csdnimg.cn/20a82b4b1c3149ae88be162410ddf5a4.png)

#### 模糊查询：LIKE

模糊匹配,不要求元素完全相同,只要求满足一定的规则,就可以了.
只支持两个用法:
1.使用%表示任意0个字符或N个字符.
2.使用_ 表示任意1个字符.


查询姓孙同学的成绩
`select * from exam_result where name like '孙%';`
查询孙开头的,`like '%孙'`是查询孙结尾的,`like %孙%`查询包含孙的.
![](https://img-blog.csdnimg.cn/f93958cbf2694b68acb61e794f69d863.png)
`insert into exam_result values(8,'孙行者悟空',null,null,null);`

![在这里插入图片描述](https://img-blog.csdnimg.cn/2752e4b4c82a4e8cb0589578982488a8.png)
`select * from exam_result where name like '孙_';`

![在这里插入图片描述](https://img-blog.csdnimg.cn/c7a065eb11fa4c7ea751bd633ef8935f.png)

`select * from exam_result where name like '孙_ _';`
![在这里插入图片描述](https://img-blog.csdnimg.cn/ee7812eeebc845e29e89740c3366613a.png)



#### NULL 的查询：IS [NOT] NULL

null 和其他数值进行运算,结果还是null,null结果在条件中,就相当于false了.
使用`<=>`这个比较相等运算,就可以处理null的比较.


`select * from exam_result where chinese <=> null;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/7d4aa9feda3740c3a522b707976fc3d9.png)
`select * from exam_result where chinese is null;`


![在这里插入图片描述](https://img-blog.csdnimg.cn/213b82b9188947b3a8955e31cf9d93d2.png)



###  分页查询：LIMIT

针对查询结果进行限制,很多地方都可以见到.




只获取前三条数据:

`select * from exam_result limit 3;`



![在这里插入图片描述](https://img-blog.csdnimg.cn/30f0d20fc8ac423194ebeb6efe6fbb71.png)
limit可以搭配offset,声明从哪一条开始查询(从0开始记数)

`select * from exam_result limit 3 offset 0;`
`select * from exam_result limit 3 offset 3;`
`select * from exam_result limit 3 offset 6;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/c6b9913bca7c4b788c5fb1127e6f4fd1.png)

`limit 3 offset 6;` 等价于 `limit 6,3;`

limit也是可以和前面那些查询搭配使用的.

> 比如:查询总分前三名的同学的信息:


1. 计算每个同学总成绩.
2. 按照成绩排序(降序).
3. 取前三条记录.

`select name, chinese + english + math as total from exam_result order by total desc limit 3;`
![加粗样式](https://img-blog.csdnimg.cn/6e196f7e09914b8f885fa502ad4d4c64.png)

## 三、Update

**update 表明 set 列名 = 值, 列名 = 值.......where 条件;**

1. 将孙悟空同学的成绩改成80分.

` update exam_result set math = 80 where name = '孙悟空';`
![在这里插入图片描述](https://img-blog.csdnimg.cn/5dd43cfb16aa4d31854fc6d51e4add5f.png)

2. 把曹孟德的数学成绩改为60,语文改为70.

` update exam_result set math = 60,chinese = 70 where name = '曹孟德';`



![在这里插入图片描述](https://img-blog.csdnimg.cn/fb17f387d7474013812981eaf5afb2da.png)


3. 把总成绩倒数前三二的 2 位同学的数学成绩加上30分.

先查询总成绩排名:
`select name,chinese + math + english as total from exam_result order by total desc;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/082b91fb8e1d4c06b45ce50fb552aac6.png)

null和其他数据运算结果还是空,所以孙行者悟空三门成绩都为null他最终总成绩就是null,如果有一门为null,最总结果也还是null, null在排序的时候,视为最小的值. 所以我们主要看宋公明的数学成绩变化就行.

![在这里插入图片描述](https://img-blog.csdnimg.cn/2b07944b3826479e885440878778e474.png)

`update exam_result set math = math + 30 order by chinese + math + english asc limit 2;`


![在这里插入图片描述](https://img-blog.csdnimg.cn/c49853741de84f62849ef84d87a787a1.png)

4. 把所有同学的语文成绩改为原来的0.5倍


`update exam_result set chinese = chinese / 2;`
修改我们发现了小问题:有7行发生了修改,有2个警告.

![在这里插入图片描述](https://img-blog.csdnimg.cn/f941aa77d3854784aaf2968e68675d56.png)
我们查看警告:
`show warnings;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/2a11fc4c4ceb4efea74f7d410dbfa1c0.png)
这里的警告信息是date截断,即小数点后位数不够就截断了,相当于把小数点后面的省略了.

![在这里插入图片描述](https://img-blog.csdnimg.cn/4449312177b948048022b80288005223.png)
本来孙悟空成绩应该是43.75,刘玄德成绩是27.75,由于约定这一列是3位有效数字,并且小数点后保留1位,此时就按照四舍五入的方式把数据截断了.

## 四、Delete


删除记录(行)

**delect from 表名 where 条件;**

`delete from exam_result where name = '孙行者悟空';`


![在这里插入图片描述](https://img-blog.csdnimg.cn/0a6246dfbeac44158681f857ceb109da.png)


删除孙字开头的:

`delete from exam_result where name like '孙%';`



![在这里插入图片描述](https://img-blog.csdnimg.cn/dac27548a43249a8b5ac6a40a413eb3b.png)
这里就是把条件匹配出来的结果,都删掉了.

下面这个操作基本相当于删表:
` delete from exam_result;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/782a336425124232b3fec8ca2761611f.png)
`delete from` 表还在,里面的数据没了.
`drop table` 表和数据都没了.
