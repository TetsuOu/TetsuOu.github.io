---
title:      LeetCode第289场周赛总结
description:   1843/7293
date:       2022-04-18 00:00:00+0000
image:      post-bg-20220418.png
categories: leetcode contest
math: true
tags:
    - 树型DP
---

![image-20220418100746064](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220418100746064.png)

第3题都快做晕了~

## 【模拟】计算字符串的数字和

### 题目

给你一个由若干数字（`0` - `9`）组成的字符串 `s` ，和一个整数。

如果 `s` 的长度大于 `k` ，则可以执行一轮操作。在一轮操作中，需要完成以下工作：

1. 将 `s` **拆分** 成长度为 `k` 的若干 **连续数字组** ，使得前 `k` 个字符都分在第一组，接下来的 `k` 个字符都分在第二组，依此类推。**注意**，最后一个数字组的长度可以小于 `k` 。
2. 用表示每个数字组中所有数字之和的字符串来 **替换** 对应的数字组。例如，`"346"` 会替换为 `"13"` ，因为 `3 + 4 + 6 = 13` 。
3. **合并** 所有组以形成一个新字符串。如果新字符串的长度大于 `k` 则重复第一步。

返回在完成所有轮操作后的 `s` 。

 

**示例 1：**

```
输入：s = "11111222223", k = 3
输出："135"
解释：
- 第一轮，将 s 分成："111"、"112"、"222" 和 "23" 。
  接着，计算每一组的数字和：1 + 1 + 1 = 3、1 + 1 + 2 = 4、2 + 2 + 2 = 6 和 2 + 3 = 5 。 
  这样，s 在第一轮之后变成 "3" + "4" + "6" + "5" = "3465" 。
- 第二轮，将 s 分成："346" 和 "5" 。
  接着，计算每一组的数字和：3 + 4 + 6 = 13 、5 = 5 。
  这样，s 在第二轮之后变成 "13" + "5" = "135" 。 
现在，s.length <= k ，所以返回 "135" 作为答案。
```

**示例 2：**

```
输入：s = "00000000", k = 3
输出："000"
解释：
将 "000", "000", and "00".
接着，计算每一组的数字和：0 + 0 + 0 = 0 、0 + 0 + 0 = 0 和 0 + 0 = 0 。 
s 变为 "0" + "0" + "0" = "000" ，其长度等于 k ，所以返回 "000" 。
```

 

**提示：**

- `1 <= s.length <= 100`
- `2 <= k <= 100`
- `s` 仅由数字（`0` - `9`）组成。

### 解题思路

简单模拟即可

### 代码

```c++
class Solution {
public:
    string digitSum(string s, int k) {
        int res = 0;
        
        while(s.size()>k){
            int len = s.size();
            string news;
            int loop =0,cur = 0;
            for(int i=0;i<len;i++){
                cur += s[i]-'0';
                loop++;
                if(loop==k){
                    loop = 0;
                    news = news + to_string(cur);
                    cur = 0;
                }
            }
            if(loop) {
                news = news + to_string(cur);
            }
            s = news;
            // cout<<news<<endl;
        }
        
        return s;
        
    }
};
```



## 【找规律】完成所有任务需要的最少轮数

### 题目

给你一个下标从 **0** 开始的整数数组 `tasks` ，其中 `tasks[i]` 表示任务的难度级别。在每一轮中，你可以完成 2 个或者 3 个 **相同难度级别** 的任务。

返回完成所有任务需要的 **最少** 轮数，如果无法完成所有任务，返回 `-1` 。

 

**示例 1：**

```
输入：tasks = [2,2,3,3,2,4,4,4,4,4]
输出：4
解释：要想完成所有任务，一个可能的计划是：
- 第一轮，完成难度级别为 2 的 3 个任务。 
- 第二轮，完成难度级别为 3 的 2 个任务。 
- 第三轮，完成难度级别为 4 的 3 个任务。 
- 第四轮，完成难度级别为 4 的 2 个任务。 
可以证明，无法在少于 4 轮的情况下完成所有任务，所以答案为 4 。
```

**示例 2：**

```
输入：tasks = [2,3,3]
输出：-1
解释：难度级别为 2 的任务只有 1 个，但每一轮执行中，只能选择完成 2 个或者 3 个相同难度级别的任务。因此，无法完成所有任务，答案为 -1 。
```

 

**提示：**

- `1 <= tasks.length <= 10^5`
- `1 <= tasks[i] <= 10^9`

### 解题思路

若这种任务只有 1个，则无法完成；
若这种任务只有 2 个，可以一次完成；
若这种任务数量是 3 的倍数，显然每次完成 3 个是最快的；
若这种任务数量除 3 余 1，需要每次完成 3 个，最后 4 个分两次完成；
若这种任务数量除 3 余 2，需要每次完成 3 个，最后完成 2 个。

### 代码

```c++
class Solution {
public:
    int minimumRounds(vector<int>& tasks) {
        
        unordered_map<int,int> table;
        for(auto x:tasks){
            if(table.count(x)) table[x]++;
            else table[x]=1;
        }
       
        int res = 0;
        for(auto [level,num]:table){
            // cout<<level<<' '<<num<<endl;
            int cnt = num/3;
            int mod = num%3;
            if(mod == 0){
                res += cnt;
            }else if(mod == 1){
                if(num == 1) return -1;
                cnt = (num - 2) /3;
                res += 1;
                res += cnt;
                res += 1;//必定剩余2
                
            }else if(mod == 2){
                res += cnt;
                res += 1;
            }
        }
        
        return res;
    }
};
```

## 【前缀和、枚举】转角路径的乘积中最多能有几个尾随零

### 题目

给你一个二维整数数组 `grid` ，大小为 `m x n`，其中每个单元格都含一个正整数。

**转角路径** 定义为：包含至多一个弯的一组相邻单元。具体而言，路径应该完全 **向水平方向** 或者 **向竖直方向** 移动过弯（如果存在弯），而不能访问之前访问过的单元格。在过弯之后，路径应当完全朝 **另一个** 方向行进：如果之前是向水平方向，那么就应该变为向竖直方向；反之亦然。当然，同样不能访问之前已经访问过的单元格。

一条路径的 **乘积** 定义为：路径上所有值的乘积。

请你从 `grid` 中找出一条乘积中尾随零数目最多的转角路径，并返回该路径中尾随零的数目。

注意：

- **水平** 移动是指向左或右移动。
- **竖直** 移动是指向上或下移动。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/03/23/ex1new2.jpg)

```
输入：grid = [[23,17,15,3,20],[8,1,20,27,11],[9,4,6,2,21],[40,9,1,10,6],[22,7,4,5,3]]
输出：3
解释：左侧的图展示了一条有效的转角路径。
其乘积为 15 * 20 * 6 * 1 * 10 = 18000 ，共计 3 个尾随零。
可以证明在这条转角路径的乘积中尾随零数目最多。

中间的图不是一条有效的转角路径，因为它有不止一个弯。
右侧的图也不是一条有效的转角路径，因为它需要重复访问已经访问过的单元格。
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2022/03/25/ex2.jpg)

```
输入：grid = [[4,3,2],[7,6,1],[8,8,8]]
输出：0
解释：网格如上图所示。
不存在乘积含尾随零的转角路径。
```

 

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 10^5`
- `1 <= m * n <= 10^5`
- `1 <= grid[i][j] <= 1000`

### 解题思路

最多有几个尾随零：取决于因子中2和5的最小个数

那么重点就是找转角路径，使得路径中有最大的2和5因子的最小个数

一开始还想深搜，但显然不可能。

想了会确定好了思路：每次找的转角路径，要使其最长，如上面例1中的绿色路径，明显还可以把6加进去。

那么就可以从边角出发，可以找**先右后下**的路径，或者是**先下后右**的路径，当然，还有先右后上和先上后右（我这里只算了前两种情况，后两种我将原矩阵进行行翻转后，再执行一次前两种情况一样可以达到效果）

下图中每一对元素如（0，1），表示grid\[0]\[0]中5的因子个数为0，2的因子个数为1。

整体思路为：

1）预处理原数据，转换为数据中包含的5因子个数和2因子个数

2）计算行上前缀和 和 列上前缀和

3）枚举拐点（或起点）计算4个方向（右下，右上，左下，左上）的情况，取最大值





<img src="https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220418103035202.png" alt="image-20220418103035202" style="zoom:50%;" />

### 代码

```c++
class Solution {
public:
    vector<vector<int>> G;
    int m,n;//m行n列
    
    vector<vector<int>> five,two,visit;
    
    int getnum5(int num){
        int res = 0;
        while(num/5){
            if(num%5==0) res++,num/=5;
            else break;
        }
        return res;
    }
    int getnum2(int num){
        int res = 0;
        while(num/2){
            if(num%2==0) res++,num/=2;
            else break;
        }
        return res;
    }
    //计算原矩阵的2因子个数和5因子个数
    void init(){
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                five[i][j] = getnum5(G[i][j]);
                two[i][j] = getnum2(G[i][j]);
            }
        }
    }

    int work(vector<vector<int>> &a, vector<vector<int>> &b){//a:5  b:2
        int res = 0;
        
        int pre5row[m][n],pre2row[m][n],pre5col[m][n],pre2col[m][n];
        memset(pre5row,0,sizeof(pre5row));
        memset(pre2row,0,sizeof(pre2row));
        memset(pre5col,0,sizeof(pre5col));
        memset(pre2col,0,sizeof(pre2col));
        //计算前缀和
        for(int i=0;i<m;i++){
            pre5row[i][0] = a[i][0];
            pre2row[i][0] = b[i][0];
            for(int j=1;j<n;j++){
                pre5row[i][j] = pre5row[i][j-1]+a[i][j];
                pre2row[i][j] = pre2row[i][j-1]+b[i][j];
            }
        }
        
        for(int j=0;j<n;j++){
            pre5col[0][j] = a[0][j];
            pre2col[0][j] = b[0][j];
            for(int i=1;i<m;i++){
                pre5col[i][j] = pre5col[i-1][j]+a[i][j];
                pre2col[i][j] = pre2col[i-1][j]+b[i][j];
            }
        }
        
        //右下
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                int numof5 = pre5row[i][j] + pre5col[m-1][j] - a[i][j];
                if(i>0) numof5 -= pre5col[i-1][j];
                int numof2 = pre2row[i][j] + pre2col[m-1][j] - b[i][j];
                if(i>0) numof2 -= pre2col[i-1][j];
                res = max(res, min(numof5,numof2));
            }
            
        }
        //下右
        for(int j=0;j<n;j++){
            for(int i=0;i<m;i++){
                int numof5 = pre5col[i][j] + pre5row[i][n-1] - a[i][j];
                if(j>0) numof5 -= pre5row[i][j-1];
                int numof2 = pre2col[i][j] + pre2row[i][n-1] - b[i][j];
                if(j>0) numof2 -= pre2row[i][j-1];
                res = max(res, min(numof5,numof2));
            }
            
        }
        return res;
        
    }
    int maxTrailingZeros(vector<vector<int>>& grid) {
        G = grid;
        m = grid.size();
        n = grid[0].size();
        five.resize(m);
        for(auto &x:five) x.resize(n,0);
        two.resize(m);
        for(auto &x:two) x.resize(n,0);
        
        
        init();
        int res = work(five,two);
        //行翻转后再调用一次
        reverse(five.begin(),five.end());
        reverse(two.begin(),two.end());
        res = max(res, work(five,two) );
        return res;
        
        
    }
};
```

### 偷学代码

```c++
class Solution {
public:
    int maxTrailingZeros(vector<vector<int>>& grid) {
        int n = grid.size(), m = grid[0].size();//n行m列
        vector<vector<int>> f2(n + 1), g2(n + 1), f5(n + 1), g5(n + 1);
        for (int i = 0; i <= n; i++) f2[i] = g2[i] = f5[i] = g5[i] = vector<int>(m + 1);
        for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) {
            int x = grid[i - 1][j - 1];
            int two = 0, five = 0;
            while (x % 2 == 0) two++, x /= 2;
            while (x % 5 == 0) five++, x /= 5;
            f2[i][j] = f2[i][j - 1] + two;//f2[i][j] 第i行第j列的行上前缀和 （2的总个数）
            g2[i][j] = g2[i - 1][j] + two;//g2[i][j] 第i行第j列的列上前缀和 （2的总个数）
            f5[i][j] = f5[i][j - 1] + five;
            g5[i][j] = g5[i - 1][j] + five;
        }
        int ans = 0;
        for (int i = 1; i <= n; i++) for (int j = 1; j <= m; j++) {
            // 从左边出发，到上边结束
            ans = max(ans, min(f2[i][j] + g2[i - 1][j], f5[i][j] + g5[i - 1][j]));
            // 从左边出发，到下边结束
            ans = max(ans, min(f2[i][j] + g2[n][j] - g2[i][j], f5[i][j] + g5[n][j] - g5[i][j]));
            // 从右边出发，到上边结束
            ans = max(ans, min(f2[i][m] - f2[i][j] + g2[i][j], f5[i][m] - f5[i][j] + g5[i][j]));
            // 从右边出发，到下边结束
            ans = max(ans, min(f2[i][m] - f2[i][j] + g2[n][j] - g2[i - 1][j], f5[i][m] - f5[i][j] + g5[n][j] - g5[i - 1][j]));
        }
        return ans;
    }
};

```

### tips

以后求前缀和，计算出来的前缀和数组还是用1开头计数吧。还是用0开始计数的话，不好写遍历，在=0的时候要加特判，而用1开始计数的话，就不用特判 （**注意保证此时第0个元素为0**）。

还有，注意看全题目，一开始只注意到了n和m的范围是10\^5，想着O(nm)的时间复杂度过不了。后来经人提醒发现，nm的乘积是小于10\^5的。

## 【DFS】相邻字符不同的最长路径

### 题目

给你一棵 **树**（即一个连通、无向、无环图），根节点是节点 `0` ，这棵树由编号从 `0` 到 `n - 1` 的 `n` 个节点组成。用下标从 **0** 开始、长度为 `n` 的数组 `parent` 来表示这棵树，其中 `parent[i]` 是节点 `i` 的父节点，由于节点 `0` 是根节点，所以 `parent[0] == -1` 。

另给你一个字符串 `s` ，长度也是 `n` ，其中 `s[i]` 表示分配给节点 `i` 的字符。

请你找出路径上任意一对相邻节点都没有分配到相同字符的 **最长路径** ，并返回该路径的长度。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/03/25/testingdrawio.png)

```
输入：parent = [-1,0,0,1,1,2], s = "abacbe"
输出：3
解释：任意一对相邻节点字符都不同的最长路径是：0 -> 1 -> 3 。该路径的长度是 3 ，所以返回 3 。
可以证明不存在满足上述条件且比 3 更长的路径。 
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2022/03/25/graph2drawio.png)

```
输入：parent = [-1,0,0,0], s = "aabc"
输出：3
解释：任意一对相邻节点字符都不同的最长路径是：2 -> 0 -> 3 。该路径的长度为 3 ，所以返回 3 。
```

 

**提示：**

- `n == parent.length == s.length`
- `1 <= n <= 10^5`
- 对所有 `i >= 1` ，`0 <= parent[i] <= n - 1` 均成立
- `parent[0] == -1`
- `parent` 表示一棵有效的树
- `s` 仅由小写英文字母组成

### 解题思路

这是道有关 树的直径 的问题，可以用树型DP解决，过几天再研究下这个专题

具体回到这道题，没有那么复杂，首先明确：这里的最长路径长度，即一个根节点，加上两条（最长和次长）的子节点的路径（再加1）即可。

然后需要判断是否有相邻节点分配到相同字符。

### 代码

```c++
class Solution {
public:
    vector<vector<int>> G;//邻接矩阵
    string s;
    int res;
    int dfs(int u){//节点u到其子孙节点合法的最长距离
        int t1=0,t2=0;//子节点到其子孙节点合法的最长距离、子节点到其子孙节点合法的次长距离
        for(auto v:G[u]){//遍历子节点
            if(s[v] == s[u]){//相邻字符相同 即子节点与父亲节点字符相同
                dfs(v);//求子节点到其子孙节点合法的最长距离 
            }else{
                int cur = dfs(v);
                if(cur>t1) t2 = t1, t1 = cur;//更新最大值
                else if(cur>t2) t2 = cur;//更新次大值
            }
        }
        res = max(res,t1+t2+1);//以u为根节点的最长路径：最长+次长+1
        return t1+1;//加上本身的1
    }
    int longestPath(vector<int>& parent, string s) {
        G.resize(parent.size());
        this->s = s;
        for(int i=1;i<parent.size();i++) 
            G[parent[i]].push_back(i);//建图
        dfs(0);//从根节点出发
        return res;
    }
};
```

