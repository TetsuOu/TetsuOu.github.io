---
title:      01背包
description:   动态规划
date:       2022-05-11 00:00:00+0000
image: post-bg-20220511.png
categories:
    - data structure and algorithm 
tags:
    - 背包
---
## 前言

最基本的01背包：

「01背包」是指给定物品价值与体积（对应了「给定价值与成本」），在规定容量下（对应了「限定决策规则」）如何使得所选物品的总价值最大。

```c++
class Solution {
    public int maxValue(int N, int C, int[] v, int[] w) {
        int[][] dp = new int[N][C+1];
        // 先处理「考虑第一件物品」的情况
        for (int i = 0; i <= C; i++) {
            dp[0][i] = i >= v[0] ? w[0] : 0;
        }
        // 再处理「考虑其余物品」的情况
        for (int i = 1; i < N; i++) {
            for (int j = 0; j < C + 1; j++) {
                // 不选该物品
                int n = dp[i-1][j]; 
                // 选择该物品，前提「剩余容量」大于等于「物品体积」
                int y = j >= v[i] ? dp[i-1][j-v[i]] + w[i] : 0; 
                dp[i][j] = Math.max(n, y);
            }
        }
        return dp[N-1][C];
    }
}
```

滚动数组版本：

```c++
class Solution {
    public int maxValue(int N, int C, int[] v, int[] w) {
        int[][] dp = new int[2][C+1];
        // 先处理「考虑第一件物品」的情况
        for (int i = 0; i < C + 1; i++) {
            dp[0][i] = i >= v[0] ? w[0] : 0;
        }
        // 再处理「考虑其余物品」的情况
        for (int i = 1; i < N; i++) {
            for (int j = 0; j < C + 1; j++) {
                // 不选该物品
                int n = dp[(i-1)&1][j]; 
                // 选择该物品
                int y = j >= v[i] ? dp[(i-1)&1][j-v[i]] + w[i] : 0;
                dp[i&1][j] = Math.max(n, y);
            }
        }
        return dp[(N-1)&1][C];
    }
}
```

一维数组版本：

```c++
class Solution {
    public int maxValue(int N, int C, int[] v, int[] w) {
        int[] dp = new int[C + 1];
        for (int i = 0; i < N; i++) {
            for (int j = C; j >= v[i]; j--) {//从大往小
                // 不选该物品
                int n = dp[j]; 
                // 选择该物品
                int y = dp[j-v[i]] + w[i]; 
                dp[j] = Math.max(n, y);
            }
        }
        return dp[C];
    }
}
```

<img src="https://raw.githubusercontent.com/zezezeking/picturebed/main/640.png" alt="640" style="zoom:50%;" />

> 参考自https://mp.weixin.qq.com/s/xmgK7SrTnFIM3Owpk-emmg

就是决策第i种物品选不选

## 【01背包存在问题】分割等和子集

### 题目

给你一个 只包含正整数 的 非空 数组 nums 。请你判断是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

 

示例 1：

输入：nums = [1,5,11,5]
输出：true
解释：数组可以分割成 [1, 5, 5] 和 [11] 。
示例 2：

输入：nums = [1,2,3,5]
输出：false
解释：数组不能分割成两个元素和相等的子集。


提示：

1 <= nums.length <= 200

1 <= nums[i] <= 100

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/partition-equal-subset-sum
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

令sum为数组总和，原问题可转化为是否可以找出子序列，使得其和为sum/2（可预先判断sum是否为偶数）

进一步地，可转换为01背包问题，能否在背包容量为sum/2的前提下装满背包，与普通的01背包的**区别在于这里的物品价值与体积是一致的**。



### 代码

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int n=nums.size();
        int sum=0,maxv=*max_element(nums.begin(),nums.end());
        for(auto &x:nums) sum+=x;
        if(sum&1 || maxv>sum/2) return false;
        vector<vector<int>> dp(n,vector<int>(sum/2+1,0));
        //dp[i][j] 前i个数不超过j的最大和
        //如果有dp[i][sum/2]=sum/2说明可以分割出等和子集
        
        for(int j=0;j<=sum/2;j++){
            if(j>=nums[0]) dp[0][nums[0]]=nums[0];
        }

        for(int i=1;i<n;i++){
            for(int j=0;j<=sum/2;j++){
                if(j-nums[i]>=0)
                    dp[i][j]=max(dp[i-1][j],dp[i-1][j-nums[i]]+nums[i]);
                else dp[i][j]=dp[i-1][j];
            }
        }
        
        if(dp[n-1][sum/2]==sum/2) return true;
        else return false;
    }
};
```

也可以这样写：

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int n = nums.size();
        if(n==1) return false;
        int sum=0;
        for(auto &x:nums) sum+=x;
        if(sum&1) return false;

        vector<vector<int>> dp(n,vector<int>(sum+1,0));
        //dp[i][j] nums[0..i]中是否可以加起来等于j
        for(int i=0;i<n;i++){
            dp[i][0] = 0;
        }
        dp[0][nums[0]] = 1;
        for(int i=1;i<n;i++){
            for(int j=1;j<sum/2+1;j++){
                if(nums[i]>j) dp[i][j] = dp[i-1][j];
                else dp[i][j] = dp[i-1][j]|dp[i-1][j-nums[i]];
            }
        }
        return dp[n-1][sum/2];
    }
};
```

## 【01背包最值问题】最后一块石头的重量II

### 题目

有一堆石头，用整数数组 stones 表示。其中 stones[i] 表示第 i 块石头的重量。

每一回合，从中选出任意两块石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x <= y。那么粉碎的可能结果如下：

如果 x == y，那么两块石头都会被完全粉碎；
如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。
最后，最多只会剩下一块 石头。返回此石头 最小的可能重量 。如果没有石头剩下，就返回 0。

 

示例 1：

输入：stones = [2,7,4,1,8,1]
输出：1
解释：
组合 2 和 4，得到 2，所以数组转化为 [2,7,1,8,1]，
组合 7 和 8，得到 1，所以数组转化为 [2,1,1,1]，
组合 2 和 1，得到 1，所以数组转化为 [1,1,1]，
组合 1 和 1，得到 0，所以数组转化为 [1]，这就是最优值。
示例 2：

输入：stones = [31,26,33,21,40]
输出：5


提示：

1 <= stones.length <= 30

1 <= stones[i] <= 100

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/last-stone-weight-ii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

任意选i块石头，使得他们的重量趋近于总重量的一半，因为这样和另一半抵消的差值就是最小的

从而转化为01背包问题

### 代码

```c++
class Solution {
public:
    int lastStoneWeightII(vector<int>& stones) {
        int n = stones.size(), sum = 0;
        for(auto &x:stones) sum+=x;
        int target = sum/2;
        
        vector<int> dp(target+1,0);
        for(int i=0;i<n;i++){
            for(int j=target;j>=stones[i];--j){
                dp[j] = max(dp[j],dp[j-stones[i]]+stones[i]);
            }
        }
        //dp[target] 背包容量为target的最大石头重量
        
        return abs(sum-dp[target]-dp[target]);
    }
};
```

## 【01背包组合问题】目标和

### 题目

给你一个整数数组 nums 和一个整数 target 。

向数组中的每个整数前添加 '+' 或 '-' ，然后串联起所有整数，可以构造一个 表达式 ：

例如，nums = [2, 1] ，可以在 2 之前添加 '+' ，在 1 之前添加 '-' ，然后串联起来得到表达式 "+2-1" 。
返回可以通过上述方法构造的、运算结果等于 target 的不同 表达式 的数目。

 

示例 1：

输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
示例 2：

输入：nums = [1], target = 1
输出：1


提示：

1 <= nums.length <= 20

0 <= nums[i] <= 1000

0 <= sum(nums[i]) <= 1000

-1000 <= target <= 1000

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/target-sum
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

原数组需要分为两部分：负数部分neg  和 非负数部分pos，再令原数组总和为sum，由题意有：

pos+neg=target;

pos-neg=sum;

去掉pos，联立可得neg=(target-sum)/2 （注意，这里求得的neg是带符号的）

因此原问题转化成了 **在原数组中找出子序列的和等于-neg（使其变为非负数，后用neg代替）的方案数**

首先预处理判断neg的合法性，然后进行动态规划

和普通01背包的定义不同，这里**定义dp[i,j]表示前i个（不包括第i个）数中子序列的和等于j的方案数**

有两个不同的点：1）第一维不包括第i个；2）第二维表示刚好等于j，而不是不超过j

这是为了方便表示什么都没选的时候，也是一种方案

初始化dp[0,0]=1，dp[0,y]=0(y>0)，表示哪个数都没选时，子序列和和只可能等于0且只有1种方案。

转移方程：

```c++
for(int i=1;i<=n;i++){
	for(int j=0;j<=neg;j++){
    	if(j-nums[i-1]>=0) dp[i][j]=dp[i-1][j]+dp[i-1][j-nums[i-1]];//可以选第i-1个数，选中的方案数+不选的方案数
        else dp[i][j]=dp[i-1][j];//选不了第i-1个数
    }
}
```

最后结果即为dp[n,neg]



其实第一维还是可以像原来背包那样，**定义dp[i,j]表示前i个（包括第i个）数中子序列的和等于j的方案数**

但初始化要改一下：

```c++
//dp[i][j] 前i个数总和为j的方案数
dp[0][0]=1;
dp[0][nums[0]]=nums[0]==0?2:1;
```

即第0个数也有选还是不选两种情况，不选的话，和为0，即dp[0,0]=1；如果选，不能简单的令dp[0, nums[0]]=1，因为此时nums[0]可能等于0，选或不选是不同的方案。

### 代码

```c++
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int n = nums.size(), sum=0;
        for(auto &x:nums) sum+=x;
        if(sum<target||(target-sum)&1) return 0;
        int neg = -(target-sum)/2;
        vector<vector<int>> dp(n+1,vector<int>(neg+1,0));
        // cout<<neg<<endl;
        //dp[i][j] 前i-1个数总和为j的方案数
        dp[0][0]=1;

        for(int i=1;i<=n;i++){
            for(int j=0;j<=neg;j++){
                if(j-nums[i-1]>=0) dp[i][j]=dp[i-1][j]+dp[i-1][j-nums[i-1]];
                else dp[i][j]=dp[i-1][j];
            }
        }
        
        return dp[n][neg];
    }
};
```

或者

```c++
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int n = nums.size(), sum=0;
        for(auto &x:nums) sum+=x;
        if(sum<target||(target-sum)&1) return 0;
        int neg = -(target-sum)/2;
        //这里将第2维拓展到了最大值，是为了避免后面赋值dp[0][nums[0]]越界
        vector<vector<int>> dp(n,vector<int>(1001,0));
        // cout<<neg<<endl;
        //dp[i][j] 前i个数总和为j的方案数
        dp[0][0]=1;
        dp[0][nums[0]]=nums[0]==0?2:1;

        for(int i=1;i<n;i++){
            for(int j=0;j<=neg;j++){
                if(j-nums[i]>=0) dp[i][j]=dp[i-1][j]+dp[i-1][j-nums[i]];
                else dp[i][j]=dp[i-1][j];
            }
        }
        
        return dp[n-1][neg];
    }
};
```



### 空间优化

01背包空间优化，内循环变为从大到小

```c++
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int n = nums.size(), sum=0;
        for(auto &x:nums) sum+=x;
        if(sum<target||(target-sum)&1) return 0;
        int neg = -(target-sum)/2;
        vector<int> dp(neg+1,0);
        // cout<<neg<<endl;
        //dp[j] 总和为j的方案数
        dp[0]=1;

        for(int i=1;i<=n;i++){
            for(int j=neg;j>=nums[i-1];j--){
                dp[j]+=dp[j-nums[i-1]];
            }
        }
        return dp[neg];
    }
};
```

## 【01二维费用背包最值问题】一和零

### 题目

给你一个二进制字符串数组 strs 和两个整数 m 和 n 。

请你找出并返回 strs 的最大子集的长度，该子集中 最多 有 m 个 0 和 n 个 1 。

如果 x 的所有元素也是 y 的元素，集合 x 是集合 y 的 子集 。

 

示例 1：

输入：strs = ["10", "0001", "111001", "1", "0"], m = 5, n = 3
输出：4
解释：最多有 5 个 0 和 3 个 1 的最大子集是 {"10","0001","1","0"} ，因此答案是 4 。
其他满足题意但较小的子集包括 {"0001","1"} 和 {"10","1","0"} 。{"111001"} 不满足题意，因为它含 4 个 1 ，大于 n 的值 3 。
示例 2：

输入：strs = ["10", "0", "1"], m = 1, n = 1
输出：2
解释：最大的子集是 {"0", "1"} ，所以答案是 2 。


提示：

1 <= strs.length <= 600

1 <= strs[i].length <= 100

strs[i] 仅由 '0' 和 '1' 组成

1 <= m, n <= 100

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/ones-and-zeroes
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

要将此问题转换为背包问题，需要抽象出【价值】与【容量】

每个字符串的【价值】都是1，而【容量】对应的则是0的总数量以及1的总数量。

和普通01背包受到单维度容量限制不同，此题容量限制有两个维度，0的总数量和1的总数量，所以定义dp数组时需要考虑到

定义dp[i,j,k]表示前i个字符串（物品）中0的总数量不超过j，1的总数量不超过k的最大子集长度

初始化：

```c++
for(int j=0;j<=m;j++){
	for(int k=0;k<=n;k++){
		if(j>=zero[0]&&k>=one[0])
        	dp[0][j][k]=1;
	}
}
```

转移方程：

```c++
 for(int i=1;i<len;i++){
	for(int j=0;j<=m;j++){
		for(int k=0;k<=n;k++){
        	if(j>=zero[i] && k>=one[i])
            	dp[i][j][k]=max(dp[i-1][j][k],dp[i-1][j-zero[i]][k-one[i]]+1);
                else dp[i][j][k]=dp[i-1][j][k];
		}
	}
}
```

最后结果即为dp[len-1, m, n]

### 代码

```c++
class Solution {
public:
    int findMaxForm(vector<string>& strs, int m, int n) {
        int len=strs.size();
        int dp[len][m+1][n+1];
        //dp[i][j][k] 前i个字符串中，0的个数不大于m，1的个数不大于0 的最多字符串个数
        memset(dp,0,sizeof(dp));
        //每个字符串中0和1的个数
        vector<int> zero,one;
        for(auto &str: strs){
            int le = str.size(),cnt=0;
            for(auto &ch: str){
                if(ch=='0') cnt++;
            }
            zero.push_back(cnt);
            one.push_back(le-cnt);
        }
        //初始化
        for(int j=0;j<=m;j++){
            for(int k=0;k<=n;k++){
                if(j>=zero[0]&&k>=one[0])
                    dp[0][j][k]=1;
            }
        }

        for(int i=1;i<len;i++){
            for(int j=0;j<=m;j++){
                for(int k=0;k<=n;k++){
                    if(j>=zero[i] && k>=one[i])
                        dp[i][j][k]=max(dp[i-1][j][k],dp[i-1][j-zero[i]][k-one[i]]+1);
                    else dp[i][j][k]=dp[i-1][j][k];
                }
            }
        }

        return dp[len-1][m][n];

    }
};
```

为了避免对初始化的分类讨论，可以假设添加一个空字符串到最前面

此时dp[i,j,k]表示前i-1个

```c++
class Solution {
public:
    int findMaxForm(vector<string>& strs, int m, int n) {
        int len=strs.size();
        int dp[len+1][m+1][n+1];
        //dp[i][j][k] 前i-1个字符串中，0的个数不大于m，1的个数不大于0 的最多字符串个数
        memset(dp,0,sizeof(dp));
        vector<int> zero,one;
        for(auto &str: strs){
            int le = str.size(),cnt=0;
            for(auto &ch: str){
                if(ch=='0') cnt++;
            }
            zero.push_back(cnt);
            one.push_back(le-cnt);
        }
        
        for(int i=1;i<=len;i++){
            for(int j=0;j<=m;j++){
                for(int k=0;k<=n;k++){
                    if(j>=zero[i-1] && k>=one[i-1])
                        dp[i][j][k]=max(dp[i-1][j][k],dp[i-1][j-zero[i-1]][k-one[i-1]]+1);
                    else dp[i][j][k]=dp[i-1][j][k];
                }
            }
        }

        return dp[len][m][n];

    }
};
```

### 空间优化

```c++
class Solution {
public:
    int findMaxForm(vector<string>& strs, int m, int n) {
        int len=strs.size();
        int dp[m+1][n+1];
        //[j][k] 0的个数不大于m，1的个数不大于0 的最多字符串个数
        memset(dp,0,sizeof(dp));
        vector<int> zero,one;
        for(auto &str: strs){
            int le = str.size(),cnt=0;
            for(auto &ch: str){
                if(ch=='0') cnt++;
            }
            zero.push_back(cnt);
            one.push_back(le-cnt);
        }
        //每个字符串中0和1的个数
        for(int j=0;j<=m;j++){
            for(int k=0;k<=n;k++){
                if(j>=zero[0]&&k>=one[0])
                    dp[j][k]=1;
            }
        }

        for(int i=1;i<len;i++){
            for(int j=m;j>=zero[i];j--){
                for(int k=n;k>=one[i];k--){
                    dp[j][k]=max(dp[j][k],dp[j-zero[i]][k-one[i]]+1);
                }
            }
        }

        return dp[m][n];

    }
};
```

## 【01二维费用背包组合问题】盈利计划

### 题目

集团里有 n 名员工，他们可以完成各种各样的工作创造利润。

第 i 种工作会产生 profit[i] 的利润，它要求 group[i] 名成员共同参与。如果成员参与了其中一项工作，就不能参与另一项工作。

工作的任何至少产生 minProfit 利润的子集称为 盈利计划 。并且工作的成员总数最多为 n 。

有多少种计划可以选择？因为答案很大，所以 返回结果模 10^9 + 7 的值。

 

示例 1：

输入：n = 5, minProfit = 3, group = [2,2], profit = [2,3]
输出：2
解释：至少产生 3 的利润，该集团可以完成工作 0 和工作 1 ，或仅完成工作 1 。
总的来说，有两种计划。
示例 2：

输入：n = 10, minProfit = 5, group = [2,3,5], profit = [6,7,8]
输出：7
解释：至少产生 5 的利润，只要完成其中一种工作就行，所以该集团可以完成任何工作。
有 7 种可能的计划：(0)，(1)，(2)，(0,1)，(0,2)，(1,2)，以及 (0,1,2) 。


提示：

1 <= n <= 100

0 <= minProfit <= 100

1 <= group.length <= 100

1 <= group[i] <= 100

profit.length == group.length

0 <= profit[i] <= 100

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/profitable-schemes
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

令dp[i,j,k] 表示前i项工作，人数不超过j，利润至少为k的总方案数

这题也有两个约束，一个是人数，一个是利润，但其中的利润又不是绝对性的限制

这种组合问题的初始化有点麻烦，需要想清楚：

```c++
for(int j=0;j<=n;j++){//成员人数
	dp[0][j][0]=1;//第0项工作不做，利润恒为0，方案数为1
	if(j>=group[0]){//人数够做第0项工作了
		//做一下吧
        for(int k=0;k<=profit[0];k++) dp[0][j][k]=1;
        dp[0][j][0]++;//还是选择不做，利润为0的方案数加1
    }
}
```

状态转移方程：

```c++
for(int i=1;i<len;i++){
	for(int j=0;j<=n;j++){
    	for(int k=0;k<=100;k++){
        	dp[i][j][k]=dp[i-1][j][k];//第i个工作不选
            	if(j>=group[i]){//选第i个工作
                	if(k>=profit[i])//如果总利润要达到k，第i份工作产生利润pi小于总利润，那么只需要前i-1份工作达到至少k-pi的利润
                        dp[i][j][k] += dp[i-1][j-group[i]][k-profit[i]];
                    //如果要达到的总利润k小于第i份工作产生的利润，那么前i-1份工作利润随便，不小于0即可，事实上，pi>=0
                    else dp[i][j][k] += dp[i-1][j-group[i]][0];
                    dp[i][j][k] %= MOD;
                }
		}
	}
}
```

最后的结果即为dp[len-1,n, minProfit]

### 代码

```c++
const int MOD = 1e9+7;
class Solution {
public:
    int profitableSchemes(int n, int minProfit, vector<int>& group, vector<int>& profit) {
        int len = group.size();
        
        long long dp[len+1][n+1][101];//dp[i,j,k] 表示前i项工作，人数不超过j，利润至少为k的总方案数
        memset(dp,0,sizeof(dp));
        
        for(int j=0;j<=n;j++){//成员人数
            dp[0][j][0]=1;//第0项工作不做，利润恒为0，方案数为1
            if(j>=group[0]){//人数够做第0项工作了
                //做一下吧
                for(int k=0;k<=profit[0];k++) dp[0][j][k]=1;
                dp[0][j][0]++;//还是选择不做，利润为0的方案数加1
            }
                
        }

        for(int i=1;i<len;i++){
            for(int j=0;j<=n;j++){
                for(int k=0;k<=100;k++){
                    dp[i][j][k]=dp[i-1][j][k];//第i个工作不选
                    if(j>=group[i]){//选第i个工作
                        if(k>=profit[i])//如果总利润要达到k，第i份工作产生利润pi小于总利润，那么只需要前i-1份工作达到至少k-pi的利润
                            dp[i][j][k] += dp[i-1][j-group[i]][k-profit[i]];
                        //如果要达到的总利润k小于第i份工作产生的利润，那么前i-1份工作利润随便，不小于0即可，事实上，pi>=0
                        else dp[i][j][k] += dp[i-1][j-group[i]][0];
                        dp[i][j][k] %= MOD;
                    }

                }
            }
        }

        return dp[len-1][n][minProfit];

    }
};
```

### 代码2.0

上一题也提到过，为了避免讨论初始化的麻烦，可以假想存在一个空工作，即所需人数为0，产生利润也为0，将该工作放在首位

那么dp[i,j,k]就用来表示前i-1个（真）工作，人数不超过j，产生利润至少为k的方案数

初始化就只需要令 dp[0,x,0]=1，其中x的范围为[0,n]，表示这项空工作，人数无论是多少，利润只能是0，方案数只有1

后面循环注意将需要修改的i变为i-1

```c++
const int MOD = 1e9+7;
class Solution {
public:
    int profitableSchemes(int n, int minProfit, vector<int>& group, vector<int>& profit) {
        int len = group.size();
        
        long long dp[len+1][n+1][101];
        memset(dp,0,sizeof(dp));
        for(int j=0;j<=n;j++) dp[0][j][0]=1;
        for(int i=1;i<=len;i++){
            for(int j=0;j<=n;j++){
                for(int k=0;k<=100;k++){
                    dp[i][j][k]=dp[i-1][j][k];//第i个工作不选
                    if(j>=group[i-1]){//选第i个工作
                        if(k>=profit[i-1])//如果总利润要达到k，第i份工作产生利润pi小于总利润，那么只需要前i-1份工作达到至少k-pi的利润
                            dp[i][j][k] += dp[i-1][j-group[i-1]][k-profit[i-1]];
                        //如果要达到的总利润k小于第i份工作产生的利润，那么前i-1份工作利润随便，不小于0即可，事实上，pi>=0
                        else dp[i][j][k] += dp[i-1][j-group[i-1]][0];
                        dp[i][j][k] %= MOD;
                    } 
                }
            }
        }
        return dp[len][n][minProfit];

    }
};
```

### 空间优化

```c++
const int MOD = 1e9+7;
class Solution {
public:
    int profitableSchemes(int n, int minProfit, vector<int>& group, vector<int>& profit) {
        int len = group.size();
        
        long long dp[n+1][101];
        memset(dp,0,sizeof(dp));
        for(int j=0;j<=n;j++) dp[j][0]=1;
        for(int i=1;i<=len;i++){
            for(int j=n;j>=group[i-1];j--){
                for(int k=100;k>=0;k--){
                    dp[j][k]=dp[j][k];
                    if(k>=profit[i-1])
                        dp[j][k] += dp[j-group[i-1]][k-profit[i-1]];
                    else dp[j][k] += dp[j-group[i-1]][0];
                    dp[j][k] %= MOD;
                }
            }
        }

        return dp[n][minProfit];

    }
};
```

### 另外一种定义方式

如果将dp[i,j,k]定义成前i-1项工作，人数**恰好**为j，利润至少为k的方案数

初始化dp[0,0,0]=1，表示空工作时，不需要人数，人数恰好为0，利润为0，方案数为1

转移方程和前面的方式没啥区别

最后的需返回dp[len,x,minProfit]的和，其中x的范围是[0,n]

官方题解：

```c++
class Solution {
public:
    int profitableSchemes(int n, int minProfit, vector<int>& group, vector<int>& profit) {
        int len = group.size(), MOD = (int)1e9 + 7;
        vector<vector<vector<int>>> dp(len + 1, vector<vector<int>>(n + 1, vector<int>(minProfit + 1)));
        dp[0][0][0] = 1;
        for (int i = 1; i <= len; i++) {
            int members = group[i - 1], earn = profit[i - 1];
            for (int j = 0; j <= n; j++) {
                for (int k = 0; k <= minProfit; k++) {
                    if (j < members) {
                        dp[i][j][k] = dp[i - 1][j][k];
                    } else {
                        dp[i][j][k] = (dp[i - 1][j][k] + dp[i - 1][j - members][max(0, k - earn)]) % MOD;
                    }
                }
            }
        }
        int sum = 0;
        for (int j = 0; j <= n; j++) {
            sum = (sum + dp[len][j][minProfit]) % MOD;
        }
        return sum;
    }
};

```

