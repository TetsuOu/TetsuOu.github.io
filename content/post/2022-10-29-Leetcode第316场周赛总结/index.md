---
title:      LeetCode第316场周赛总结
description:   540/6387
date:       2022-10-29
image: post-bg-20221029.png
categories: 
    - leetcode contest
tags:
    
---

![image-20221029225332985](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20221029225332985.png)

## 【模拟】判断两个事件是否存在冲突

### 题目

给你两个字符串数组 `event1` 和 `event2` ，表示发生在同一天的两个闭区间时间段事件，其中：

- `event1 = [startTime1, endTime1]` 且
- `event2 = [startTime2, endTime2]`

事件的时间为有效的 24 小时制且按 `HH:MM` 格式给出。

当两个事件存在某个非空的交集时（即，某些时刻是两个事件都包含的），则认为出现 **冲突** 。

如果两个事件之间存在冲突，返回 `true` ；否则，返回 `false` 。

 

**示例 1：**

```
输入：event1 = ["01:15","02:00"], event2 = ["02:00","03:00"]
输出：true
解释：两个事件在 2:00 出现交集。
```

**示例 2：**

```
输入：event1 = ["01:00","02:00"], event2 = ["01:20","03:00"]
输出：true
解释：两个事件的交集从 01:20 开始，到 02:00 结束。
```

**示例 3：**

```
输入：event1 = ["10:00","11:00"], event2 = ["14:00","15:00"]
输出：false
解释：两个事件不存在交集。
```

 

**提示：**

- `evnet1.length == event2.length == 2.`
- `event1[i].length == event2[i].length == 5`
- `startTime1 <= endTime1`
- `startTime2 <= endTime2`
- 所有事件的时间都按照 `HH:MM` 格式给出

### 解题思路

判断两个区间是否相交

其实直接比较字符串大小即可

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
class Solution {
public:
    int fun(string s){
        int res = 0;
        res += s[4]-'0';
        res += 10*(s[3]-'0');
        int x = ((s[0]-'0')*10 + (s[1]-'0'));
        res += x*60;
        return res;
    }
    bool haveConflict(vector<string>& event1, vector<string>& event2) {
        int x1 = fun(event1[0]),x2 = fun(event1[1]);
        int y1 = fun(event2[0]),y2 = fun(event2[1]);
        if(x1>y2 || x2<y1) return false;
        return true;
    }
};
```

## 【枚举+gcd】最大公因数等于 K 的子数组数目

### 题目

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 `nums` 的子数组中元素的最大公因数等于 `k` 的子数组数目。

**子数组** 是数组中一个连续的非空序列。

**数组的最大公因数** 是能整除数组中所有元素的最大整数。

 

**示例 1：**

```
输入：nums = [9,3,1,2,6,3], k = 3
输出：4
解释：nums 的子数组中，以 3 作为最大公因数的子数组如下：
- [9,3,1,2,6,3]
- [9,3,1,2,6,3]
- [9,3,1,2,6,3]
- [9,3,1,2,6,3]
```

**示例 2：**

```
输入：nums = [4], k = 7
输出：0
解释：不存在以 7 作为最大公因数的子数组。
```

 

**提示：**

- `1 <= nums.length <= 1000`
- `1 <= nums[i], k <= 10^9`

### 解题思路

枚举子数组并计算最大公因数即可 

可惜一开始想太简单了  WA了两发

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
class Solution {
public:
    int subarrayGCD(vector<int>& nums, int k) {
        int n = nums.size();
        int res = 0;
        for(int i=0;i<n;i++){
            if(nums[i] == k) res++;
            int g = nums[i];
            for(int j=i-1;j>=0;j--){
                if(__gcd(g,nums[j])==k) {
                    res++;
                }
                g = __gcd(g,nums[j]);
            }
        }
        return res;
    }
};
```

## 【枚举+数学】使数组相等的最小开销

### 题目

给你两个下标从 **0** 开始的数组 `nums` 和 `cost` ，分别包含 `n` 个 **正** 整数。

你可以执行下面操作 **任意** 次：

- 将 `nums` 中 **任意** 元素增加或者减小 `1` 。

对第 `i` 个元素执行一次操作的开销是 `cost[i]` 。

请你返回使 `nums` 中所有元素 **相等** 的 **最少** 总开销。

 

**示例 1：**

```
输入：nums = [1,3,5,2], cost = [2,3,1,14]
输出：8
解释：我们可以执行以下操作使所有元素变为 2 ：
- 增加第 0 个元素 1 次，开销为 2 。
- 减小第 1 个元素 1 次，开销为 3 。
- 减小第 2 个元素 3 次，开销为 1 + 1 + 1 = 3 。
总开销为 2 + 3 + 3 = 8 。
这是最小开销。
```

**示例 2：**

```
输入：nums = [2,2,2,2,2], cost = [4,2,8,1,3]
输出：0
解释：数组中所有元素已经全部相等，不需要执行额外的操作。
```

 

**提示：**

- `n == nums.length == cost.length`
- `1 <= n <= 10^5`
- `1 <= nums[i], cost[i] <= 10^6`

### 解题思路

可以对nums数组进行排序，枚举将所有元素都变为num[i]所需的cost

**考虑一步到下一步所需的cost的变化即可**  可发现数学规律

RE了一发是 int溢出  记得多乘个1ll

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
class Solution {
public:
    long long minCost(vector<int>& nums, vector<int>& cost) {
        int n = nums.size();
        vector<int> idx(n);
        iota(idx.begin(),idx.end(),0);
        sort(idx.begin(),idx.end(),[&](int a,int b){
            return nums[a]<nums[b];
        });
        ll sum = 0;
        for(auto &x: cost) sum += x;
        ll res = 0, u = 0;
        for(int i=0;i<n;i++){
            u += 1ll*(nums[idx[i]]-nums[idx[0]])*cost[idx[i]];
        }
        res = u;
        ll head = cost[idx[0]], tail = sum - head;
        for(int i=1;i<n;i++){
            ll diff = nums[idx[i]] - nums[idx[i-1]];
            u += diff * head;
            u -= diff * tail;
            head += cost[idx[i]];
            tail -= cost[idx[i]];
            res = min(res, u);
        }
        return res;
    }
};
```

## 【贪心+排序】使数组相似的最少操作次数

### 题目

给你两个正整数数组 `nums` 和 `target` ，两个数组长度相等。

在一次操作中，你可以选择两个 **不同** 的下标 `i` 和 `j` ，其中 `0 <= i, j < nums.length` ，并且：

- 令 `nums[i] = nums[i] + 2` 且
- 令 `nums[j] = nums[j] - 2` 。

如果两个数组中每个元素出现的频率相等，我们称两个数组是 **相似** 的。

请你返回将 `nums` 变得与 `target` 相似的最少操作次数。测试数据保证 `nums` 一定能变得与 `target` 相似。

 

**示例 1：**

```
输入：nums = [8,12,6], target = [2,14,10]
输出：2
解释：可以用两步操作将 nums 变得与 target 相似：
- 选择 i = 0 和 j = 2 ，nums = [10,12,4] 。
- 选择 i = 1 和 j = 2 ，nums = [10,14,2] 。
2 次操作是最少需要的操作次数。
```

**示例 2：**

```
输入：nums = [1,2,5], target = [4,1,3]
输出：1
解释：一步操作可以使 nums 变得与 target 相似：
- 选择 i = 1 和 j = 2 ，nums = [1,4,3] 。
```

**示例 3：**

```
输入：nums = [1,1,1,1,1], target = [1,1,1,1,1]
输出：0
解释：数组 nums 已经与 target 相似。
```

 

**提示：**

- `n == nums.length == target.length`
- `1 <= n <= 10^5`
- `1 <= nums[i], target[i] <= 10^6`
- `nums` 一定可以变得与 `target` 相似。

### 解题思路

每次加减2操作不改变奇偶性，所以可以分别对奇偶进行讨论

贪心的想，对原数组和目标数组**进行排序后，一一对应**，增的操作次数一定和减的操作次数相等



### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    long long makeSimilar(vector<int>& nums, vector<int>& target) {
        vector<int> nums1,nums2,target1,target2;
        for(auto &x: nums){
            if(x&1) nums1.push_back(x);
            else nums2.push_back(x);
        }
        for(auto &x: target){
            if(x&1) target1.push_back(x);
            else target2.push_back(x);
        }
        sort(nums1.begin(),nums1.end());
        sort(nums2.begin(),nums2.end());
        sort(target1.begin(),target1.end());
        sort(target2.begin(),target2.end());
        long long u=0,v=0;
        for(int i=0;i<nums1.size();i++){
            if(nums1[i]>target1[i]){
                u += (nums1[i]-target1[i])/2;
            }
        }
        for(int i=0;i<nums2.size();i++){
            if(nums2[i]>target2[i]){
                v += (nums2[i]-target2[i])/2;
            }
        }
        return u+v;
    }
};
```

