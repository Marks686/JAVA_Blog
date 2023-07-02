# 查询结果作为新增数据


把查询和新增联合起来,把查询结果作为新增的数据.
![在这里插入图片描述](https://img-blog.csdnimg.cn/d3b5d75322e9465c8da7a1978fe9b52f.png)

先创建两张表:1.`create table student (id int, name varchar(20));` 2.`create table student2 (id int, name varchar(20));`
然后插入数据:`insert into student values (1,'张三'),(2,'李四'),(3,'王五');`此时第一张表里面已经有数据,第二张表是空的.
![在这里插入图片描述](https://img-blog.csdnimg.cn/9f5953f1b76e4924b4192f63bb0452c7.png)
此时我们想把第一张表中的数据拷贝到第二张表中:

`insert into student2 select * from student;`就相当于把查询结果作为新增的数据插入.也有要求:查询结果得到的列数,类型需要和插入表的列数;类型匹配.

![在这里插入图片描述](https://img-blog.csdnimg.cn/0a82c785f3e54a3a91b510b114d22b06.png)
# 查询进阶

## 聚合查询
查询过程中,表的**行和行之间**进行一定的运算.依赖聚合函数,这些是SQL提供的库函数.

### 聚合函数

创建一个新的student表,插入四个信息:
`create table student (id int,name varchar(20));`
`insert into student values (1,'张三'),(2,'李四'),(3,'王五'),(4,NULL)`
![在这里插入图片描述](https://img-blog.csdnimg.cn/00ad59e68db84d76a92ee1b2e7e344f0.png)


----

> `COUNT([DISTINCT] expr)`    返回查询到的数据的数量.

  
  使用范例:
`select count(*) from student;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/f883f44b5bc7438a8f868be269e27e20.png)
这个4就是我们查询的行数,这个操作可以理解成,先执行`select *`,然后去计算`select *` 查询结果有多少行.

按照id查是4行,按name查是3行,因为我们插入数据的时候,把一个名字置为空.

![在这里插入图片描述](https://img-blog.csdnimg.cn/830f4e14e3f8426194d711b81fe9c14a.png)


>  `SUM([DISTINCT] expr)` 返回查询到的数据的 总和，不是数字没有意义.


我们先创建一个exam_result表并且插入数据:

`CREATE TABLE exam_result (
 id INT,
 name VARCHAR(20),
 chinese DECIMAL(3,1),
 math DECIMAL(3,1),
 english DECIMAL(3,1)
);`
`INSERT INTO exam_result (id,name, chinese, math, english) VALUES
 (1,'唐三藏', 67, 98, 56),
 (2,'孙悟空', 87.5, 78, 77),
 (3,'猪悟能', 88, 98.5, 90),
 (4,'曹孟德', 82, 84, 67),
 (5,'刘玄德', 55.5, 85, 45),
 (6,'孙权', 70, 73, 78.5),
 (7,'宋公明', 75, 65, 30);`
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/0da1c07e90ee40b198e5f6beaca21dd7.png)
使用范例:计算所有同学的语文成绩之和.

`select sum(chinese) from exam_result;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/cde8d8ca50814e639d69af32b8f642f1.png)
如果数据中有null,不影响计算.即sum操作会自动跳过结果为null的行.
![在这里插入图片描述](https://img-blog.csdnimg.cn/ef2cfb4f63224943906bfb36f6d90101.png)
我们对name来进行操作:`select sum(name) from exam_result;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/6f173406205f43678d0099e12997b456.png)
发现有8个warnings,我们查看警告:得出结论针对非数字列是无法进行计算的.
![在这里插入图片描述](https://img-blog.csdnimg.cn/a4a465e5c5264d0a8e4aa6fdde2dc3b4.png)


>  `AVG([DISTINCT] expr)` 返回查询到的数据的 平均值，不是数字没有意义.

使用范例:
`select avg(chinese) from exam_result;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/9916c8935d324eb7893b73258881dd1e.png)
搭配表达式:求总成绩的平均分


    

>  `MAX([DISTINCT] expr)` 返回查询到的数据的 最大值，不是数字没有意义.

使用范例:
`select avg(chinese+math+english) from exam_result;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/be653c78c13b4b73b703da670a3e4d53.png)
sql是有一定的统计计算能力的,就像excel一样.正因为sql有这样的统计能力,sql也是非技术岗要掌握的技能.


>  `MIN([DISTINCT] expr)` 返回查询到的数据的 最小值，不是数字没有意义.


使用范例:
`select max(chinese),min(chinese) from exam_result;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/db72d0dae71f461e919a2836b6c5b35f.png)





### GROUP BY子句
上面聚合函数默认都是针对这个表里的所有类进行了聚合,有时候,还需要分组聚合.(按照指定的字段,把记录分成若干组,每一组分别使用聚合函数)

首先创建一张emp表:`create table emp (id int, name varchar(20),role varchar(20),salary int);`
插入几条记录:
`insert into emp values(1,'孙悟空','讲师',10000);`
`insert into emp values(2,'猪悟能','讲师',11000);`
`insert into emp values(3,'沙悟净','讲师',12000);`
`insert into emp values(4,'刘玄德','学管师',10000);`
`insert into emp values(5,'曹孟德','学管师',9000);`
`insert into emp values(6,'如来佛祖','老板',100000);`
`insert into emp values(7,'太上老君','老板',120000);`

![在这里插入图片描述](https://img-blog.csdnimg.cn/f8a63c0bf19f4d9c90854c25d6ba2032.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/45e25e0fee0043f4b6bd25daaee8b87c.png)
求出每个岗位的平均薪资:group by 指定一个列,就会把列里的值,相同的分到同一个组中.

把讲师分一组,学管师分一组,老板在分一组.


`select role,avg(salary) from emp group by role;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/f77fb5a2d1d34bf2a701534d93adc220.png)

分组的时候可以指定条件筛选,但是要搞清楚筛选条件是分组前还是分组后.
分组前筛选使用where条件:求每个岗位的平均薪资,但是刨除孙悟空.

`select role,avg(salary) from emp where name != '孙悟空' group by role;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/be610da6ff024d3b8535176bcad58488.png)



分组后筛选使用having.

### HAVING
求每个岗位的平均薪资,刨除老板.

`mysql> select role, avg(salary) from emp group by role having role != '老板';`
![在这里插入图片描述](https://img-blog.csdnimg.cn/52a75f9e891e4e0cadebc97dea4e6ace.png)

也可以同时在分组前和分组后筛选.`select role, avg(salary) from emp where name != '孙悟空' group by role having role != '老板';`

![在这里插入图片描述](https://img-blog.csdnimg.cn/5da1df17d0f641ad92ffa907a7a2703d.png)




## 联合查询

把多个表联合到一起进行查询.联合查询是基于笛卡尔积运算产生的.笛卡尔积,其实就是一种排列组合,把两张表的记录尽可能的排列组合出N中情况.
![在这里插入图片描述](https://img-blog.csdnimg.cn/421e3cb2adab4b9eaf135e4ee61ad512.png)

由于笛卡尔积是排列组合出来的结果,这里有些数据是无效的.真正有效的数据就四条,我们可以发现这些数据 的两个classId都相等.当我们去掉无效数据之后,笛卡尔积里剩余的数据就是当前每个同学在哪个班级 这样的信息了.
像"两个classId相等"就可以用一个while子句来描述.基于笛卡尔积再加上一定的条件进行查询此时就是联合查询/多表查询.


新创建表并插入数据:
![在这里插入图片描述](https://img-blog.csdnimg.cn/b7a71d2839324361994c0264f95b753c.png)
先查看学生表:
![在这里插入图片描述](https://img-blog.csdnimg.cn/8c845920d5dd49d986d577b5f19ce0b3.png)
班级表:
![在这里插入图片描述](https://img-blog.csdnimg.cn/54f1b527426a4ee1b64707a4940ef920.png)
课程表:
![在这里插入图片描述](https://img-blog.csdnimg.cn/91459a4648834c9bb40f3ffd8dffdfbb.png)
分数表:

![在这里插入图片描述](https://img-blog.csdnimg.cn/9c62e3721ca74ac983864ed3d5a5278e.png)
四张表三个实体,分别是:学生,班级,课程.
1. 学生和班级是一对多关系.
2. 学生和课程是多对多关系.score表相当于是关联表.
3. 班级和课程之间没关系.
### 内连接

> （1）查询“许仙”同学的 成绩:

要找的两个关键信息student和score存在于不同的表中.
1) 先把student和score做笛卡尔积.
`select * from student, score;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/c7e18ce4ec2b4344854b7197be2a407c.png)
2) 去掉无效数据.

`select * from student, score where student.id = score.student_id;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/c4dc140c24614239856d8822cccf6e17.png)
剩下的这些数据都是有效数据.

3) 按照许仙名字来筛选.


`select * from student, score where student.id = score.student_id and student.name = '许仙';`


![在这里插入图片描述](https://img-blog.csdnimg.cn/2f41b829969c474baa0c2b3c59c07d0a.png)
当前结果行数已经是所求了,还需要针对列数也精简一下:
`select student.name, score.score  from student, score where student.id = score.student_id and student.name = '许仙';`

![在这里插入图片描述](https://img-blog.csdnimg.cn/160e7f7aeea049949dfc5577d0467be9.png)

另一种写法:
`select * from student join score;`也是可以完成笛卡尔积.此时后续的条件不用where而是使用on.
`select * from student join score on student.id = score.student_id;`
`select * from student join score on student.id = score.student_id and student.name = '许仙';`
`select student.name, score.score from student join score on student.id = score.student_id and student.name = '许仙';`
![在这里插入图片描述](https://img-blog.csdnimg.cn/0ac1c7950a1e4f5bb67d6947cde8d4f5.png)

> 查询所有同学的总成绩,及同学的个人信息:

`select student.name,sum(score.score) from student, score where student.id = score.student.id group by id;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/22251ec8100e435b80122161381e8c9e.png)

> 查询所有同学的成绩,及同学的个人信息.

查询的时候既有同学名字,也有课程名字.
`select student.name, course.name, score.score from student, course,score where student.id = score.student.id and course.id = score.course_id  group by id;`


### 外连接


内连接和外连接都是进行笛卡尔积,但是细节有差别.
创建新的数据库:`create database java107_2 charset utf8;`
创建student和score表:
`create table student(id int,name varchar(20));`
`create table score(student_id int, score int);`
插入信息:
`insert into student values(1,'张三'),(2,'李四'),(3,'王五');`
`insert into score values(1,90),(2,80),(3,70); `


![在这里插入图片描述](https://img-blog.csdnimg.cn/b555834d4942473795aee8990f2d0ac3.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/f20c596cbbf74a879e15856b5ccc246c.png)

左外连接:`select name,score from student left join score on student.id = score.student_id;`
右外连接:`select name,score from student right join score on student.id = score.student_id;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/b958e1b5258e4fde9b29fb447692b935.png)

如果两张表的数据出现对应不上的情况此时内外连接差别比较明显.比如把score表中的id 3改成4,
`update score set student_id = 4 where score = 70;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/4f0f4f36734e4de49166a92cf3170c90.png)

如果两个表中数据不一 一对应了,此时进行内连接结果就只是两个表中都有体现的数据.
`select name,score from student join score on student.id = score.student_id;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/04b47a7574fa4760995503aca0828ef2.png)

此时进行左外连接查询(以左侧的表为准,左侧的表中的所有数据都能体现出来,右侧表没有的数据会以空值填充):
`select name,score from student left join score on student.id = score.student_id;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/18200efee20b49ce88d7806e6b83c2af.png)
进行右外连接查询:`select name,score from student right join score on student.id = score.student_id;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/dd3eadafbb6e4847af399edf37780b13.png)
相比之下,内连接的使用更多,外连接主要是一些特定的场景.
### 自连接
一张表自己和自己做笛卡尔积.这个操作本质上是把行转成列.SQL中进行条件查询,都是指定某一列/多个列之间进行运算,无法行和行之间关系运算,为了实现行之间的比较,就需要用自连接把行关系转成列关系.

> 显示所有"计算机原理"成绩比"Java"成绩高的成绩信息

![在这里插入图片描述](https://img-blog.csdnimg.cn/db6df1eb74bc4b47af73a479c824e61b.png)


找到分数表:
![在这里插入图片描述](https://img-blog.csdnimg.cn/badfbd9ff4a145efb5ceeef43c5d7b27.png)

把问题转化成查看分数表中哪个同学的课程id为3并且比课程id为1的分数要高.当前3号课程和1号课程分数是行关系,此时不能直接比较,我们就需要把行转成列:
自连接的时候需要指定一下表的别名:
`select * from score as s1, score as s2 where s1.student_id = s2.student_id`

![在这里插入图片描述](https://img-blog.csdnimg.cn/35a7f67c13fb46e593879969dee0e8a4.png)

`select * from score as s1, score as s2 where s1.student_id = s2.student_id and s1.course_id = 1 and s2.course_id = 3;`

![在这里插入图片描述](https://img-blog.csdnimg.cn/8d6b9f9ea5d449789209d9549fa42d42.png)
这个结果表示有三个同学修了这两个课程.

最后一步:

`select * from score as s1, score as s2 where s1.student_id = s2.student_id and s1.course_id = 1 and s2.course_id = 3 and s1.score < s2.score;`
![在这里插入图片描述](https://img-blog.csdnimg.cn/fae4549167444bd88ad987b84cf1b7f8.png)


SQL的逻辑表达能力有限,很多时候不是做不了,而是代价大,相比之下,使用Java这样的语言来描述复杂逻辑是更好的选择. 


### 子查询



把多个查询语句来合并成一个,子查询在实际开发中要慎重,子查询一旦嵌套的层次多了,对于代码的可读性是毁灭性的打击.


#### 单行子查询

查询于"不想毕业"同学的同班同学:

`select classes_id from student where name = '不想毕业';`
`select name from student where classes_id  =1;`

嵌套:`select name from student where classes_id  = (select classes_id from student where name = '不想毕业');`

 

#### 多行子查询

查询"语文"或"英文"课程的成绩信息:先查询出这两个课程的课程id,拿着课程id去分数表中查询.


`select id from course where name = '语文' or name = '英文';`
`select * from score where course_id in (4,6);`

![在这里插入图片描述](https://img-blog.csdnimg.cn/50e3e7fcbac941eb9493ddd1f5289d6f.png)
整体写起来:`select * from score where course_id in (select id from course where name = '语文' or name = '英文');`


### 合并查询

相当于把两个查询语句的结果合并到一起。使用`union`和`union all`.

> 查询id小于3，或者名字为"英文”的课程


`select * from course where id < 3 union select * from course where name = '英文'`
![在这里插入图片描述](https://img-blog.csdnimg.cn/7db483aec804432aacf8824fd9465795.png)

or只能针对一个表,union可以把多个表的查询结果合并.(要求多个结果列得对应)


union会自动去重,union all不会去重.
