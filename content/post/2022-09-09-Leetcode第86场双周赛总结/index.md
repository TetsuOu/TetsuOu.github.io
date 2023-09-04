---
title:      LeetCode第86场双周赛总结
description:   376/4401
date:       2022-09-09
image: post-bg-20220909.png
categories:
    - leetcode contest
tags:
    - 二分
    - 线段树
---

![image-20220909162147861](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220909162147861.png)

## 【枚举】和相等的子数组

### 题目

给你一个下标从 **0** 开始的整数数组 `nums` ，判断是否存在 **两个** 长度为 `2` 的子数组且它们的 **和** 相等。注意，这两个子数组起始位置的下标必须 **不相同** 。

如果这样的子数组存在，请返回 `true`，否则返回 `false` 。

**子数组** 是一个数组中一段连续非空的元素组成的序列。

 

**示例 1：**

```
输入：nums = [4,2,4]
输出：true
解释：元素为 [4,2] 和 [2,4] 的子数组有相同的和 6 。
```

**示例 2：**

```
输入：nums = [1,2,3,4,5]
输出：false
解释：没有长度为 2 的两个子数组和相等。
```

**示例 3：**

```
输入：nums = [0,0,0]
输出：true
解释：子数组 [nums[0],nums[1]] 和 [nums[1],nums[2]] 的和相等，都为 0 。
注意即使子数组的元素相同，这两个子数组也视为不相同的子数组，因为它们在原数组中的起始位置不同。
```

 

**提示：**

- `2 <= nums.length <= 1000`
- `-10^9 <= nums[i] <= 10^9`

### 解题思路

记录出现过的值即可

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    bool findSubarrays(vector<int>& nums) {
        int n = nums.size();
        map<long long,int> tab;
        for(int i=0;i<n-1;i++){
            long long cur = nums[i]+nums[i+1];
            if(tab.count(cur)) return true;
            tab[cur]++;
        }
        return false;
    }
};
```

## 【思维】严格回文的数字

### 题目

如果一个整数 `n` 在 `b` 进制下（`b` 为 `2` 到 `n - 2` 之间的所有整数）对应的字符串 **全部** 都是 **回文的** ，那么我们称这个数 `n` 是 **严格回文** 的。

给你一个整数 `n` ，如果 `n` 是 **严格回文** 的，请返回 `true` ，否则返回 `false` 。

如果一个字符串从前往后读和从后往前读完全相同，那么这个字符串是 **回文的** 。

 

**示例 1：**

```
输入：n = 9
输出：false
解释：在 2 进制下：9 = 1001 ，是回文的。
在 3 进制下：9 = 100 ，不是回文的。
所以，9 不是严格回文数字，我们返回 false 。
注意在 4, 5, 6 和 7 进制下，n = 9 都不是回文的。
```

**示例 2：**

```
输入：n = 4
输出：false
解释：我们只考虑 2 进制：4 = 100 ，不是回文的。
所以我们返回 false 。
```

 

**提示：**

- `4 <= n <= 10^5`

### 解题思路

直觉上来说，这种回文的难度太大了，应该不存在哈哈

数字 4 在二进制下不是回文的。对于 n ≥ 5，它们的 (n−2) 进制表示都是 12，因此也都不是回文的。直接返回 false 即可

### 代码

```c++
class Solution {
public:
    bool isStrictlyPalindromic(int n) {
        return false;
    }
};
```

## 【枚举】被列覆盖的最多行数

### 题目

给你一个下标从 **0** 开始的 `m x n` 二进制矩阵 `mat` 和一个整数 `cols` ，表示你需要选出的列数。

如果一行中，所有的 `1` 都被你选中的列所覆盖，那么我们称这一行 **被覆盖** 了。

请你返回在选择 `cols` 列的情况下，**被覆盖** 的行数 **最大** 为多少。

 

**示例 1：**

**<img src="https://assets.leetcode.com/uploads/2022/07/14/rowscovered.png" alt="img" style="zoom: 33%;" />**

```
输入：mat = [[0,0,0],[1,0,1],[0,1,1],[0,0,1]], cols = 2
输出：3
解释：
如上图所示，覆盖 3 行的一种可行办法是选择第 0 和第 2 列。
可以看出，不存在大于 3 行被覆盖的方案，所以我们返回 3 。
```

**示例 2：**

**<img src="https://assets.leetcode.com/uploads/2022/07/14/rowscovered2.png" alt="img" style="zoom:33%;" />**

```
输入：mat = [[1],[0]], cols = 1
输出：2
解释：
选择唯一的一列，两行都被覆盖了，原因是整个矩阵都被覆盖了。
所以我们返回 2 。
```

 

**提示：**

- `m == mat.length`
- `n == mat[i].length`
- `1 <= m, n <= 12`
- `mat[i][j]` 要么是 `0` 要么是 `1` 。
- `1 <= cols <= n`

### 解题思路

NP hard  

只能枚举做了 

复杂度$O(2^n\times nm)$

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    int maximumRows(vector<vector<int>>& mat, int cols) {
        int n = mat.size(), m = mat[0].size();
        vector<int> a(m, 0);
        for(int i=m-1,j=0;j<cols;i--,j++) a[i]=1;
        int res=0;
        do{
//            for(auto &x: a) cout<<x<<' ';cout<<endl;
            int cur = 0;
            for(int i=0;i<n;i++){
                int all = 0, tmp = 0;
                for(int j=0;j<m;j++){
                    if(mat[i][j]==1) all++;
                    if(mat[i][j]==1 && a[j]==1) tmp++;
                }
                if(tmp==all) cur++;
            }
            res = max(res, cur);
        }while(next_permutation(a.begin(),a.end()));

        return res;
    }
};
```



### 偷学代码（二进制枚举）

```c++
class Solution {
public:
    int maximumRows(vector<vector<int>>& mat, int cols) {
        int m = mat.size();
        int n = mat[0].size();
        int ans = 0;

        vector<int> mask(m);
        for (int i = 0; i < m; i++) { /* 每一行1的位置, 用二进制表示 */
            for (int j = 0; j < n; j++) {
                mask[i] |= mat[i][j] << j;
            }
        }

        for (int i = 0; i < (1 << n); i++) {
            if (__builtin_popcount(i) == cols) { /* 选了cols列 */
                int cnt = 0;
                for (int j = 0; j < m; j++) {
                    if ((mask[j] & i) == mask[j]) { /* j行是二进制枚举的子集 */
                        cnt++;
                    }
                }
                ans = max(ans, cnt);
            }
        }
        return ans;
    }
};
```

## 【二分+线段树+前缀和】预算内的最多机器人数目

### 题目

你有 `n` 个机器人，给你两个下标从 **0** 开始的整数数组 `chargeTimes` 和 `runningCosts` ，两者长度都为 `n` 。第 `i` 个机器人充电时间为 `chargeTimes[i]` 单位时间，花费 `runningCosts[i]` 单位时间运行。再给你一个整数 `budget` 。

运行 `k` 个机器人 **总开销** 是 `max(chargeTimes) + k * sum(runningCosts)` ，其中 `max(chargeTimes)` 是这 `k` 个机器人中最大充电时间，`sum(runningCosts)` 是这 `k` 个机器人的运行时间之和。

请你返回在 **不超过** `budget` 的前提下，你 **最多** 可以 **连续** 运行的机器人数目为多少。

 

**示例 1：**

```
输入：chargeTimes = [3,6,1,3,4], runningCosts = [2,1,3,4,5], budget = 25
输出：3
解释：
可以在 budget 以内运行所有单个机器人或者连续运行 2 个机器人。
选择前 3 个机器人，可以得到答案最大值 3 。总开销是 max(3,6,1) + 3 * sum(2,1,3) = 6 + 3 * 6 = 24 ，小于 25 。
可以看出无法在 budget 以内连续运行超过 3 个机器人，所以我们返回 3 。
```

**示例 2：**

```
输入：chargeTimes = [11,12,19], runningCosts = [10,8,7], budget = 19
输出：0
解释：即使运行任何一个单个机器人，还是会超出 budget，所以我们返回 0 。
```

 

**提示：**

- `chargeTimes.length == runningCosts.length == n`
- `1 <= n <= 5 * 10^4`
- `1 <= chargeTimes[i], runningCosts[i] <= 10^5`
- `1 <= budget <= 10^15`

### 解题思路

如果运行连续k个机器人是可行的话，那么运行连续k-1个机器人也肯定不会超预算

如果运行连续k个机器人是不可行的话，那么运行连续k+1个机器人也肯定会超预算

考虑二分答案

需要判断连续k个机器人是否可行

利用前缀和维护sum(runningCosts)

利用线段树（或其他rmq数据结构）维护max(chargeTimes)

上述利用线段树的时间复杂度为$O(n\ log(n))$

总时间复杂度为$O(n\ log^2(n))$

WA了一发是因为线段树的节点数量忘记<<2了，看来还是要备板子了。。不然速度还是提不上来，可能还会犯小错误。

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
const int MAXN = 5e4+5;
#define ll long long
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
struct node{
    int maxv;
}T[MAXN];
void push_up(int p){
    T[p].maxv = max(T[ls(p)].maxv, T[rs(p)].maxv);
}
void build(int p, int l, int r, vector<int> &a){
    if(l==r){
        T[p].maxv = a[l-1];
        return;
    }
    int mid=(l+r)>>1;
    build(ls(p),l,mid,a);
    build(rs(p),mid+1,r,a);
    push_up(p);
}
int query(int p, int l, int r, int nl, int nr){
    if(nl<=l && nr>=r) return T[p].maxv;
    int res = 0;
    int mid = (l+r)>>1;
    if(nl<=mid) res = max(res, query(ls(p),l,mid,nl,nr));
    if(nr>mid) res = max(res, query(rs(p),mid+1,r,nl,nr));
    return res;
}
class Solution {
public:
    int maximumRobots(vector<int>& chargeTimes, vector<int>& runningCosts, long long budget) {
        int n = chargeTimes.size();
        vector<ll> sum(n+1, 0);
        for(int i=1;i<=n;i++) sum[i] = sum[i-1]+runningCosts[i-1];
        build(1,1,n,chargeTimes);


        function<bool(int)> C=[&](int k)->bool{
            for(int i=1;i+k-1<=n;i++){
                ll run = (sum[i+k-1]-sum[i-1])*1ll*k;
                ll ma = query(1,1,n,i,i+k-1);
                ll cur = ma+run;
                if(cur<=budget) return true;
            }
            return false;
        };

        int l=1,r = n+1;
        while(l<r){
            int mid = (l+r)>>1;
            if(C(mid)) l=mid+1;
            else r=mid;
        }
      
        return l-1;
    }
};
```

