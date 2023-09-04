---
title:      LeetCode第81场双周赛总结
description:   855/3847
date:       2022-06-27
image: post-bg-20220627.png
categories:
    - leetcode contest
tags:
    - 并查集
    - 动态规划
---
![image-20220627182728170](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220627182728170.png)

## 【模拟】统计星号

### 题目

给你一个字符串 `s` ，每 **两个** 连续竖线 `'|'` 为 **一对** 。换言之，第一个和第二个 `'|'` 为一对，第三个和第四个 `'|'` 为一对，以此类推。

请你返回 **不在** 竖线对之间，`s` 中 `'*'` 的数目。

**注意**，每个竖线 `'|'` 都会 **恰好** 属于一个对。

 

**示例 1**：

```
输入：s = "l|*e*et|c**o|*de|"
输出：2
解释：不在竖线对之间的字符加粗加斜体后，得到字符串："l|*e*et|c**o|*de|" 。
第一和第二条竖线 '|' 之间的字符不计入答案。
同时，第三条和第四条竖线 '|' 之间的字符也不计入答案。
不在竖线对之间总共有 2 个星号，所以我们返回 2 。
```

**示例 2：**

```
输入：s = "iamprogrammer"
输出：0
解释：在这个例子中，s 中没有星号。所以返回 0 。
```

**示例 3：**

```
输入：s = "yo|uar|e**|b|e***au|tifu|l"
输出：5
解释：需要考虑的字符加粗加斜体后："yo|uar|e**|b|e***au|tifu|l" 。不在竖线对之间总共有 5 个星号。所以我们返回 5 。
```

 

**提示：**

- `1 <= s.length <= 1000`
- `s` 只包含小写英文字母，竖线 `'|'` 和星号 `'*'` 。
- `s` 包含 **偶数** 个竖线 `'|'` 。

### 解题思路

按提议模拟

总的*个数 减去 竖线对之间的 *个数

### 代码

```c++
class Solution {
public:
    int countAsterisks(string s) {
        int n = s.size();
        int in = 0, all = 0;
        bool hasleft=false;
        for(auto &ch: s){
            if(ch=='*') all++;
            if(ch=='|'){
                if(hasleft) hasleft=false;
                else{
                    hasleft=true;
                }
            }
            if(hasleft&&ch=='*') in++;
        }
        return all-in;
    }
};
```

## 【求连通块大小】统计无向图中无法互相到达点对数

### 题目

给你一个整数 `n` ，表示一张 **无向图** 中有 `n` 个节点，编号为 `0` 到 `n - 1` 。同时给你一个二维整数数组 `edges` ，其中 `edges[i] = [ai, bi]` 表示节点 `ai` 和 `bi` 之间有一条 **无向** 边。

请你返回 **无法互相到达** 的不同 **点对数目** 。

 

**示例 1：**

<img src="https://assets.leetcode.com/uploads/2022/05/05/tc-3.png" alt="img" style="zoom:50%;" />

```
输入：n = 3, edges = [[0,1],[0,2],[1,2]]
输出：0
解释：所有点都能互相到达，意味着没有点对无法互相到达，所以我们返回 0 。
```

**示例 2：**

<img src="https://assets.leetcode.com/uploads/2022/05/05/tc-2.png" alt="img" style="zoom:50%;" />

```
输入：n = 7, edges = [[0,2],[0,5],[2,4],[1,6],[5,4]]
输出：14
解释：总共有 14 个点对互相无法到达：
[[0,1],[0,3],[0,6],[1,2],[1,3],[1,4],[1,5],[2,3],[2,6],[3,4],[3,5],[3,6],[4,6],[5,6]]
所以我们返回 14 。
```

 

**提示：**

- `1 <= n <= 10^5`
- `0 <= edges.length <= 2 * 10^5`
- `edges[i].length == 2`
- `0 <= ai, bi < n`
- `ai != bi`
- 不会有重复边。

### 解题思路

求出每个点所在联通块的大小s，其对答案的贡献为 n-s

或者考虑单个联通块大小s，其对答案的贡献为s*(n-s)

无向图，最后除以2

可以用并查集求连通块，也可以用BFS/DFS

### 代码

#### 并查集

```c++
const int MAXN=1e5+5;
int p[MAXN],s[MAXN];
int setfind(int x){
    if(p[x]!=x) p[x]=setfind(p[x]);
    return p[x];
}
void setunion(int x, int y){
    if(setfind(x)==setfind(y)) return;
    s[setfind(y)]+=s[setfind(x)];
    p[setfind(x)]=setfind(y);
    return;
}
int setsize(int x){
    return s[setfind(x)];
}
class Solution {
public:
    void init(int n){
        for(int i=0;i<n;i++){
            p[i]=i;
            s[i]=1;
        }
    }
    long long countPairs(int n, vector<vector<int>>& edges) {
        long long res = 0;
        init(n);
        for(auto &x: edges){
            setunion(x[0],x[1]);
        }
        for(int i=0;i<n;i++){
            int sz = s[setfind(i)];
            res += n-sz;
        }
        return res/2;
    }
};
```

#### BFS

```c++
class Solution {
    vector<vector<int>> e;
    vector<bool> vis;

    int bfs(int S) {
        int ret = 0;
        queue<int> q;
        q.push(S); vis[S] = true;
        while (!q.empty()) {
            ret++;
            int sn = q.front(); q.pop();
            for (int fn : e[sn]) if (!vis[fn]) q.push(fn), vis[fn] = true;
        }
        return ret;
    }

public:
    long long countPairs(int n, vector<vector<int>>& edges) {
        e.resize(n, vector<int>());
        vis.resize(n, false);
        for (auto &edge : edges) e[edge[0]].push_back(edge[1]), e[edge[1]].push_back(edge[0]);

        long long ans = 0;
        for (int i = 0; i < n; i++) if (!vis[i]) {
            int t = bfs(i);
            ans += 1LL * t * (n - t);
        }
        return ans / 2;
    }
};

// 作者：TsReaper
// 链接：https://leetcode.cn/circle/discuss/Yj3lGZ/view/yUnvMQ/
// 来源：力扣（LeetCode）
// 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 【思维+位操作】操作后的最大异或和

### 题目

给你一个下标从 **0** 开始的整数数组 `nums` 。一次操作中，选择 **任意** 非负整数 `x` 和一个下标 `i` ，**更新** `nums[i]` 为 `nums[i] AND (nums[i] XOR x)` 。

注意，`AND` 是逐位与运算，`XOR` 是逐位异或运算。

请你执行 **任意次** 更新操作，并返回 `nums` 中所有元素 **最大** 逐位异或和。

 

**示例 1：**

```
输入：nums = [3,2,4,6]
输出：7
解释：选择 x = 4 和 i = 3 进行操作，num[3] = 6 AND (6 XOR 4) = 6 AND 2 = 2 。
现在，nums = [3, 2, 4, 2] 且所有元素逐位异或得到 3 XOR 2 XOR 4 XOR 2 = 7 。
可知 7 是能得到的最大逐位异或和。
注意，其他操作可能也能得到逐位异或和 7 。
```

**示例 2：**

```
输入：nums = [1,2,3,9,2]
输出：11
解释：执行 0 次操作。
所有元素的逐位异或和为 1 XOR 2 XOR 3 XOR 9 XOR 2 = 11 。
可知 11 是能得到的最大逐位异或和。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^8`

### 解题思路

x可以取任意值，那么 nums[i] XOR x 的结果也是任意值

上述结果再与nums[i]按位与，结果一定小于等于nums[i]，从位的角度看，即是将nums[i]的若干个1变成0

最后要取最大的异或和，从位的角度看，能是1的位就令其为1，否则为0

### 代码

```c++
class Solution {
public:
    int maximumXOR(vector<int>& nums) {
        int n = nums.size();
        vector<int> cnt(32,0);
        for(auto &num: nums){
            for(int i=0;i<32;i++){
                if((num>>i)&1) cnt[i]++;
            }
        }
        int res = 0;
        for(int i=0;i<32;i++){
            if(cnt[i]==0) continue;
            res += (1<<i);
        }
        return res;
    }
};
```

### 偷学代码

```c++
class Solution {
public:
    int maximumXOR(vector<int>& nums) {
        int ans = 0;
        for (int x : nums) ans |= x;
        return ans;
    }
};

// 作者：TsReaper
// 链接：https://leetcode.cn/circle/discuss/Yj3lGZ/view/yUnvMQ/
// 来源：力扣（LeetCode）
// 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 【DP】不同骰子序列的数目

### 题目

给你一个整数 `n` 。你需要掷一个 6 面的骰子 `n` 次。请你在满足以下要求的前提下，求出 **不同** 骰子序列的数目：

1. 序列中任意 **相邻** 数字的 **最大公约数** 为 `1` 。
2. 序列中 **相等** 的值之间，至少有 `2` 个其他值的数字。正式地，如果第 `i` 次掷骰子的值 **等于** 第 `j` 次的值，那么 `abs(i - j) > 2` 。

请你返回不同序列的 **总数目** 。由于答案可能很大，请你将答案对 `10^9 + 7` **取余** 后返回。

如果两个序列中至少有一个元素不同，那么它们被视为不同的序列。

 

**示例 1：**

```
输入：n = 4
输出：184
解释：一些可行的序列为 (1, 2, 3, 4) ，(6, 1, 2, 3) ，(1, 2, 3, 1) 等等。
一些不可行的序列为 (1, 2, 1, 3) ，(1, 2, 3, 6) 。
(1, 2, 1, 3) 是不可行的，因为第一个和第三个骰子值相等且 abs(1 - 3) = 2 （下标从 1 开始表示）。
(1, 2, 3, 6) i是不可行的，因为 3 和 6 的最大公约数是 3 。
总共有 184 个不同的可行序列，所以我们返回 184 。
```

**示例 2：**

```
输入：n = 2
输出：22
解释：一些可行的序列为 (1, 2) ，(2, 1) ，(3, 2) 。
一些不可行的序列为 (3, 6) ，(2, 4) ，因为最大公约数不为 1 。
总共有 22 个不同的可行序列，所以我们返回 22 。
```

 

**提示：**

- `1 <= n <= 10^4`

### 解题思路

一眼DP，但是状态定义错了，赛时考虑的是二维DP，结果不对，状态转移的时候考虑的情况有重复

应该定义成三维的，第i位受限制于第i-1位和第i-2位

令dp[i, j, k]为 子序列[1..i] 最后一位为k，倒数第二位为j的 可行序列方案数

然后根据题意转移就方便很多，且不会重复了

### 代码

```c++
#define ll long long
const int MOD = 1e9+7;
class Solution {
public:
    int distinctSequences(int n) {
        if(n==1) return 6;
        long long dp[n+1][7][7];
        memset(dp,0,sizeof(dp));
        for(int j=1;j<=6;j++){
            for(int k=1;k<=6;k++){
                dp[1][j][k]=1;
            }
        }
        for(int j=1;j<=6;j++){
            for(int k=1;k<=6;k++){
                if(__gcd(j,k)==1&&j!=k){
                    dp[2][j][k] = 1;
                }
            }
        }
        for(int i=3;i<=n;i++){//第i位
            for(int j=1;j<=6;j++){//枚举第i-1位的数为j
                for(int k=1;k<=6;k++){//枚举第i位的数为k
                    if(__gcd(j,k)!=1) continue;
                    if(k==j) continue;
                    for(int p=1;p<=6;p++){//枚举第i-2位的数
                        if(__gcd(p,j)!=1) continue;
                        if(p==k) continue;
                        dp[i][j][k]=(dp[i][j][k]+dp[i-1][p][j])%MOD;
                    }
                }
            }
        }
        ll res = 0;
        for(int j=1;j<=6;j++){
            for(int k=1;k<=6;k++){
                res = (res+dp[n][j][k])%MOD;
            }
        }
        
        return res;
    }
};
```

