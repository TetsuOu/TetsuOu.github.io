---
title:      买卖股票
description:   动态规划
date:       2022-05-19 00:00:00+0000
image: post-bg-20220519.png
categories:
    - data structure and algorithm
tags:
    - 动态规划
---

分析清楚题意，弄清转移过程（是否需要其他辅助变量，是否需要用到前面的dp数组等）

可以设状态，弄清状态转移

可以增加维数，以限制条件

## 买卖股票的最佳时机

### 题目

给定一个数组 prices ，它的第 i 个元素 prices[i] 表示一支给定股票第 i 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

 

示例 1：

输入：[7,1,5,3,6,4]
输出：5
解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
示例 2：

输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 没有交易完成, 所以最大利润为 0。


提示：

1 <= prices.length <= 10^5
0 <= prices[i] <= 10^4

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/best-time-to-buy-and-sell-stock
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### DP version1

令dp[i]为第i天卖出能获得的最大利润

初始化：dp[0] = 0，第i天卖出的话只能在第i天买入，不赚不亏

考虑dp[i]，令p为今天卖出比昨天卖出新增的利润，可能为正，也可能为负，但归纳起来就3种情况，1）前一天卖出的最大利润+新增利润，表示在前i天买入的情况；2）dp[i]，即初始化的0，亏钱是不可能亏钱的；3）前一天买入，第i天卖出

结果为转移过程中的最大值

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n==1) return 0;
        vector<int> dp(n,0);
        //dp[i] 第i天卖出能获得的最大利润
        dp[0] = 0;
        for(int i=1;i<n;i++){
            int p = prices[i]-prices[i-1];//新增利润
            dp[i]=max(dp[i-1]+p,max(dp[i],p));
        }
        return *max_element(dp.begin(),dp.end());
    }
};
```

### DP version2

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n==1) return 0;
        int dp[n][2];
        //dp[i][0] 前i天，且第i天未持有股票 的最大利润 
        //dp[i][1] 前i天，且第i天持有股票 的最大利润
        int res = 0;
        dp[0][0] = 0;
        dp[0][1] = -prices[0];
        for(int i=1;i<n;i++){
            dp[i][0] = max(dp[i-1][1]+prices[i], dp[i-1][0]);//要么第i天卖出，要么不操作
            dp[i][1] = max(-prices[i],dp[i-1][1]);//要么第i天买入，要么不操作
            //写成下面这样是错误的，如果第i天买入，说明前i-1天都不能操作！所以是由-prices[i]转化而来
            //因为dp[i-1][0]只说明了持不持有股票，但没记录交易了几次！题目中限制只能交易1次
            // dp[i][1] = max(dp[i-1][0]-prices[i], dp[i-1][1]);
        }
        return dp[n-1][0];
    }
};
```

### DP version3

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n==1) return 0;
        int dp[n][2][2];
        //dp[i][0][k] 前i天，且第i天未持有股票 的最大利润 已经交易了k次
        //dp[i][1][k] 前i天，且第i天持有股票 的最大利润   已经交易了k次
        int res = 0;
        dp[0][0][0] = 0, dp[0][0][1] = 0;
        dp[0][1][0] = -prices[0], dp[0][1][1] = -prices[0];
        for(int i=1;i<n;i++){
            dp[i][0][0] = 0;
            dp[i][0][1] = max(dp[i-1][1][0]+prices[i],dp[i-1][0][1]);//要么在第i天卖出，要么不操作
            dp[i][1][0] = max(dp[i-1][0][0]-prices[i],dp[i-1][1][0]);//要么在第i天买入，要么不操作
            dp[i][1][1] = max(dp[i-1][0][1]-prices[i],dp[i-1][1][1]);//要么在第i天卖出，要么不操作
        }
        return dp[n-1][0][1];
    }
};
```

### 贪心

> 只需要遍历价格数组一遍，记录历史最低点，然后在每一天考虑这么一个问题：如果我是在历史最低点买进的，那么我今天卖出能赚多少钱？当考虑完所有天数之时，我们就得到了最好的答案。
>
> 作者：LeetCode-Solution
> 链接：https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/solution/121-mai-mai-gu-piao-de-zui-jia-shi-ji-by-leetcode-/
> 来源：力扣（LeetCode）
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int inf = 1e9;
        int minprice = inf, maxprofit = 0;
        for (int price: prices) {
            maxprofit = max(maxprofit, price - minprice);
            minprice = min(price, minprice);
        }
        return maxprofit;
    }
};
```

## 买卖股票的最佳时机II

### 题目

给你一个整数数组 prices ，其中 prices[i] 表示某支股票第 i 天的价格。

在每一天，你可以决定是否购买和/或出售股票。你在任何时候 最多 只能持有 一股 股票。你也可以先购买，然后在 同一天 出售。

返回 你能获得的 最大 利润 。

 

示例 1：

输入：prices = [7,1,5,3,6,4]
输出：7
解释：在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6 - 3 = 3 。
     总利润为 4 + 3 = 7 。
示例 2：

输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5 - 1 = 4 。
     总利润为 4 。
示例 3：

输入：prices = [7,6,4,3,1]
输出：0
解释：在这种情况下, 交易无法获得正利润，所以不参与交易可以获得最大利润，最大利润为 0 。


提示：

1 <= prices.length <= 3 * 10^4
0 <= prices[i] <= 10^4

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 贪心

这题虽然还是一次只能最多持有1支股票，但可交易多次。

那只有将存在利润的地方都加起来即可。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n==1) return 0;
        int res = 0;
        for(int i=1;i<n;i++){
            res+=prices[i]>prices[i-1]?prices[i]-prices[i-1]:0;
        }
        return res;
    }
};
```

### DP

此处将上一题的version2小改一下即可

因为取消了只能交易一次的限制

计算第i天的情况，**考虑前一天的情况：持有股票或不持有股票**

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n==1) return 0;
        int dp[n][2];
        //dp[i][0] 前i天，且第i天未持有一支股票的最大利润 
        //dp[i][1] 前i天，且第i天持有一支股票的最大利润
        dp[0][0] = 0;
        dp[0][1] = -prices[0];
        
        for(int i=1;i<n;i++){
            dp[i][0] = max(dp[i-1][1]+prices[i], dp[i-1][0]);
            dp[i][1] = max(dp[i-1][0]-prices[i], dp[i-1][1]);//注意这里
        }
        return dp[n-1][0];
    }
};
```

## 买卖股票的最佳时机III

### 题目

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

 

示例 1:

输入：prices = [3,3,5,0,0,3,1,4]
输出：6
解释：在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
     随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。
示例 2：

输入：prices = [1,2,3,4,5]
输出：4
解释：在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。   
     注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。   
     因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
示例 3：

输入：prices = [7,6,4,3,1] 
输出：0 
解释：在这个情况下, 没有交易完成, 所以最大利润为 0。
示例 4：

输入：prices = [1]
输出：0


提示：

1 <= prices.length <= 10^5
0 <= prices[i] <= 10^5

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### DP version 1

交易一次，说明买入-卖出一次

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n==1) return 0;
        int dp[n][2][3];
        // memset(dp,0,sizeof(dp));
        //dp[i][0][k]   前i天，且第i天未持有股票，已经交易了k次的最大利润
        //dp[i][1][k]   前i天，且第i天持有股票，已经交易了k次的最大利润

        dp[0][0][0]=0,dp[0][0][1]=0,dp[0][0][2]=0;
        dp[0][1][0]=-prices[0];
        dp[0][1][1]=-prices[0];
        dp[0][1][2]=-prices[0];
        for(int i=1;i<n;i++){
            dp[i][0][0] = 0;
            dp[i][0][1] = max(dp[i-1][1][0]+prices[i], dp[i-1][0][1]);//第i天卖出，交易次数加一；要么不操作
            dp[i][0][2] = max(dp[i-1][1][1]+prices[i], dp[i-1][0][2]);//第i天卖出，交易次数加一；要么不操作
            dp[i][1][0] = max(dp[i-1][0][0]-prices[i], dp[i-1][1][0]);//第i天买入，交易次数不变；要么不操作
            dp[i][1][1] = max(dp[i-1][0][1]-prices[i], dp[i-1][1][1]);//第i天买入，交易次数不变，要么不操作
            //下面这行其实用不到
            dp[i][1][2] = max(dp[i-1][0][2]-prices[i], dp[i-1][1][2]);//第i天买入，交易次数不变，要么不操作
        }

        return dp[n-1][0][2];
    }
};
```

### DP version 2

上面这种写法好写一点，也可以提前想好状态。一天就5个状态：

1. 没有操作
2. 第一次买入
3. 第一次卖出
4. 第二次买入
5. 第二次卖出

令dp[i]\[j]表示前i天，且第i天的状态为j 的最大利润

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n==1) return 0;
        int dp[n][5];
        //dp[i][j] 前i天 且第i天状态为j 的最大利润
        dp[0][0]=0;//不操作
        dp[0][1]=-prices[0];//第一次买入
        dp[0][2]=0;//第一次卖出
        dp[0][3]=-prices[0];//第二次买入
        dp[0][4]=0;//第二次卖出
        
        for(int i=1;i<n;i++){
            dp[i][0] = 0;
            dp[i][1] = max(dp[i-1][0]-prices[i], dp[i-1][1]);//买入；不操作
            dp[i][2] = max(dp[i-1][1]+prices[i], dp[i-1][2]);//卖出；不操作
            dp[i][3] = max(dp[i-1][2]-prices[i], dp[i-1][3]);//买入，不操作
            dp[i][4] = max(dp[i-1][3]+prices[i], dp[i-1][4]);//卖出，不操作
        }

        return dp[n-1][4];
    }
};
```

## 买卖股票的最佳时机IV

### 题目

给定一个整数数组 prices ，它的第 i 个元素 prices[i] 是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

 

示例 1：

输入：k = 2, prices = [2,4,1]
输出：2
解释：在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。
示例 2：

输入：k = 2, prices = [3,2,6,5,0,3]
输出：7
解释：在第 2 天 (股票价格 = 2) 的时候买入，在第 3 天 (股票价格 = 6) 的时候卖出, 这笔交易所能获得利润 = 6-2 = 4 。
     随后，在第 5 天 (股票价格 = 0) 的时候买入，在第 6 天 (股票价格 = 3) 的时候卖出, 这笔交易所能获得利润 = 3-0 = 3 。


提示：

0 <= k <= 100
0 <= prices.length <= 1000
0 <= prices[i] <= 1000

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### DP

和III思路是一样的，变成了最多可以交易k次。那么最终答案在dp[n-1]\[0]\[p]中选，其中p的范围是[0..k]

```c++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        if(k==0||n==0) return 0;
        int dp[n][2][k+1];
        //dp[i][0][p]  前i天，且第i天未持有股票，已经交易了p次
        //dp[i][1][p]  前i天，且第i天持有股票，已经交易了p次
        
        for(int p=0;p<=k;p++)
            dp[0][0][p] = 0;
        for(int p=0;p<=k;p++)
            dp[0][1][p] = -prices[0];

        for(int i=1;i<n;i++){
            dp[i][0][0] = 0;
            dp[i][1][0] = max(dp[i-1][0][0]-prices[i], dp[i-1][1][0]);
            for(int p=1;p<=k;p++){
                dp[i][0][p]=max(dp[i-1][1][p-1]+prices[i],dp[i-1][0][p]);
                dp[i][1][p]=max(dp[i-1][0][p]-prices[i], dp[i-1][1][p]);
            }
        }

        int res=0;
        for(int p=0;p<=k;p++){
            res=max(res,dp[n-1][0][p]);
        }
        return res;
    }
};
```

## 最佳买卖股票时机含冷冻期

### 题目

给定一个整数数组prices，其中第  prices[i] 表示第 i 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。
注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

 

示例 1:

输入: prices = [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
示例 2:

输入: prices = [1]
输出: 0


提示：

1 <= prices.length <= 5000
0 <= prices[i] <= 1000

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### DP version1

初看是有打家劫舍那味儿的

卖出股票后，无法在第二天买入股票 (即冷冻期为 1 天)。说明买入股票时，不能由前一天未持有股票时转移过来，而应该由前两天未持有股票时转移过来（因为有冷冻期，此时不考虑前1天的不就行了嘛）。

但i-2，会出现负坐标，所以拆开来写。

i>=2时，下标合法，就那样写就行

i<2时，此时将dp[-1]\[0]当作0即可。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n==1) return 0;

        int dp[n][2];
        //dp[i][0] 前i天，且第i天不持有股票 的最大利润
        //dp[i][1] 前i天，且第i天持有股票 的最大利润
        dp[0][0] = 0;
        dp[0][1] = -prices[0];

        for(int i=1;i<n;i++){
            dp[i][0] = max(dp[i-1][1]+prices[i],dp[i-1][0]);
            if(i>=2) dp[i][1]=max(dp[i-2][0]-prices[i],dp[i-1][1]);
            else dp[i][1]=max(-prices[i],dp[i-1][1]);
        }

        return dp[n-1][0];
    }
};
```

### DP version2

考虑状态

每天分为三种状态：

0.手上没有股票且不处于冷冻期的最大收益

1.手上没有股票但处于冷冻期的最大收益

2.手上有股票的最大收益

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if(n == 1)
            return 0;
        vector<vector<int>> dp(n, vector<int>(3));
        dp[0][0] = 0;                              //手上没有股票且不处于冷冻期的最大收益
        dp[0][1] = 0;                              //手上没有股票但处于冷冻期的最大收益
        dp[0][2] = - prices[0];                    //手上有股票的最大收益
        for(int i = 1; i < n; i ++)
        {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1]);      //手上没有股票说明前一天没有或者前一天处于冷冻期
            dp[i][1] = dp[i - 1][2] + prices[i];
            dp[i][2] = max(dp[i - 1][2], dp[i - 1][0] - prices[i]);
        }
        return max(dp[n - 1][0], dp[n - 1][1]);
    }
};
```

## 买卖股票的最佳时机含手续费

### 题目

给定一个整数数组 prices，其中 prices[i]表示第 i 天的股票价格 ；整数 fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

 

示例 1：

输入：prices = [1, 3, 2, 8, 4, 9], fee = 2
输出：8
解释：能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8
示例 2：

输入：prices = [1,3,7,5,10,3], fee = 3
输出：6


提示：

1 <= prices.length <= 5 * 10^4
1 <= prices[i] < 5 * 10^4
0 <= fee < 5 * 10^4

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### DP version1

和II类似，注意支付手续费就行

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        if(n==1) return 0;
        int dp[n][2];
        //dp[i][0] 前i天，且第i天未持有股票 的最大利润
        //dp[i][1] 前i天，且第i天持有股票  的最大利润
        dp[0][0]=0;
        dp[0][1]=-prices[0];
        for(int i=1;i<n;i++){
            dp[i][0] = max(dp[i-1][1]+prices[i]-fee, dp[i-1][0]);
            dp[i][1] = max(dp[i-1][0]-prices[i], dp[i-1][1]);
        }

        return dp[n-1][0];
    }
};
```

