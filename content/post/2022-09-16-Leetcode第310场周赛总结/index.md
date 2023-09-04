---
title:      LeetCode第310场周赛总结
description:   1591/6081
date:       2022-09-16
image: post-bg-20220916.png
categories:
    - leetcode contest
tags:
    - 差分数组
    - 线段树
    - 动态规划
---

![image-20220916162741453](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220916162741453.png)

久违的掉大分

## 【模拟】出现最频繁的偶数元素

### 题目

给你一个整数数组 `nums` ，返回出现最频繁的偶数元素。

如果存在多个满足条件的元素，只需要返回 **最小** 的一个。如果不存在这样的元素，返回 `-1` 。

 

**示例 1：**

```
输入：nums = [0,1,2,2,4,4,1]
输出：2
解释：
数组中的偶数元素为 0、2 和 4 ，在这些元素中，2 和 4 出现次数最多。
返回最小的那个，即返回 2 。
```

**示例 2：**

```
输入：nums = [4,4,4,9,2,4]
输出：4
解释：4 是出现最频繁的偶数元素。
```

**示例 3：**

```
输入：nums = [29,47,21,41,13,37,25,7]
输出：-1
解释：不存在偶数元素。
```

 

**提示：**

- `1 <= nums.length <= 2000`
- `0 <= nums[i] <= 10^5`

### 解题思路

按题意模拟即可	

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 105;
class Solution {
public:
    int mostFrequentEven(vector<int>& nums) {
        int n = nums.size();
        map<int,int> tab;
        for(auto &x: nums){
            tab[x]++;
        }
        int cnt=0,res=-1;
        for(auto &x:tab){
            if(x.first&1) continue;
            if(x.second>cnt){
                cnt=x.second;
                res = x.first;
            }
        }
        return res;
    }
};
```

## 【贪心】子字符串的最优划分

### 题目

给你一个字符串 `s` ，请你将该字符串划分成一个或多个 **子字符串** ，并满足每个子字符串中的字符都是 **唯一** 的。也就是说，在单个子字符串中，字母的出现次数都不超过 **一次** 。

满足题目要求的情况下，返回 **最少** 需要划分多少个子字符串*。*

注意，划分后，原字符串中的每个字符都应该恰好属于一个子字符串。

 

**示例 1：**

```
输入：s = "abacaba"
输出：4
解释：
两种可行的划分方法分别是 ("a","ba","cab","a") 和 ("ab","a","ca","ba") 。
可以证明最少需要划分 4 个子字符串。
```

**示例 2：**

```
输入：s = "ssssss"
输出：6
解释：
只存在一种可行的划分方法 ("s","s","s","s","s","s") 。
```

 

**提示：**

- `1 <= s.length <= 10^5`
- `s` 仅由小写英文字母组成

### 解题思路

每次出现重复字母时再新起一个划分

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    int partitionString(string s) {
        int n = s.size();
        int res=0;
        map<char,int> tab;
        for(int i=0;i<n;i++){
            if(!tab.count(s[i])){
                tab[s[i]]=i;
            }else{
                res++;
                tab.clear();
                tab[s[i]]=i;
            }
        }
        if(!tab.empty()) res++;
        return res;
    }
};
```

## 【二分/贪心+优先队列/差分数组】将区间分为最少组数

### 题目

给你一个二维整数数组 `intervals` ，其中 `intervals[i] = [lefti, righti]` 表示 **闭** 区间 `[lefti, righti]` 。

你需要将 `intervals` 划分为一个或者多个区间 **组** ，每个区间 **只** 属于一个组，且同一个组中任意两个区间 **不相交** 。

请你返回 **最少** 需要划分成多少个组。

如果两个区间覆盖的范围有重叠（即至少有一个公共数字），那么我们称这两个区间是 **相交** 的。比方说区间 `[1, 5]` 和 `[5, 8]` 相交。

 

**示例 1：**

```
输入：intervals = [[5,10],[6,8],[1,5],[2,3],[1,10]]
输出：3
解释：我们可以将区间划分为如下的区间组：
- 第 1 组：[1, 5] ，[6, 8] 。
- 第 2 组：[2, 3] ，[5, 10] 。
- 第 3 组：[1, 10] 。
可以证明无法将区间划分为少于 3 个组。
```

**示例 2：**

```
输入：intervals = [[1,3],[5,6],[8,10],[11,13]]
输出：1
解释：所有区间互不相交，所以我们可以把它们全部放在一个组内。
```

 

**提示：**

- `1 <= intervals.length <= 10^5`
- `intervals[i].length == 2`
- `1 <= lefti <= righti <= 10^6`

### 解题思路

经典贪心。但是没印象。

赛时用的二分。将区间排序（第一维升序，第二维升序），考虑划分成k个组是否ok。对于第i个区间，在k个组中找到第一个结尾最小且不冲突的区间，若不存在则不ok，若存在，则将第i个区间放在该组后面。

一开始想错了，还以为这个也能二分，最后就是二分套二分，但很显然不是，然后想到了用线段树维护每个组的最小结尾，并记录相应的id。

然后还RE了一发，是因为自定义排序那儿，手误把a[1]<b[1]打成了b[0]<b[1]。

其实，这根本用不到二分，按上面的想，如果不存在合法的组，直接新建一个组即可。



更进一步，直接用一个优先队列维护即可。



> 另外一种思路是转换成上下车模型，每个区间看成一个人，他在 left 时刻上车，right+1 时刻下车，最后答案为同时在车上的人数的最大值。这可以用差分数组实现
>



### 代码

#### 二分

```c++
#include<bits/stdc++.h>
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
using namespace std;
const int MAXN = 1e5+5;
struct node{
    int minv,idx;
}T[MAXN<<2];
void push_up(int p){
    T[p].minv = min(T[ls(p)].minv, T[rs(p)].minv);
    T[p].idx = T[ls(p)].minv<=T[rs(p)].minv?T[ls(p)].idx:T[rs(p)].idx;
}
void build(int p, int l, int r){
    if(l==r){
        T[p].minv = 0;
        T[p].idx=l;
        return ;
    }
    int mid=(l+r)>>1;
    build(ls(p),l,mid);
    build(rs(p),mid+1,r);
    push_up(p);
}
void update(int p, int l, int r, int lr, int val){
    if(l==r && l==lr){
        T[p].minv = val;
        return;
    }
    int mid=(l+r)>>1;
    if(lr<=mid) update(ls(p),l,mid,lr,val);
    else update(rs(p),mid+1,r,lr,val);
    push_up(p);
}
int query(int p, int l, int r, int val){
    if(T[p].minv>=val) return -1;
    if(l==r) return T[p].idx;
    int mid=(l+r)>>1;
    if(T[ls(p)].minv<val) return query(ls(p),l,mid,val);
    else return query(rs(p),mid+1,r,val);
}
class Solution {
public:
    int minGroups(vector<vector<int>>& intervals) {
        int n = intervals.size();
        sort(intervals.begin(), intervals.end(),[&](vector<int>& a, vector<int>&b){
            if(a[0]!=a[1]) return a[0]<b[0];
            return a[1]<b[1];
        });
       
        auto C =[&](int k)->bool{
            build(1,1,k);
            for(int i=0;i<n;i++){
                int idx = query(1,1,k,intervals[i][0]);
                if(idx==-1) return false;
                update(1,1,k,idx,intervals[i][1]);
            }
            return true;
        };

        int left=1,right=n+1;
        while(left<right){
            int mid=(left+right)>>1;
            if(C(mid)) right = mid;
            else left = mid+1;
        }
        return right;
        // return 0;
    }
};
```

#### 优先队列

```c++
class Solution {
public:
    int minGroups(vector<vector<int>>& intervals) {
        sort(intervals.begin(),intervals.end(),[](auto &a,auto &b){
            if(a[0]!=b[0]) return a[0]<b[0];
            return a[1]<b[1];
        });
        priority_queue<int,vector<int>,greater<int>> pq;
        for(auto &inter: intervals){
            if(!pq.empty() && pq.top()<inter[0]) pq.pop();
            pq.emplace(inter[1]);
            
        }   
        return pq.size();
    }
};
```

#### 差分

```c++
class Solution {
public:
    int minGroups(vector<vector<int>> &intervals) {
        map<int, int> diff;
        for (auto &p : intervals)
            ++diff[p[0]], --diff[p[1] + 1];
        int ans = 0, sum = 0;
        for (auto &[_, d] : diff)
            ans = max(ans, sum += d);
        return ans;
    }
};

```

## 【线段树优化DP】最长递增子序列 II

### 题目

给你一个整数数组 `nums` 和一个整数 `k` 。

找到 `nums` 中满足以下要求的最长子序列：

- 子序列 **严格递增**
- 子序列中相邻元素的差值 **不超过** `k` 。

请你返回满足上述要求的 **最长子序列** 的长度。

**子序列** 是从一个数组中删除部分元素后，剩余元素不改变顺序得到的数组。

 

**示例 1：**

```
输入：nums = [4,2,1,4,3,4,5,8,15], k = 3
输出：5
解释：
满足要求的最长子序列是 [1,3,4,5,8] 。
子序列长度为 5 ，所以我们返回 5 。
注意子序列 [1,3,4,5,8,15] 不满足要求，因为 15 - 8 = 7 大于 3 。
```

**示例 2：**

```
输入：nums = [7,4,5,1,8,12,4,7], k = 5
输出：4
解释：
满足要求的最长子序列是 [4,5,8,12] 。
子序列长度为 4 ，所以我们返回 4 。
```

**示例 3：**

```
输入：nums = [1,5], k = 1
输出：1
解释：
满足要求的最长子序列是 [1] 。
子序列长度为 1 ，所以我们返回 1 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i], k <= 10^5`

### 解题思路

普通LIS的常规解法是$O(n^2)$的dp，有一种$O(n\ logn)$的解法是基于二分的dp。

碰到这题往二分那种思路想了，即dp[i]表示长度为i的最小元素，结果没整出来

赛后才了解到，还能用线段树实现$O(n\ logn)$复杂度的普通LIS。



对于这题，令dp[i]\[j]表示前i个数且末尾元素为j的合法LIS的最长长度，

那么以j结尾的合法LIS的前一个数字的值域应在[j-k, j-1]范围内   dp[i]\[j]  =  max{ dp[i-1]\[j-k...j-1] }+1，

第一维可以省略

利用线段树维护这个dp数组



### 代码

```c++
const int MAXN = 1e5+5;
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
struct node{
    int maxv;
}T[MAXN<<2];
void push_up(int p){
    T[p].maxv = max(T[ls(p)].maxv, T[rs(p)].maxv);
}
void build(int p, int l, int r){
    if(l==r){
        T[p].maxv = 0;
        return;
    }
    int mid = (l+r)>>1;
    build(ls(p),l,mid);
    build(rs(p),mid+1,r);
    push_up(p);
}
void update(int p,int l,int r,int nl,int nr,int v){
    if(nl<=l && nr>=r){
        T[p].maxv = v;
        return;
    }
    int mid=(l+r)>>1;
    if(nl<=mid) update(ls(p),l,mid,nl,nr,v);
    if(nr>mid) update(rs(p),mid+1,r,nl,nr,v);
    push_up(p);
}
int query(int p,int l,int r,int nl,int nr){
    if(nl<=l && nr>=r) return T[p].maxv;
    int mid=(l+r)>>1;
    int res=0;
    if(nl<=mid) res=max(res,query(ls(p),l,mid,nl,nr));
    if(nr>mid) res=max(res,query(rs(p),mid+1,r,nl,nr));
    return res; 
}
class Solution {
public:
    int lengthOfLIS(vector<int>& nums, int k) {
        int n = nums.size();
        //dp[j] 前i个数以j结尾的LIS长度，省略第一维
        vector<int> dp(MAXN,0);
        int m = 1e5;
        build(1,1,m);
        dp[nums[0]]++;
        update(1,1,m,nums[0],nums[0],1);
        for(int i=1;i<n;i++){
            //   nums[i]-k   <= pre < nums[i]
            if(nums[i]==1){
                update(1,1,m,nums[i],nums[i],1);
                continue;
            }
            int left;
            if(nums[i]-k<=1) left=1;
            else left = nums[i]-k;
            int pv = query(1,1,m,left,nums[i]-1);
            update(1,1,m,nums[i],nums[i],pv+1);
        }
        return query(1,1,m,1,m);
    }
};
```

