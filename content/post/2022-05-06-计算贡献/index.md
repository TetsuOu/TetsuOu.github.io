---
title:      计算贡献
description:   动态规划
date:       2022-05-06 00:00:00+0000
image: post-bg-20220506.png
categories:
    - data structure and algorithm 
tags:
    
---
## 前言

计算贡献，如果是连续序列，一般也可从动态规划的视角进行求解。如果不是连续序列，具体情况具体分析。

## 统计子串中的唯一字符

### 题目

我们定义了一个函数 countUniqueChars(s) 来统计字符串 s 中的唯一字符，并返回唯一字符的个数。

例如：s = "LEETCODE" ，则其中 "L", "T","C","O","D" 都是唯一字符，因为它们只出现一次，所以 countUniqueChars(s) = 5 。

本题将会给你一个字符串 s ，我们需要返回 countUniqueChars(t) 的总和，其中 t 是 s 的子字符串。注意，某些子字符串可能是重复的，但你统计时也必须算上这些重复的子字符串（也就是说，你必须统计 s 的所有子字符串中的唯一字符）。

由于答案可能非常大，请将结果 mod 10 ^ 9 + 7 后再返回。

 

示例 1：

输入: s = "ABC"
输出: 10
解释: 所有可能的子串为："A","B","C","AB","BC" 和 "ABC"。
     其中，每一个子串都由独特字符构成。
     所以其长度总和为：1 + 1 + 1 + 2 + 2 + 3 = 10
示例 2：

输入: s = "ABA"
输出: 8
解释: 除了 countUniqueChars("ABA") = 1 之外，其余与示例 1 相同。
示例 3：

输入：s = "LEETCODE"
输出：92


提示：

0 <= s.length <= 10^5
s 只包含大写英文字符

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/count-unique-characters-of-all-substrings-of-a-given-string
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

令dp[i,j]表示子字符串s[0..i]中字符'A'+j的贡献度

初始化很显然dp[0]\[s[0]-'A']=1，dp[0]\[其他]=0

考虑字符s[i]造成的贡献，如果[0,i-1]中没有相同字符，显然新增贡献为[0,i]区间的长度（加在末尾），即i+1
而如果[0,i-1]中出现相同字符，设位置为p，那么不会对[0,p]区间新增贡献，贡献变为没有相同字符的区间[p+1,i]长度，即i-p

答案即是所有的dp之和

pos数组记录左侧第一个相同字符的位置

和这题很像的有一题是，291场周赛第4题。

### 代码

```c++
const int MOD = 1e9+7;
class Solution {
public:
    int uniqueLetterString(string s) {
        int n = s.size();
        if(!n) return 0;
        vector<vector<int>> dp(n,vector<int>(26,0));
        vector<int> pos(26,-1);
        //dp[i][j] s[0..i]的所有子字符串中字符'A'+j的贡献度
        //pos[i] 与s[i]相同字符的左侧最近位置，辅助

        dp[0][s[0]-'A']=1;
        pos[s[0]-'A']=0;
        int res = 1;

        for(int i=1;i<n;i++){
            for(int j=0;j<26;j++){
                dp[i][j] = dp[i-1][j];
            }
            if(pos[s[i]-'A']>-1) dp[i][s[i]-'A']=i-pos[s[i]-'A'];
            else dp[i][s[i]-'A']+=i+1;
            res += accumulate(dp[i].begin(),dp[i].end(),0)%MOD;
            pos[s[i]-'A']=i;
        }
       
        return res;
    }
};
```

## 子数组的最小值之和

### 题目

给定一个整数数组 arr，找到 min(b) 的总和，其中 b 的范围为 arr 的每个（连续）子数组。

由于答案可能很大，因此 返回答案模 10^9 + 7 。

 

示例 1：

输入：arr = [3,1,2,4]
输出：17
解释：
子数组为 [3]，[1]，[2]，[4]，[3,1]，[1,2]，[2,4]，[3,1,2]，[1,2,4]，[3,1,2,4]。 
最小值为 3，1，2，4，1，1，2，1，1，1，和为 17。
示例 2：

输入：arr = [11,81,94,43,3]
输出：444


提示：

1 <= arr.length <= 3 * 10^4

1 <= arr[i] <= 3 * 10^4

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/sum-of-subarray-minimums
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

令dp[i]表示arr[0..i]所有子数组的最小值之和

初始化显然dp[0] = arr[0]

考虑第i个数arr[i]，如果左侧的数都比它大，即它是区间[0,i]上的最小值，那么dp[i]的贡献完全由arr[i]提供，即arr[i]*(i+1)
如果向左侧第一个比它小（或等于）的数的下标为p，那么它只在[p+1,i]上新增贡献，则dp[i]=dp[p]+(i-p)\*arr[i]

### 代码

```c++
const int MOD = 1e9+7;
class Solution {
public:
    int sumSubarrayMins(vector<int>& arr) {
        int n = arr.size();
        vector<int> dp(n,0);//dp[i] arr[0..i]所有子数组的最小值之和
        vector<int> L(n,-1);//L[i] 向左侧第一个比arr[i]小的数的位置
        dp[0] = arr[0];
        int res = dp[0];
        
        stack<int> st;
        st.push(0);
        for(int i=1;i<n;i++){
            while(!st.empty() && arr[st.top()]>=arr[i]) st.pop();
            if(st.empty()) L[i] = -1;
            else L[i] = st.top();
            st.push(i);
        }

        for(int i=1;i<n;i++){
            int p=L[i];
            
            if(p == -1){//前面的全部都大，arr[i]在[0,i]做贡献
                dp[i] = arr[i]*(i+1);
            }else{//arr[i]在[p+1,i]上做贡献
                dp[i] = (i-p)*arr[i]+dp[p];
            }
            
            res = (res+dp[i])%MOD;
        }
        return res;
    }
};
```

## 子数组范围和

### 题目

给你一个整数数组 nums 。nums 中，子数组的 范围 是子数组中最大元素和最小元素的差值。

返回 nums 中 所有 子数组范围的 和 。

子数组是数组中一个连续 非空 的元素序列。

 

示例 1：

输入：nums = [1,2,3]
输出：4
解释：nums 的 6 个子数组如下所示：
[1]，范围 = 最大 - 最小 = 1 - 1 = 0 
[2]，范围 = 2 - 2 = 0
[3]，范围 = 3 - 3 = 0
[1,2]，范围 = 2 - 1 = 1
[2,3]，范围 = 3 - 2 = 1
[1,2,3]，范围 = 3 - 1 = 2
所有范围的和是 0 + 0 + 0 + 1 + 1 + 2 = 4
示例 2：

输入：nums = [1,3,3]
输出：4
解释：nums 的 6 个子数组如下所示：
[1]，范围 = 最大 - 最小 = 1 - 1 = 0
[3]，范围 = 3 - 3 = 0
[3]，范围 = 3 - 3 = 0
[1,3]，范围 = 3 - 1 = 2
[3,3]，范围 = 3 - 3 = 0
[1,3,3]，范围 = 3 - 1 = 2
所有范围的和是 0 + 0 + 0 + 2 + 0 + 2 = 4
示例 3：

输入：nums = [4,-2,-3,4,1]
输出：59
解释：nums 中所有子数组范围的和是 59


提示：

1 <= nums.length <= 1000

-109 <= nums[i] <= 10^9

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/sum-of-subarray-ranges
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

这里的范围和，即所有的最大值之和减去所有的最小值之和

大体思路和上一题一样

### 代码

```c++
class Solution {
public:
    long long subArrayRanges(vector<int>& nums) {
        int n = nums.size();
        
        vector<int> Lmax(n,-1),Lmin(n,-1);
        stack<int> stk;
        stk.push(0);
        for(int i=1;i<n;i++){
            while(!stk.empty()&&nums[i]>=nums[stk.top()]) stk.pop();
            if(stk.empty()) Lmax[i]=-1;
            else Lmax[i]=stk.top();
            stk.push(i);
        }
        stack<int> stk2;
        stk2.push(0);
        for(int i=1;i<n;i++){
            while(!stk2.empty()&&nums[i]<=nums[stk2.top()]) stk2.pop();
            if(stk2.empty()) Lmin[i]=-1;
            else Lmin[i]=stk2.top();
            stk2.push(i);
        }
        
        long long res = 0;
        vector<long long> dpmax(n,0),dpmin(n,0);
        dpmax[0]=nums[0],dpmin[0]=nums[0];
        for(int i=1;i<n;i++){
            if(Lmax[i]==-1) dpmax[i]=1LL*(i+1)*nums[i];
            else dpmax[i]=1LL*(i-Lmax[i])*nums[i]+dpmax[Lmax[i]];
            if(Lmin[i]==-1) dpmin[i]=1LL*(i+1)*nums[i];
            else dpmin[i]=1LL*(i-Lmin[i])*nums[i]+dpmin[Lmin[i]];
            res += dpmax[i]-dpmin[i];
        }
        return res;
    }
};
```

