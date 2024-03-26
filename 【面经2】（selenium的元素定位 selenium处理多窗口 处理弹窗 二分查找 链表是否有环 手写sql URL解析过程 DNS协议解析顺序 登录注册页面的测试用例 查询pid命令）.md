# 1.selenium的元素定位有哪些

- id
- name
- class name
- link text
- partial link text
- tag name
- xpath
- css selector


Selenium提供了多种方法来定位页面上的元素，具体如下：

1. **通过ID定位**：使用`find_element_by_id()`或`find_element(By.ID, value)`来定位拥有特定ID属性的元素。
2. **通过name属性定位**：使用`find_element_by_name()`或`find_element(By.NAME, value)`来定位拥有特定name属性的元素。
3. **通过class属性定位**：使用`find_element_by_class_name()`或`find_element(By.CLASS_NAME, value)`来定位拥有特定class属性的元素。
4. **通过tag name定位**：使用`find_element_by_tag_name()`或`find_element(By.TAG_NAME, value)`来定位特定类型的HTML标签元素。
5. **通过link text定位**：使用`find_element_by_link_text()`或`find_element(By.LINK_TEXT, value)`来定位链接文本匹配的元素。
6. **通过partial link text定位**：使用`find_element_by_partial_link_text()`或`find_element(By.PARTIAL_LINK_TEXT, value)`来定位部分链接文本匹配的元素。
7. **通过CSS选择器定位**：使用`find_element_by_css_selector()`或`find_element(By.CSS_SELECTOR, value)`来定位符合特定CSS选择器规则的元素。
8. **通过XPath定位**：使用`find_element_by_xpath()`或`find_element(By.XPATH, value)`来定位符合特定XPath表达式的元素。

总的来说，在使用这些方法时，需要确保导入了正确的模块和方法。例如，新版的Selenium推荐使用`find_element(By.xxx, value)`的形式，并需要从`selenium.webdriver.common.by`导入`By`类。这些定位方法在不同的测试场景和页面结构中各有优势，选择合适的方法可以提高测试的效率和准确性。
# 2.用selenium怎么处理多窗口？怎么处理弹窗？



1. **处理多窗口**

在Java中，可以使用`driver.getWindowHandles()`方法获取所有当前打开的窗口句柄列表。然后使用`driver.switchTo().window(handle)`方法切换到指定的窗口进行操作。例如，执行JavaScript脚本`window.open()`打开一个新窗口后，可以利用`driver.getWindowHandle()`获取当前窗口的句柄，并通过遍历句柄列表来切换不同的窗口。

示例代码如下：

```java
// 获取所有窗口句柄
Set<String> handles = driver.getWindowHandles();
for (String handle : handles) {
    // 切换到指定窗口
    driver.switchTo().window(handle);
    // 对窗口进行操作
}
```

2. **处理弹窗**

在Java中，可以使用`Alert`类来处理JavaScript中的Alert、Confirm和Prompt弹窗。可以使用`driver.switchTo().alert()`方法获取当前的Alert弹窗对象，并对其进行操作。对于Alert弹窗，可以使用`accept()`方法来点击确定按钮或`dismiss()`方法来点击取消按钮。而对于Prompt弹窗，除了上述两个方法外，还可以用`sendKeys()`来输入文本内容。需要注意的是，无法使用常规的元素定位方式来直接定位弹窗中的元素，必须通过切换到弹窗的操作来处理它们。

示例代码如下：

```java
// 获取当前Alert弹窗对象
Alert alert = driver.switchTo().alert();
// 点击确定按钮
alert.accept();
// 点击取消按钮
alert.dismiss();
// 输入文本内容
alert.sendKeys("text");
```

综上所述，在使用Java中的Selenium进行自动化测试时，了解如何处理多窗口和弹窗是非常必要的。这可以帮助确保测试能够顺利地在不同场景下运行，提高测试的效率和准确性。



# 3.算法：二分查找


题目链接: [leetcode704.二分查找](https://leetcode.cn/problems/binary-search/description/)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/37f789bd8f344686acba5e5ea78b98f4.png)





在一个数组中找一个点,用这个点和target作比较后划分出两个区域,根据规律我们可以选择性的去舍去一个区域,然后在另一个区域去寻找.,所以题目中,有"二段性"就可以用二分查找算法.

下面的点都有可能被选而和target值作比较,那我们到底如何选择呢?其实本质上,我们只要找到的点可以把区间分成两部分就可以了.
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/80c15d9db1d042b783e26b0611a399fc.png)


我们有非常多的方案来选择划分点,但其实我们还是要选择中间的那个点来划分,这就涉及到我们概率学中的问题:求数学期望.在上面众多方案中,我们选择中间的那个点来划分时间复杂度是最好的.

朴素版本的二分查找:
![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/5fed0f2b7af64d0e8639e5d92d905d91.png)

> 细节问题:

1. 循环结束的条件:left > right
2. 时间复杂度

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/d6d1d3be980a421f99762dc731a07730.png)

```java
class Solution {
    public int search(int[] nums, int target) {
        int left = 0,right = nums.length - 1;
        while(left <= right){
            int mid = left + (right - left)/2;
            if(nums[mid] > target){
                right = mid - 1;
            }else if(nums[mid] < target){
                left = mid + 1;
            }else{
                return mid;
            }
        }
        return -1;
    }
}
```

# 4.算法：链表是否有环
题目链接: [leetcode141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/description/)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/3ec4bb3ef71048449468262d959c7e5c.png)

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        Set<ListNode> seen = new HashSet<ListNode>();
        while (head != null) {
            if (!seen.add(head)) {
                return true;
            }
            head = head.next;
        }
        return false;
    }
}
```

# 5.手写sql



| name| course| grade|
|--|--|--|
|zhangsan  |数学 |  80  |
| zhangsan  | 语文|  90  |
|lisi   |语文|   80 |
|lisi   |数学|   100 |


查询学生中总分大于300的学生姓名

`SELECT name, SUM(grade) as total_grade
able_name
GROUP BY name
HAVING total_grade > 300;
`

1. `SELECT name, SUM(grade) as total_grade`：选择"name"列和"grade"列的总和，并将总和命名为"total_grade"。
2. `FROM table_name`：指定要查询的表名为"table_name"。
3. `GROUP BY name`：按照"name"列进行分组。
4. `HAVING total_grade > 300`：筛选出"total_grade"大于300的记录。

# 6.URL解析过程

URL解析过程涉及多个步骤，主要包括**用户输入URL、DNS域名解析、建立TCP连接、发送HTTP请求、服务器处理请求以及浏览器渲染等**。具体如下：

1. **用户输入URL**：用户在浏览器地址栏输入想要访问的网址。
2. **DNS域名解析**：浏览器首先检查域名对应的IP地址是否已经缓存，如果没有或缓存过期，则发起DNS查询将域名转换为对应的IP地址。
3. **建立TCP连接**：使用HTTP或HTTPS协议与服务器建立TCP连接，对于HTTPS还需要进行TLS/SSL握手来确保安全通信。
4. **发送HTTP请求**：浏览器向服务器发送一个HTTP请求报文，该报文包含了请求的资源路径、请求方法（如GET、POST等）、头部信息等。
5. **服务器处理请求**：服务器接收到请求后，根据资源路径和请求类型进行处理，可能包括动态内容生成、数据库检索或返回静态资源等操作。
6. **服务器响应**：服务器处理完成后，返回HTTP响应报文给客户端，包含状态码、响应头和响应体。
7. **浏览器渲染**：浏览器接收到响应后，开始解析HTML、CSS和JavaScript内容，构建DOM树和CSSOM树，并通过JavaScript API操作这些树，最终渲染出网页给用户查看。

总之，在整个过程中，还涉及到一些其他的细节，比如HTTP重定向、缓存策略、异步加载等，这些都是为了提高网页加载效率和用户体验。
# 7.DNS协议里解析顺序

DNS协议中的解析顺序涉及**多个步骤**，主要目的是将用户输入的域名转换为对应的IP地址。以下是该过程的关键步骤：

1. **检查浏览器缓存**：当用户在浏览器中输入一个域名时，本地电脑首先会检查浏览器缓存中是否已有这个域名对应的IP地址。如果存在，则直接使用该IP地址，解析过程结束。
2. **系统缓存查询**：如果浏览器缓存中没有找到相应的记录，系统会检查操作系统的hosts文件，看是否有手动添加的域名和IP地址的映射关系。
3. **本地DNS服务器查询**：如果上述步骤仍未获得结果，请求将被发送到配置在本地计算机上的DNS服务器（通常由ISP提供），这台服务器会试图解析该域名。
4. **递归查询**：如果本地DNS服务器没有所需的信息，它将进行递归查询，首先查询根域名服务器，然后是顶级域名服务器，接着是权威名称服务器。每个步骤都可能涉及查询不同的DNS服务器。
5. **返回结果**：一旦找到正确的IP地址，本地DNS服务器将把这个结果返回给请求的计算机。计算机随后将这个结果保存在本地DNS缓存中以供后续使用，并将IP地址传递给应用程序（如网页浏览器）。
6. **TTL的影响**：DNS解析结果有一个时间生存期（Time To Live, TTL），它决定了数据可以存储在缓存中多长时间。较短的TTL意味着缓存的数据会更快过期，从而可能导致更频繁的DNS查询。

总的来说，这个过程确保了全球互联网上无数的域名能够快速、准确地被解析成IP地址，从而使用户可以访问网站。
# 8.pc端用户名密码登录注册页面的测试用例


1. 测试用例名称：正确的用户名和密码登录
   输入：用户名 - "testuser"，密码 - "testpassword"
   预期结果：成功登录并跳转到主页

2. 测试用例名称：错误的用户名登录
   输入：用户名 - "wronguser"，密码 - "testpassword"
   预期结果：显示错误消息，提示用户名或密码错误

3. 测试用例名称：错误的密码登录
   输入：用户名 - "testuser"，密码 - "wrongpassword"
   预期结果：显示错误消息，提示用户名或密码错误

4. 测试用例名称：空的用户名登录
   输入：用户名 - ""，密码 - "testpassword"
   预期结果：显示错误消息，提示用户名不能为空

5. 测试用例名称：空的密码登录
   输入：用户名 - "testuser"，密码 - ""
   预期结果：显示错误消息，提示密码不能为空

6. 测试用例名称：注册新用户
   输入：用户名 - "newuser"，密码 - "newpassword"
   预期结果：成功注册新用户并跳转到登录页面

7. 测试用例名称：已存在的用户名注册
   输入：用户名 - "testuser"，密码 - "newpassword"
   预期结果：显示错误消息，提示用户名已存在

8. 测试用例名称：短密码注册
   输入：用户名 - "shortpass"，密码 - "123"
   预期结果：显示错误消息，提示密码长度不足

9. 测试用例名称：长密码注册
   输入：用户名 - "longpass"，密码 - "a" * 21
   预期结果：显示错误消息，提示密码长度过长

10. 测试用例名称：特殊字符在用户名中注册
    输入：用户名 - "spec!al"，密码 - "testpassword"
    预期结果：显示错误消息，提示用户名包含非法字符

11. 测试用例名称：特殊字符在密码中注册
    输入：用户名 - "testuser"，密码 - "spec!al"
    预期结果：显示错误消息，提示密码包含非法字符

12. 测试用例名称：数字和字母混合的用户名登录
    输入：用户名 - "test123"，密码 - "testpassword"
    预期结果：成功登录并跳转到主页

13. 测试用例名称：数字和字母混合的密码登录
    输入：用户名 - "testuser"，密码 - "test123"
    预期结果：成功登录并跳转到主页

14. 测试用例名称：大小写混合的用户名登录
    输入：用户名 - "TestUser"，密码 - "testpassword"
    预期结果：成功登录并跳转到主页

15. 测试用例名称：大小写混合的密码登录
    输入：用户名 - "testuser"，密码 - "TestPassword"
    预期结果：成功登录并跳转到主页

# 9.如何查询一个进程的pid

在Linux中，可以使用`ps`命令结合`grep`命令来查询一个进程的pid。具体操作如下：

1. 打开终端。
2. 输入以下命令，将`process_name`替换为你要查询的进程名称：

```bash
ps aux | grep process_name
```

这个命令会显示与`process_name`相关的所有进程信息，包括pid、用户、CPU占用率等。从输出结果中找到你想要查询的进程对应的pid即可。




# 10.你还了解哪些命令



1. 使用`ps`命令：`ps -ef | grep process_name`，其中`process_name`是要查询的进程名称。
2. 使用`pgrep`命令：`pgrep process_name`，其中`process_name`是要查询的进程名称。
3. 使用`pidof`命令：`pidof process_name`，其中`process_name`是要查询的进程名称。
4. 使用`/proc`文件系统：`ls /proc | grep [0-9]`，这个命令会列出所有正在运行的进程的pid。
5. 使用`top`或`htop`命令：这些命令可以实时显示系统中正在运行的进程及其相关信息，包括pid。



`ls`	查看当前文件夹下的内容
`pwd`	查看当前所在文件夹
`cd[目录名]`切换文件夹
`touch[文件名]`   如果文件不存在，新建文件
`mkdir[目录名]`  创建目录
`rm[文件名]`	删除指定文件
`clear`	清屏
