---
title:      LeetCode第315场周赛总结
description:   ---/6490
date:       2022-10-23
image:  post-bg-20221023.png
categories:
    - leetcode contest
tags:
    
---

没打

## 【模拟】与对应负数同时存在的最大正整数

### 题目

给你一个 **不包含** 任何零的整数数组 `nums` ，找出自身与对应的负数都在数组中存在的最大正整数 `k` 。

返回正整数 `k` ，如果不存在这样的整数，返回 `-1` 。

 

**示例 1：**

```
输入：nums = [-1,2,-3,3]
输出：3
解释：3 是数组中唯一一个满足题目要求的 k 。
```

**示例 2：**

```
输入：nums = [-1,10,6,7,-7,1]
输出：7
解释：数组中存在 1 和 7 对应的负数，7 的值更大。
```

**示例 3：**

```
输入：nums = [-10,8,6,7,-2,-3]
输出：-1
解释：不存在满足题目要求的 k ，返回 -1 。
```

 

**提示：**

- `1 <= nums.length <= 1000`
- `-1000 <= nums[i] <= 1000`
- `nums[i] != 0`

### 解题思路

按题意模拟即可

### 代码

```c++
class Solution {
public:
    int findMaxK(vector<int>& nums) {
        int res = -1;
        unordered_set<int> tab;
        for(auto &x:nums) tab.insert(x);
        for(auto &x: nums){
            if(x>0 && tab.count(-x)) res = max(res, x);
        }
        return res;
    }
};
```

## 【模拟】反转之后不同整数的数目

### 题目

给你一个由 **正** 整数组成的数组 `nums` 。

你必须取出数组中的每个整数，**反转其中每个数位**，并将反转后得到的数字添加到数组的末尾。这一操作只针对 `nums` 中原有的整数执行。

返回结果数组中 **不同** 整数的数目。

 

**示例 1：**

```
输入：nums = [1,13,10,12,31]
输出：6
解释：反转每个数字后，结果数组是 [1,13,10,12,31,1,31,1,21,13] 。
反转后得到的数字添加到数组的末尾并按斜体加粗表示。注意对于整数 10 ，反转之后会变成 01 ，即 1 。
数组中不同整数的数目为 6（数字 1、10、12、13、21 和 31）。
```

**示例 2：**

```
输入：nums = [2,2,2]
输出：1
解释：反转每个数字后，结果数组是 [2,2,2,2,2,2] 。
数组中不同整数的数目为 1（数字 2）。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^6`

### 解题思路

按题意模拟即可

### 代码

```c++
class Solution {
public:
    int countDistinctIntegers(vector<int>& nums) {
        set<int> tab;
        for(auto x: nums){
            tab.insert(x);
            int u = 0;
            while(x){
                int tail = x%10;
                x = x/10;
                u = 10*u + tail;
            }
            tab.insert(u);
        }
        return tab.size();
    }
};
```

## 【枚举】反转之后的数字和

### 题目

给你一个 **非负** 整数 `num` 。如果存在某个 **非负** 整数 `k` 满足 `k + reverse(k) = num` ，则返回 `true` ；否则，返回 `false` 。

`reverse(k)` 表示 `k` 反转每个数位后得到的数字。

 

**示例 1：**

```
输入：num = 443
输出：true
解释：172 + 271 = 443 ，所以返回 true 。
```

**示例 2：**

```
输入：num = 63
输出：false
解释：63 不能表示为非负整数及其反转后数字之和，返回 false 。
```

**示例 3：**

```
输入：num = 181
输出：true
解释：140 + 041 = 181 ，所以返回 true 。注意，反转后的数字可能包含前导零。
```

 

**提示：**

- `0 <= num <= 10^5`

### 解题思路

枚举即可

### 代码

```c++
class Solution {
public:
    int rev(int num){
        int res = 0;
        while(num){
            res = 10*res+num%10;
            num/=10;
        }
        return res;
    }
    bool sumOfNumberAndReverse(int num) {
        for(int i=0;i<=num;i++){
            if(i+rev(i) == num) return true;
        }
        return false;
    }
};
```

## 【枚举+统计子数组】统计定界子数组的数目

### 题目

给你一个整数数组 `nums` 和两个整数 `minK` 以及 `maxK` 。

`nums` 的定界子数组是满足下述条件的一个子数组：

- 子数组中的 **最小值** 等于 `minK` 。
- 子数组中的 **最大值** 等于 `maxK` 。

返回定界子数组的数目。

子数组是数组中的一个连续部分。

 

**示例 1：**

```
输入：nums = [1,3,5,2,7,5], minK = 1, maxK = 5
输出：2
解释：定界子数组是 [1,3,5] 和 [1,3,5,2] 。
```

**示例 2：**

```
输入：nums = [1,1,1,1], minK = 1, maxK = 1
输出：10
解释：nums 的每个子数组都是一个定界子数组。共有 10 个子数组。
```

 

**提示：**

- `2 <= nums.length <= 10^5`
- `1 <= nums[i], minK, maxK <= 10^6`

### 解题思路

考虑以nums[i]结尾的合法数组，是否合法取决于：[0..i]范围内的minK、maxK出现情况，以及[minK, maxK]范围之外的数的出现情况

那么，维护三个东西：存储minK出现位置的数组，存储maxK出现位置的数组，存储范围之外的数出现位置的数组

（实际上，只需要维护出现的最后一个位置即可）



### 代码

```c++
class Solution {
public:
    long long countSubarrays(vector<int>& nums, int minK, int maxK) {
        int n = nums.size();
        vector<int> minvec,maxvec,outvec;
        long long res = 0;
        for(int i=0;i<n;i++){
            if(nums[i]<minK || nums[i]>maxK){
                outvec.push_back(i);
                minvec.clear();
                maxvec.clear();
                continue;
            }
            if(nums[i] == minK) {
                minvec.push_back(i);
            }
            if(nums[i] == maxK) {
                maxvec.push_back(i);
            }
            if(minvec.empty() || maxvec.empty()) continue;
            int x = outvec.empty()?-1:outvec.back();
            int u = min(minvec.back(), maxvec.back());
            // cout<<i<<' '<<u-x<<endl;
            res += u-x;

        }
        return res;

    }
};
```

### 偷学代码

![](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20221023222840374.png)

```c++
class Solution {
public:
    long long countSubarrays(vector<int> &nums, int min_k, int max_k) {
        long long ans = 0L;
        int n = nums.size(), min_i = -1, max_i = -1, i0 = -1;
        for (int i = 0; i < n; ++i) {
            int x = nums[i];
            if (x == min_k) min_i = i;
            if (x == max_k) max_i = i;
            if (x < min_k || x > max_k) i0 = i; // 子数组不能包含 nums[i0]
            ans += max(min(min_i, max_i) - i0, 0);
        }
        return ans;
    }
};
```

