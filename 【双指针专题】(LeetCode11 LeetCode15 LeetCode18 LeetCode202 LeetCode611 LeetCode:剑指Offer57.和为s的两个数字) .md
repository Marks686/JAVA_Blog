# LeetCode202.快乐数

题目链接: [leetcode202.快乐数](https://leetcode.cn/problems/happy-number/)



## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/f872682ae4c44b889f72670124748c53.png)



我们在重复上述过程的时候,题目告诉我们这个数会变成两种情况,第一种是1,第二种是数会无限循环.
![在这里插入图片描述](https://img-blog.csdnimg.cn/dab97be8bdfd46fa89aee4e5bd93507a.png)
## 算法原理

情况一:这个数一直在变最后变成1.情况二:这个数一直变,最后变成一个数进到循环里面.我们现在把这两种情况抽象成一种情况:即第一种情况中最后也进到循环里面了,只不过这个循环的数全是1.第二种情况循环中的数都不是1.也就是说情况一和情况二不论哪种情况,总会变成一个循环.


![在这里插入图片描述](https://img-blog.csdnimg.cn/7f97f06112114fd69c318ffefd3c1423.png)
看到上面的图我们就很容易想到,我们在学习链表的时候遇到的一种题:判断链表是否有环.

在这道题中我们可以把题中的数据抽象成链表,一个数变成一个数,一个接一个就相当于和链表一样被串了起来,所以我们在这题中不需要判断链表是否循环,我们只需要判断环里的值是否为1就可以了.

所以我们这道题就是用快慢双指针的方法.


> 快慢双指针

1. 定义快慢指针.
2. 慢指针每次向后移动一步,快指针每次移动两步.
3. 判断相遇时候的值即可.


双指针仅仅是一种思想,这道题中我们可以把某一个数当成指针.
我们让slow指向2, slow向右移动一位就意味着我们进行了一次操作,即让slow = 4 , 这个操作就相当于修改了指针,让slow指向4位置.
![在这里插入图片描述](https://img-blog.csdnimg.cn/defde87fd9564927a722e99e19a20485.png)
同理我们来看fast, fast刚开始指向2, fast往后移动两步,即进行了两次操作,fast = 16, 也就是修改指针让fast指向16.
![在这里插入图片描述](https://img-blog.csdnimg.cn/15bf685e5d8047ae9b8ce8ef19c4457c.png)
那么有一个很重要的问题:为什么我们的数变换的过程中一定是有环的?我们如何证明?

> 鸽巢原理(抽屉原理):

已知:n个巢,n+1个鸽子.
结论:至少有一个巢穴里面的鸽子数大于1.

我们题目给的int数据最大就是2.1 × 10^9,现在我们给一个数:9999999999,10个9,这个数所有位数的数经过题目叙述的操作变成的数一定是大于int的范围内所有的数经过操作后得到的数.9999999999经过操作后得到数是810,也就是说int范围的数在操作后得到的数总是在区间[1,810]之间. 将[1,810]类比于巢, [1,810]这个区间里面一共有810个数,任意取一个属于int范围内的数x,让这个数变化811次,最后这个数变成了y,经过811次操作必定会出现重复的数,因为x它每次得到的值在[1,810]这个区间里面,假如经过810次操作一次都没有重复,那么,当在进行811次操作的时候它也是出现区间[1,810]中某个值,这个时候它一定是和之前某次出现的值重复了.即证明了数变换的过程中一定是有环的.(只要变换此时超过了810都可以证明上面的问题).

## 编写代码


```java
class Solution {
    public int bitSum(int n)// 返回n这个数每一位上的平方和
    {
        int sum = 0;
        while(n != 0)
        {
            int t = n % 10;
            sum += t * t;
            n /= 10;
        }
        return sum;
    }
    public boolean isHappy(int n) {
        int slow = n,fast = bitSum(n);
        while(slow != fast)
        {
            slow = bitSum(slow);
            fast = bitSum(bitSum(fast));
        }
        return slow == 1;
    }
}
```



# LeetCode11.盛最多水的容器

题目链接:[leetcode11.盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

## 题目解析

![在这里插入图片描述](https://img-blog.csdnimg.cn/eb3225ba40de47ec91ec592f2b3722f6.png)


比如我们选1号线和8号线计算存储最大的水量,7×7=49,继续选择1号线和6号线计算存储最大的水量5×8=40.经过各种测试我们知道最大储水量的两条线就是1号和8号,然后返回存储最大的水量.
## 算法原理

**解法一:暴力枚举(超时)时间复杂度O(N^2)**

两层for循环,先固定最左边的线1,依次去枚举右边的线,然后把所有情况的容积全算出来找一个最大值,当最左边1固定完了后,然后固定最左边的线为8,依次去枚举它右边的线,重复上面的操作,最后一定会得到盛水最多的容积.
![在这里插入图片描述](https://img-blog.csdnimg.cn/ba1d04d2c7f849d4a56991ec4b5849b4.png)


**解法二:利用单调性,使用双指针来解决问题.时间复杂度O(N)**
![在这里插入图片描述](https://img-blog.csdnimg.cn/249deabe8e8641eb92ea2131082aa5fc.png)


任选两个数,研究它俩区间的情况,V(体积) = h(高度) × w(宽度).假如我们选6和4这个小区间,V = 6 × 4 = 12,我们继续用4和2计算,4和5计算发现, 宽度在不断减小,高度的变化有两种情况:
1. 4在向内枚举的时候碰到一个比它小的数,此时高度和宽度都在减小,最终结果也一定在减小.
2. 4在向内枚举的时候碰到一个比它大的数,此时高度不变,宽度在减小,总体积也一定是在减小.



如果我们拿区间两边中小的数来向内枚举会发现容积一直在减小,因此4我们就不用考虑了.所以发现了上面的规律我们直接把区间定为最左边和最右边的数,也就是1和7直接开始计算.

![在这里插入图片描述](https://img-blog.csdnimg.cn/c7a10535422c411da0332a72f91395c0.png)


当我们算出容积V1后就可以大胆的把1去掉,因为1和8,6......依次枚举容积都是小于V1的.然后我们就开始研究8到7这段区间,这段区间的容积是V2,此时就可以把7去掉了,因为7向内枚举所得的容积都是比V2小,反复如此操作...当左右区间两个数相同的时候,去掉任意一个数都行,最后当两个指针相遇的时候我们算出了很多的体积,我们在所有的体积里面求一个max就可以了.


步骤:移动指针,移动完成后算容积,然后继续移动指针继续算容积,重复操作直到两个指针相遇.



## 编写代码

```java
class Solution {
    public int maxArea(int[] height) {
        int left = 0,right = height.length - 1,ret = 0;
        while(left != right)
        {
            int v = Math.min(height[left],height[right]) * (right - left);
            ret = Math.max(ret,v);
            if(height[left] < height[right])
            {
                left++;
            }
            else
            {
                right--;
            }
            
        }
        return ret;
    }
}
```

# LeetCode611.有效三角形的个数

题目链接:[leetcode611.有效三角形的个数](https://leetcode.cn/problems/valid-triangle-number/)


## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/6eec2ee9570c4d20a67a9a806da02fc1.png)
题目意思很简单,就是给我们一个数组,让我们在数组中挑出来三个数,看这三个数能否组成三角形,然后看有几种挑法(选法不一样重复的也是要统计在内的).
![在这里插入图片描述](https://img-blog.csdnimg.cn/d3a8b3024b9f476e95b681b5c93c4687.png)

## 算法原理

> 补充一个数学知识: 给我们三个数,判断是否能够构成三角形.

如果我们知道三个数的大小顺序了,仅需判断较小的两个数之和大于第三个数就行了.


解释:

![在这里插入图片描述](https://img-blog.csdnimg.cn/524c10f072ad47c8a33dedbfda7ef493.png)
通过上面的补充知识,我们就应该要明白一个优化:在判断前先对数组中的数进行排序.


**解法一:暴力枚举**

伪代码:

![在这里插入图片描述](https://img-blog.csdnimg.cn/3c817cc30e8446169ef80d7fb8824486.png)
三层for循环的时间复杂度就是O(N^3)然后看常数级别的时间复杂度.check(i,j,k),里面的判断如果是采用最初始的判断三角形的方法.相当于最内层for循环,check这个语句执行三次,用这种判断策略的话整个算法的时间复杂度就是O(3 * N ^3).若我们先排序,然后仅需判断一次check(i,j,k)就可以了,这种策略我们需要加上排序的时间复杂度(N * logN),采用这种策略整个算法的时间复杂度就是O(N* logN + N ^3),因此优化后对暴力枚举这种方法的时间复杂度是非常大的.


排序完此时我们的数组就已经有序了,通过它的单调性我们可以做更多的优化.


**解法二:利用单调性,使用双指针算法**

1. 先固定最大的数
2. 在最大的数左区间内,使用双指针算法,快速统计出符合要求的三元组的个数.

整体时间复杂度是O(N^2)
![在这里插入图片描述](https://img-blog.csdnimg.cn/2ab06a5d3b9843c5b8bc0676eb472086.png)





## 编写代码


```java
class Solution {
    public int triangleNumber(int[] nums) {
        //1.优化:排序
        Arrays.sort(nums);

        //2.利用双指针解决问题
        int ret = 0, n = nums.length;
        //先固定最大的数
        for(int i = n - 1; i >= 2; i--)
        {
            //利用双指针快速统计出符合要求的三元组的个数
            int left = 0, right = i - 1;
            while(left < right)
            {
                if(nums[left] + nums[right] > nums[i])
                {
                    ret += right - left;
                    right--;
                }
                else
                {
                    left++;
                }
            }
        }
        return ret;
    }
}
```

# LeetCode:剑指Offer57.和为s的两个数字
题目链接:[剑指Offer57.和为s的两个数字](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/)
## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/7d40c13fbee4459da2342d0d85418519.png)
递增排序的数组说明这个数组里面是有序的.返回数是要求我们返回数,返回顺序是无所谓的.

## 算法原理


**解法一:暴力枚举(时间复杂度O(N^2))**

固定一个数,依次和后面的数进行枚举即可,然后固定下一个数继续枚举,依次固定完所有的数,或者找到了我们需要的两个数.

![在这里插入图片描述](https://img-blog.csdnimg.cn/0685404dc2a748758a822f811b6f4fcf.png)

暴力解法并没有利用到我们数组是有序的特性,因此代码是可以继续优化的.

**解法二:利用单调性,使用双指针算法解决问题(时间复杂度(O(N))**



![在这里插入图片描述](https://img-blog.csdnimg.cn/b9bd447e8160409c93561282056d71fd.png)




## 编写代码

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while(left < right)
        {
            int sum = nums[left] + nums[right];
            if(sum > target)
            {
                right--;
            }
            else if(sum < target)
            {
                left++;
            }
            else
            {
                return new int[] {nums[left],nums[right]};
            }
        }
        //编译器机制,此处我们再随便给个返回值
        return new int[]{0};
    }
}
```


# LeetCode15.三数之和
题目链接：[leetcode15.三数之和](https://leetcode.cn/problems/3sum/)
## 题目解析

![在这里插入图片描述](https://img-blog.csdnimg.cn/b6a1458b3c04406eafe09802d41bef29.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/34d4c2433a474bb7b7530bf0951a5396.png)

三元组不重复就是三元组里面元素对应不相同.

## 算法原理
这道题难就难在去重操作.


解法一:排序+暴力枚举+利用set去重(时间复杂度O(n^3))

![在这里插入图片描述](https://img-blog.csdnimg.cn/9428f4aed56c487fb66157a7d3fbb186.png)

解法二:排序+双指针(时间复杂度O(n^2))
1. 先排序;
2. 固定一个数a;
3. 在该数后面的区间内,利用"双指针算法"快速找到两个的和等于-a即可.
![在这里插入图片描述](https://img-blog.csdnimg.cn/4f1b678fec2d4e1fbbb37f9909b652d6.png)
当我们固定的数a为正数的时候,后面的区间就找不到两个数之和等于-a.所以我们固定数a <= 0.



处理细节问题:
1. 去重:
 (1)找到一种结果之后,left 和 right指针要跳过重复重复元素.
 (2)当使用完一次双指针算法之后,i也需要跳过重复元素.
 (3)移动指针的时候一定要避免越界.
2. 不漏:
 找到一种结果之后,不要停,缩小区间,就行寻找.



## 编写代码

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) 
    {
        List<List<Integer>> ret = new ArrayList<>();
        //1.排序
        Arrays.sort(nums);
        //3.利用双指针解决问题
        int n = nums.length;
        for(int i = 0; i < n;) // 固定数a
        {
            if(nums[i] > 0) break; //优化
            int left = i + 1,right = n - 1,target = -nums[i];
            while(left < right)
            {
                int sum = nums[left] + nums[right];
                if(sum > target) right--;
                else if(sum < target) left++;
                else
                {
                    // nums[i]  nums[left]  nums[right]
                    ret.add(new ArrayList<Integer>(Arrays.asList(nums[i],nums[left],nums[right])));
                    left++;right--;//缩小区间,继续查找
                    //去重: left right
                    while(left < right && nums[left] == nums[left - 1])
                    {
                        left++;
                    }
                     while(left < right && nums[right] == nums[right + 1])
                    {
                        right--;
                    }
                }
            }
            //去重: i  
            i++;
            while(i < n && nums[i] == nums[i - 1])
            {
                i++;
            }
        }
        return ret;
    }
}
```



# LeetCode18.四数之和
## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/6b31a248a12c4aba89c7d02a39ec2e0f.png)

## 算法原理
解法一:排序 + 暴力枚举 + 利用set去重


解法二:排序 + 双指针(时间复杂度O(N^3))
1. 依次固定一个数a;
2. 在a后面的区间内,利用"三数之和"找到三个数,使这三个数的和等于target - a即可.
![在这里插入图片描述](https://img-blog.csdnimg.cn/417d4c278e49468197d75ff4ed65c9c2.png)

**处理细节问题:**

1. 不重:
![在这里插入图片描述](https://img-blog.csdnimg.cn/7c5f8bc3811e436a9bab66bc00e4267f.png)

2. 不漏:
在寻找两个数的和等于target-a-b的时候,当我们找到一个结果的时候不要停,继续缩小区间.

## 编写代码

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> ret = new ArrayList<>();
        //1.排序
        Arrays.sort(nums);

        //2.利用双指针解决问题
        int n = nums.length;
        for(int i = 0; i < n;)//固定数a
        {
            //三数之和
            for(int j = i + 1; j < n;) //固定数b
            {
                //双指针
                int left = j + 1,right = n - 1;
                long aim = (long)target - nums[i] - nums[j];
                while(left < right)
                {
                    int sum = nums[left] + nums[right];
                    if(sum > aim) right--;
                    else if(sum < aim) left++;
                    else
                    {
                        ret.add(Arrays.asList(nums[i],nums[j],nums[left++],nums[right--]));
                        //去重一
                        while(left < right && nums[left] == nums[left - 1]) left++;
                        while(left < right && nums[right] == nums[right + 1]) right--;
                    }
                }
                //去重二
                j++;
                while(j < n && nums[j] == nums[j-1]) j++;
            }
            //去重三
            i++;
            while(i < n && nums[i] == nums[i - 1]) i++;
        }
        return ret;
    }
}
```
