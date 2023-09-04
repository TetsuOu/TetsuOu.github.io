---
title:      LeetCode第291场周赛总结
description:   2819/6574
date:       2022-05-04 00:00:00+0000
image: post-bg-20220504.png
categories:
    - leetcode contest
tags:
    
---

![image-20220504162826480](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220504162826480.png)

这场状态真差，掉大分

## 【模拟】移除指定数字得到的最大结果

### 题目

给你一个表示某个正整数的字符串 `number` 和一个字符 `digit` 。

从 `number` 中 **恰好** 移除 **一个** 等于 `digit` 的字符后，找出并返回按 **十进制** 表示 **最大** 的结果字符串。生成的测试用例满足 `digit` 在 `number` 中出现至少一次。

 

**示例 1：**

```
输入：number = "123", digit = "3"
输出："12"
解释："123" 中只有一个 '3' ，在移除 '3' 之后，结果为 "12" 。
```

**示例 2：**

```
输入：number = "1231", digit = "1"
输出："231"
解释：可以移除第一个 '1' 得到 "231" 或者移除第二个 '1' 得到 "123" 。
由于 231 > 123 ，返回 "231" 。
```

**示例 3：**

```
输入：number = "551", digit = "5"
输出："51"
解释：可以从 "551" 中移除第一个或者第二个 '5' 。
两种方案的结果都是 "51" 。
```

 

**提示：**

- `2 <= number.length <= 100`
- `number` 由数字 `'1'` 到 `'9'` 组成
- `digit` 是 `'1'` 到 `'9'` 中的一个数字
- `digit` 在 `number` 中出现至少一次

### 解题思路

比赛的时候想的太琐碎了，WA了两发

其实遍历一遍，所有情况拿出来比一下不就好了

难受

### 代码

```c++
class Solution {
public:
    string removeDigit(string number, char digit) {
        int n = number.size();
        int idx=-1;
        
        for(int i=0;i<n;i++){
            if(number[i] == digit){
                if(idx==-1){
                    idx = i;
                    if(number[i]<number[i+1]){
                        idx=i;break;
                    }
                }else {
                    if(i!=n-1 && number[i]<number[i+1]){
                        idx = i;
                        break;
                    } else {
                        idx = i;
                    }
                }
            }
        }
        
        number.erase(idx,1);
        return number;
        
    }
};
```

```c++
class Solution {
public:
    string removeDigit(string number, char digit) {
        set<string> se;
        for(int i=0;i<number.size();i++){
            if(number[i]==digit){
                string s = number;
                s.erase(s.begin()+i);
                se.insert(s);
            }
        }
        return *se.rbegin();
    }
};
```

## 【滑动窗口】必须拿起的最小连续卡牌数

### 题目

给你一个整数数组 `cards` ，其中 `cards[i]` 表示第 `i` 张卡牌的 **值** 。如果两张卡牌的值相同，则认为这一对卡牌 **匹配** 。

返回你必须拿起的最小连续卡牌数，以使在拿起的卡牌中有一对匹配的卡牌。如果无法得到一对匹配的卡牌，返回 `-1` 。

 

**示例 1：**

```
输入：cards = [3,4,2,3,4,7]
输出：4
解释：拿起卡牌 [3,4,2,3] 将会包含一对值为 3 的匹配卡牌。注意，拿起 [4,2,3,4] 也是最优方案。
```

**示例 2：**

```
输入：cards = [1,0,5,3]
输出：-1
解释：无法找出含一对匹配卡牌的一组连续卡牌。
```

 

**提示：**

- `1 <= cards.length <= 10^5`
- `0 <= cards[i] <= 10^6`

### 解题思路

刚好上周刷了一些滑动窗口的题，看到可以用滑动窗口做，就用滑动窗口了

维护一个滑动窗口，再维护一个哈希表存储卡牌信息即可

### 代码

```c++
class Solution {
public:
    int minimumCardPickup(vector<int>& cards) {
        int n = cards.size();
        unordered_map<int,int> tab;
        int l=0,r=0,res=n+1;
        while(r<n){
            int cur = cards[r];
            r++;
            tab[cur]++;
            while(tab[cur]>1){//有两张卡牌的值相同了，开始收缩窗口
                res = min(res,r-l);
                tab[cards[l]]--;
                l++;  
            }
        }
        return res==n+1?-1:res;
    }
};
```

## 【模拟】含最多 K 个可整除元素的子数组

### 题目

给你一个整数数组 `nums` 和两个整数 `k` 和 `p` ，找出并返回满足要求的不同的子数组数，要求子数组中最多 `k` 个可被 `p` 整除的元素。

如果满足下述条件之一，则认为数组 `nums1` 和 `nums2` 是 **不同** 数组：

- 两数组长度 **不同** ，或者
- 存在 **至少** 一个下标 `i` 满足 `nums1[i] != nums2[i]` 。

**子数组** 定义为：数组中的连续元素组成的一个 **非空** 序列。

 

**示例 1：**

```
输入：nums = [2,3,3,2,2], k = 2, p = 2
输出：11
解释：
位于下标 0、3 和 4 的元素都可以被 p = 2 整除。
共计 11 个不同子数组都满足最多含 k = 2 个可以被 2 整除的元素：
[2]、[2,3]、[2,3,3]、[2,3,3,2]、[3]、[3,3]、[3,3,2]、[3,3,2,2]、[3,2]、[3,2,2] 和 [2,2] 。
注意，尽管子数组 [2] 和 [3] 在 nums 中出现不止一次，但统计时只计数一次。
子数组 [2,3,3,2,2] 不满足条件，因为其中有 3 个元素可以被 2 整除。
```

**示例 2：**

```
输入：nums = [1,2,3,4], k = 4, p = 1
输出：10
解释：
nums 中的所有元素都可以被 p = 1 整除。
此外，nums 中的每个子数组都满足最多 4 个元素可以被 1 整除。
因为所有子数组互不相同，因此满足所有限制条件的子数组总数为 10 。
```

 

**提示：**

- `1 <= nums.length <= 200`
- `1 <= nums[i], p <= 200`
- `1 <= k <= nums.length`

### 解题思路

这题没细看，初看很烦，就一直想第4题去了

其实这题很简单，模拟一下就好了，数据范围很小

### 代码

```c++
#define ull unsigned long long 
const ull B = 1e9+7;

class Solution {
public:
    int countDistinct(vector<int>& nums, int k, int p) {
        unordered_set<ull> tab;
        int n = nums.size();
        for(int i=0;i<n;i++){//起点
            for(int j=1;i+j-1<n;j++){//长度
                ull Hash = 0;
                int cnt=0;
                for(int q=i;q<i+j;q++){
                    if(nums[q]%p==0) cnt++;
                    if(cnt>k) break;
                    Hash = Hash*B + nums[q];
                }
                if(cnt<=k) tab.insert(Hash);
            }
        }
        return tab.size();
    }
};
```

### 偷学代码

不必手动hash，但要注意加分隔符

```c++
class Solution {
public:
    int countDistinct(vector<int>& nums, int k, int p) {
        int n = nums.size();
        unordered_set<string> st;

        for (int i = 0; i < n; i++) {
            string t;
            int cnt = 0;
            for (int j = i; j < n; j++) {
                if (nums[j] % p == 0) cnt++;
                if (cnt > k) break;
                t += to_string(nums[j]) + "|";
                st.insert(t);
            }
        }
        return st.size();
    }
};

```



```c++
class Solution {
public:
    int countDistinct(vector<int>& nums, int k, int p) {
        int n = nums.size();
        map<vector<int>, int> mp;
        for (int i = 0; i < n; i++)
        {
            int cnt = 0;
            vector<int> v;
            for (int j = i; j < n; j++)
            {
                if (nums[j] % p == 0) cnt++;
                if (cnt > k) break;
                v.push_back(nums[j]);
                mp[v] = 1;
            }
        }
        return (int)mp.size();
    }
};
```



## 【动态规划】字符串的总引力

### 题目

字符串的 **引力** 定义为：字符串中 **不同** 字符的数量。

- 例如，`"abbca"` 的引力为 `3` ，因为其中有 `3` 个不同字符 `'a'`、`'b'` 和 `'c'` 。

给你一个字符串 `s` ，返回 **其所有子字符串的总引力** **。**

**子字符串** 定义为：字符串中的一个连续字符序列。

 

**示例 1：**

```
输入：s = "abbca"
输出：28
解释："abbca" 的子字符串有：
- 长度为 1 的子字符串："a"、"b"、"b"、"c"、"a" 的引力分别为 1、1、1、1、1，总和为 5 。
- 长度为 2 的子字符串："ab"、"bb"、"bc"、"ca" 的引力分别为 2、1、2、2 ，总和为 7 。
- 长度为 3 的子字符串："abb"、"bbc"、"bca" 的引力分别为 2、2、3 ，总和为 7 。
- 长度为 4 的子字符串："abbc"、"bbca" 的引力分别为 3、3 ，总和为 6 。
- 长度为 5 的子字符串："abbca" 的引力为 3 ，总和为 3 。
引力总和为 5 + 7 + 7 + 6 + 3 = 28 。
```

**示例 2：**

```
输入：s = "code"
输出：20
解释："code" 的子字符串有：
- 长度为 1 的子字符串："c"、"o"、"d"、"e" 的引力分别为 1、1、1、1 ，总和为 4 。
- 长度为 2 的子字符串："co"、"od"、"de" 的引力分别为 2、2、2 ，总和为 6 。
- 长度为 3 的子字符串："cod"、"ode" 的引力分别为 3、3 ，总和为 6 。
- 长度为 4 的子字符串："code" 的引力为 4 ，总和为 4 。
引力总和为 4 + 6 + 6 + 4 = 20 。
```

 

**提示：**

- `1 <= s.length <= 10^5`
- `s` 由小写英文字母组成

### 解题思路

比赛的时候想到了主体思路，那就是想方法计算出单个字符的贡献，最后加一起。

补题的时候发现这类计算贡献的题，用动态规划的效果很不错

令dp[i]表示s[0..i]的所有子字符串的总引力

那么最终结果为dp[0]+dp[1]+...+dp[n-1]

即所有的子字符串 由 以s[0]结尾的字符串、以s[1]结尾的字符串、...。以s[n-1]结尾的字符串组成

dp[0]=1显然可得

递推关系怎么得呢，考虑第i个字符s[i]，它会加在前面组成的字符串末尾，假设前面第j个字符s[j]==s[i]，那么s[i]会对在区间[j+1,i]结尾的子字符串提供贡献，即i-j

这个j代表的就是s[i]左侧第一个出现的相同字符的位置

### 代码

```c++
class Solution {
public:
    long long appealSum(string s) {
        int n = s.size();
        vector<long long> dp(n,0),pos(26,-1);
        //dp[i] s[0..i]的所有子字符串的总引力
        //pos[i] 与s[i]相同字符的左侧最近位置，辅助

        dp[0] = 1;
        pos[s[0]-'a'] = 0; 
        for(int i=1;i<n;i++){
            dp[i] = dp[i-1]+i-pos[s[i]-'a'];
            pos[s[i]-'a']=i;
        }
        
        return accumulate(dp.begin(),dp.end(),(long long)0);
    }
};
```

