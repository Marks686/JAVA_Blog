








# LeetCode209.长度最小的子数组

题目链接: [leetcode209.长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

## 题目解析

![在这里插入图片描述](https://img-blog.csdnimg.cn/47bafb7d472541149f1fb5fc49c7a888.png)

## 算法原理


> 解法一:暴力枚举出所有的子数组的和 O(n^3)






> 解法二:利用单调性,使用同向双指针来优化. O(n)



同向双指针即是left和right都向同一个方向移动.同向双指针又称为滑动窗口.

优化: 先固定左边的left然后让right指向left,定义一个sum,让right向右移动,用sum存区间之和,当sum >= 7时,然后left++,right重新移到left的位置,再将right移动进行求和,我们发现此步骤很繁琐,所以我们干脆不移动right,将它放在原位置,光让left++,再求区间大小.
 我们发现了两个规律:
 1. 当right移动到合适位置的时候,后面的情况就不用再枚举了.
 2. 当left向右移动一步之后right是不回退的.


![在这里插入图片描述](https://img-blog.csdnimg.cn/fbff7ceb90944f8788598954df7df6c0.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/c006228ce4174be1a0d6be0965016c70.png)
用法:
1. left = 0,right = 0.
2. 进窗口
3. 判断 (出窗口)


这个方法利用了单调性规避了很多没有必要的枚举行为.


## 编写代码


```java
class Solution {
    public int minSubArrayLen(int target, int[] nums)
    {
        int n = nums.length,sum = 0,len = Integer.MAX_VALUE;
        for(int left=0,right=0; right < n; right++)
        {
            sum += nums[right];//进窗口
            while(sum >= target)//判断
            {
                len = Math.min(len,right - left + 1);//更新结果
                sum -= nums[left++];//出窗口
            }
        }
        return len == Integer.MAX_VALUE?0 : len;
    }
}
```




# LeetCode3.无重复字符的最长子串

题目链接: [leetcode3.无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/description/)

## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/e73d87f56f884a94a280a14068aa4b78.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/631f3f96c5e848ad881e00a88249475d.png)


## 算法原理

> 解法一: 暴力枚举 + 哈希表(判断字符是否重复出现) O(n^2)



> 解法二:利用规律,使用"滑动窗口"来解决问题.

![在这里插入图片描述](https://img-blog.csdnimg.cn/7831d87c2d9c4f299e79ab859d080c44.png)


1. left=0,right=0
2. 进窗口:让字符进入哈希表
3. 判断, 窗口内出现重复字符进行出窗口(从哈希表中删除该字符)




 


## 编写代码

```java
class Solution {
    public int lengthOfLongestSubstring(String ss) {
        char[] s = ss.toCharArray();
        int[] hash = new int[128]; //用数组模拟哈希表
        int left = 0,right = 0,n = ss.length();
        int ret = 0;
        while(right < n)
        {
            hash[s[right]]++; // 进入窗口
            while(hash[s[right]] > 1) // 判断
            {
                hash[s[left++]]--; // 出窗口
            }
            ret = Math.max(ret,right - left + 1);//更新结果
            right++;
        }
        return ret;
    }
}
```
















# LeetCode1004.最大连续1的个数 III

题目链接: [leetcode1004.最大连续1的个数 III](https://leetcode.cn/problems/max-consecutive-ones-iii/description/)

## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/84fba7ffa3034cf59e186589f0b5d272.png)最多k个0,说明可以翻转的个数是小于等于k


![在这里插入图片描述](https://img-blog.csdnimg.cn/95918ab54c18460ab013559e05a5e0e7.png)



## 算法原理

在这个数组中,找到一个连续的区域,只要让这个区域满足0的个数不要超过k次就行.
将原始的问题转化为:找出最长的子数组,0的个数不超过k个.



>解法一:暴力枚举 + zero 计数器



>解法二:滑动窗口

![在这里插入图片描述](https://img-blog.csdnimg.cn/ceec742422c3412098acda0978e461b5.png)


1. left=0,right=0
2. 进窗口,right指的如果是1,无视,如果是0,计数器+1
3. 判断当zero > k时,让left一直右移,直到窗口合法为止.出窗口和进窗口是一样的逻辑.left指的如果是1,无视,如果是0,计数器-1.


## 编写代码

```java
class Solution {
    public int longestOnes(int[] nums, int k) {
        int ret = 0; 
        for(int left = 0,right = 0, zero = 0; right < nums.length; right++)
        {
            if(nums[right] == 0) zero++;//进窗口
            while(zero > k) //判断
            {
                if(nums[left++] == 0) zero--; //出窗口
            }
            ret = Math.max(ret,right - left + 1);
        }
        return ret;
    }
}
```




























# LeetCode1658.将 x 减到 0 的最小操作数

题目链接: [leetcode1658.将 x 减到 0 的最小操作数](https://leetcode.cn/problems/minimum-operations-to-reduce-x-to-zero/description/)

## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/3f67c1b9cfb4418db7d42001fbd75961.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/f90a6b504e2e4c0bb4e369a1a69e0297.png)



## 算法原理

正难则反.
![在这里插入图片描述](https://img-blog.csdnimg.cn/dedbe39746074882ae011f87db14ea8a.png)
问题转化:找出最长的子数组的长度(len),所有的元素的和正好等于sum - x(target)

>解法:滑动窗口



![在这里插入图片描述](https://img-blog.csdnimg.cn/2ac4c40dd9564aa7969d3667a2dc5aa9.png)

1. left=0,right=0
2. 进窗口 tmp+= nums[right]
3. 判断  tmp > target ,出窗口 tmp-= nums[left],更新结果 sum == target.

## 编写代码

```java
class Solution {
    public int minOperations(int[] nums, int x) {
        int sum = 0;
        for(int a : nums) sum += a;
        int target = sum - x;
        //处理细节
        if(target < 0) return -1;

        int ret = -1;
        for(int left = 0, right = 0, tmp = 0; right < nums.length; right++)
        {
            tmp += nums[right]; // 进窗口
            while(tmp > target) // 判断
                tmp -= nums[left++]; // 出窗口
            if(tmp == target)
                ret = Math.max(ret,right - left + 1);// 更新结果
        }
        if(ret == -1) return ret;
        else return nums.length - ret;
    }
}
```


















# LeetCode904.水果成篮

题目链接: [leetcode904.水果成篮](https://leetcode.cn/problems/fruit-into-baskets/description/)

## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/90d45829e1f14cbebdc75d35e61590fa.png)

将问题转化:找出一个最长的子数组的长度,子数组里面不超过两种类型的水果.

## 算法原理


> 解法一: 暴力枚举 + 哈希表

![在这里插入图片描述](https://img-blog.csdnimg.cn/640ba161a13249cca4fd7a34fbee7a7b.png)



> 解法二:滑动窗口



![在这里插入图片描述](https://img-blog.csdnimg.cn/804962f35f7c45fd8e9ae880156157d6.png)


1. left=0,right=0
2. 进窗口,把right所指向的元素放进哈希表里.hash[f[right]]++
3. 判断 如果hash.length >  2就出窗口,出窗口(hash[f[left++]]--)

![在这里插入图片描述](https://img-blog.csdnimg.cn/33c08652bca3476fa1d7fc6415927ad3.png)

## 编写代码



```java
class Solution {
    public int totalFruit(int[] f) {
        Map<Integer,Integer> hash = new HashMap<Integer,Integer>();//统计窗口水果种类
            int ret = 0;
        for(int left = 0,right = 0; right < f.length; right++)
        {
            int in = f[right];
            hash.put(in,hash.getOrDefault(in,0) + 1);//进窗口
            while(hash.size() > 2)
            {
                int out = f[left];
                hash.put(out,hash.get(out) - 1);//出窗口
                if(hash.get(out) == 0)
                {
                    hash.remove(out);
                }
                left++;
            }
            // 更新结果
            ret = Math.max(ret,right - left + 1);
        }
        return ret;
    }
}
```

```java
class Solution {
    public int totalFruit(int[] f) {
        int n = f.length;
        int[] hash = new int[n +1]; 
        int ret = 0;
        for(int left = 0,right = 0,kinds = 0; right < n; right++)
        {
            int in = f[right];
            if(hash[in] == 0)
            {
                kinds++; //维护水果种类
            } 
            hash[in]++;//进窗口
            while(kinds > 2)
            {
                int out = f[left];
                hash[out]--;//出窗口
                if(hash[out] == 0)
                {
                    kinds--;
                }
                left++;
            }
            // 更新结果
            ret = Math.max(ret,right - left + 1);
        }
        return ret;
    }
}
```











# LeetCode438.找到字符串中所有字母异位词

题目链接: [leetcode438.找到字符串中所有字母异位词](https://leetcode.cn/problems/find-all-anagrams-in-a-string/description/)

## 题目解析
![在这里插入图片描述](https://img-blog.csdnimg.cn/d4063eaf14c643b8ba671deb86de7f9a.png)

异位词:如下abc acb bac bca cab cba都是p的异位词.
![在这里插入图片描述](https://img-blog.csdnimg.cn/803c0a95a4034cc48593a6a246edfdf7.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/97f61b50164f455d84e52df301f5dc4a.png)




## 算法原理
1.如何快速判断两个字符串是否是"异位词"?判断每个字符串里的每个字符出现的次数.
![在这里插入图片描述](https://img-blog.csdnimg.cn/68f5c993753548a7bcfee38903fb1465.png)
我们可以利用哈希表.遍历每个字符,比较两个哈希表对应位置字符出现的个数是否相等.

2.解决问题

> 解法一:暴力解法

先把p字符串里每个字符出现了多少个放到hash1表里,p这个字符串的长度是m,然后在s字符串里找到所有长度为m的字串
找出来后,把其放进另一个哈希表里,然后比较两个哈希表是否相等即可.


![在这里插入图片描述](https://img-blog.csdnimg.cn/b456e3abf8354cf0a86d3a6a278be9a0.png)


s字符串里找到所有长度为m的字串:
![在这里插入图片描述](https://img-blog.csdnimg.cn/21c9f09c44f8407e944eb1ca0efe1930.png)

> 解法二:滑动窗口


![在这里插入图片描述](https://img-blog.csdnimg.cn/49f356595de345cda0517026ea256af7.png)

1. left=0,right=0
 
2. 进窗口 hash2[in]++

3. 判断 当窗口大于m就要移动left即 (right - left + 1 > m),如果这个条件成立就要出窗口一次,hash2[out]--,最后检查hash2和hash1的信息是否一致,然后更新结果.


优化:更新结果的判断条件


![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/34313742a3fb47b284327a0a2effd51b.png)
进窗口:进入后判断hash2[in] <= hash1[in]如果符合就让count++.

出窗口:出去前判断hash2[out] <= hash1[out]如果符合就让count--

更新结果: count == m.
## 编写代码


```java
class Solution {
    public List<Integer> findAnagrams(String ss, String pp) {
        List<Integer> ret = new ArrayList<Integer>();
        char[] s = ss.toCharArray();
        char[] p = pp.toCharArray();
        int[] hash1 = new int[26]; // 统计字符串p中每一个字符出现的个数
        for(char ch : p) 
        {
            hash1[ch - 'a']++;
        }
        int[] hash2 = new int[26];//统计窗口中每个字符出现的个数
        int m = p.length;
        for(int left = 0, right = 0,count = 0;right < s.length;right++)
        {
            char in = s[right];
            hash2[in - 'a']++;
            if(hash2[in - 'a'] <= hash1[in - 'a']) 
            {
                count++;// 进窗口 + 维护count变量
            }
            if(right - left + 1 > m)
            {
                char out = s[left++];
                if(hash2[out - 'a']-- <= hash1[out - 'a'])
                {
                    count--;//出窗口 + 维护count
                }
            }
            // 更新hash表
            if(count == m)
            {
                ret.add(left);
            }
        }
        return ret;
    }
}
```

















# LeetCode30.串联所有单词的子串

题目链接: [leetcode30.串联所有单词的子串](https://leetcode.cn/problems/substring-with-concatenation-of-all-words/description/)

## 题目解析

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/150ffdaaceab443f95b1b203fd366d36.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/b8ee53fba76b41d9b294450ec1d45c42.png)

## 算法原理

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/c5b93422fa9a49a5a371aba3b22c909a.png)
我们发现这道题和438题就变成了一个题了.

> 解法:滑动窗口 + 哈希表

对比之前的算法有不同的点如下:

1. 哈希表

2. left和right指针的移动


3. 滑动窗口执行的次数

![在这里插入图片描述](https://img-blog.csdnimg.cn/direct/4b6197255848484ca2bca0edea5a0243.png)


## 编写代码

```java
class Solution
{
    public List < Integer > findSubstring(String s, String[] words)
    {
        List < Integer > ret = new ArrayList < Integer > ();
        // 保存字典中所有单词的频次
        Map < String, Integer > hash1 = new HashMap < String, Integer > ();
        for(String str: words) hash1.put(str, hash1.getOrDefault(str, 0) + 1);
        int len = words[0].length(), m = words.length;
        for(int i = 0; i < len; i++) // 执⾏次数
        {
            // 保存窗⼝内所有单词的频次
            Map < String, Integer > hash2 = new HashMap < String, Integer > ();
            for(int left = i, right = i, count = 0; right + len <= s.length(); right += len)
            {
                // 进窗⼝ + 维护 count
                String in = s.substring(right, right + len);
                hash2.put( in , hash2.getOrDefault( in , 0) + 1);
                if(hash2.get( in ) <= hash1.getOrDefault( in , 0)) count++;
                // 判断
                if(right - left + 1 > len * m)
                {
                    // 出窗⼝ + 维护 count
                    String out = s.substring(left, left + len);
                    if(hash2.get(out) <= hash1.getOrDefault(out, 0)) count--;
                    hash2.put(out, hash2.get(out) - 1);
                    left += len;
                }
                // 更新结果
                if(count == m) ret.add(left);
            }
        }
        return ret;
    }
}
```
