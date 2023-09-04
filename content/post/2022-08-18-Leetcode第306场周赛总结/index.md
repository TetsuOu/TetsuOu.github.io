---
title:      LeetCode第306场周赛总结
description:   429/7500
date:       2022-08-18
image: post-bg-20220818.png
categories:
    - leetcode contest
tags:
    - 数位DP
---

![image-20220818140257603](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220818140257603.png)

## 【模拟】矩阵中的局部最大值

### 题目

给你一个大小为 `n x n` 的整数矩阵 `grid` 。

生成一个大小为 `(n - 2) x (n - 2)` 的整数矩阵 `maxLocal` ，并满足：

- `maxLocal[i][j]` 等于 `grid` 中以 `i + 1` 行和 `j + 1` 列为中心的 `3 x 3` 矩阵中的 **最大值** 。

换句话说，我们希望找出 `grid` 中每个 `3 x 3` 矩阵中的最大值。

返回生成的矩阵。

 

**示例 1：**

<img src="https://assets.leetcode.com/uploads/2022/06/21/ex1.png" alt="img" style="zoom:50%;" />

```
输入：grid = [[9,9,8,1],[5,6,2,6],[8,2,6,4],[6,2,2,2]]
输出：[[9,9],[8,6]]
解释：原矩阵和生成的矩阵如上图所示。
注意，生成的矩阵中，每个值都对应 grid 中一个相接的 3 x 3 矩阵的最大值。
```

**示例 2：**

<img src="https://assets.leetcode.com/uploads/2022/07/02/ex2new2.png" alt="img" style="zoom:50%;" />

```
输入：grid = [[1,1,1,1,1],[1,1,1,1,1],[1,1,2,1,1],[1,1,1,1,1],[1,1,1,1,1]]
输出：[[2,2,2],[2,2,2],[2,2,2]]
解释：注意，2 包含在 grid 中每个 3 x 3 的矩阵中。
```

 

**提示：**

- `n == grid.length == grid[i].length`
- `3 <= n <= 100`
- `1 <= grid[i][j] <= 100`

### 解题思路

麻了，一开始用max 列表，报错了，还以为是c++版本没跟上，结束后才发现是 grid[i+1]\[j] 写成了grid[i+1, j]

### 代码

```c++
class Solution {
public:
    int fun(int i, int j,vector<vector<int>>& grid){
        int res=0;
        
        res = max(res, grid[i-1][j-1]);
        res = max(res, grid[i-1][j]);
        res = max(res, grid[i-1][j+1]);
        
        res = max(res, grid[i][j-1]);
        res = max(res, grid[i][j]);
        res = max(res, grid[i][j+1]);
        res = max(res, grid[i+1][j-1]);
        res = max(res, grid[i+1][j]);
        res = max(res, grid[i+1][j+1]);
        return res;
    }
    vector<vector<int>> largestLocal(vector<vector<int>>& grid) {
        int n = grid.size();
        vector<vector<int>> res(n-2, vector<int>(n-2,0));
        for(int i=1;i<n-1;i++){
            for(int j=1;j<n-1;j++){
                // res[i-1][j-1] = max({grid[i][j],grid[i-1][j-1],grid[i-1][j],grid[i-1][j+1],grid[i][j-1],grid[i][j+1],grid[i+1][j-1],grid[i+1][j],grid[i+1][j+1]});
                res[i-1][j-1] = fun(i,j,grid);
            }
        }
        return res;
    }
};
```

## 【模拟】边积分最高的节点

### 题目

给你一个有向图，图中有 `n` 个节点，节点编号从 `0` 到 `n - 1` ，其中每个节点都 **恰有一条** 出边。

图由一个下标从 **0** 开始、长度为 `n` 的整数数组 `edges` 表示，其中 `edges[i]` 表示存在一条从节点 `i` 到节点 `edges[i]` 的 **有向** 边。

节点 `i` 的 **边积分** 定义为：所有存在一条指向节点 `i` 的边的节点的 **编号** 总和。

返回 **边积分** 最高的节点。如果多个节点的 **边积分** 相同，返回编号 **最小** 的那个。

 

**示例 1：**

<img src="https://assets.leetcode.com/uploads/2022/06/20/image-20220620195403-1.png" alt="img" style="zoom:50%;" />

```
输入：edges = [1,0,0,0,0,7,7,5]
输出：7
解释：
- 节点 1、2、3 和 4 都有指向节点 0 的边，节点 0 的边积分等于 1 + 2 + 3 + 4 = 10 。
- 节点 0 有一条指向节点 1 的边，节点 1 的边积分等于 0 。
- 节点 7 有一条指向节点 5 的边，节点 5 的边积分等于 7 。
- 节点 5 和 6 都有指向节点 7 的边，节点 7 的边积分等于 5 + 6 = 11 。
节点 7 的边积分最高，所以返回 7 。
```

**示例 2：**

<img src="https://assets.leetcode.com/uploads/2022/06/20/image-20220620200212-3.png" alt="img" style="zoom:50%;" />

```
输入：edges = [2,0,0,2]
输出：0
解释：
- 节点 1 和 2 都有指向节点 0 的边，节点 0 的边积分等于 1 + 2 = 3 。
- 节点 0 和 3 都有指向节点 2 的边，节点 2 的边积分等于 0 + 3 = 3 。
节点 0 和 2 的边积分都是 3 。由于节点 0 的编号更小，返回 0 。
```

 

**提示：**

- `n == edges.length`
- `2 <= n <= 10^5`
- `0 <= edges[i] < n`
- `edges[i] != i`

### 解题思路

麻了，第一发超int，第二发将记录数组改成了long long，但忘记将后面比较的也改成 long long， 又WA了

太紧张了没发现

用python重写交了

### 代码

```python
class Solution:
    def edgeScore(self, edges: List[int]) -> int:
        n = len(edges)
        a = [0 for i in range(0,n)]
        for i in range(0,n):
            a[edges[i]] += i;
        res = 0
        maxv = 0
        for i  in range(0,n):
            if (a[i]>maxv):
                maxv = a[i]
                res = i
        ## print(a)
        return res
```

## 【DFS/贪心】根据模式串构造最小数字

### 题目

给你下标从 **0** 开始、长度为 `n` 的字符串 `pattern` ，它包含两种字符，`'I'` 表示 **上升** ，`'D'` 表示 **下降** 。

你需要构造一个下标从 **0** 开始长度为 `n + 1` 的字符串，且它要满足以下条件：

- `num` 包含数字 `'1'` 到 `'9'` ，其中每个数字 **至多** 使用一次。
- 如果 `pattern[i] == 'I'` ，那么 `num[i] < num[i + 1]` 。
- 如果 `pattern[i] == 'D'` ，那么 `num[i] > num[i + 1]` 。

请你返回满足上述条件字典序 **最小** 的字符串 `num`。

 

**示例 1：**

```
输入：pattern = "IIIDIDDD"
输出："123549876"
解释：
下标 0 ，1 ，2 和 4 处，我们需要使 num[i] < num[i+1] 。
下标 3 ，5 ，6 和 7 处，我们需要使 num[i] > num[i+1] 。
一些可能的 num 的值为 "245639871" ，"135749862" 和 "123849765" 。
"123549876" 是满足条件最小的数字。
注意，"123414321" 不是可行解因为数字 '1' 使用次数超过 1 次。
```

**示例 2：**

```
输入：pattern = "DDD"
输出："4321"
解释：
一些可能的 num 的值为 "9876" ，"7321" 和 "8742" 。
"4321" 是满足条件最小的数字。
```

 

**提示：**

- `1 <= pattern.length <= 8`
- `pattern` 只包含字符 `'I'` 和 `'D'` 。

### 解题思路

看数据范围，直接DFS走起

也可以用贪心

### 代码

#### DFS

```c++
class Solution {
public:
    string res;
    string pat;
    int n;
    vector<int> vis;
    void dfs(int i, string cur){
        if(i == n+1){
            res = min(res, cur);
            // cout<<cur<<endl;
            return;
        }
        
        for(int j=1;j<=9;j++){
            if(vis[j]) continue;
            if(i>0){
                if(pat[i-1]=='I') {
                    if(j < cur.back()-'0') continue;
                }else if(pat[i-1]=='D'){
                    if(j > cur.back()-'0') continue;
                }
            }
            cur.push_back(j+'0');
            vis[j] = 1;
            dfs(i+1,cur);
            vis[j] = 0;
            cur.pop_back();
            
        }
        
    }
    string smallestNumber(string pattern) {
        n = pattern.size();
        pat = pattern;
        vis.assign(10,0);
        string emp;
        res = "999999999";
        dfs(0,emp);
       
        // cout<<res<<endl;
        return res;
    }
};
```

#### 贪心

> 把pattern 按照III⋯IDDD⋯D 分组，每组前一段是I，后一段是 D。
>
> 遍历每一段，设当前段的长度为 x，我们应该把剩余最小的 x 个数字填到该段上（如果是第一段则填最小的 x+1个数字），从而保证字典序最小。
>
> 举例说明，假如第一段为IIIDDD，构造方案如下：
>
> 前 2 个 I 视作长度为 3 的上升段；
> 剩余的 I 和 D 视作长度为 4 的下降段；
> 最小的 3 个数给上升段，然后剩余最小的 4 个数给下降段；
> 构造结果为1237654。
> 按照该方案分组模拟即可。

```c++
class Solution {
public:
    string smallestNumber(string pattern) {
        int i = 0, n = pattern.length();
        char cur = '1';
        string ans(n + 1, 0);
        while (i < n) {
            if (i && pattern[i] == 'I') ++i;
            for (; i < n && pattern[i] == 'I'; ++i) ans[i] = cur++;
            int i0 = i;
            while (i < n && pattern[i] == 'D') ++i;
            for (int j = i; j >= i0; --j) ans[j] = cur++;
        }
        return ans;
    }
};
```

## 【数位DP】统计特殊整数

### 题目

如果一个正整数每一个数位都是 **互不相同** 的，我们称它是 **特殊整数** 。

给你一个 **正** 整数 `n` ，请你返回区间 `[1, n]` 之间特殊整数的数目。

 

**示例 1：**

```
输入：n = 20
输出：19
解释：1 到 20 之间所有整数除了 11 以外都是特殊整数。所以总共有 19 个特殊整数。
```

**示例 2：**

```
输入：n = 5
输出：5
解释：1 到 5 所有整数都是特殊整数。
```

**示例 3：**

```
输入：n = 135
输出：110
解释：从 1 到 135 总共有 110 个整数是特殊整数。
不特殊的部分数字为：22 ，114 和 131 。
```

 

**提示：**

- `1 <= n <= 2 * 10^9`

### 解题思路

经典数位DP，原题了属于是（1012题）

### 代码

```c++
class Solution {
public:
    int countSpecialNumbers(int n) {
        string num = to_string(n);
        int m = num.size(), dp[m][1<<10];
        memset(dp,-1,sizeof(dp));
       
        function<int(int,int,bool,bool)> dfs =
            [&](int pos, int state, bool is_limit, bool is_num)->int{
            if(pos == m) return is_num;
            if(!is_limit && is_num && dp[pos][state]>=0) return dp[pos][state];
            int res=0;
            //如果前面都没填，第i位明显不受限制，如n=123,第0位没填的话，第1位可填9
            if(!is_num) res = dfs(pos+1,state,false,false);//可以跳过，不填数字
            //根据is_num的值决定从0开始还是1开始，上界由is_limit决定
            //枚举要填的数字
            for(int d=1-is_num,up=is_limit?num[pos]-'0':9;d<=up;d++){
                if((state>>d &1)==0){//只有当前填的数字与之前填的数字没有重复时 才填
                    res += dfs(pos+1,state|(1<<d),is_limit&&d==up,true);
                }
            }
            if(!is_limit && is_num) dp[pos][state] = res;
            return res;
        };
        return dfs(0,0,true,false);
    }
};
```

