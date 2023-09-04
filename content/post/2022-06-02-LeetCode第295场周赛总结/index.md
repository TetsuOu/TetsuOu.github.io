---
title:      LeetCode第295场周赛总结
description:   2181/6447
date:       2022-06-02 00:00:00+0000
image: post-bg-20220602.png
categories:
    - leetcode contest
tags:
    - 线段树
    - BFS
---
![image-20220602162232937](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220602162232937.png)

## 【枚举】重排字符形成目标字符串

### 题目

给你两个下标从 **0** 开始的字符串 `s` 和 `target` 。你可以从 `s` 取出一些字符并将其重排，得到若干新的字符串。

从 `s` 中取出字符并重新排列，返回可以形成 `target` 的 **最大** 副本数。

 

**示例 1：**

```
输入：s = "ilovecodingonleetcode", target = "code"
输出：2
解释：
对于 "code" 的第 1 个副本，选取下标为 4 、5 、6 和 7 的字符。
对于 "code" 的第 2 个副本，选取下标为 17 、18 、19 和 20 的字符。
形成的字符串分别是 "ecod" 和 "code" ，都可以重排为 "code" 。
可以形成最多 2 个 "code" 的副本，所以返回 2 。
```

**示例 2：**

```
输入：s = "abcba", target = "abc"
输出：1
解释：
选取下标为 0 、1 和 2 的字符，可以形成 "abc" 的 1 个副本。 
可以形成最多 1 个 "abc" 的副本，所以返回 1 。
注意，尽管下标 3 和 4 分别有额外的 'a' 和 'b' ，但不能重用下标 2 处的 'c' ，所以无法形成 "abc" 的第 2 个副本。
```

**示例 3：**

```
输入：s = "abbaccaddaeea", target = "aaaaa"
输出：1
解释：
选取下标为 0 、3 、6 、9 和 12 的字符，可以形成 "aaaaa" 的 1 个副本。
可以形成最多 1 个 "aaaaa" 的副本，所以返回 1 。
```

 

**提示：**

- `1 <= s.length <= 100`
- `1 <= target.length <= 10`
- `s` 和 `target` 由小写英文字母组成

### 解题思路

枚举。

### 代码

```c++
class Solution {
public:
    int rearrangeCharacters(string s, string target) {
        vector<int> cnt(26,0),t(26,0);
        for(auto &x: s) cnt[x-'a']++;
        for(auto &x: target) t[x-'a']++;
        int res=100;
        for(int i=0;i<26;i++){
            if(!t[i]) continue;
            res = min(res, cnt[i]/t[i]);
        }
        return res;
    }
};
```

## 【模拟】价格减免

### 题目

**句子** 是由若干个单词组成的字符串，单词之间用单个空格分隔，其中每个单词可以包含数字、小写字母、和美元符号 `'$'` 。如果单词的形式为美元符号后跟着一个非负实数，那么这个单词就表示一个价格。

- 例如 `"$100"`、`"$23"` 和 `"$6.75"` 表示价格，而 `"100"`、`"$"` 和 `"2$3"` 不是。

**注意：**本题输入中的价格均为整数。

给你一个字符串 `sentence` 和一个整数 `discount` 。对于每个表示价格的单词，都在价格的基础上减免 `discount%` ，并 **更新** 该单词到句子中。所有更新后的价格应该表示为一个 **恰好保留小数点后两位** 的数字。

返回表示修改后句子的字符串。

 

**示例 1：**

```
输入：sentence = "there are $1 $2 and 5$ candies in the shop", discount = 50
输出："there are $0.50 $1.00 and 5$ candies in the shop"
解释：
表示价格的单词是 "$1" 和 "$2" 。 
- "$1" 减免 50% 为 "$0.50" ，所以 "$1" 替换为 "$0.50" 。
- "$2" 减免 50% 为 "$1" ，所以 "$1" 替换为 "$1.00" 。
```

**示例 2：**

```
输入：sentence = "1 2 $3 4 $5 $6 7 8$ $9 $10$", discount = 100
输出："1 2 $0.00 4 $0.00 $0.00 7 8$ $0.00 $10$"
解释：
任何价格减免 100% 都会得到 0 。
表示价格的单词分别是 "$3"、"$5"、"$6" 和 "$9"。
每个单词都替换为 "$0.00"。
```

 

**提示：**

- `1 <= sentence.length <= 10^5`
- `sentence` 由小写英文字母、数字、`' '` 和 `'$'` 组成
- `sentence` 不含前导和尾随空格
- `sentence` 的所有单词都用单个空格分隔
- 所有价格都是 **正** 整数且不含前导零
- 所有价格 **最多** 为 `10` 位数字
- `0 <= discount <= 100`

### 解题思路

C++做这题真的人麻了。

两个坑吧，一个是价格最多为10位数字，用stoi函数将string转int时是不够的，可能会溢出，要用stol函数；

一个是精度问题。利用字符串流。

```c++
    long long price = stol(cur.substr(1));
    double p = 1.00*price*(100-discount)/100.00;
    stringstream out;
    out<<fixed<<setprecision(2)<<p;
    string s = out.str();
```

### 代码

```c++
class Solution {
public:
    string discountPrices(string sentence, int discount) {
        stringstream ss(sentence);
        string res,cur;
        while(ss>>cur){
            if(cur.size()>1 && cur[0]=='$'){
                bool flag=true;
                for(int i=1;i<cur.size()&&flag;i++) {
                    if(cur[i]>='0' && cur[i]<='9') continue;
                    else flag=false;
                }
                if(!flag){
                    res += cur+' ';
                    continue;
                }
                long long price = stol(cur.substr(1));
                double p = 1.00*price*(100-discount)/100.00;
                stringstream out;
                out<<fixed<<setprecision(2)<<p;
                string s = out.str();
                cur = '$'+s;
            }
            res += cur+' ';
        }
        res = res.substr(0,res.size()-1);
        return res;
    }
};
```

### 偷学代码

用python很舒服

```python
class Solution:
    def discountPrices(self, sentence: str, discount: int) -> str:
        arr = []
        for x in sentence.split():
            if x.startswith('$') and x[1:].isdigit():
                num = float(x[1:]) * (100 - float(discount)) / 100
                arr.append('${:.2f}'.format(num))
            else:
                arr.append(x)
        return ' '.join(arr)
```

## 【单调栈+DP】使数组按非递减顺序排列

### 题目

给你一个下标从 **0** 开始的整数数组 `nums` 。在一步操作中，移除所有满足 `nums[i - 1] > nums[i]` 的 `nums[i]` ，其中 `0 < i < nums.length` 。

重复执行步骤，直到 `nums` 变为 **非递减** 数组，返回所需执行的操作数。

 

**示例 1：**

```
输入：nums = [5,3,4,4,7,3,6,11,8,5,11]
输出：3
解释：执行下述几个步骤：
- 步骤 1 ：[5,3,4,4,7,3,6,11,8,5,11] 变为 [5,4,4,7,6,11,11]
- 步骤 2 ：[5,4,4,7,6,11,11] 变为 [5,4,7,11,11]
- 步骤 3 ：[5,4,7,11,11] 变为 [5,7,11,11]
[5,7,11,11] 是一个非递减数组，因此，返回 3 。
```

**示例 2：**

```
输入：nums = [4,5,7,7,13]
输出：0
解释：nums 已经是一个非递减数组，因此，返回 0 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`

### 解题思路

> 元素 x 会被左边某个比他大的元素 y 给删除（如果存在的话）。
>
> 我们需要计算在删除 x 之前，删除了多少个比 y 小的元素，从而算出删除 x 的时刻（第几步操作）。
>
> 答案可以转换成所有元素被删除的时刻的最大值。
>

考虑每一个数被删除的时间点。如果num[i]左侧没有比它更大的数，那么该数不会被删除；如果左侧有比它更大的数，记左侧最近比它大的数的位置为L，那么num[L+1..i]这几个数都可以看成是被num[L]删除的，此时t[i]=max(t[L+1..i-1])+1，即在中间的数删除后加1。

### 单调栈+DP+线段树

```c++
const int MAXN = 1e5+5;
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
class Solution {
public:
    struct node{
        int maxv;
    }T[MAXN<<2];
    void push_up(int p){
        T[p].maxv=max(T[ls(p)].maxv,T[rs(p)].maxv);
    }
    void build(int p, int l, int r){
        T[p]={0};
        if(l==r) return;
        int mid=(l+r)>>1;
        build(ls(p),l,mid);
        build(rs(p),mid+1,r);
    }
    void update(int p, int l, int r, int nr, int k){
        if(l==r && l==nr){
            T[p].maxv = k;
            return;
        }
        int mid=(l+r)>>1;
        if(nr<=mid) update(ls(p),l,mid,nr,k);
        else update(rs(p),mid+1,r,nr,k);
        push_up(p);
    }
    int query(int p, int l, int r, int nl, int nr){
        if(nl<=l&&nr>=r) return T[p].maxv;
        int mid=(l+r)>>1;
        int res=0;
        if(nl<=mid) res=max(res,query(ls(p),l,mid,nl,nr));
        if(nr>mid) res=max(res,query(rs(p),mid+1,r,nl,nr));
        return res;
    }
    int totalSteps(vector<int>& nums) {
        int n = nums.size();
        vector<int> L(n,-1);//L[i] 左边第一个比nums[i]大的数的位置
        stack<int> stk;
        stk.push(0);
        for(int i=1;i<n;i++){
            while(!stk.empty() && nums[i]>=nums[stk.top()]) stk.pop();
            if(stk.empty()) L[i] = -1;
            else L[i] = stk.top();
            stk.push(i);
        }

        vector<int> t(n,0);//t[i] 删除nums[i]所需执行的操作数
        int res = 0;
        
        build(1,1,n);
        for(int i=0;i<n;i++){
            if(L[i]==-1) t[i]=0;//nums[i]左边没有比它更大的数，不会被删除
            //else if(L[i]==i-1) t[i]=1;//会被相邻左边元素删掉，步数为1
            else{
                //t[i]=max(t[l+1..i-1])
                t[i]=query(1,1,n,L[i]+1+1,i-1+1)+1;
            }
            update(1,1,n,i+1,t[i]);
            res = max(res,t[i]);
        }

        return res;
    }
};
```

### 单调栈+DP

仅仅使用单调栈也是可以的。

```c++
class Solution {
public:
    int totalSteps(vector<int>& nums) {
        int n = nums.size();
        vector<int> t(n,0);//t[i] 删除nums[i]所需要的时间
        int res=0;
        stack<int> stk;
        for(int i=0;i<n;i++){
            int cur=0;//删除nums[i]所需要的时间
            while(!stk.empty() && nums[i]>=nums[stk.top()]){
                cur=max(cur,t[stk.top()]);
                stk.pop();
            } 
            if(stk.empty()) cur=0;//左侧没有更大的数，不会被删
            else cur+=1;//有，删除该数需要加1次操作
            stk.push(i);
            t[i]=cur;
            res=max(res,cur);
        }

        return res;
    }
};
```



## 【最短路】到达角落需要移除障碍物的最小数目

### 题目

给你一个下标从 **0** 开始的二维整数数组 `grid` ，数组大小为 `m x n` 。每个单元格都是两个值之一：

- `0` 表示一个 **空** 单元格，
- `1` 表示一个可以移除的 **障碍物** 。

你可以向上、下、左、右移动，从一个空单元格移动到另一个空单元格。

现在你需要从左上角 `(0, 0)` 移动到右下角 `(m - 1, n - 1)` ，返回需要移除的障碍物的 **最小** 数目。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/04/06/example1drawio-1.png)

```
输入：grid = [[0,1,1],[1,1,0],[1,1,0]]
输出：2
解释：可以移除位于 (0, 1) 和 (0, 2) 的障碍物来创建从 (0, 0) 到 (2, 2) 的路径。
可以证明我们至少需要移除两个障碍物，所以返回 2 。
注意，可能存在其他方式来移除 2 个障碍物，创建出可行的路径。
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2022/04/06/example1drawio.png)

```
输入：grid = [[0,1,0,0,0],[0,1,0,1,0],[0,0,0,1,0]]
输出：0
解释：不移除任何障碍物就能从 (0, 0) 到 (2, 4) ，所以返回 0 。
```

 

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 10^5`
- `2 <= m * n <= 10^5`
- `grid[i][j]` 为 `0` **或** `1`
- `grid[0][0] == grid[m - 1][n - 1] == 0`

### 解题思路

一开始没想到用最短路相关算法做。

只想着用BFS+优先队列，一路标记状态避免访问重复状态，最先到达终点时就是最小移除障碍数目。标记状态时，状态有3部分。

这种做法很接近堆优化的Dijkstra算法了。

注意一点，优先队列priority_queue默认是最大堆，要么在结构体定义<号时反过来，~~要么还是在结构体中正常定义，但是将堆声明为最小堆~~（这种方式好像不行，只能在结构体定义中注意一下了）。

### 代码1.0（TLE）

状态是3元组，用set存自定义结构体。超时了。

```c++
class Solution {
public:
    struct node{
        int x,y;
        int del;
        node(int _x,int _y,int _d){
            x=_x,y=_y,del=_d;
        }
        friend bool operator < (const node& a, const node& b){
            if(a.del!=b.del) return a.del>b.del;
            else if(a.x!=b.x) return a.x<b.x;
            else return a.y<b.y;
        }
    };
    int dir[4][2]={ {0,1},{0,-1},{1,0},{-1,0} };//右 左 上 下
    
    int minimumObstacles(vector<vector<int>>& grid) {
        int n = grid.size(),m=grid[0].size();
        set<node> tab;
        // queue<node> que;
        priority_queue<node> que;
        que.emplace(0,0,0);
        tab.insert(node(0,0,0));
        function<bool(int,int)> isok = [&](int p,int q){
            if(p<0||p>=n) return false;
            if(q<0||q>=m) return false;
            return true;
        };   
       
        while(!que.empty()){
            auto [x,y,del] = que.top();
            que.pop();
            if(x==n-1&&y==m-1) return del;
            for(int i=0;i<4;i++){
                int dx=x+dir[i][0];
                int dy=y+dir[i][1];
                if(!isok(dx,dy)) continue;
                if(grid[dx][dy]==1) {
                    if(tab.count(node(dx,dy,del+1))) continue;
                    tab.insert(node(dx,dy,del+1));
                    que.push(node(dx,dy,del+1));
                }else{
                    if(tab.count(node(dx,dy,del))) continue;
                    tab.insert(node(dx,dy,del));
                    que.push(node(dx,dy,del));
                }
            } 
        }
        return 0;
    }
};
```



### 代码2.0（TLE）

将set换成unordered_set，需要自定义哈希方法，结果需要的时间反而更多？通过样例更少

```c++
class Solution {
public:
    struct node{
        int x,y;
        int del;
        node(int _x,int _y,int _d){
            x=_x,y=_y,del=_d;
        }
        friend bool operator < (const node& a, const node& b){
            if(a.del!=b.del) return a.del>b.del;
            else if(a.x!=b.x) return a.x<b.x;
            else return a.y<b.y;
        }
        friend bool operator ==(const node& a, const node& b){
            return a.x==b.x&&a.y==b.y&&a.del==b.del;
        }
    };
    struct nodeHash{
        size_t operator () (const node& t) const{
            return t.x*100+t.y*10+t.del;
        }
    };
    int dir[4][2]={ {0,1},{0,-1},{1,0},{-1,0} };//右 左 上 下
    
    int minimumObstacles(vector<vector<int>>& grid) {
        int n = grid.size(),m=grid[0].size();
        // set<node> tab;
        unordered_set<node,nodeHash> tab;
        
        // queue<node> que;
        priority_queue<node> que;
        que.emplace(0,0,0);
        tab.insert(node(0,0,0));
        function<bool(int,int)> isok = [&](int p,int q){
            if(p<0||p>=n) return false;
            if(q<0||q>=m) return false;
            return true;
        };   
       
        while(!que.empty()){
            auto [x,y,del] = que.top();
            que.pop();
            
            if(x==n-1&&y==m-1) return del;
            for(int i=0;i<4;i++){
                int dx=x+dir[i][0];
                int dy=y+dir[i][1];
                if(!isok(dx,dy)) continue;
                if(grid[dx][dy]==1) {
                    if(tab.count(node(dx,dy,del+1))) continue;
                    tab.insert(node(dx,dy,del+1));
                    que.push(node(dx,dy,del+1));
                }else{
                    if(tab.count(node(dx,dy,del))) continue;
                    tab.insert(node(dx,dy,del));
                    que.push(node(dx,dy,del));
                }
            }
        }
        return 0;
    }
};
```

### 代码3.0（堆优化的Dijkstra）

将空白处的2个节点相互之间是一条权值为0的边，与障碍物则是一条权值为1的边，实际上这题就是最短路径问题。

dst[x,y] 起点到该点的最短距离

```c++
const int INF = 0x3f3f3f3f;
class Solution {
public:
    struct node{
        int x,y;
        int del;
        node(int _x,int _y,int _d){
            x=_x,y=_y,del=_d;
        }
        friend bool operator < (const node& a, const node& b){
            if(a.del!=b.del) return a.del>b.del;
            else if(a.x!=b.x) return a.x<b.x;
            else return a.y<b.y;
        }
       
    };
    
    int dir[4][2]={ {0,1},{0,-1},{1,0},{-1,0} };//右 左 上 下
    
    int minimumObstacles(vector<vector<int>>& grid) {
        int n = grid.size(),m=grid[0].size();
        
        vector<vector<int>> dst(n,vector<int>(m,INF));
        vector<vector<int>> vis(n,vector<int>(m,0));
        
        priority_queue<node> que;
        que.emplace(0,0,0);
        dst[0][0]=0;
        vis[0][0]=1;
        function<bool(int,int)> isok = [&](int p,int q){
            if(p<0||p>=n) return false;
            if(q<0||q>=m) return false;
            return true;
        };   
        while(!que.empty()){
            auto [x,y,del] = que.top();
            que.pop();
            if(x==n-1&&y==m-1) return del;
            for(int i=0;i<4;i++){
                int dx=x+dir[i][0];
                int dy=y+dir[i][1];
                if(!isok(dx,dy)) continue;
                if(vis[dx][dy]) continue;
                if(del+grid[dx][dy]<dst[dx][dy]){
                    dst[dx][dy]=del+grid[dx][dy];
                    que.push(node(dx,dy,dst[dx][dy]));
                    vis[dx][dy]=1;
                }
            }
            
        }
        return dst[n-1][m-1];
    }
};
```

### 代码4.0（01BFS）

BFS为什么能求最短路径。在每个权值相同的情况下，BFS求的是最短路径。像这种只有0和另一权值的，也可以用BFS，不过一个队列是不够的，要么拿两个队列，要么拿一个双端队列，0的话加在开头，另一权值的加在末尾。

即不用到优先队列，就可以判断最短距离。注意，只有权值为0和其他值才可以，但是如果是2个不为0的，就不能保证。

```c++
const int INF = 0x3f3f3f3f;
class Solution {
public:
    struct node{
        int x,y;
        int del;
        node(int _x,int _y,int _d){
            x=_x,y=_y,del=_d;
        }
    };
    
    int dir[4][2]={ {0,1},{0,-1},{1,0},{-1,0} };//右 左 上 下
    
    int minimumObstacles(vector<vector<int>>& grid) {
        int n = grid.size(),m=grid[0].size();
        
        vector<vector<int>> dst(n,vector<int>(m,INF));
        vector<vector<int>> vis(n,vector<int>(m,0));
        deque<node> que;
        que.emplace_back(0,0,0);
        dst[0][0]=0;
        vis[0][0]=1;
        function<bool(int,int)> isok = [&](int p,int q){
            if(p<0||p>=n) return false;
            if(q<0||q>=m) return false;
            return true;
        };   
        while(!que.empty()){
            auto [x,y,del] = que.front();
            que.pop_front();
            if(x==n-1&&y==m-1) return del;
            for(int i=0;i<4;i++){
                int dx=x+dir[i][0];
                int dy=y+dir[i][1];
                if(!isok(dx,dy)) continue;
                if(vis[dx][dy]) continue;
                if(grid[dx][dy]==1) que.emplace_back(dx,dy,del+1);
                else que.emplace_front(dx,dy,del);
                vis[dx][dy]=1;
            }
        }
        return 0;
    }
};
```

