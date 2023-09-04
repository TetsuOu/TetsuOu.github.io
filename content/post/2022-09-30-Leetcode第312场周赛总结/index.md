---
title:      LeetCode第312场周赛总结
description:   1166/6638
date:       2022-09-30
image: post-bg-20220930.png
categories:
    - leetcode contest
tags:
    - 动态规划
    - 线段树
    - 并查集
---

连续几周掉分了

![image-20220930202019053](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220930202019053.png)

## 【自定义排序】按身高排序

### 题目

给你一个字符串数组 `names` ，和一个由 **互不相同** 的正整数组成的数组 `heights` 。两个数组的长度均为 `n` 。

对于每个下标 `i`，`names[i]` 和 `heights[i]` 表示第 `i` 个人的名字和身高。

请按身高 **降序** 顺序返回对应的名字数组 `names` 。

 

**示例 1：**

```
输入：names = ["Mary","John","Emma"], heights = [180,165,170]
输出：["Mary","Emma","John"]
解释：Mary 最高，接着是 Emma 和 John 。
```

**示例 2：**

```
输入：names = ["Alice","Bob","Bob"], heights = [155,185,150]
输出：["Bob","Alice","Bob"]
解释：第一个 Bob 最高，然后是 Alice 和第二个 Bob 。
```

 

**提示：**

- `n == names.length == heights.length`
- `1 <= n <= 10^3`
- `1 <= names[i].length <= 20`
- `1 <= heights[i] <= 10^5`
- `names[i]` 由大小写英文字母组成
- `heights` 中的所有值互不相同

### 解题思路

自定义排序

### 代码

```c++
#include<iostream>
#include<cstring>
#include<algorithm>
#include<deque>
#include<vector>
#include<queue>
#include<set>
#include<numeric>
using namespace std;
class Solution {
public:
    vector<string> sortPeople(vector<string>& names, vector<int>& heights) {
        int n = names.size();
        vector<int> a(n,0);
        iota(a.begin(),a.end(),0);
        sort(a.begin(),a.end(),[&](int x, int y){
            return heights[x]>heights[y];
        });
        vector<string> res(n);
        for(int i=0;i<n;i++){
            res[i] = names[a[i]];
        }
        return res;

    }
};

```

## 【思维】按位与最大的最长子数组

### 题目

给你一个长度为 `n` 的整数数组 `nums` 。

考虑 `nums` 中进行 **按位与（bitwise AND）**运算得到的值 **最大** 的 **非空** 子数组。

- 换句话说，令 `k` 是 `nums` **任意** 子数组执行按位与运算所能得到的最大值。那么，只需要考虑那些执行一次按位与运算后等于 `k` 的子数组。

返回满足要求的 **最长** 子数组的长度。

数组的按位与就是对数组中的所有数字进行按位与运算。

**子数组** 是数组中的一个连续元素序列。

 

**示例 1：**

```
输入：nums = [1,2,3,3,2,2]
输出：2
解释：
子数组按位与运算的最大值是 3 。
能得到此结果的最长子数组是 [3,3]，所以返回 2 。
```

**示例 2：**

```
输入：nums = [1,2,3,4]
输出：1
解释：
子数组按位与运算的最大值是 4 。 
能得到此结果的最长子数组是 [4]，所以返回 1 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^6`

### 解题思路

根据按位与运算的特性，有 a AND b <= min(a, b)。题目首先要求子数组按位与结果最大，然后求最长子数组。因此题目实际上求的是数组中的最大值最多连续出现了几次

### 代码

```c++
#include<iostream>
#include<cstring>
#include<algorithm>
#include<deque>
#include<vector>
#include<queue>
#include<set>
#include<numeric>
using namespace std;
class Solution {
public:
    int longestSubarray(vector<int>& nums) {
        int n = nums.size();
        int maxv = *max_element(nums.begin(),nums.end());
        int res = 0;
        int cur=0;
        for(int i=0;i<n;i++){
            if(nums[i]==maxv){
                cur++;
            }else cur=0;
            res = max(res, cur);
        }
        return res;
    }
};
```

## 【DP/线段树】找到所有好下标

### 题目

给你一个大小为 `n` 下标从 **0** 开始的整数数组 `nums` 和一个正整数 `k` 。

对于 `k <= i < n - k` 之间的一个下标 `i` ，如果它满足以下条件，我们就称它为一个 **好** 下标：

- 下标 `i` **之前** 的 `k` 个元素是 **非递增的** 。
- 下标 `i` **之后** 的 `k` 个元素是 **非递减的** 。

按 **升序** 返回所有好下标。

 

**示例 1：**

```
输入：nums = [2,1,1,1,3,4,1], k = 2
输出：[2,3]
解释：数组中有两个好下标：
- 下标 2 。子数组 [2,1] 是非递增的，子数组 [1,3] 是非递减的。
- 下标 3 。子数组 [1,1] 是非递增的，子数组 [3,4] 是非递减的。
注意，下标 4 不是好下标，因为 [4,1] 不是非递减的。
```

**示例 2：**

```
输入：nums = [2,1,1,2], k = 2
输出：[]
解释：数组中没有好下标。
```

 

**提示：**

- `n == nums.length`
- `3 <= n <= 10^5`
- `1 <= nums[i] <= 10^6`
- `1 <= k <= n / 2`

### 解题思路

思维钝化了，直接用线段树莽了，调了很长时间，还WA了一发

用线段树的话，可以维护每一个区间的单调性，额外记录对应区间的左右端点值即可

简单的想，其实用一维数组即可维护单调性情况

### 代码

#### DP

```c++
class Solution {
public:
    vector<int> goodIndices(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> L(n,1),R(n,1);
        //L[i]表示 nums[i]左侧（含） 非递增元素 的最长长度
        //R[i]表示 nums[i]右侧（含） 非递减元素 的最长长度
        for(int i=1;i<n;i++){
            if(nums[i]<=nums[i-1]) L[i] = L[i-1]+1;
        }
        for(int i=n-2;i>=0;i--){
            if(nums[i]<=nums[i+1]) R[i] = R[i+1]+1;
        }
        vector<int> res;
        for(int i=1;i<n-1;i++){
            // cout<<i<<' '<<L[i]<<' '<<R[i]<<endl;
            if(L[i-1]>=k && R[i+1]>=k) res.push_back(i);
        }
        return res;
    }
};
```

#### 线段树

```c++
#include<iostream>
#include<cstring>
#include<algorithm>
#include<deque>
#include<vector>
#include<queue>
#include<set>
#include<numeric>
using namespace std;
#define ll long long
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
const int MAXN = 1e5+5;
struct node{
    int lv,rv;
    int inc,dec;
}T[MAXN<<2];
void push_up(int p){
    T[p].lv = T[ls(p)].lv;
    T[p].rv = T[rs(p)].rv;
    if(T[ls(p)].inc && T[rs(p)].inc && T[ls(p)].rv<=T[rs(p)].lv){
        T[p].inc = 1;
    }else T[p].inc = 0;
    if(T[ls(p)].dec && T[rs(p)].dec && T[ls(p)].rv>=T[rs(p)].lv){
        T[p].dec = 1;
    }else T[p].dec = 0;
}
void build(int p, int l, int r, vector<int>& a){
    if(l==r){
        T[p].lv = T[p].rv = a[l-1];
        T[p].dec = T[p].inc = 1;
        return;
    }
    int mid = (l+r)>>1;
    build(ls(p),l,mid,a);
    build(rs(p),mid+1,r,a);
    push_up(p);
}
node query(int p, int l, int r, int nl, int nr){
    if(nl>r || nr<l) return {-1,-1,-1,-1};
    if(nl<=l && nr>=r) return T[p];
    int mid=(l+r)>>1;
    node left = query(ls(p),l,mid,nl,nr);
    node right = query(rs(p),mid+1,r,nl,nr);
    if(left.lv==-1 && right.lv!=-1) return right;
    if(left.lv!=-1 && right.lv==-1) return left;
    if(left.lv==-1 && right.lv==-1) return {-1,-1,-1,-1};
    node res = {233,233,0,0};
    res.lv = left.lv;
    res.rv = right.rv;
    if(left.inc && right.inc && left.rv<=right.lv){
        res.inc = 1;
    }else res.inc = 0;
    if(left.dec && right.dec && left.rv>=right.lv){
        res.dec = 1;
    }else res.dec = 0;
    return res;
}
class Solution {
public:
    vector<int> goodIndices(vector<int>& nums, int k) {
        int n = nums.size();
        build(1,1,n,nums);
    
        vector<int> res;
        for(int i=k+1;i<=n-k;i++){
            node L = query(1,1,n,i-k,i-1);
            node R = query(1,1,n,i+1,i+k);
            if(L.dec && R.inc) res.push_back(i-1);
        }
        return res;
    }
};
```

## 【并查集+离线】好路径的数目

### 题目

给你一棵 `n` 个节点的树（连通无向无环的图），节点编号从 `0` 到 `n - 1` 且恰好有 `n - 1` 条边。

给你一个长度为 `n` 下标从 **0** 开始的整数数组 `vals` ，分别表示每个节点的值。同时给你一个二维整数数组 `edges` ，其中 `edges[i] = [ai, bi]` 表示节点 `ai` 和 `bi` 之间有一条 **无向** 边。

一条 **好路径** 需要满足以下条件：

1. 开始节点和结束节点的值 **相同** 。
2. 开始节点和结束节点中间的所有节点值都 **小于等于** 开始节点的值（也就是说开始节点的值应该是路径上所有节点的最大值）。

请你返回不同好路径的数目。

注意，一条路径和它反向的路径算作 **同一** 路径。比方说， `0 -> 1` 与 `1 -> 0` 视为同一条路径。单个节点也视为一条合法路径。

 

**示例 1：**

<img src="https://assets.leetcode.com/uploads/2022/08/04/f9caaac15b383af9115c5586779dec5.png" alt="img" style="zoom:50%;" />

```
输入：vals = [1,3,2,1,3], edges = [[0,1],[0,2],[2,3],[2,4]]
输出：6
解释：总共有 5 条单个节点的好路径。
还有 1 条好路径：1 -> 0 -> 2 -> 4 。
（反方向的路径 4 -> 2 -> 0 -> 1 视为跟 1 -> 0 -> 2 -> 4 一样的路径）
注意 0 -> 2 -> 3 不是一条好路径，因为 vals[2] > vals[0] 。
```

**示例 2：**

<img src="https://assets.leetcode.com/uploads/2022/08/04/149d3065ec165a71a1b9aec890776ff.png" alt="img" style="zoom:50%;" />

```
输入：vals = [1,1,2,2,3], edges = [[0,1],[1,2],[2,3],[2,4]]
输出：7
解释：总共有 5 条单个节点的好路径。
还有 2 条好路径：0 -> 1 和 2 -> 3 。
```

**示例 3：**

![img](https://assets.leetcode.com/uploads/2022/08/04/31705e22af3d9c0a557459bc7d1b62d.png)

```
输入：vals = [1], edges = []
输出：1
解释：这棵树只有一个节点，所以只有一条好路径。
```

 

**提示：**

- `n == vals.length`
- `1 <= n <= 3 * 104`
- `0 <= vals[i] <= 105`
- `edges.length == n - 1`
- `edges[i].length == 2`
- `0 <= ai, bi < n`
- `ai != bi`
- `edges` 表示一棵合法的树。

### 解题思路

解法很有意思。化静为动。

**考虑重新构造出这棵树，按节点值从小到大的顺序，期间利用并查集维护联通性，以及每个联通块所含最大值及相应数目**

这样在构造过程中，使得每个联通块的代表元都是当前最大值，方便得出题目中所要求的“好路径”

详见注释

### 代码

```c++
class Solution {
public:
    int numberOfGoodPaths(vector<int>& vals, vector<vector<int>>& edges) {
        int n = vals.size();
        vector<int> fa(n),idx(n),cnt(n,1);//cnt[i] 表示i所在连通块最大值的个数
        vector<int> G[n];
        for(auto &x:edges){
            G[x[0]].push_back(x[1]);
            G[x[1]].push_back(x[0]);
        }
        iota(fa.begin(),fa.end(),0);
        iota(idx.begin(),idx.end(),0);
        sort(idx.begin(),idx.end(),[&](int a,int b){
            return vals[a]<vals[b];
        });
        function<int(int)> find = [&](int x)->int{
            return x==fa[x]?x:(fa[x]=find(fa[x]));
        };
        int res = n;//至少有n条单个节点的好路径
        for(int i=0;i<n;i++){//按节点值从小到大遍历
            int u = idx[i];
            int fu = find(u);//当前结点u所在连通块的代表元
            for(auto &v: G[u]){
                int fv = find(v);//u的邻居v 所在连通块的 代表元
                if(fu == fv) continue;//已经处于同一连通块了，已经被计算过了
                //fu的val值小 fv的val值大时 不考虑将v和u连通，只考虑u和比它小的值，以满足“好路径”的定义
                //记录了双向图，待后面遍历到大->小时再连通
                if(vals[fv]>vals[fu]) continue;
                if(vals[fv] == vals[fu]){//两个连通块中的最大val值相等，可以构成好路径
                    res += cnt[fv] * cnt[fu];//乘法原理计数
                    cnt[fu] += cnt[fv];
                }

                fa[fv] = fu;// 让val值小的 指向 val值大的
            }
        }

        return res;
    }
};
```

