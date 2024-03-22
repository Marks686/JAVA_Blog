


# 1.mysql多表联合查询有哪些方式?


1. **内连接（INNER JOIN）**：返回两个表中满足连接条件的记录。这是一种常用的连接方式，特别是当需要从多个表中获取相关数据时。
2. **左外连接（LEFT JOIN）**：返回左表的所有记录，即使右表中没有匹配的记录。如果右表中没有匹配，则结果是NULL。
3. **右外连接（RIGHT JOIN）**：返回右表的所有记录，即使左表中没有匹配的记录。如果左表中没有匹配，则结果是NULL。
4. **交叉连接（CROSS JOIN）**：返回两个表所有可能的组合记录，通常用于生成测试数据或者在特定情况下使用。
5. **子查询**：一个查询中嵌套另一个查询，可以用于WHERE子句、FROM子句等，提供了强大的查询能力。
6. **自关联查询**：一个表与自身进行连接，通常用于解决具有层次结构或递归关系的数据问题。
7. **联合查询（UNION）**：用于合并两个或多个SELECT语句的结果集，但要求被合并的列数和类型必须相同。默认会去除重复的记录，可以使用UNION ALL保留重复行。
8. **交集查询（INTERSECT）**：用于获取两个查询结果集的交集部分，即两个结果集中都存在的记录。虽然MySQL不支持INTERSECT关键字，但可以通过其他方式实现相同的效果。

总的来说，每种查询方式都有其特定的使用场景，选择合适的查询方式可以有效提高数据处理的效率和准确性。
# 2.什么是内连接、外连接?
## 内连接
内连接也称为等值连接，只返回两个表中键值匹配的行，即只有在两个表中都有匹配的数据时才会返回。


`select column
from table1inner join table2
on table1.column = table2.column`

## 外连接
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/a822a3c16d744f2a919890b9df5aff54.png)


**左外连接**
连接返回左表中所有记录和右表中匹配的记录，如果右表中没有匹配的记录，则返回 NULL 值。

`select column
from table1
left join table2
on table1.column = table2.column`


**右外连接**
连接返回右表中所有记录和左表中匹配的记录，如果左表中没有匹配的记录，则返回 NULL 值。
`select column
from table1
right join table2
on table1.column = table2.column`



**完整外部联接**
完整外部联接返回左右表中所有的记录和左右表中连接字段相等的记录。
`select column
from table1
full join table2
on table1.column = table2.column`



# 3.MySQL求最大用什么函数
在MySQL中，求最大值可以使用**MAX()函数**。

MAX()函数是MySQL中用于返回一组值中的最大值的函数。它不仅适用于数值类型的数据，也适用于字符类型的数据。对于字符类型，比较时会按照字符的ASCII码值大小进行。

以下是一些使用MAX()函数的示例：

- **查找某列的最大值**：如果要找出表中某一列的最大值，可以使用`SELECT MAX(column_name) FROM table_name;`这样的语句。
- **查找不同值的最大值**：如果添加了DISTINCT运算符，即`SELECT MAX(DISTINCT column_name) FROM table_name;`，则MAX()函数会返回不同值中的最大值。
- **获取行中的最大值**：如果要在一行中的多个列之间找到最大值，可以使用GREATEST()函数，例如`SELECT GREATEST(a, b, c) FROM table_name;`来获取a, b, c列中的最大值。

MAX()函数是一个非常实用的函数，它可以在多种情况下使用，帮助用户快速找到所需的最大值。
# 4.列表和元组有什么区别

 - 列表属于可变序列，它的元素可以随时修改或删除，元组是不可变序列，其中元素不可修改，只能整体替换。
 - 列表可以使用append()、extend()、insert()、remove()和pop()等方法实现添加和修改，元组则没有这几个方法。
 - 元组比列表的访问和处理速度快，如果只需要访问不需要修改，建议使用元组。
 - 列表不能作为字典的键，而元组则可以。

# 5.如何判断两个对象相等

> equals()是Object类中定义的方法，用于比较两个对象是否相等。

- == 判断两对象相等，是判断它们的地址是否相等
 - equals 未被重写，也是通过判断地址来判断相等，等同于 ==


默认情况下，"equals()"与"=="的作用相同，比较的是对象的地址值。但是，可以根据具体的类重写该方法，以实现自定义的比较逻辑。



 



注意：
1. 对于基本数据类型，使用 == 进行比较更加直接和高效 
2. 对于引用类型，使用"equals()"进行比较更加准确和灵活，但需要注意重写"equals()"方法，以满足自定义的比较需求。


总结起来，== 比较的是变量的值或引用的地址值，而"equals()"比较的是对象的内容。

# 6.将两个有序数组或链表合并为一个，不改变顺序

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        if(list1 == null){
            return list2;
        } else if(list2 == null){
            return list1;
        } else if(list1.val < list2.val){
            list1.next = mergeTwoLists(list1.next,list2);
            return list1;
        }else{
            list2.next = mergeTwoLists(list1,list2.next);
            return list2;
        }
    }
}
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/64415d781b6549489aa9aea3ee53bbd6.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/090d22b60f4043e29fab55d702ae1f77.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/1a3bc460fbfe44e9a9c203ca9176516b.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/ea2f2010a9df47d2a0be09f8617f741e.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/35a6850ccf5d43738d91d71f043ee556.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/31dd794e4e78499093d920b2087fed1c.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f591f76c0a264b0ea00b09e8955bb978.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/0638afcdf12540f79202f701ffe939d2.png)

# 7.什么是PO模式
PO模式是自动化测试项目开发实践的最佳设计模式之一。

它的主要用途是把一个具体的页面转换成编程语言当中的一个对象，页面特性转化成对象属性，页面操作转换成对象方法。在自动化测试当中，主要用来实现对页面操作和测试。
PO模式的核心思想是通过对界面元素的封装减少冗余代码，同时在后期维护中，若元素定位发生变化，只需要调整页面元素封装的代码，提高测试用例的可维护性、可读性。

 优点:

- 代码可读性高，减少冗余代码；
- 增加用例的可维护性，业务代码和测试代码被分开，降低耦合性维护成本低；
- 增强复用性。
# 8.测试的流程

1. **需求分析**：看需求是否完整，需求是否正确。
2. **测试计划**：确定软件测试由谁测试，什么时候开始测试，什么时候结束测试，测试哪些板块。
3. **测试设计**：写测试用例（手工测试用例，自动化测试用例）
4. **测试开发**：编写测试工具。
5. **测试执行**：执行测试用例。
6. **测试评估**：测试人员产生测试报告。
# 9.怎么进行测试用例设计？

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/523ae8aad7344ca8adfc4fe2b13a5961.png)

**等价类：**
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/f3d0b9ab11054875b2415733d527d1e0.png)



**边界值：**
1. 上点：边界上的点
2. 内点：边界内的点
3. 离点：边界值附近的一个点（**闭区间区间外距离上点最近的点，开区间区间内距离上点最近的点**）
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/06c6d70c2a994648a3012b1c04be3687.png)



# 10.测试用例设计，场景是京东登录界面
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/e98ea6e50ca94393aab4863c74cedc07.png)

