---
title:      LeetCode第300场周赛总结
description:   2566/6792
date:       2022-07-06
image: post-bg-20220706.png
categories:
    - leetcode contest
tags:
    - 动态规划
---

![image-20220706102838821](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220706102838821.png)

## 【模拟】解密消息

### 题目

给你字符串 `key` 和 `message` ，分别表示一个加密密钥和一段加密消息。解密 `message` 的步骤如下：

1. 使用 `key` 中 26 个英文小写字母第一次出现的顺序作为替换表中的字母 **顺序** 。
2. 将替换表与普通英文字母表对齐，形成对照表。
3. 按照对照表 **替换** `message` 中的每个字母。
4. 空格 `' '` 保持不变。

- 例如，`key = "***hap***p***y*** ***bo***y"`（实际的加密密钥会包含字母表中每个字母 **至少一次**），据此，可以得到部分对照表（`'h' -> 'a'`、`'a' -> 'b'`、`'p' -> 'c'`、`'y' -> 'd'`、`'b' -> 'e'`、`'o' -> 'f'`）。

返回解密后的消息。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/05/08/ex1new4.jpg)

```
输入：key = "the quick brown fox jumps over the lazy dog", message = "vkbs bs t suepuv"
输出："this is a secret"
解释：对照表如上图所示。
提取 "the quick brown fox jumps over the lazy dog" 中每个字母的首次出现可以得到替换表。
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2022/05/08/ex2new.jpg)

```
输入：key = "eljuxhpwnyrdgtqkviszcfmabo", message = "zwx hnfx lqantp mnoeius ycgk vcnjrdb"
输出："the five boxing wizards jump quickly"
解释：对照表如上图所示。
提取 "eljuxhpwnyrdgtqkviszcfmabo" 中每个字母的首次出现可以得到替换表。
```

 

**提示：**

- `26 <= key.length <= 2000`
- `key` 由小写英文字母及 `' '` 组成
- `key` 包含英文字母表中每个字符（`'a'` 到 `'z'`）**至少一次**
- `1 <= message.length <= 2000`
- `message` 由小写英文字母和 `' '` 组成

### 解题思路

简单模拟即可

### 代码

```c++
class Solution {
public:
    string decodeMessage(string key, string message) {
        vector<int> mp(26,-1);
        int id=0;
        for(auto &x: key){
            if(x==' ') continue;
            if(mp[x-'a']==-1){
                mp[x-'a'] = id++;
            }
        }
        for(auto &x: message){
            if(x==' ') continue;
            x = 'a' + mp[x-'a'];
        }        
        return message;
    }
};
```

## 【模拟】螺旋矩阵 IV

### 题目

给你两个整数：`m` 和 `n` ，表示矩阵的维数。

另给你一个整数链表的头节点 `head` 。

请你生成一个大小为 `m x n` 的螺旋矩阵，矩阵包含链表中的所有整数。链表中的整数从矩阵 **左上角** 开始、**顺时针** 按 **螺旋** 顺序填充。如果还存在剩余的空格，则用 `-1` 填充。

返回生成的矩阵。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/05/09/ex1new.jpg)

```
输入：m = 3, n = 5, head = [3,0,2,6,8,1,7,9,4,2,5,5,0]
输出：[[3,0,2,6,8],[5,0,-1,-1,1],[5,2,4,9,7]]
解释：上图展示了链表中的整数在矩阵中是如何排布的。
注意，矩阵中剩下的空格用 -1 填充。
```

**示例 2：**

![img](https://assets.leetcode.com/uploads/2022/05/11/ex2.jpg)

```
输入：m = 1, n = 4, head = [0,1,2]
输出：[[0,1,2,-1]]
解释：上图展示了链表中的整数在矩阵中是如何从左到右排布的。 
注意，矩阵中剩下的空格用 -1 填充。
```

 

**提示：**

- `1 <= m, n <= 10^5`
- `1 <= m * n <= 10^5`
- 链表中节点数目在范围 `[1, m * n]` 内
- `0 <= Node.val <= 1000`

### 解题思路

利用宽搜进行模拟。

WA了一次是因为最后一个点，4个方向都走不了的时候，应该返回了

### 代码

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> spiralMatrix(int m, int n, ListNode* head) {
        //右 下 左 上
        int dir[4][2] = { {0,1}, {1,0}, {0,-1}, {-1,0}};
        
        vector<vector<int>> vis(m, vector<int>(n,0));
        vector<vector<int>> g(m,vector<int>(n,-1));
        ListNode* tmp = head;
        queue<pair<int,int>> que;
        que.emplace(0,0);
        vis[0][0]=1;
        int flag = 0;//初始向右
        while(tmp!=nullptr){
            auto [x,y] = que.front();
            g[x][y] = tmp->val;
            int dx = x+dir[flag][0];
            int dy = y+dir[flag][1];
            int num=0;
            while((dx<0 || dx>=m || dy<0 || dy>=n) || vis[dx][dy] ){
                flag = (flag+1)%4;
                num++;
                dx = x+dir[flag][0];
                dy = y+dir[flag][1];
                if(num>=4) return g;
            }
            que.pop();
            vis[dx][dy]=1;
            tmp = tmp->next;
            que.emplace(dx, dy);
        }
        return g;
    }
};
```

## 【差分】知道秘密的人数

### 题目

在第 `1` 天，有一个人发现了一个秘密。

给你一个整数 `delay` ，表示每个人会在发现秘密后的 `delay` 天之后，**每天** 给一个新的人 **分享** 秘密。同时给你一个整数 `forget` ，表示每个人在发现秘密 `forget` 天之后会 **忘记** 这个秘密。一个人 **不能** 在忘记秘密那一天及之后的日子里分享秘密。

给你一个整数 `n` ，请你返回在第 `n` 天结束时，知道秘密的人数。由于答案可能会很大，请你将结果对 `10^9 + 7` **取余** 后返回。

 

**示例 1：**

```
输入：n = 6, delay = 2, forget = 4
输出：5
解释：
第 1 天：假设第一个人叫 A 。（一个人知道秘密）
第 2 天：A 是唯一一个知道秘密的人。（一个人知道秘密）
第 3 天：A 把秘密分享给 B 。（两个人知道秘密）
第 4 天：A 把秘密分享给一个新的人 C 。（三个人知道秘密）
第 5 天：A 忘记了秘密，B 把秘密分享给一个新的人 D 。（三个人知道秘密）
第 6 天：B 把秘密分享给 E，C 把秘密分享给 F 。（五个人知道秘密）
```

**示例 2：**

```
输入：n = 4, delay = 1, forget = 3
输出：6
解释：
第 1 天：第一个知道秘密的人为 A 。（一个人知道秘密）
第 2 天：A 把秘密分享给 B 。（两个人知道秘密）
第 3 天：A 和 B 把秘密分享给 2 个新的人 C 和 D 。（四个人知道秘密）
第 4 天：A 忘记了秘密，B、C、D 分别分享给 3 个新的人。（六个人知道秘密）
```

 

**提示：**

- `2 <= n <= 1000`
- `1 <= delay < forget <= n`

### 解题思路

赛时太迷糊了

赛后按原思路写出来了

第一个人知道秘密的时间区间为[1, 1+forget-1]，而会去分享秘密的时间区间为[1+delay, 1+forget-1]

在第i天时，只有处于分享秘密状态的人才会去分享秘密

设第i天处于分享秘密状态的人数为sub，那么在区间[i, i+forget-1]上会新增sub个人知道秘密，在区间[i+delay, i+forget-1]上会新增sub个人开始分享秘密

利用差分数组进行区间修改、单点查询

### 代码

```c++
#define ll long long
const int MOD = 1e9+7;
class Solution {
public:
    int peopleAwareOfSecret(int n, int delay, int forget) {
        ll dif[2234], know[2234];
        memset(dif,0,sizeof(dif));
        memset(know,0,sizeof(know));
        dif[1+delay]++, dif[1+forget]--;//一开始只有1个人，它的派生区间
        know[1]++, know[1+forget]--;//这个人的记忆区间
      
        ll all=1;//当前天知道秘密的人数
        ll sub=0;//当前天可派生人数
        for(int i=2;i<=n;i++){
            sub = (sub + dif[i])%MOD; 
            //可派生人数 开始传播秘密
            know[i] = (know[i] + sub)%MOD;
            know[i+forget] = (know[i+forget]-sub+MOD)%MOD;
            //新增 可派生
            dif[i+delay] = (dif[i+delay] + sub)%MOD;
            dif[i+forget] = (dif[i+forget] - sub+MOD)%MOD;
            all = (all + know[i])%MOD ;
        }
        return all;
    }
};
```

## 【记忆化搜索】网格图中递增路径的数目

### 题目

给你一个 `m x n` 的整数网格图 `grid` ，你可以从一个格子移动到 `4` 个方向相邻的任意一个格子。

请你返回在网格图中从 **任意** 格子出发，达到 **任意** 格子，且路径中的数字是 **严格递增** 的路径数目。由于答案可能会很大，请将结果对 `10^9 + 7` **取余** 后返回。

如果两条路径中访问过的格子不是完全相同的，那么它们视为两条不同的路径。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/05/10/griddrawio-4.png)

```
输入：grid = [[1,1],[3,4]]
输出：8
解释：严格递增路径包括：
- 长度为 1 的路径：[1]，[1]，[3]，[4] 。
- 长度为 2 的路径：[1 -> 3]，[1 -> 4]，[3 -> 4] 。
- 长度为 3 的路径：[1 -> 3 -> 4] 。
路径数目为 4 + 3 + 1 = 8 。
```

**示例 2：**

```
输入：grid = [[1],[2]]
输出：3
解释：严格递增路径包括：
- 长度为 1 的路径：[1]，[2] 。
- 长度为 2 的路径：[1 -> 2] 。
路径数目为 2 + 1 = 3 。
```

 

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 1000`
- `1 <= m * n <= 10^5`
- `1 <= grid[i][j] <= 10^5`

### 解题思路

可惜了，没去做

令dp[i, j]表示以[i, j]结尾的递增路径的数目，进行记忆化搜索即可

### 代码

```c++
const int MOD=1e9+7;
#define ll long long
class Solution {
public:
    int countPaths(vector<vector<int>>& grid) {
        int n = grid.size(),m=grid[0].size();
        int dir[4][2]={ {-1,0},{1,0},{0,1},{0,-1} };
        vector<vector<ll>> dp(n, vector<ll>(m,0));

        function<ll(int,int)> dfs = [&](int x, int y){
            if(dp[x][y]>0) return dp[x][y];
            dp[x][y] = 1;
            for(int i=0;i<4;i++){
                int dx=x+dir[i][0];
                int dy=y+dir[i][1];
                if(dx<0||dy<0||dx>=n||dy>=m) continue;
                if(grid[dx][dy] >= grid[x][y]) continue;
                dp[x][y] = (dp[x][y]+dfs(dx, dy))%MOD;;
            }
            return dp[x][y];

        };

        ll res=0;
        for(int i=0;i<n;i++){
            for(int j=0;j<m;j++){
                res = (res+dfs(i,j))%MOD;
            }
        }
        return res;

    }
};
```

