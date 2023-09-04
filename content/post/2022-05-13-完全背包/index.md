---
title:      完全背包
description:   动态规划
date:       2022-05-13 00:00:00+0000
image: post-bg-20220513.png
categories: 
    - data structure and algorithm 
tags:
    - 背包
---
## 前言

完全背包和01背包的区别在于完全背包问题种的物品可以拿多次，而不是只能拿0或1次

```c++
dp[i][j] = max(dp[i-1][j],dp[i-1][j-k*v[i]]+k*w[i])//0<k*v[i]<=j  (k>=1,k<=j/v[i])
         = max(dp[i-1][j],dp[i][j-v[i]]+w[i])
   dp[j] = max(dp[j],dp[j-v[i]]+w[i]) //维度消除 ->
```

![640](https://raw.githubusercontent.com/zezezeking/picturebed/main/641.png)

常规解法：

```c++
class Solution {
    public int maxValue(int N, int C, int[] v, int[] w) {
        int[][] dp = new int[N][C + 1];
        
        // 先预处理第一件物品
        for (int j = 0; j <= C; j++) {
            // 显然当只有一件物品的时候，在容量允许的情况下，能选多少件就选多少件
            int maxK = j / v[0];
            dp[0][j] = maxK * w[0];
        }
        
        // 处理剩余物品
        for (int i = 1; i < N; i++) {
            for (int j = 0; j <= C; j++) {
                // 不考虑第 i 件物品的情况（选择 0 件物品 i）
                int n = dp[i - 1][j];
                // 考虑第 i 件物品的情况
                int y = 0;
                for (int k = 1 ;; k++) {
                    if (j < v[i] * k) {
                        break;
                    }
                    y = Math.max(y, dp[i - 1][j - k * v[i]] + k * w[i]);
                }
                dp[i][j] = Math.max(n, y);
            }
        }
        return dp[N - 1][C];
    }
}
```

滚动数组，和01背包类似。

空间优化：

```c++
class Solution {
    public int maxValue(int N, int C, int[] v, int[] w) {
        int[] dp = new int[C + 1];
        for (int i = 0; i < N; i++) {
            for (int j = 0; j <= C; j++) {
                // 不考虑第 i 件物品的情况（选择 0 件物品 i）
                int n = dp[j];
                // 考虑第 i 件物品的情况
                int y = j - v[i] >= 0 ? dp[j - v[i]] + w[i] : 0; 
                dp[j] = Math.max(n, y);
            }
        }
        return dp[C];
    }
}
```

> **形式上，我们只需要将 01 背包问题的「一维空间优化」解法中的「容量维度」遍历方向从「从大到小 改为 从小到大」就可以解决完全背包问题。**
>
> **但本质是因为两者进行状态转移时依赖了不同的格子：**
>
> - **01 背包依赖的是「上一行正上方的格子」和「上一行左边的格子」。**
> - **完全背包依赖的是「上一行正上方的格子」和「本行左边的格子」。**
>
> 我们可以发现通过「一维空间优化」方式，可以将求解「完全背包」问题的时间复杂度从O(N\*C\*C)降为O(N*C)

> 参考自https://mp.weixin.qq.com/s?__biz=MzU4NDE3MTEyMA==&mid=2247486107&idx=1&sn=e5fa523008fc5588737b7ed801caf4c3


## 【完全背包组合问题】零钱兑换II

### 题目

给你一个整数数组 coins 表示不同面额的硬币，另给一个整数 amount 表示总金额。

请你计算并返回可以凑成总金额的硬币组合数。如果任何硬币组合都无法凑出总金额，返回 0 。

假设每一种面额的硬币有无限个。 

题目数据保证结果符合 32 位带符号整数。

 

示例 1：

输入：amount = 5, coins = [1, 2, 5]
输出：4
解释：有四种方式可以凑成总金额：
5=5
5=2+2+1
5=2+1+1+1
5=1+1+1+1+1
示例 2：

输入：amount = 3, coins = [2]
输出：0
解释：只用面额 2 的硬币不能凑成总金额 3 。
示例 3：

输入：amount = 10, coins = [10] 
输出：1


提示：

1 <= coins.length <= 300

1 <= coins[i] <= 5000

coins 中的所有值 互不相同

0 <= amount <= 5000

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/coin-change-2
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

转化为完全背包问题 

求组合数

令dp[i,j]表示前i种面额硬币能凑成总金额为j的方案数

初始化：

```c++
for(int j=0;j<=amount;j++)
	if(j%coins[0]==0) dp[0][j]=1;
```

考虑第i种面额硬币，可以不选，可以选1，2，...，k张

```c++
for(int i=1;i<n;i++){
	for(int j=0;j<=amount;j++){
		dp[i][j] = dp[i-1][j];//不使用第i种
		for(int k=1;j-k*coins[i]>=0;k++){//使用k张第i种
			dp[i][j] += dp[i-1][j-k*coins[i]];
   		}
	}
}
```

最后结果为dp[n-1,amount]

### 代码1.0

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size();
        vector<vector<int>> dp(n,vector<int>(amount+1,0));
        //dp[i][j] 前i种面额硬币能凑成总金额为j的方案数
        for(int j=0;j<=amount;j++)
            if(j%coins[0]==0) dp[0][j]=1;
        
        for(int i=1;i<n;i++){
            for(int j=0;j<=amount;j++){
                dp[i][j] = dp[i-1][j];//不使用第i种
                for(int k=1;j-k*coins[i]>=0;k++){//使用k张第i种
                    dp[i][j] += dp[i-1][j-k*coins[i]];
                }
            }
        }
        return dp[n-1][amount];
    }
};
```

### 代码2.0

消除重复计算

```c++
dp[i][j-v[i]] = dp[i-1][j-v[i]]+dp[i-1][j-2*v[i]]+...+dp[i-1][j-k*v[i]]
dp[i][j] = dp[i-1][j]+dp[i-1][j-v[i]]+...+dp[i-1][j-k*v[i]]
    	 = dp[i-1][j] + dp[i][j-v[i]]
```

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size();
        vector<vector<int>> dp(n,vector<int>(amount+1,0));
        //dp[i][j] 前i种面额硬币能凑成总金额为j的方案数
        for(int j=0;j<=amount;j++)
            if(j%coins[0]==0) dp[0][j]=1;
        
        for(int i=1;i<n;i++){
            for(int j=0;j<=amount;j++){
                dp[i][j] = dp[i-1][j];//不使用第i种
                if(j>=coins[i]) dp[i][j] += dp[i][j-coins[i]];//使用第i种
            }
        }
        return dp[n-1][amount];
    }
};
```

### 代码3.0（空间优化）

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size();
        vector<int> dp(amount+1,0);
        //dp[j] 能凑成总金额为j的方案数
        dp[0] = 1;
        
        for(int i=0;i<n;i++){
            for(int j=0;j<=amount;j++){
                if(j>=coins[i]) dp[j] += dp[j-coins[i]];
            }
        }

        return dp[amount];
    }
};
```

## 【完全背包最值问题】零钱兑换

### 题目

给你一个整数数组 coins ，表示不同面额的硬币；以及一个整数 amount ，表示总金额。

计算并返回可以凑成总金额所需的 最少的硬币个数 。如果没有任何一种硬币组合能组成总金额，返回 -1 。

你可以认为每种硬币的数量是无限的。

 

示例 1：

输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
示例 2：

输入：coins = [2], amount = 3
输出：-1
示例 3：

输入：coins = [1], amount = 0
输出：0


提示：

1 <= coins.length <= 12

1 <= coins[i] <= 2^31 - 1

0 <= amount <= 10^4

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/coin-change
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

转换为完全背包问题

令dp[i, j]表示前i种硬币凑成j所需的最少硬币个数

初始化：

```c++
for(int j=0;j<=amount;j++){
	if(j%coins[0]==0) dp[0][j]=j/coins[0];
}//其余值都设为极大值
```

状态转移：

```c++
 for(int i=1;i<n;i++){
	for(int j=0;j<=amount;j++){
		dp[i][j] = min(dp[i][j],dp[i-1][j]);//不选第i种
		if(j>=coins[i]) dp[i][j]=min(dp[i][j],dp[i][j-coins[i]]+1);//选第i种
	}
}
```

### 代码

```c++
const int INF = 0x3f3f3f3f;
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        int n = coins.size();
        vector<vector<int>> dp(n,vector<int>(amount+1,INF));
        //dp[i][j]前i种硬币凑成j所需的最少硬币个数
        for(int j=0;j<=amount;j++){
            if(j%coins[0]==0) dp[0][j]=j/coins[0];
        }

        for(int i=1;i<n;i++){
            for(int j=0;j<=amount;j++){
                dp[i][j] = min(dp[i][j],dp[i-1][j]);
                if(j>=coins[i]) dp[i][j]=min(dp[i][j],dp[i][j-coins[i]]+1);
            }
        }

        if(dp[n-1][amount]==INF) return -1;
        else return dp[n-1][amount];
    }
};
```

### 空间优化

```c++
const int INF = 0x3f3f3f3f;
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        int n = coins.size();
        vector<int> dp(amount+1,INF);
        //dp[j] 凑成j所需的最少硬币个数
        for(int j=0;j<=amount;j++){
            if(j%coins[0]==0) dp[j]=j/coins[0];
        }

        for(int i=1;i<n;i++){
            for(int j=0;j<=amount;j++){
                if(j>=coins[i]) dp[j]=min(dp[j],dp[j-coins[i]]+1);
            }
        }

        if(dp[amount]==INF) return -1;
        else return dp[amount];
    }
};
```

## 【完全背包排列问题】组合总和Ⅳ

### 题目

给你一个由 不同 整数组成的数组 nums ，和一个目标整数 target 。请你从 nums 中找出并返回总和为 target 的元素组合的个数。

题目数据保证答案符合 32 位整数范围。

 

示例 1：

输入：nums = [1,2,3], target = 4
输出：7
解释：
所有可能的组合为：
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)
请注意，顺序不同的序列被视作不同的组合。
示例 2：

输入：nums = [9], target = 3
输出：0


提示：

1 <= nums.length <= 200

1 <= nums[i] <= 1000

nums 中的所有元素 互不相同

1 <= target <= 1000

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/combination-sum-iv
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 常规动态规划

在零钱兑换II问题种，可以用完全背包来求组合数，但这题求的是排列数。

二维的完全背包不好直接用来求排列数，但空间优化后的完全背包小小改动一下就可用来求排列数，等下再提。

以另外一种角度来定义dp数组

令dp[i, j] 长度为i 和为j 的排列数

初始化，显然dp[0, 0]=1，即没选数时和为0，就1种方案

转移方程：

对任意的dp[i, j]，考虑末尾的数，这个末尾的数可以是nums[0]，此时需加上dp[i-1, j-nums[0]]的方案数；也可以是nums[1]，此时需加上dp[i-1, j-nums[1]]的方案数。。。

最后结果即是dp[i, targen]，其中i的范围是[1, len]，其中len表示最长排列的长度

注意到求方案数什么的，方案数可能很大，除非题目说明，不然注意数据溢出，这题用long long 也会溢出。

```c++
#define ull unsigned long long
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        int len=target;
        vector<vector<ull>> dp(len+1,vector<ull>(target+1,0));
        //dp[i][j] 长度为i 和为j 的排列数
        dp[0][0] = 1;
        
        int res = 0;
        for(int i=1;i<=len;i++){
            for(int j=0;j<=target;j++){
                for(auto &x:nums){
                    if(j>=x) dp[i][j]+=dp[i-1][j-x];
                }
            }
            res+=dp[i][target];
        }
        
        return res;
    }
};
```

### 完全背包求排列数

**如果求组合数就是外层for循环遍历物品，内层for遍历背包**。

**如果求排列数就是外层for遍历背包，内层for循环遍历物品**。

（代码随想录中给的推论，这是建立在空间优化后一维数组的情况，二维经此题测试不适用）

```c++
#define ull unsigned long long
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        int n = nums.size();
        vector<ull> dp(target+1,0);
        //dp[i] 总和为i的排列数
        dp[0]=1;
        for(int j=0;j<=target;j++){//先遍历背包
            for(int i=0;i<n;i++){//再遍历物品
                if(j>=nums[i]) dp[j] += dp[j-nums[i]];
            }
        }
        return dp[target];
    }
};
```

理解起来可以参考上一条

即总和为i的排列数dp[i]，最后一个数可以选择nums[0]、nums[1]...如果可以的话

那么dp[i]的方案数就是dp[i-nums[0]]+dp[i-nums[1]]+...如果可以的话

## 【完全背包最值问题】完全平方数

### 题目

给你一个整数 n ，返回 和为 n 的完全平方数的最少数量 。

完全平方数 是一个整数，其值等于另一个整数的平方；换句话说，其值等于一个整数自乘的积。例如，1、4、9 和 16 都是完全平方数，而 3 和 11 不是。

 

示例 1：

输入：n = 12
输出：3 
解释：12 = 4 + 4 + 4
示例 2：

输入：n = 13
输出：2
解释：13 = 4 + 9

提示：

1 <= n <= 10^4

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/perfect-squares
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

转化问题：从可选则的完全平方数中，选出最小个数使得其和为n

和零钱兑换那题思路是一样的

### 代码

```c++
class Solution {
public:
    int numSquares(int n) {
        vector<int> v;//容量数组
        for(int i=1;i*i<=n;i++){
            v.push_back(i*i);
        }
       
        int len=v.size();//len件物品
        int V=n;//容量限制
        vector<int> dp(n+1);
        //dp[i] 和为i 的完全平方数的最少数量
        for(int j=0;j<=V;j++){
            dp[j]=j/v[0];
        }
        for(int i=1;i<len;i++){
            for(int j=0;j<=V;j++){
                if(j>=v[i])
                    dp[j]=min(dp[j],dp[j-v[i]]+1);
            }
        }

        return dp[n];
    }
};
```

## 【完全背包存在问题】单词拆分

### 题目

给你一个字符串 s 和一个字符串列表 wordDict 作为字典。请你判断是否可以利用字典中出现的单词拼接出 s 。

注意：不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。

 

示例 1：

输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以由 "leet" 和 "code" 拼接成。
示例 2：

输入: s = "applepenapple", wordDict = ["apple", "pen"]
输出: true
解释: 返回 true 因为 "applepenapple" 可以由 "apple" "pen" "apple" 拼接成。
     注意，你可以重复使用字典中的单词。
示例 3：

输入: s = "catsandog", wordDict = ["cats", "dog", "sand", "and", "cat"]
输出: false


提示：

1 <= s.length <= 300

1 <= wordDict.length <= 1000

1 <= wordDict[i].length <= 20

s 和 wordDict[i] 仅有小写英文字母组成

wordDict 中的所有字符串 互不相同

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/word-break
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

物品：字典中的字符串

背包：字符串s

每个单词可以重复选择，所以是完全背包。但感觉也不是标准的完全背包。

一开始将字典中字符串看做物品是没问题的，然后将字符串s看做背包的限制，一直在思考如果合理地表示状态，如何转移，思索无果

竟是要直接将字符串s看做背包，而不是像数字那样看成背包的容量

令dp[i]表示字符串s的前i位（不含）是否可由字典中的字符串列表拼接得出

初始化：dp[0]=true（字符串为空的话，应该是可以的）

状态转移：dp[i] = dp[i] | ( dp[j] & check(j,i) )，其中check(j+1,i)表示判断子字符串s[j+1, i]是否是字典中的字符串

最后结果即是dp[s.size()]

### 代码

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> tab(wordDict.begin(),wordDict.end());
        int n = s.size();
        vector<bool> dp(n+1,false);
        //dp[i] 前i位（不含）是否可由字典中的单词拼接而来
        dp[0]=true;
        
        for(int i=1;i<=n;i++){
            for(int j=0;j<i;j++){//子字符串的起始位置
                string subs = s.substr(j,i-j);
                bool cur=false;
                if(tab.count(subs)) {
                    cur=true & dp[j]; 
                }
                dp[i]=dp[i]|cur;
            }
        }
        return dp[n];
    }
};
```

这里顺便记下substr的时间复杂度：

> Unspecified, but generally linear in the [length](http://cplusplus.com/string::length) of the returned object.

