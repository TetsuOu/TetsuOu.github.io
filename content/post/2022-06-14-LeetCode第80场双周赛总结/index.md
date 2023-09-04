---
title:      LeetCode第80场双周赛总结
description:   1152/3949
date:       2022-06-14
image: post-bg-20220614.png
categories:
    - leetcode contest
tags:
    - 滑动窗口
    
---

![image-20220614171923446](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220614171923446.png)

## 【模拟】强密码检验器 II

### 题目

如果一个密码满足以下所有条件，我们称它是一个 **强** 密码：

- 它有至少 `8` 个字符。
- 至少包含 **一个小写英文** 字母。
- 至少包含 **一个大写英文** 字母。
- 至少包含 **一个数字** 。
- 至少包含 **一个特殊字符** 。特殊字符为：`"!@#$%^&*()-+"` 中的一个。
- 它 **不** 包含 `2` 个连续相同的字符（比方说 `"aab"` 不符合该条件，但是 `"aba"` 符合该条件）。

给你一个字符串 `password` ，如果它是一个 **强** 密码，返回 `true`，否则返回 `false` 。

 

**示例 1：**

```
输入：password = "IloveLe3tcode!"
输出：true
解释：密码满足所有的要求，所以我们返回 true 。
```

**示例 2：**

```
输入：password = "Me+You--IsMyDream"
输出：false
解释：密码不包含数字，且包含 2 个连续相同的字符。所以我们返回 false 。
```

**示例 3：**

```
输入：password = "1aB!"
输出：false
解释：密码不符合长度要求。所以我们返回 false 。
```

 

**提示：**

- `1 <= password.length <= 100`
- `password` 包含字母，数字和 `"!@#$%^&*()-+"` 这些特殊字符。

### 解题思路

按题意模拟即可

### 代码

```c++
class Solution {
public:
    bool issp(char ch){
        //!@#$%^&*()-+
        if(ch=='!'||ch=='@'||ch=='#'||ch=='$'||ch=='%'||ch=='^'||ch=='&'||ch=='*'||ch=='('||ch==')'||ch=='-'||ch=='+') return true;
        return false;
    }
    int type(char ch){
        if(issp(ch)) return 4;
        else if(ch>='a'&&ch<='z') return 1;
        else if(ch>='A'&&ch<='Z') return 2;
        else if(ch>='0'&&ch<='9') return 3;
        return 0;
    }
    bool strongPasswordCheckerII(string password) {
        int n = password.size();
        if(n<8) return false;
        vector<int> t(5,0);
        bool flag=true;
        t[type(password[0])]++;
        for(int i=1;i<n;i++){
            t[type(password[i])]++;
            if(password[i]==password[i-1]){
                flag = false;
                break;
            }
        }
        for(int i=1;i<=4;i++){
            if(t[i]<1) flag=false;
        }
        return flag;
    }
};
```

### 偷学代码

优雅

```c++
class Solution {
public:
    bool strongPasswordCheckerII(string s) {
        if (s.size() < 8) return false;
        for (int i = 1; i < s.size(); i += 1)
            if (s[i] == s[i - 1]) return false;
        int ans = 0;
        string t = "!@#$%^&*()-+";
        for (char c : s) {
            if (isdigit(c)) ans |= 1;
            if (islower(c)) ans |= 2;
            if (isupper(c)) ans |= 4;
            if (count(t.begin(), t.end(), c)) ans |= 8;
        }
        return ans == 15;
    }
};
```

## 【排序+二分】咒语和药水的成功对数

### 题目

给你两个正整数数组 `spells` 和 `potions` ，长度分别为 `n` 和 `m` ，其中 `spells[i]` 表示第 `i` 个咒语的能量强度，`potions[j]` 表示第 `j` 瓶药水的能量强度。

同时给你一个整数 `success` 。一个咒语和药水的能量强度 **相乘** 如果 **大于等于** `success` ，那么它们视为一对 **成功** 的组合。

请你返回一个长度为 `n` 的整数数组 `pairs`，其中 `pairs[i]` 是能跟第 `i` 个咒语成功组合的 **药水** 数目。

 

**示例 1：**

```
输入：spells = [5,1,3], potions = [1,2,3,4,5], success = 7
输出：[4,0,3]
解释：
- 第 0 个咒语：5 * [1,2,3,4,5] = [5,10,15,20,25] 。总共 4 个成功组合。
- 第 1 个咒语：1 * [1,2,3,4,5] = [1,2,3,4,5] 。总共 0 个成功组合。
- 第 2 个咒语：3 * [1,2,3,4,5] = [3,6,9,12,15] 。总共 3 个成功组合。
所以返回 [4,0,3] 。
```

**示例 2：**

```
输入：spells = [3,1,2], potions = [8,5,8], success = 16
输出：[2,0,2]
解释：
- 第 0 个咒语：3 * [8,5,8] = [24,15,24] 。总共 2 个成功组合。
- 第 1 个咒语：1 * [8,5,8] = [8,5,8] 。总共 0 个成功组合。
- 第 2 个咒语：2 * [8,5,8] = [16,10,16] 。总共 2 个成功组合。
所以返回 [2,0,2] 。
```

 

**提示：**

- `n == spells.length`
- `m == potions.length`
- `1 <= n, m <= 10^5`
- `1 <= spells[i], potions[i] <= 10^5`
- `1 <= success <= 10^10`

### 解题思路

将药水从小到到大排序，乘积都是整数，且具有单调性，所以可以二分

$x\times y \ge success$等价于$y\ge \lceil \frac{success}{x} \rceil$，也等价于$y>\lfloor \frac{success-1}{x}\rfloor$，或者是$y\ge \lfloor \frac{success+x-1}{x}  \rfloor$

### 代码

#### Version 1.0

比赛时WA了一发，没有将target转为long long

后面将p数组也转为了long long ，其实没必要

按照了是否能整除来判断是用lower_bound还是upper_bound

```c++
class Solution {
public:
    vector<int> successfulPairs(vector<int>& spells, vector<int>& potions, long long success) {
        int n = spells.size(), m = potions.size();
        vector<long long> p(potions.begin(),potions.end());
        sort(p.begin(),p.end());
        vector<int> res(n,0);
        for(int i=0;i<n;i++){
            if(1ll*spells[i]>=success){
                res[i] = m;
                continue;
            }
            long long target = success/spells[i];
            int idx=-1;
            if(success%(spells[i]*1ll)==0) 
                idx = lower_bound(p.begin(),p.end(),target)-p.begin();
            else idx = upper_bound(p.begin(),p.end(),target)-p.begin();
            
            res[i] = m-idx;
            
        }
        return res;
    }
};
```

#### Version 2.0

$y>\lfloor \frac{success-1}{x}\rfloor$

```c++
class Solution {
public:
    vector<int> successfulPairs(vector<int>& spells, vector<int>& potions, long long success) {
        int n = spells.size(), m = potions.size();
        vector<long long> p(potions.begin(),potions.end());
        sort(p.begin(),p.end());
        vector<int> res(n,0);
        for(int i=0;i<n;i++){
            if(1ll*spells[i]>=success){
                res[i] = m;
                continue;
            }
            long long target = (success-1)/spells[i];
            int idx=-1;
            idx = upper_bound(p.begin(),p.end(),target)-p.begin();
            res[i] = m-idx;
        }
        return res;
    }
};
```

#### Version 3.0

$y\ge \lfloor \frac{success+x-1}{x}  \rfloor$

```c++
class Solution {
public:
    vector<int> successfulPairs(vector<int>& spells, vector<int>& potions, long long success) {
        int n = spells.size(), m = potions.size();
        sort(potions.begin(),potions.end());
        vector<int> res(n,0);
        for(int i=0;i<n;i++){
            if(spells[i]>=success){
                res[i] = m;
                continue;
            }
            long long target = (success+spells[i]-1)/spells[i];
            int idx=-1;
            idx = lower_bound(potions.begin(),potions.end(),target)-potions.begin();
            res[i] = m-idx;
        }
        return res;
    }
};
```

## 【枚举】替换字符后匹配

### 题目

给你两个字符串 `s` 和 `sub` 。同时给你一个二维字符数组 `mappings` ，其中 `mappings[i] = [oldi, newi]` 表示你可以将 `sub` 中任意数目的 `oldi` 字符替换为 `newi` 。`sub` 中每个字符 **不能** 被替换超过一次。

如果使用 `mappings` 替换 0 个或者若干个字符，可以将 `sub` 变成 `s` 的一个子字符串，请你返回 `true`，否则返回 `false` 。

一个 **子字符串** 是字符串中连续非空的字符序列。

 

**示例 1：**

```
输入：s = "fool3e7bar", sub = "leet", mappings = [["e","3"],["t","7"],["t","8"]]
输出：true
解释：将 sub 中第一个 'e' 用 '3' 替换，将 't' 用 '7' 替换。
现在 sub = "l3e7" ，它是 s 的子字符串，所以我们返回 true 。
```

**示例 2：**

```
输入：s = "fooleetbar", sub = "f00l", mappings = [["o","0"]]
输出：false
解释：字符串 "f00l" 不是 s 的子串且没有可以进行的修改。
注意我们不能用 'o' 替换 '0' 。
```

**示例 3：**

```
输入：s = "Fool33tbaR", sub = "leetd", mappings = [["e","3"],["t","7"],["t","8"],["d","b"],["p","b"]]
输出：true
解释：将 sub 里第一个和第二个 'e' 用 '3' 替换，用 'b' 替换 sub 里的 'd' 。
得到 sub = "l33tb" ，它是 s 的子字符串，所以我们返回 true 。
```

 

**提示：**

- `1 <= sub.length <= s.length <= 5000`
- `0 <= mappings.length <= 1000`
- `mappings[i].length == 2`
- `oldi != newi`
- `s` 和 `sub` 只包含大写和小写英文字母和数字。
- `oldi` 和 `newi` 是大写、小写字母或者是个数字。

### 解题思路

离谱啊，枚举就行了

枚举`s[i..i+m-1] = sub[0..m-1]`相匹配，字符相同就不用换，不同就在mappings里找能否替换，利用哈希表在O(1)时间内查询

'0'的ascii码是48

'A'的ascii码是65

'a'的ascii码是97

### 代码

```c++
class Solution {
public:
    bool matchReplacement(string s, string sub, vector<vector<char>>& mappings) {
        unordered_set<char> tab[100];
        for(auto &x: mappings){
            tab[x[0]-'0'].insert(x[1]);
        }
        int n=s.size(),m=sub.size();
        for(int i=0;i+m-1<n;i++){
            //s[i..i+m-1] = sub[0..m-1]
            bool flag = true;
            for(int j=0;j<m&&flag;j++){
                if(s[i+j]==sub[j]) continue;
                if(tab[sub[j]-'0'].count(s[i+j])) continue;
                flag = false;
            }
            if(flag) return true;
        }

        return 0;
    }
};
```

## 【前缀和+二分/滑动窗口】统计得分小于 K 的子数组数目

### 题目

一个数字的 **分数** 定义为数组之和 **乘以** 数组的长度。

- 比方说，`[1, 2, 3, 4, 5]` 的分数为 `(1 + 2 + 3 + 4 + 5) * 5 = 75` 。

给你一个正整数数组 `nums` 和一个整数 `k` ，请你返回 `nums` 中分数 **严格小于** `k` 的 **非空整数子数组数目**。

**子数组** 是数组中的一个连续元素序列。

 

**示例 1：**

```
输入：nums = [2,1,4,3,5], k = 10
输出：6
解释：
有 6 个子数组的分数小于 10 ：
- [2] 分数为 2 * 1 = 2 。
- [1] 分数为 1 * 1 = 1 。
- [4] 分数为 4 * 1 = 4 。
- [3] 分数为 3 * 1 = 3 。 
- [5] 分数为 5 * 1 = 5 。
- [2,1] 分数为 (2 + 1) * 2 = 6 。
注意，子数组 [1,4] 和 [4,3,5] 不符合要求，因为它们的分数分别为 10 和 36，但我们要求子数组的分数严格小于 10 。
```

**示例 2：**

```
输入：nums = [1,1,1], k = 5
输出：5
解释：
除了 [1,1,1] 以外每个子数组分数都小于 5 。
[1,1,1] 分数为 (1 + 1 + 1) * 3 = 9 ，大于 5 。
所以总共有 5 个子数组得分小于 5 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^5`
- `1 <= k <= 10^15`

### 解题思路

比赛时是用二分做的

考虑`[l,r]`区间内的分数，利用前缀和可在$O(1)$时间内计算：`(sum[r]-sum[l-1])*(r-l+1)`

考虑前i个数（以1开始计数），且末尾元素是nums[i]的子数组范围，它的子数组范围为：`[1..i], [2..i], [3..i],...,[i..i]`，由于元素值都是大于0的，可以发现上述子数组的分数是单调递增的。

所以可以固定右端点，再二分查找出分数严格小于k的最小左端点，统计进总数即可。这样时间复杂度是$O(nlogn)$

注意数据类型。

WA了两发是数组起始0和1搞混了

### 代码

#### 二分


```c++
#define ll long long
class Solution {
public:
    long long countSubarrays(vector<int>& nums, long long k) {
        int n = nums.size();
        vector<ll> sum(n+1,0);
        for(int i=1;i<=n;i++) sum[i]=sum[i-1]+nums[i-1];
        
        ll res = 0;
        
        for(int i=1;i<=n;i++){//枚举右端点为i
            if(nums[i-1]>=k) continue;
            int l=1,r=i;//左端点的范围为[1,i]
            while(l<=r){//左闭右闭
                int mid=(l+r)>>1;
                long long cur = (sum[i]-sum[mid-1])*(i-mid+1);
                if(cur>=k) l=mid+1;
                else r=mid-1;//最后要小于k，满足条件推出循环后mid=r+1，故左端点为r+1
            }
            //r+1   [r+1,i]中所有以nums[i]结尾的子数组的分数都严格小于k，共有i-(r+1)+1个
            res += i-(r+1)+1;
        }
        
        return res;
    }
};
```

### 偷学代码

#### 滑动窗口

滑动窗口考量区间[l,r]是否满足题中要求:

- 不满足就右移l
- 满足的话,[l+1,r],[l+2,r]...等区间也满足,计数增加 ++r - l

```c++
class Solution {
  public long countSubarrays(int[] nums, long k) {
    int n = nums.length;
    long res = 0;
    long[] sum = new long[n + 1];
    for (int i = 0; i < n; i++) {
      sum[i + 1] = sum[i] + nums[i];//前缀和
    }
    int l = 0, r = 0;
    while (r < n) {
      while (l <= r && (r - l + 1) * (sum[r + 1] - sum[l]) >= k) l++;//右移l直到满足
      res += ++r - l;//计数并右移r
    }
    return res;
  }
}
```



