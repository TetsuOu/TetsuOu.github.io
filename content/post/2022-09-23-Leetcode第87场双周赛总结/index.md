---
title:      LeetCode第87场双周赛总结
description:   385/4005
date:       2022-09-23
image: post-bg-20220923.png
categories: 
    - leetcode contest
tags:
    - 思维
---

![image-20220923225405893](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220923225405893.png)

## 【模拟】统计共同度过的日子数

### 题目

Alice 和 Bob 计划分别去罗马开会。

给你四个字符串 `arriveAlice` ，`leaveAlice` ，`arriveBob` 和 `leaveBob` 。Alice 会在日期 `arriveAlice` 到 `leaveAlice` 之间在城市里（**日期为闭区间**），而 Bob 在日期 `arriveBob` 到 `leaveBob` 之间在城市里（**日期为闭区间**）。每个字符串都包含 5 个字符，格式为 `"MM-DD"` ，对应着一个日期的月和日。

请你返回 Alice和 Bob 同时在罗马的天数。

你可以假设所有日期都在 **同一个** 自然年，而且 **不是** 闰年。每个月份的天数分别为：`[31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]` 。

 

**示例 1：**

```
输入：arriveAlice = "08-15", leaveAlice = "08-18", arriveBob = "08-16", leaveBob = "08-19"
输出：3
解释：Alice 从 8 月 15 号到 8 月 18 号在罗马。Bob 从 8 月 16 号到 8 月 19 号在罗马，他们同时在罗马的日期为 8 月 16、17 和 18 号。所以答案为 3 。
```

**示例 2：**

```
输入：arriveAlice = "10-01", leaveAlice = "10-31", arriveBob = "11-01", leaveBob = "12-31"
输出：0
解释：Alice 和 Bob 没有同时在罗马的日子，所以我们返回 0 。
```

 

**提示：**

- 所有日期的格式均为 `"MM-DD"` 。
- Alice 和 Bob 的到达日期都 **早于或等于** 他们的离开日期。
- 题目测试用例所给出的日期均为 **非闰年** 的有效日期。

### 解题思路

这种模拟题用C++模拟挺费时的，尤其是在浏览器上码代码

求两个区间[x1,y1]，[x2,y2]的重叠部分，可以用min(y1,y2)-max(x1,x2)，看是否大于0

### 代码

```c++
class Solution {
public:
    int countDaysTogether(string arriveAlice, string leaveAlice, string arriveBob, string leaveBob) {
        int a[12] = {31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
        int sum[13] ={0};
        for(int i=1;i<=12;i++) sum[i] = sum[i-1]+a[i-1];
        // for(int i=1;i<=12;i++) cout<<sum[i]<<' ';cout<<endl;
        
        
        int a1 = (arriveAlice[0]-'0')*10+(arriveAlice[1]-'0');
        int a2 = (leaveAlice[0]-'0')*10+(leaveAlice[1]-'0');
        int b1 = (arriveAlice[3]-'0')*10+(arriveAlice[4]-'0');
        int b2 = (leaveAlice[3]-'0')*10+(leaveAlice[4]-'0');
        int x1 = sum[a1-1]+b1,y1=sum[a2-1]+b2;
        // cout<<x1<<' '<<y1<<endl;
        
        a1 = (arriveBob[0]-'0')*10+(arriveBob[1]-'0');
        a2 = (leaveBob[0]-'0')*10+(leaveBob[1]-'0');
        b1 = (arriveBob[3]-'0')*10+(arriveBob[4]-'0');
        b2 = (leaveBob[3]-'0')*10+(leaveBob[4]-'0');
        int x2 = sum[a1-1]+b1,y2=sum[a2-1]+b2;
        // cout<<x2<<' '<<y2<<endl;
        int res = 0;
        
        // vector<int> vis(380,0);
        // for(int i=x1;i<=y1;i++) vis[i]++;
        // for(int i=x2;i<=y2;i++) vis[i]++;
        // for(int i=1;i<=365;i++) if(vis[i]>=2) res++;
        
        return max(0,min(y2,y1)-max(x2,x1)+1);
        
    }
};
```

## 【贪心】运动员和训练师的最大匹配数

### 题目

给你一个下标从 **0** 开始的整数数组 `players` ，其中 `players[i]` 表示第 `i` 名运动员的 **能力** 值，同时给你一个下标从 **0** 开始的整数数组 `trainers` ，其中 `trainers[j]` 表示第 `j` 名训练师的 **训练能力值** 。

如果第 `i` 名运动员的能力值 **小于等于** 第 `j` 名训练师的能力值，那么第 `i` 名运动员可以 **匹配** 第 `j` 名训练师。除此以外，每名运动员至多可以匹配一位训练师，每位训练师最多可以匹配一位运动员。

请你返回满足上述要求 `players` 和 `trainers` 的 **最大** 匹配数。

 

**示例 1：**

```
输入：players = [4,7,9], trainers = [8,2,5,8]
输出：2
解释：
得到两个匹配的一种方案是：
- players[0] 与 trainers[0] 匹配，因为 4 <= 8 。
- players[1] 与 trainers[3] 匹配，因为 7 <= 8 。
可以证明 2 是可以形成的最大匹配数。
```

**示例 2：**

```
输入：players = [1,1,1], trainers = [10]
输出：1
解释：
训练师可以匹配所有 3 个运动员
每个运动员至多只能匹配一个训练师，所以最大答案是 1 。
```

 

**提示：**

- `1 <= players.length, trainers.length <= 10^5`
- `1 <= players[i], trainers[j] <= 10^9`

### 解题思路

对运动员按能力值从小到大排序，肯定先对能力值小的运行员选择训练师，训练师的选择也是尽可能的选小的，可以利用双指针或者堆进行维护

### 代码

```c++
class Solution {
public:
    int matchPlayersAndTrainers(vector<int>& players, vector<int>& trainers) {
        int n = players.size();
        sort(players.begin(),players.end());
        priority_queue<int,vector<int>,greater<int>> pq;
        for(auto &x: trainers) pq.push(x);
        int res=0;
        for(int i=0;i<n;i++){
            while(!pq.empty() && pq.top()<players[i]) pq.pop();
            if(pq.empty()) break;
            pq.pop();res++;
            
        }
        return res;
        
    }
};
```

## 【双指针】按位或最大的最小子数组长度

### 题目

给你一个长度为 `n` 下标从 **0** 开始的数组 `nums` ，数组中所有数字均为非负整数。对于 `0` 到 `n - 1` 之间的每一个下标 `i` ，你需要找出 `nums` 中一个 **最小** 非空子数组，它的起始位置为 `i` （包含这个位置），同时有 **最大** 的 **按位或运算值** 。

- 换言之，令 $B_{ij}$ 表示子数组 `nums[i...j]` 的按位或运算的结果，你需要找到一个起始位置为 `i` 的最小子数组，这个子数组的按位或运算的结果等于 $max(B_{ik})$ ，其中 `i <= k <= n - 1` 。

一个数组的按位或运算值是这个数组里所有数字按位或运算的结果。

请你返回一个大小为 `n` 的整数数组 `answer`，其中 `answer[i]`是开始位置为 `i` ，按位或运算结果最大，且 **最短** 子数组的长度。

**子数组** 是数组里一段连续非空元素组成的序列。

 

**示例 1：**

```
输入：nums = [1,0,2,1,3]
输出：[3,3,2,2,1]
解释：
任何位置开始，最大按位或运算的结果都是 3 。
- 下标 0 处，能得到结果 3 的最短子数组是 [1,0,2] 。
- 下标 1 处，能得到结果 3 的最短子数组是 [0,2,1] 。
- 下标 2 处，能得到结果 3 的最短子数组是 [2,1] 。
- 下标 3 处，能得到结果 3 的最短子数组是 [1,3] 。
- 下标 4 处，能得到结果 3 的最短子数组是 [3] 。
所以我们返回 [3,3,2,2,1] 。
```

**示例 2：**

```
输入：nums = [1,2]
输出：[2,1]
解释：
下标 0 处，能得到最大按位或运算值的最短子数组长度为 2 。
下标 1 处，能得到最大按位或运算值的最短子数组长度为 1 。
所以我们返回 [2,1] 。
```

 

**提示：**

- `n == nums.length`
- `1 <= n <= 10^5`
- `0 <= nums[i] <= 10^9`

### 解题思路

赛时想到的思路是 倒序处理每一个数

对于最后一个数，答案肯定是1

维护倒序遍历过程中的**最大或运算值maxv**，记录尾指针tail，利用双指针维护拥有最大或运算值的结尾。

什么时候尾指针tail可以减小呢，**当减去尾指针元素的贡献时，maxv不会缩小则可以减小**

WA了一发是写错了一个条件判断

### 代码

```c++
class Solution {
public:
    vector<int> smallestSubarrays(vector<int>& nums) {
        int n = nums.size();
        vector<int> res(n,0);
        res[n-1] = 1;//最后一个元素的答案必定是1
        int maxv = nums[n-1];
        int curv = nums[n-1];
        int tail = n-1;
        
        vector<int> a(32,0);
        for(int i=0;i<32;i++){
            if((curv >> i)&1)  a[i]++;
        }
        auto getv = [&]()->int{
             int v = 0;
            for(int i=0;i<32;i++){
                if(a[i]>0) v += (1<<i);
            }
            return v;
        };
        
        
        for(int i=n-2;i>=0;i--){
            for(int j=0;j<32;j++) if((nums[i]>>j)&1) a[j]++;
            curv = getv();
            maxv = max(maxv, curv);
           
            while(tail>i){
                for(int j=0;j<32;j++) if((nums[tail]>>j)&1) a[j]--;
                if(getv() < maxv ){
                    for(int j=0;j<32;j++) if((nums[tail]>>j)&1) a[j]++;
                    break;
                }
                tail--;
            }
            res[i] = tail-i+1;
        }
        
        
        return res;
    }
};
```

## 【思维+枚举】完成所有交易的初始最少钱数

### 题目

给你一个下标从 **0** 开始的二维整数数组 `transactions`，其中`transactions[i] = [costi, cashbacki]` 。

数组描述了若干笔交易。其中每笔交易必须以 **某种顺序** 恰好完成一次。在任意一个时刻，你有一定数目的钱 `money` ，为了完成交易 `i` ，`money >= costi` 这个条件必须为真。执行交易后，你的钱数 `money` 变成 `money - costi + cashbacki` 。

请你返回 **任意一种** 交易顺序下，你都能完成所有交易的最少钱数 `money` 是多少。

 

**示例 1：**

```
输入：transactions = [[2,1],[5,0],[4,2]]
输出：10
解释：
刚开始 money = 10 ，交易可以以任意顺序进行。
可以证明如果 money < 10 ，那么某些交易无法进行。
```

**示例 2：**

```
输入：transactions = [[3,0],[0,3]]
输出：3
解释：
- 如果交易执行的顺序是 [[3,0],[0,3]] ，完成所有交易需要的最少钱数是 3 。
- 如果交易执行的顺序是 [[0,3],[3,0]] ，完成所有交易需要的最少钱数是 0 。
所以，刚开始钱数为 3 ，任意顺序下交易都可以全部完成。
```

 

**提示：**

- `1 <= transactions.length <= 10^5`
- `transactions[i].length == 2`
- `0 <= costi, cashbacki <= 10^9`

### 解题思路

对于每个交易[cost, cashback]，假定交易后的钱数为y，那么根据题意有，交易前的钱数x>=y+cost-cashback，为了满足最小，x应当取y+cost-castback。当然，上述是在cost>=cashback的情况下。那如果cost<cashback呢，这时候，x应当满足x>=max(cost,y)。

题中所求的是所有情况下，都要能完成交易的最少初始钱数。

倒过来进行考虑，枚举第i个交易作为最后一个交易，此时最后交易前的钱数y = cost_i，逆推过去，对于其他的交易，如果cost>=cashback，y应该增大，累加即可，如果cost<cashback，应该令其不影响y，取0即可



### 代码

```c++
class Solution {
public:
    long long minimumMoney(vector<vector<int>>& transactions) {
        int n = transactions.size();
        
        vector<long long> ini(n,0), diff(n,0);
        long long sum=0;
        for(int i=0;i<n;i++) {
            ini[i] = transactions[i][0];
            diff[i] = transactions[i][0] - transactions[i][1];
            diff[i] = max(1ll*0,diff[i]);
            sum += diff[i];
        }
        
        long long res = 0;
        for(int i=0;i<n;i++){//以第i件商品做最后一件
            long long cur = sum-diff[i] + ini[i];
            res = max({res, cur, ini[i]});
        }
        return res;
    }
};
```

