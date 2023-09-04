---
title:      滑动窗口
description:   尺取法
date:       2022-04-30 00:00:00+0000
image: post-bg-20220430.png
categories:
    - data structure and algorithm 
tags:
    - 滑动窗口
---

## 前言

```c++
int left = 0, right = 0;
//[left, right)左闭右开，区间长度为r
while (right < s.size()) {
    // 增大窗口
    var cur = s[right];
    window.add(cur);
    right++;
    
    while (window needs shrink) {
        // 缩小窗口
        window.remove(s[left]);
        left++;
    }
}
```

注意窗口大小是固定的，还是不固定的

即注意左边界的变化情况

主要是看**用什么数据结构维护窗口滑动过程中的变化量，以及什么时候更新答案**



## 最小覆盖子串

### 题目

给你一个字符串 s 、一个字符串 t 。返回 s 中涵盖 t 所有字符的最小子串。如果 s 中不存在涵盖 t 所有字符的子串，则返回空字符串 "" 。

 

注意：

对于 t 中重复字符，我们寻找的子字符串中该字符数量必须不少于 t 中该字符数量。
如果 s 中存在这样的子串，我们保证它是唯一的答案。


示例 1：

输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
示例 2：

输入：s = "a", t = "a"
输出："a"
示例 3:

输入: s = "a", t = "aa"
输出: ""
解释: t 中两个字符 'a' 均应包含在 s 的子串中，
因此没有符合条件的子字符串，返回空字符串。


提示：

1 <= s.length, t.length <= 10^5

s 和 t 由英文字母组成

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/minimum-window-substring
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

[left, right)维护字符串s上一个滑动窗口，判断该滑动窗口中的字符串是否涵盖t所有字符，最后求出这个最小长度的滑动窗口

重点是**如何判断滑动窗口中的字符串是否涵盖t所有字符**

对滑动窗口内的字符串和t分别维护一个哈希表，用来存储需要覆盖字符及个数

窗口右边界增长时，判断新增字符是否为需要字符，若是，则添加进代表窗口内字符串的哈希表中，并且计算满足要求的字符种类数

当窗口中字符串满足题意时，更新答案，并且尝试增长左边界，窗口进行滑动

### 代码

```c++
class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char,int> sm,tm;
        for(auto &c: t) tm[c]++;
        int left=0,right=0;
        int valid = 0;//涵盖t中字符种类个数，当valid==t中字符种类个数时说明已涵盖
        int start=0,length=0x3f3f3f3f;
        while(right<s.size()){
            char c = s[right];
            right++;

            if(tm.count(c)){
                sm[c]++;
                if(tm[c]==sm[c]) valid++;
            }
            while(valid == tm.size()){
                if(right-left<length){
                    length = right-left;
                    start = left;
                }
                if(tm.count(s[left])){
                    if(sm[s[left]] == tm[s[left]]) valid--;
                    sm[s[left]]--;
                }
                left++;
            }

        }
        if(length == 0x3f3f3f3f) return "";
        else return s.substr(start,length);
    }   
};
```

## 字符串的排列

### 题目

给你两个字符串 s1 和 s2 ，写一个函数来判断 s2 是否包含 s1 的排列。如果是，返回 true ；否则，返回 false 。

换句话说，s1 的排列之一是 s2 的 子串 。

 

示例 1：

输入：s1 = "ab" s2 = "eidbaooo"
输出：true
解释：s2 包含 s1 的排列之一 ("ba").
示例 2：

输入：s1= "ab" s2 = "eidboaoo"
输出：false


提示：

1 <= s1.length, s2.length <= 10^4

s1 和 s2 仅包含小写字母

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/permutation-in-string
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

此题的窗口稳定之后大小是固定的

同样的，可以利用哈希表来维护需要比较判断的东西

### 代码

```c++
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        int len1=s1.size(),len2=s2.size();
        if(len1>len2) return false;

        unordered_map<char,int> tab1,tab2;
        for(auto &x: s1) tab1[x]++;

        int l=0,r=0;
        while(r<len2){
            tab2[s2[r]]++;
            r++;
            if(tab1==tab2){
                return true;
            }
            if(r-l>=len1){//右边界开始增长
                tab2[s2[l]]--;
                if(!tab2[s2[l]]) 
                    tab2.erase(s2[l]);
                l++;
            }
        } 
        return false;
    }
};
```

## 无重复字符的最长子串

### 题目

给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。

 

示例 1:

输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
示例 2:

输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
示例 3:

输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。


提示：

0 <= s.length <= 5 * 10^4

s 由英文字母、数字、符号和空格组成

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/longest-substring-without-repeating-characters
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

利用哈希表判断串口内字符串中是否有字符数量超过1，有则开始增加左边界直到无重复字符

### 代码

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        int n = s.size();
        unordered_map<char,int> tab;
        int l=0,r=0,res=0;
        while(r<n){
            char cur = s[r];
            r++;
            tab[cur]++;
            while(tab[cur]>1) {
                tab[s[l]]--;
                l++;
            }
            res = max(res,r-l);
        }
        return res;
    }
};
```

## 最大连续1的个数 III

### 题目

给定一个二进制数组 nums 和一个整数 k，如果可以翻转最多 k 个 0 ，则返回 数组中连续 1 的最大个数 。

 

示例 1：

输入：nums = [1,1,1,0,0,0,1,1,1,1,0], K = 2
输出：6
解释：[1,1,1,0,0,1,1,1,1,1,1]
粗体数字从 0 翻转到 1，最长的子数组长度为 6。
示例 2：

输入：nums = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], K = 3
输出：10
解释：[0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,1]
粗体数字从 0 翻转到 1，最长的子数组长度为 10。


提示：

1 <= nums.length <= 10^5

nums[i] 不是 0 就是 1

0 <= k <= nums.length

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/max-consecutive-ones-iii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

此题只需要一个int变量记录已经进行翻转0的个数即可（即0的个数）

### 代码

```c++
class Solution {
public:
    int longestOnes(vector<int>& nums, int k) {
        int n = nums.size();
        int l=0,r=0;
        int res=0;
        int cnt=0;//翻转0的个数
        while(r<n){
            int cur = nums[r];
            r++;
            if(cur==0) cnt++;
            while(cnt>k){
                if(nums[l]==0) cnt--;
                l++;
            }
            res = max(res,r-l);
        }
        return res;
    }
};
```

## 存在重复元素 III

### 题目

给你一个整数数组 nums 和两个整数 k 和 t 。请你判断是否存在 两个不同下标 i 和 j，使得 abs(nums[i] - nums[j]) <= t ，同时又满足 abs(i - j) <= k 。

如果存在则返回 true，不存在返回 false。

 

示例 1：

输入：nums = [1,2,3,1], k = 3, t = 0
输出：true
示例 2：

输入：nums = [1,0,1,1], k = 1, t = 2
输出：true
示例 3：

输入：nums = [1,5,9,1,5,9], k = 2, t = 3
输出：false


提示：

0 <= nums.length <= 2 * 10^4

-231 <= nums[i] <= 2^31 - 1

0 <= k <= 10^4

0 <= t <= 2^31 - 1

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/contains-duplicate-iii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 滑动窗口1.0

注意数据范围，运算结果有可能超int，醉了

利用map的有序性，维护每个数及出现的次数

其实也可以只用一次二分判断，见2.0

```c++
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        int n = nums.size();
        int l=0,r=0;
        map<long long,long long> tab;
        
        while(r<n){
            int cur = nums[r];
            r++;
            tab[cur]++;
            if(tab[cur]>1) {//窗口前面出现过相同的数，==0
                return true;
            }
            auto idx1 = tab.upper_bound(cur);
            if(idx1!=tab.end() && abs(idx1->first-cur)<=t) {//大于cur的第一个数
                return true;
            }
            auto idx2 = tab.lower_bound(cur);
            if(idx2!=tab.begin() && abs((--idx2)->first-cur)<=t){//小于cur的第一个数
                return true;
            }
            if(r-l>k){
                tab[nums[l]]--;
                if(tab[nums[l]]==0) tab.erase(nums[l]);
                l++;
            }

        }
        return false;
    }
};
```

### 滑动窗口2.0

窗口内每次添加进一个元素cur时，检查前面添加进窗口的元素中，是否存在pre，使得pre-cur<=t 且 cur-pre<=t

则pre的范围为[cur-t, cur+t]

此时只需要用一个set维护即可，但更新变化量的过程要放在答案更新的后面

注意是两个不同下标

```c++
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        int n = nums.size();
        int l=0,r=0;
        set<long long> se;
        
        while(r<n){
            long long cur = nums[r];
            r++;
            auto idx = se.lower_bound(cur-t);
            if(idx!=se.end() && *idx<=cur+t) return true;
            se.insert(cur);
            if(r-l>k){
                se.erase(nums[l]);
                l++;
            }
        }
        return false;
    }
};
```

## 乘积小于K的子数组

### 题目

给你一个整数数组 nums 和一个整数 k ，请你返回子数组内所有元素的乘积严格小于 k 的连续子数组的数目。


示例 1：

输入：nums = [10,5,2,6], k = 100
输出：8
解释：8 个乘积小于 100 的子数组分别为：[10]、[5]、[2],、[6]、[10,5]、[5,2]、[2,6]、[5,2,6]。
需要注意的是 [10,5,2] 并不是乘积小于 100 的子数组。
示例 2：

输入：nums = [1,2,3], k = 0
输出：0


提示: 

1 <= nums.length <= 3 * 10^4

1 <= nums[i] <= 1000

0 <= k <= 10^6

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/subarray-product-less-than-k
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

维护区间[l,r-1]使得区间内乘积小于k，每次加上以a[r-1]结尾满足条件的子数组数目即可

### 代码

```c++
class Solution {
public:
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        if(k<=1) return 0;
        int n = nums.size();
        int res = 0;
        int l=0,r=0;
        int prod=1;
        while(r<n){
            int cur = nums[r];
            r++;
            prod = prod*cur;
            while(prod>=k){
                prod = prod/nums[l];
                l++;
            }
            //[l,r-1]区间元素乘积小于k
            //加上以nums[r-1]结尾的所有子数组数目
            res += r-l;
        }
        return res;
    }
};
```

