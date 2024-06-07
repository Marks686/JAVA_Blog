 <a name="MLfb2"></a>
# 一、数据定义语言
<a name="FSbkJ"></a>
## 1.1  操作库：
```sql
创建库
show databases;create database db1;

创建库是否存在，不存在则创建
create database if not exists db1;

查看所有数据库
show databases;

查看某个数据库的定义信息 
show create database db1; 

修改数据库字符信息
alter database db1 character set utf8; 

删除数据库
drop database db1; 
```
<a name="U1Mv2"></a>
## 1.2  操作表：
```sql
创建表
create table student(
    id int,
    name varchar(32),
    age int ,
    score double(4,1),
    birthday date,
    insert_time timestamp
);
 
查看表结构
desc 表名;

查看创建表的SQL语句
show create table 表名;

修改表名
alter table 表名 rename to 新的表名;

添加一列
alter table 表名 add 列名 数据类型;

删除列
alter table 表名 drop 列名;

删除表
drop table 表名;
drop table  if exists 表名 ;
```
<a name="T7Iix"></a>
# 二、数据操作语言
<a name="tnWR6"></a>
## 2.1  增加 insert into
```sql
写全所有列名
insert into 表名(列名1,列名2,...列名n) values(值1,值2,...值n);

不写列名（所有列全部添加）
insert into 表名 values(值1,值2,...值n);

插入部分数据
insert into 表名(列名1,列名2) values(值1,值2);
```
<a name="Hs74W"></a>
## 2.2  删除 delete
```sql
删除表中数据
delete from 表名 where 列名  = 值;

删除表中所有数据
delete from 表名;

删除表中所有数据（高效 先删除表，然后再创建一张一样的表。）
truncate table 表名;
```
<a name="imSs8"></a>
## 2.3  修改update
```sql
不带条件的修改(会修改所有行)
update 表名 set 列名 = 值;

带条件的修改
update 表名 set 列名 = 值 where 列名=值;
```
<a name="NGw90"></a>
# 三、数据查询语言
<a name="wxMCp"></a>
## 3.1  基础关键字
**BETWEEN...AND （在什么之间）、IN( 集合)：**
```sql
查询年龄大于等于20 小于等于30				
SELECT * FROM student WHERE age >= 20 &&  age <=30;
SELECT * FROM student WHERE age >= 20 AND  age <=30;
SELECT * FROM student WHERE age BETWEEN 20 AND 30;
				
查询年龄22岁，18岁，25岁的信息
SELECT * FROM student WHERE age = 22 OR age = 18 OR age = 25
SELECT * FROM student WHERE age IN (22,18,25);
```
**is not null(不为null值) 、like（模糊查询）、distinct（去除重复值）：**
```sql
查询英语成绩不为null
SELECT * FROM student WHERE english  IS NOT NULL;
	
 _:单个任意字符
 %：多个任意字符
查询姓马的有哪些？ like
SELECT * FROM student WHERE NAME LIKE '马%';

查询姓名第二个字是化的人			
SELECT * FROM student WHERE NAME LIKE "_化%";	

查询姓名是3个字的人
SELECT * FROM student WHERE NAME LIKE '___';		

查询姓名中包含德的人
SELECT * FROM student WHERE NAME LIKE '%德%';
 
关键词 DISTINCT 用于返回唯一不同的值。
语法：SELECT DISTINCT 列名称 FROM 表名称
SELECT DISTINCT NAME FROM  student ;
```
<a name="wGJVX"></a>
## 3.2  排序查询 order by
```sql
语法：
order by 子句

例子
SELECT * FROM person ORDER BY math; --默认升序
SELECT * FROM person ORDER BY math desc; --降序

如果有多个排序条件，则当前边的条件值一样时，才会判断第二条件。
```
<a name="vDdhC"></a>
## 3.3  聚合函数：将一列数据作为一个整体进行纵向计算
```sql
1.count：计算个数

2.max：计算最大值

3.min：计算最小值

4.sum：计算和

5.avg：计算平均数
```
<a name="OD8mm"></a>
## 3.4  分组查询 group by
```sql
按照性别分组。分别查询男、女同学的平均分
SELECT sex , AVG(math) FROM student GROUP BY sex;
 
按照性别分组。分别查询男、女同学的平均分,人数
 SELECT sex , AVG(math),COUNT(id) FROM student GROUP BY sex;
 
按照性别分组。分别查询男、女同学的平均分,人数 要求：分数低于70分的人，不参与分组
SELECT sex , AVG(math),COUNT(id) FROM student WHERE math > 70 GROUP BY sex;
 
按照性别分组。分别查询男、女同学的平均分,人数 要求：分数低于70分的人，不参与分组,分组之后。人数要大于2个人
SELECT sex , AVG(math),COUNT(id) FROM student WHERE math > 70 GROUP BY sex HAVING COUNT(id) > 2;
SELECT sex , AVG(math),COUNT(id) 人数 FROM student WHERE math > 70 GROUP BY sex HAVING 人数 > 2;
```
<a name="HqKX0"></a>
## 3.5  分页查询
```sql
1. 语法：limit 开始的索引,每页查询的条数;
2. 公式：开始的索引 = （当前的页码 - 1） * 每页显示的条数

每页显示3条记录 
SELECT * FROM student LIMIT 0,3; -- 第1页
 
SELECT * FROM student LIMIT 3,3; -- 第2页
 
SELECT * FROM student LIMIT 6,3; -- 第3页
```
<a name="BxkOI"></a>
## 3.6  内连接查询
<a name="ABNlQ"></a>
### 3.6.1  隐式内连接：使用where条件消除无用数据
```sql
查询员工表的名称，性别。部门表的名称
SELECT emp.name,emp.gender,dept.name FROM emp,dept WHERE emp.`dept_id` = dept.`id`;
 
SELECT 
    t1.name, -- 员工表的姓名
    t1.gender,-- 员工表的性别
    t2.name -- 部门表的名称
FROM
    emp t1,
    dept t2
WHERE 
    t1.`dept_id` = t2.`id`;
```
<a name="en5sc"></a>
### 3.6.2  显式内连接
```sql
语法： 
select 字段列表 from 表名1 [inner] join 表名2 on 条件

例如：
SELECT * FROM emp INNER JOIN dept ON emp.`dept_id` = dept.`id`;   
SELECT * FROM emp JOIN dept ON emp.`dept_id` = dept.`id`; 
```
<a name="n0k7S"></a>
## 3.7  外连接查询
<a name="m5Hk6"></a>
### 3.7.1  左外连接 -- 查询的是左表所有数据以及其交集部分
```sql
语法：select 字段列表 from 表1 left [outer] join 表2 on 条件；

例子：
查询所有员工信息，如果员工有部门，则查询部门名称，没有部门，则不显示部门名称
SELECT  t1.*,t2.`name` FROM emp t1 LEFT JOIN dept t2 ON t1.`dept_id` = t2.`id`;
```
<a name="JhaVu"></a>
### 3.7.2  右外连接  -- 查询的是右表所有数据以及其交集部分
```sql
语法：
select 字段列表 from 表1 right [outer] join 表2 on 条件；

例子：
SELECT  * FROM dept t2 RIGHT JOIN emp t1 ON t1.`dept_id` = t2.`id`;
```
<a name="AgkAh"></a>
## 3.8  子查询：查询中嵌套查询
```sql
查询工资最高的员工信息
1 查询最高的工资是多少（9000）
SELECT MAX(salary) FROM emp;
 
2 查询员工信息，并且工资等于9000的
SELECT * FROM emp WHERE emp.`salary` = 9000;
 
一条sql就完成这个操作。这就是子查询
SELECT * FROM emp WHERE emp.`salary` = (SELECT MAX(salary) FROM emp);
```
<a name="QvMFY"></a>
### 3.8.1  子查询的结果是单行单列的：**子查询可以作为条件，使用运算符去判断。 运算符： > >= < <= =**
```sql
查询员工工资小于平均工资的人
SELECT * FROM emp WHERE emp.salary < (SELECT AVG(salary) FROM emp);
```
<a name="J6tbe"></a>
### 3.8.2  子查询的结果是多行单列的：子查询可以作为条件，使用**运算符in**来判断
```sql
查询'财务部'和'市场部'所有的员工信息
SELECT id FROM dept WHERE NAME = '财务部' OR NAME = '市场部';
SELECT * FROM emp WHERE dept_id = 3 OR dept_id = 2;
 
子查询
SELECT * FROM emp WHERE dept_id IN (SELECT id FROM dept WHERE NAME = '财务部' OR NAME = '市场部');
```
<a name="z0X8i"></a>
### 3.8.3  子查询的结果是多行多列的：子查询可以作为一张**虚拟表**参与查询
```sql
查询员工入职日期是2011-11-11日之后的员工信息和部门信息
子查询
SELECT * FROM dept t1 ,(SELECT * FROM emp WHERE emp.`join_date` > '2011-11-11') t2 WHERE t1.id = t2.dept_id;
 
普通内连接
SELECT * FROM emp t1,dept t2 WHERE t1.`dept_id` = t2.`id` AND t1.`join_date` >  '2011-11-11'
```
