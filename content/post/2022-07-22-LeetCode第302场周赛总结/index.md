---
title:      LeetCode第302场周赛总结
description:   1049/7092
date:       2022-07-22
image: post-bg-20220722.png
categories:
    - leetcode contest
tags:
    
---

![image-20220722155815761](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220722155815761.png)

## 【模拟】数组能形成多少数对

### 题目

给你一个下标从 **0** 开始的整数数组 `nums` 。在一步操作中，你可以执行以下步骤：

- 从 `nums` 选出 **两个** **相等的** 整数
- 从 `nums` 中移除这两个整数，形成一个 **数对**

请你在 `nums` 上多次执行此操作直到无法继续执行。

返回一个下标从 **0** 开始、长度为 `2` 的整数数组 `answer` 作为答案，其中 `answer[0]` 是形成的数对数目，`answer[1]` 是对 `nums` 尽可能执行上述操作后剩下的整数数目。

 

**示例 1：**

```
输入：nums = [1,3,2,1,3,2,2]
输出：[3,1]
解释：
nums[0] 和 nums[3] 形成一个数对，并从 nums 中移除，nums = [3,2,3,2,2] 。
nums[0] 和 nums[2] 形成一个数对，并从 nums 中移除，nums = [2,2,2] 。
nums[0] 和 nums[1] 形成一个数对，并从 nums 中移除，nums = [2] 。
无法形成更多数对。总共形成 3 个数对，nums 中剩下 1 个数字。
```

**示例 2：**

```
输入：nums = [1,1]
输出：[1,0]
解释：nums[0] 和 nums[1] 形成一个数对，并从 nums 中移除，nums = [] 。
无法形成更多数对。总共形成 1 个数对，nums 中剩下 0 个数字。
```

**示例 3：**

```
输入：nums = [0]
输出：[0,1]
解释：无法形成数对，nums 中剩下 1 个数字。
```

 

**提示：**

- `1 <= nums.length <= 100`
- `0 <= nums[i] <= 100`

### 解题思路

按题意模拟即可。

### 代码

```c++
class Solution {
public:
    vector<int> numberOfPairs(vector<int>& nums) {
        int cnt=0,rst=nums.size();
        int a[101];
        memset(a,0,sizeof(a));
        for(auto &x: nums){
            a[x]++;
            if(a[x]>=2) cnt++,rst-=2,a[x]=0;
        }
        return {cnt,rst};
    }
};
```

## 【枚举】数位和相等数对的最大和

### 题目

给你一个下标从 **0** 开始的数组 `nums` ，数组中的元素都是 **正** 整数。请你选出两个下标 `i` 和 `j`（`i != j`），且 `nums[i]` 的数位和 与 `nums[j]` 的数位和相等。

请你找出所有满足条件的下标 `i` 和 `j` ，找出并返回 `nums[i] + nums[j]` 可以得到的 **最大值** *。*

 

**示例 1：**

```
输入：nums = [18,43,36,13,7]
输出：54
解释：满足条件的数对 (i, j) 为：
- (0, 2) ，两个数字的数位和都是 9 ，相加得到 18 + 36 = 54 。
- (1, 4) ，两个数字的数位和都是 7 ，相加得到 43 + 7 = 50 。
所以可以获得的最大和是 54 。
```

**示例 2：**

```
输入：nums = [10,12,19,14]
输出：-1
解释：不存在满足条件的数对，返回 -1 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`

### 解题思路

只有数位和相同的数才能相加，因此可统计不同数位和的数

`multiset<int> G[101];` G[i]表示数位和为i的数的集合

WA了一发是忽略了可能存在相同的数，一开始声明的是set，应该用multiset

### 代码

```c++
class Solution {
public:
    int maximumSum(vector<int>& nums) {
        int n = nums.size();
        multiset<int> G[101];
        for(auto &x: nums){
            int cur=0,num=x;
            while(x){
                cur+=x%10;
                x/=10;
            }
            G[cur].insert(num);
        }
        
        int res = -1;
        for(int i=0;i<101;i++){
            if(G[i].size()<=1) continue;
            int maxv = *G[i].rbegin();
            auto it = ++G[i].rbegin();
            int secv = *it;
            res = max(res, maxv+secv);
        }
        return res;
        
    }
};
```

## 【排序】裁剪数字后查询第 K 小的数字

### 题目

给你一个下标从 **0** 开始的字符串数组 `nums` ，其中每个字符串 **长度相等** 且只包含数字。

再给你一个下标从 **0** 开始的二维整数数组 `queries` ，其中 `queries[i] = [ki, trimi]` 。对于每个 `queries[i]` ，你需要：

- 将 `nums` 中每个数字 **裁剪** 到剩下 **最右边** `trimi` 个数位。
- 在裁剪过后的数字中，找到 `nums` 中第 `ki` 小数字对应的 **下标** 。如果两个裁剪后数字一样大，那么下标 **更小** 的数字视为更小的数字。
- 将 `nums` 中每个数字恢复到原本字符串。

请你返回一个长度与 `queries` 相等的数组 `answer`，其中 `answer[i]`是第 `i` 次查询的结果。

**提示：**

- 裁剪到剩下 `x` 个数位的意思是不断删除最左边的数位，直到剩下 `x` 个数位。
- `nums` 中的字符串可能会有前导 0 。

 

**示例 1：**

```
输入：nums = ["102","473","251","814"], queries = [[1,1],[2,3],[4,2],[1,2]]
输出：[2,2,1,0]
解释：
1. 裁剪到只剩 1 个数位后，nums = ["2","3","1","4"] 。最小的数字是 1 ，下标为 2 。
2. 裁剪到剩 3 个数位后，nums 没有变化。第 2 小的数字是 251 ，下标为 2 。
3. 裁剪到剩 2 个数位后，nums = ["02","73","51","14"] 。第 4 小的数字是 73 ，下标为 1 。
4. 裁剪到剩 2 个数位后，最小数字是 2 ，下标为 0 。
   注意，裁剪后数字 "02" 值为 2 。
```

**示例 2：**

```
输入：nums = ["24","37","96","04"], queries = [[2,1],[2,2]]
输出：[3,0]
解释：
1. 裁剪到剩 1 个数位，nums = ["4","7","6","4"] 。第 2 小的数字是 4 ，下标为 3 。
   有两个 4 ，下标为 0 的 4 视为小于下标为 3 的 4 。
2. 裁剪到剩 2 个数位，nums 不变。第二小的数字是 24 ，下标为 0 。
```

 

**提示：**

- `1 <= nums.length <= 100`
- `1 <= nums[i].length <= 100`
- `nums[i]` 只包含数字。
- 所有 `nums[i].length` 的长度 **相同** 。
- `1 <= queries.length <= 100`
- `queries[i].length == 2`
- `1 <= ki <= nums.length`
- `1 <= trimi <= nums[0].length`

### 解题思路

看数据范围，选择暴力，即先求出最右边trimi个数位的数字数组，再对其进行排序，然后即可查询第K小的数字

WA了第一发，画蛇添足的中间将字符串转换为数字后再排序，由于num.length可达100，stoi转int失败，其实用不着转int，按字典序排一样的。

TLE了第二发，排序中使用了lambda函数，参数应该用引用形式

### 代码

```c++
class Solution {
public:
    vector<int> smallestTrimmedNumbers(vector<string>& nums, vector<vector<int>>& queries) {
        int n = nums.size();
        int m = nums[0].size();
        vector<pair<string,int>> G[m+1];//G[i] 最右边i个数的数组
        
        for(int k=0;k<n;k++){
            string num = nums[k];
            string str;
            for(int i=m;i>=1;i--){
                str = num[i-1]+str;
                G[m-i+1].push_back(make_pair(str, k));
            }
            // for(int i=1;i<=m;i++){
            //     str = num.substr(m-1-i+1,i);
            //     G[i].push_back(make_pair(str, k));
            // }
        }
        
        for(int i=1;i<=m;i++){
            sort(G[i].begin(), G[i].end(), [&](pair<string,int>& a, pair<string,int>& b){
                if(a.first!=b.first) return a.first<b.first;
                return a.second<b.second;
            });
        }
        
        vector<int> res;
        for(auto &x: queries){
            int k=x[0],trim=x[1];
            res.push_back(G[trim][k-1].second);
        }
        
        return res;
    }
};
```

## 【数学】使数组可以被整除的最少删除次数

### 题目

给你两个正整数数组 `nums` 和 `numsDivide` 。你可以从 `nums` 中删除任意数目的元素。

请你返回使 `nums` 中 **最小** 元素可以整除 `numsDivide` 中所有元素的 **最少** 删除次数。如果无法得到这样的元素，返回 `-1` 。

如果 `y % x == 0` ，那么我们说整数 `x` 整除 `y` 。

 

**示例 1：**

```
输入：nums = [2,3,2,4,3], numsDivide = [9,6,9,3,15]
输出：2
解释：
[2,3,2,4,3] 中最小元素是 2 ，它无法整除 numsDivide 中所有元素。
我们从 nums 中删除 2 个大小为 2 的元素，得到 nums = [3,4,3] 。
[3,4,3] 中最小元素为 3 ，它可以整除 numsDivide 中所有元素。
可以证明 2 是最少删除次数。
```

**示例 2：**

```
输入：nums = [4,3,6], numsDivide = [8,2,6,10]
输出：-1
解释：
我们想 nums 中的最小元素可以整除 numsDivide 中的所有元素。
没有任何办法可以达到这一目的。
```

 

**提示：**

- `1 <= nums.length, numsDivide.length <= 10^5`
- `1 <= nums[i], numsDivide[i] <= 10^9`

### 解题思路

水题。求出numsDivide的最大公约数

然后每次删nums中的最小元素看是否满足条件

### 代码

```c++
class Solution {
public:
    int minOperations(vector<int>& nums, vector<int>& numsDivide) {
        int Gcd = numsDivide[0];
        for(int i=1;i<numsDivide.size();i++) Gcd = __gcd(Gcd,numsDivide[i]);
        // cout<<Gcd<<endl;
        sort(nums.begin(),nums.end());
        int res = -1;
        if(Gcd%nums[0]==0) {
            res = 0;
            return res;
        }
        
        int n = nums.size(),cur=0;
        
        for(int i=0;i<n;i++){
            if(Gcd%nums[i]!=0) {
                cur++;
            }else{
                return cur;
            }
        }
        
        return -1;
    }
};
```

