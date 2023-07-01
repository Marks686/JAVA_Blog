# 数据库约束
约束就是数据库针对里面的数据能写啥,给出的一组"检验规则".

这样的约束可以是程序员人工来保证,也可以是程序自动保证.和计算机相比,人往往"不靠谱",计算机的存储能力,远远超过人类.计算机的纪律性也是远远超过人脑的.

约束就是为了提高效率,提高准确性,让数据库这个软件集成的一个针对数据校验的功能.
## NOT NULL

`not null`当前这一列的内容不为空(必填项)

当我们没有设置任何约束的时候此时表里面可以随意插入空值.
![在这里插入图片描述](https://img-blog.csdnimg.cn/f310f27142764c8ca65dfba5b753ed31.png)
接下来我们删表重建:

`create table student(id int not null, name varchar(20) not null);`

![在这里插入图片描述](https://img-blog.csdnimg.cn/c8d779d22b1e4aec9715f8c7697087fd.png)
现在这张表就带了约束.
NULL就是不允许为空,我们此时插入空值会插入失败,而且会有一个很明显的错误提示:id不能位null.

![在这里插入图片描述](https://img-blog.csdnimg.cn/09a4e8b6aed941da8297fdbfe615511e.png)


## UNIQUE

`unique`让列的值是唯一的(不和别的重复)比如说:学号 手机号 身份证号.
当我们插入/修改新数据的时候, 会先查询,先看看数据是否已经存在,如果不存在,就能够插入/修改成功,如果存在,则插入/修改失败.

不加任何约束,创建student,


![在这里插入图片描述](https://img-blog.csdnimg.cn/3f7c371fc8ca487197e90ce67b0fdbc0.png)

通过插入我们可以看见数据库已经有三条重复的数据了,接下来我们加上约束:

`create table student(id int unique, name varchar(20));`

![在这里插入图片描述](https://img-blog.csdnimg.cn/9bc6eab5770d4134a41bb3f4eaf9f5cf.png)
我们发现在desc结果里面,在Key这一列里面显示id这一列有个`UNI`的标记,表示是唯一的,我们接着插入数据:

![在这里插入图片描述](https://img-blog.csdnimg.cn/dbc56dd3d4b14939bcd98359d1d5f729.png)
第一次插入没问题,第二次插入就会给出一个很明显的错误提示:Duplicate意思是重复的,entry意思是入口,条目.报错意思是id这一列1这个值重复出现了,所以就插入失败了.
数据库在插入之前查询了下,才知道1的存在.因此多了unique约束,就多了个查询的成本,有这个约束比没有这个约束插入速度要慢.



## DEFAULT


`default`默认值,当这一行没填数据的时候,默认填的值.

设置默认值,默认值是在insert指定列插入的时候,其他未被指定到的列就是按照默认值来填充的.


任意拿一张表初始的情况下默认值都是null
![在这里插入图片描述](https://img-blog.csdnimg.cn/cf1e44fe32ea4ce1839733bb4638e069.png)

当我们按照指定列插入,没有插入name这一列的时候,结果是null.
`insert into student (id) values(2);`

![在这里插入图片描述](https://img-blog.csdnimg.cn/412000deb8614ca4a4a037d439b027e0.png)

接下来我们继续删表,再建表加上约束:
`create table student (id int,name varchar(20) default '无名氏');`


指定插入:`insert into student (id) values(1);`然后查询表就会看到,name列填成了我们设置的默认值.


![在这里插入图片描述](https://img-blog.csdnimg.cn/12194cf03b8f4e4ba73f9b823906b836.png)

## PRIMARY KEY

`primary key`主键,一条记录,主要表示这条数据的身份标识,身份标识就是用来区分这条数据和其他数据的关键指标.比如:手机号码,身份照吗,学号.所以主键要求是唯一的并且不能为空.
主键 = unique + not null 
mysql要求一个表里面只能有一个主键,创建主键的时候,可以使用一个列作为主键,也可以使用多个列作为主键(复合主键).

设置主键约束:`create table student (id int primary key,name varchar(20));`


![在这里插入图片描述](https://img-blog.csdnimg.cn/4b27153fe67b497c97219ec17bfc0ff1.png)
设置了主键约束后在desc结果里面我们就能看到Null值里面id不允许为null,Key显示为PRI就是id这一列不能为空,也不能重复.接下来我们尝试插入数据:

![在这里插入图片描述](https://img-blog.csdnimg.cn/9facc03ed4084996b3c1635314dda146.png)

从上述信息我们可以看出主键约束就和我们的unique和not null合在一起是类似的.



> 如何给一个记录安排主键?

mysql提供了一个简单粗暴的做法:**自增主键.要求主键是一个自增的整数,依次进行累加.**

下面我们看看自增主键如何使用:

`create table student (id int primary key auto_increment, name varchar(20));`


![在这里插入图片描述](https://img-blog.csdnimg.cn/305ea2929a064ccfb384cb4136b1fe04.png)
当前我们desc的结果除了前面所看到的NO 和 PRI外,后面Extra多了个auto_increment的标识,即当前这一列就是自增主键了,我们给自增主键插入数据的时候,可以手动指定一个值,也可以让mysql自己分配(在insert语句的时候把id设为null).如下:

`insert into student values(null, '张三');`这里设为null并不是真正的设为空,而是让mysql自己看着办.
继续:
`insert into student values(null, '李四');`

`insert into student values(null, '王五');`

![在这里插入图片描述](https://img-blog.csdnimg.cn/2c307fe5568d4aceb08e8f75328a0a37.png)
可以看到插入的三条记录分别是1 2 3,它们是顺着自增主键下来的.
我们自己指定id:
`insert into student values(100, '赵六');`

![在这里插入图片描述](https://img-blog.csdnimg.cn/6fbc540506af453ab9d03495c1a9f492.png)
接着让插入田七id为null,查看表发现mysql自动接着我们定义的id增加:
`insert into student values(null,'田七');`
![在这里插入图片描述](https://img-blog.csdnimg.cn/624b8db313994c42a55955d62f0b2a63.png)
中间的4-99就已经浪费了,除非我们自己定义,否在mysql会一直从最大值往后自增.
## FOREIGN KEY
`foreign key`外键,涉及到两张表之间的关联.


创建一个班级表:
`create table class(classID int primary key auto_increament,className varchar(20));`
创建一个学生表并创建外键约束:要求student表中的每个记录的classId得在class表中classId存在.
`create table student(studentId int primary key auto_increment,name varchar(20),classId int, foreign key (classId) references class (classId));`

student受到class的约束,就把class叫做student的父表,student就是class的子表.
此时班级表不存在,插入学生信息就会失败.
![在这里插入图片描述](https://img-blog.csdnimg.cn/615f1b3d94024f9f8a279c3e92de2dde.png)

接下里我们先给班级表插入数据,完成后,再看学生信息的插入会不会成功:


![在这里插入图片描述](https://img-blog.csdnimg.cn/cd84c42fc8354873a498ef4725c4059e.png)

往学生表插入:
`insert into student values (null,'张三',1);`

![在这里插入图片描述](https://img-blog.csdnimg.cn/76f26310ba2748cfb86c754e314e1e90.png)


当我们执行student信息插入的时候,mysql就会先拿着记录的classId去class表中查一下,如果有才能完成后续的插入,没有就插入失败.

尝试修改student表中的classId:

`update student set classId = 10 where studentId = 2;`

显示报错信息:外键约束失败.
![在这里插入图片描述](https://img-blog.csdnimg.cn/ac04a47ac991432b8456e1e5cd4c5fe3.png)
针对父表我们也不能delete和update:此时外键约束已经生效了.
`delete from class where classId = 1;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/e63f6121f4fe48878d0b79ce8acb944c.png)
外键约束并非是单向的,父表和子表互相约束.

# 表的设计


表的设计要求有一定的经验.所以我们先了解一些简单的方法,能够解决常见的数据库设计问题就行.

表的设计就是根据需求,把表应该是啥样的写出来.比如:有几个表,每个表是干啥的,每个表有几个字段,啥类型,有啥约束.如果是简单的场景,设计无从谈起,因为如何建表是比较明显的.


> 设计表分两步走:

1. 梳理清楚需求中的"实体".

实体类似于面向对象中的对象,就是需求中的关键性质的名词.比如:学生,教师,作业......很多时候,每个实体就需要对应一张表来进行表示.


2. 梳理清楚实体之间的关系,按照关系代入既定的公式中.

实体之间的关系主要有三种(严格说四种):一对一,一对多,多对多,没关系.

## 一对一
一个学生只能有一个账号,一个账号只能供一个学生所有.

> 如何设计表:


1.建一个大表,包含学生信息 + 账号信息
`account-student(accountId,username,password,studentName);`

2.建两个表,相互关联连
`account(accountId, username,password,studentId);`
`student(studentId,name......)`

3.建两个表
`account(accountId,username,password);`
`student(studentId,studentName,accountId)`

后续建一些教师表啥的,也能和account关联.



## 一对多
一个班级可以包含多个学生,一个学生只能处于一个班级.这种情况也有两种表示方式:
> 如何设计表:


1.第一种:
`student(studentId,name);`
`class(classId,className,studentIdList);`
 这里有一个数组或者列表来保存 学生id,在mysql中没有数组类型,所以不能这样操作,但是有些数据库可以比如:redis是有数组类型,可以考虑这样设计.
 
2.第二种:这种方案是最典型的一对多的设计方式.
`class(classId,className);`
`student(studentId,name,classId);`

![在这里插入图片描述](https://img-blog.csdnimg.cn/65d38ec392c54697bef7c927abc218b3.png)




## 多对多

一个学生可以选择多个课程,一个课程也可以提供给多个学生.

> 如何设计表:

`student(studentId,name)`

`course(courseld,name)`

![在这里插入图片描述](https://img-blog.csdnimg.cn/43e6eae37a5b4b528640ac4237814245.png)

为了把student和course关联起来,我们还需要一个关联表:`student_course(studentId ,courseId)`
![在这里插入图片描述](https://img-blog.csdnimg.cn/98d18d5fbda942ea806a7c4f0f45b9c6.png)

1选了语文和数学,2选了语文.语文这个课程提供给了1和2.一般来说,只要实体和关系都明确了,此表的设计基本就差不多了.如果实体比较多,关系比较复杂,可以画一个"实体关系图"(ER图)来表示关系.
