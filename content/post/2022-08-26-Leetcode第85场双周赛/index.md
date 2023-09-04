---
title:      LeetCode第85场双周赛总结
subtitle:   742/4193
date:       2022-08-26
image: post-bg-20220826.png
categories:
    - leetcode contest
tags:
    - 线段树
---

![image-20220826200102721](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220826200102721.png)

## 【前缀和/滑动窗口】得到 K 个黑块的最少涂色次数

### 题目

给你一个长度为 `n` 下标从 **0** 开始的字符串 `blocks` ，`blocks[i]` 要么是 `'W'` 要么是 `'B'` ，表示第 `i` 块的颜色。字符 `'W'` 和 `'B'` 分别表示白色和黑色。

给你一个整数 `k` ，表示想要 **连续** 黑色块的数目。

每一次操作中，你可以选择一个白色块将它 **涂成** 黑色块。

请你返回至少出现 **一次** 连续 `k` 个黑色块的 **最少** 操作次数。

 

**示例 1：**

```
输入：blocks = "WBBWWBBWBW", k = 7
输出：3
解释：
一种得到 7 个连续黑色块的方法是把第 0 ，3 和 4 个块涂成黑色。
得到 blocks = "BBBBBBBWBW" 。
可以证明无法用少于 3 次操作得到 7 个连续的黑块。
所以我们返回 3 。
```

**示例 2：**

```
输入：blocks = "WBWBBBW", k = 2
输出：0
解释：
不需要任何操作，因为已经有 2 个连续的黑块。
所以我们返回 0 。
```

 

**提示：**

- `n == blocks.length`
- `1 <= n <= 100`
- `blocks[i]` 要么是 `'W'` ，要么是 `'B'` 。
- `1 <= k <= n`

### 解题思路

所求为长度为k的区间内黑色块的最多的个数

赛时用的是前缀和维护黑色块的数目

也可以用滑动窗口

### 代码

```c++
class Solution {
public:
    int minimumRecolors(string blocks, int k) {
        int n = blocks.size();
        vector<int> sum(n+1,0);
        for(int i=1;i<=n;i++){
            sum[i] = sum[i-1] + (blocks[i-1]=='B'?1:0);
        }
        int maxs = 0;
        for(int i=1;i+k-1<=n;i++){
            maxs = max(maxs, sum[i+k-1]-sum[i-1]);
        }
        if(maxs>=k) return 0;
        else return k-maxs;
    }
};
```

## 【模拟/动态规划】二进制字符串重新安排顺序需要的时间

### 题目

给你一个二进制字符串 `s` 。在一秒之中，**所有** 子字符串 `"01"` **同时** 被替换成 `"10"` 。这个过程持续进行到没有 `"01"` 存在。

请你返回完成这个过程所需要的秒数。

 

**示例 1：**

```
输入：s = "0110101"
输出：4
解释：
一秒后，s 变成 "1011010" 。
再过 1 秒后，s 变成 "1101100" 。
第三秒过后，s 变成 "1110100" 。
第四秒后，s 变成 "1111000" 。
此时没有 "01" 存在，整个过程花费 4 秒。
所以我们返回 4 。
```

**示例 2：**

```
输入：s = "11100"
输出：0
解释：
s 中没有 "01" 存在，整个过程花费 0 秒。
所以我们返回 0 。
```

 

**提示：**

- `1 <= s.length <= 1000`
- `s[i]` 要么是 `'0'` ，要么是 `'1'` 。

### 解题思路

数据量小，可以用$O(n^2)$的模拟做。

此外，这题还存在$O(n)$的解法

> 思考时，我们可以把 01→ 10 的替换看成是 1 向左移动。每一秒，如果 1 的左面是 0，则会向左移动一步。注意连续的 1 不能同时向左移动。
>
> 从左到右遍历字符串：
>
> 1. 如果 1 的左侧没有 0，则无需移动。
>
> 2. 如果 11 的左侧存在 cnt(cnt > 0) 个 0，则至少需要 cnt 秒来移动；同时，如果这个 1 的左侧还存在 1，那么它至少需要比左侧那个 1 多一秒才能到达最终位置。
>
>    这是因为，当左侧的 1 刚到达最终位置的时刻，右侧的 1 一定不会移动到最终位置，而是至少与前面的 1 间隔一个 0，因为连续的 11 无法同时向左移动，从而无法同时到达最终位置。
>

### 代码

#### 模拟

```c++
class Solution {
public:
    int secondsToRemoveOccurrences(string s) {
        int n = s.size();
        int step = 0;
        while(1){
            bool flag = true;
            for(int i=0;i<n;){
                if(s[i]=='0' && i!=n-1 && s[i+1]=='1') s[i]='1',s[i+1]='0',i+=2, flag = false;
                else i++;
            }
            if(flag) break;
            step++;
        }
        return step;
    }
};
```

#### 动态规划

```c++
class Solution {
public:
    int secondsToRemoveOccurrences(string s) {
        int n = s.size();
        //dp[i] 前i个字符完成安排顺序后所需要的时间
        vector<int> dp(n,0);
        int pre0 = s[0]=='0'?1:0;
        for(int i=1;i<n;i++){
            if(s[i]=='0') dp[i] = dp[i-1], pre0++;
            else if(pre0) dp[i] = max(pre0, dp[i-1]+1);
        }
        return dp[n-1];
    }
};
```

## 【差分数组】字母移位 II

### 题目

给你一个小写英文字母组成的字符串 `s` 和一个二维整数数组 `shifts` ，其中 `shifts[i] = [starti, endi, directioni]` 。对于每个 `i` ，将 `s` 中从下标 `starti` 到下标 `endi` （两者都包含）所有字符都进行移位运算，如果 `directioni = 1` 将字符向后移位，如果 `directioni = 0` 将字符向前移位。

将一个字符 **向后** 移位的意思是将这个字符用字母表中 **下一个** 字母替换（字母表视为环绕的，所以 `'z'` 变成 `'a'`）。类似的，将一个字符 **向前** 移位的意思是将这个字符用字母表中 **前一个** 字母替换（字母表是环绕的，所以 `'a'` 变成 `'z'` ）。

请你返回对 `s` 进行所有移位操作以后得到的最终字符串。

 

**示例 1：**

```
输入：s = "abc", shifts = [[0,1,0],[1,2,1],[0,2,1]]
输出："ace"
解释：首先，将下标从 0 到 1 的字母向前移位，得到 s = "zac" 。
然后，将下标从 1 到 2 的字母向后移位，得到 s = "zbd" 。
最后，将下标从 0 到 2 的字符向后移位，得到 s = "ace" 。
```

**示例 2:**

```
输入：s = "dztz", shifts = [[0,0,0],[1,1,1]]
输出："catz"
解释：首先，将下标从 0 到 0 的字母向前移位，得到 s = "cztz" 。
最后，将下标从 1 到 1 的字符向后移位，得到 s = "catz" 。
```

 

**提示：**

- `1 <= s.length, shifts.length <= 5 * 10^4`
- `shifts[i].length == 3`
- `0 <= starti <= endi < s.length`
- `0 <= directioni <= 1`
- `s` 只包含小写英文字母。

### 解题思路

区间修改+单点查询 可用差分数组

最后单点查询的时候再判断总共移位的情况

RE了一发是忽略了ASCII码值的范围是[0,127]，而'a'=97，'z'=122，'z'+26>127超出了char的范围

```c++
if(a[i]>0){//赛时写成这样了，可惜
	s[i]+=a[i];
	if(s[i]>'z') s[i]='a'+s[i]-'z'-1;
}
```



### 代码

```c++
class Solution {
public:
    string shiftingLetters(string s, vector<vector<int>>& shifts) {
        int n = s.size();
        vector<int> d(n+1, 0), a(n,0);
        for(auto &x: shifts){
            int st=x[0],e=x[1],dir=x[2];
            if(dir==1){//向后移
                d[st]+=1,d[e+1]-=1;
            }else{//向前移
                d[st]+=-1,d[e+1]-=-1;
            }
        }
        int now=0;
        for(int i=0;i<n;i++){
            now+=d[i];
            a[i]=(now%26+26)%26;
            // cout<<a[i]<<' ';
            if(a[i]>0){
                while(a[i]){
                    s[i]++;
                    if(s[i]>'z') s[i]='a';
                    a[i]--;
                }
            }
        }
        // cout<<endl;
        return s;
        
        
    }
};
```

## 【倒序+线段树/并查集】删除操作后的最大子段和

### 题目

给你两个下标从 **0** 开始的整数数组 `nums` 和 `removeQueries` ，两者长度都为 `n` 。对于第 `i` 个查询，`nums` 中位于下标 `removeQueries[i]` 处的元素被删除，将 `nums` 分割成更小的子段。

一个 **子段** 是 `nums` 中连续 **正** 整数形成的序列。**子段和** 是子段中所有元素的和。

请你返回一个长度为 `n` 的整数数组 `answer` ，其中 `answer[i]`是第 `i` 次删除操作以后的 **最大** 子段和。

**注意：** 一个下标至多只会被删除一次。

 

**示例 1：**

```
输入：nums = [1,2,5,6,1], removeQueries = [0,3,2,4,1]
输出：[14,7,2,2,0]
解释：用 0 表示被删除的元素，答案如下所示：
查询 1 ：删除第 0 个元素，nums 变成 [0,2,5,6,1] ，最大子段和为子段 [2,5,6,1] 的和 14 。
查询 2 ：删除第 3 个元素，nums 变成 [0,2,5,0,1] ，最大子段和为子段 [2,5] 的和 7 。
查询 3 ：删除第 2 个元素，nums 变成 [0,2,0,0,1] ，最大子段和为子段 [2] 的和 2 。
查询 4 ：删除第 4 个元素，nums 变成 [0,2,0,0,0] ，最大子段和为子段 [2] 的和 2 。
查询 5 ：删除第 1 个元素，nums 变成 [0,0,0,0,0] ，最大子段和为 0 ，因为没有任何子段存在。
所以，我们返回 [14,7,2,2,0] 。
```

**示例 2：**

```
输入：nums = [3,2,11,1], removeQueries = [3,2,1,0]
输出：[16,5,3,0]
解释：用 0 表示被删除的元素，答案如下所示：
查询 1 ：删除第 3 个元素，nums 变成 [3,2,11,0] ，最大子段和为子段 [3,2,11] 的和 16 。
查询 2 ：删除第 2 个元素，nums 变成 [3,2,0,0] ，最大子段和为子段 [3,2] 的和 5 。
查询 3 ：删除第 1 个元素，nums 变成 [3,0,0,0] ，最大子段和为子段 [3] 的和 3 。
查询 5 ：删除第 0 个元素，nums 变成 [0,0,0,0] ，最大子段和为 0 ，因为没有任何子段存在。
所以，我们返回 [16,5,3,0] 。
```

 

**提示：**

- `n == nums.length == removeQueries.length`
- `1 <= n <= 10^5`
- `1 <= nums[i] <= 10^9`
- `0 <= removeQueries[i] < n`
- `removeQueries` 中所有数字 **互不相同** 。

### 解题思路

倒序操作，变成从空数组开始合并

线段树维护每个区间的最大子段和，左子段和，右子段和，以及它们对应的长度

还可以用并查集

### 代码（线段树）

```c++
#define ll long long
const int MAXN = 1e5+5;
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
struct node{
    ll lv,rv,v;//含左端点在内的最大子段和；含右端点在内的最大子段和；最大子段和
    int lenl,lenr;//左端点开始的连续非零长度；右端点开始的连续非零长度
}T[MAXN<<2];
void push_up(int p, int l, int r){
    int mid=(l+r)>>1;
    T[p].lenl = T[ls(p)].lenl + (T[ls(p)].lenl==(mid-l+1)?T[rs(p)].lenl:0);
    T[p].lenr = T[rs(p)].lenr + (T[rs(p)].lenr==(r-mid)?T[ls(p)].lenr:0);

    T[p].lv = T[ls(p)].lv + (T[ls(p)].lenl==(mid-l+1)?T[rs(p)].lv:0);
    T[p].rv = T[rs(p)].rv + (T[rs(p)].lenr==(r-mid)?T[ls(p)].rv:0);
    
    T[p].v = max({T[ls(p)].v, T[rs(p)].v, T[p].lv, T[p].rv, T[ls(p)].rv+T[rs(p)].lv});
    
}
void update(int p, int l, int r, int nl, int nr, int k){
    if(nl<=l && nr>=r) {
        T[p].lv = T[p].rv = T[p].v = k;
        T[p].lenl=T[p].lenr=1;
        return ;
    }
    int mid=(l+r)>>1;
    if(nl<=mid) update(ls(p),l,mid,nl,nr,k);
    if(nr>mid) update(rs(p),mid+1,r,nl,nr,k);
    push_up(p,l,r);
} 
ll query(int p, int l, int r, int nl, int nr){
    if(nl<=l && nr>=r) return T[p].v;
    return 0;
}
class Solution {
public:
    vector<long long> maximumSegmentSum(vector<int>& nums, vector<int>& removeQueries) {
        memset(T,0,sizeof(T));
        vector<ll> res;
        int n = removeQueries.size();
        for(int i=n-1;i>=0;i--){
            res.push_back(query(1,1,n,1,n));
            update(1,1,n,removeQueries[i]+1,removeQueries[i]+1,nums[removeQueries[i]]);
        }
        reverse(res.begin(),res.end());
        return res;
        
    }
};
```

### 偷学代码（并查集）

```c++
class Solution {
public:
    vector<long long> maximumSegmentSum(vector<int> &nums, vector<int> &removeQueries) {
        int n = nums.size();
        int fa[n + 1];
        iota(fa, fa + n + 1, 0);
        long long sum[n + 1];
        memset(sum, 0, sizeof(sum));
        function<int(int)> find = [&](int x) -> int { return fa[x] == x ? x : fa[x] = find(fa[x]); };

        vector<long long> ans(n);
        for (int i = n - 1; i > 0; --i) {
            int x = removeQueries[i];
            int to = find(x + 1);
            fa[x] = to; // 合并 x 和 x+1
            sum[to] += sum[x] + nums[x];
            ans[i - 1] = max(ans[i], sum[to]);
        }
        return ans;
    }
};

```

