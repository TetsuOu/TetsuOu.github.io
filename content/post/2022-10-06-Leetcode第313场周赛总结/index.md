---
title:      LeetCode第313场周赛总结
description:   ----/5445
date:       2022-10-06
image: post-bg-20221006.png
categories:
    - leetcode contest
tags:
    - 动态规划
---

国庆期间没打

## 【模拟】公因子的数目

### 题目

给你两个正整数 `a` 和 `b` ，返回 `a` 和 `b` 的 **公** 因子的数目。

如果 `x` 可以同时整除 `a` 和 `b` ，则认为 `x` 是 `a` 和 `b` 的一个 **公因子** 。

 

**示例 1：**

```
输入：a = 12, b = 6
输出：4
解释：12 和 6 的公因子是 1、2、3、6 。
```

**示例 2：**

```
输入：a = 25, b = 30
输出：2
解释：25 和 30 的公因子是 1、5 。
```

 

**提示：**

- `1 <= a, b <= 1000`

### 解题思路

简单模拟即可

### 代码

```c++
class Solution {
public:
    int commonFactors(int a, int b) {
        int res = 0;
        for(int i=1;i<=min(a,b);i++){
            if(a%i==0 && b%i==0) res++;
        }
        return res;
    }
};
```

## 【模拟】沙漏的最大总和

### 题目

给你一个大小为 `m x n` 的整数矩阵 `grid` 。

按以下形式将矩阵的一部分定义为一个 **沙漏** ：

<img src="https://assets.leetcode.com/uploads/2022/08/21/img.jpg" alt="img" style="zoom:67%;" />

返回沙漏中元素的 **最大** 总和。

**注意：**沙漏无法旋转且必须整个包含在矩阵中。

 

**示例 1：**

<img src="https://assets.leetcode.com/uploads/2022/08/21/1.jpg" alt="img" style="zoom:67%;" />

```
输入：grid = [[6,2,1,3],[4,2,1,5],[9,2,8,7],[4,1,2,9]]
输出：30
解释：上图中的单元格表示元素总和最大的沙漏：6 + 2 + 1 + 2 + 9 + 2 + 8 = 30 。
```

**示例 2：**

<img src="https://assets.leetcode.com/uploads/2022/08/21/2.jpg" alt="img" style="zoom:67%;" />

```
输入：grid = [[1,2,3],[4,5,6],[7,8,9]]
输出：35
解释：上图中的单元格表示元素总和最大的沙漏：1 + 2 + 3 + 5 + 7 + 8 + 9 = 35 。
```

 

**提示：**

- `m == grid.length`
- `n == grid[i].length`
- `3 <= m, n <= 150`
- `0 <= grid[i][j] <= 10^6`

### 解题思路

简单模拟即可

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    int maxSum(vector<vector<int>>& grid) {
        int n = grid.size(),m = grid[0].size();
        int res=0;
        for(int i=0;i+2<n;i++){
            for(int j=0;j+2<m;j++){
                int cur = 0;
                cur += grid[i][j] + grid[i][j+1] + grid[i][j+2];
                cur += grid[i+1][j+1];
                cur += grid[i+2][j] + grid[i+2][j+1] + grid[i+2][j+2];
                res = max(res, cur);
            }
        }
        return res;
    }
};
```

## 【位运算+分类讨论】最小 XOR

### 题目

给你两个正整数 `num1` 和 `num2` ，找出满足下述条件的整数 `x` ：

- `x` 的置位数和 `num2` 相同，且
- `x XOR num1` 的值 **最小**

注意 `XOR` 是按位异或运算。

返回整数 `x` 。题目保证，对于生成的测试用例， `x` 是 **唯一确定** 的。

整数的 **置位数** 是其二进制表示中 `1` 的数目。

 

**示例 1：**

```
输入：num1 = 3, num2 = 5
输出：3
解释：
num1 和 num2 的二进制表示分别是 0011 和 0101 。
整数 3 的置位数与 num2 相同，且 3 XOR 3 = 0 是最小的。
```

**示例 2：**

```
输入：num1 = 1, num2 = 12
输出：3
解释：
num1 和 num2 的二进制表示分别是 0001 和 1100 。
整数 3 的置位数与 num2 相同，且 3 XOR 1 = 2 是最小的。
```

 

**提示：**

- `1 <= num1, num2 <= 10^9`

### 解题思路

统计num1的二进制表示中1的个数n，以及num2的二进制表示中1的个数m

注意找的是x，令x XOR num1的最小值为minv

当n=m时，很显然minv=0，此时x为num1

当n>m时，说明num1的前m个高位1应该变成0

当n<m时，说明num1的前m-n个低位0应该变成1

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    int minimizeXor(int num1, int num2) {
        int n=0,m=0;
        for(int i=0;i<32;i++) if((num1>>i)&1) n++;
        for(int i=0;i<32;i++) if((num2>>i)&1) m++;
        if(n==m) return num1;
        int res = 0;
        if(m>n){
            res = num1;
            for(int i=0,rest=m-n;i<32&&rest;i++){
               if((num1>>i)&1) continue;
               res ^= (1<<i);
               rest--;
            }
        }else {
            for(int i=31,rest=m;i>=0 && rest;i--){
                if((num1>>i)&1) {
                    res ^= (1<<i), rest--; 
                }
            }
        }
        return res;
    }
};
```

## 【线性DP】对字母串可执行的最大删除数

### 题目

给你一个仅由小写英文字母组成的字符串 `s` 。在一步操作中，你可以：

- 删除 **整个字符串** `s` ，或者
- 对于满足 `1 <= i <= s.length / 2` 的任意 `i` ，如果 `s` 中的 **前** `i` 个字母和接下来的 `i` 个字母 **相等** ，删除 **前** `i` 个字母。

例如，如果 `s = "ababc"` ，那么在一步操作中，你可以删除 `s` 的前两个字母得到 `"abc"` ，因为 `s` 的前两个字母和接下来的两个字母都等于 `"ab"` 。

返回删除 `s` 所需的最大操作数。

 

**示例 1：**

```
输入：s = "abcabcdabc"
输出：2
解释：
- 删除前 3 个字母（"abc"），因为它们和接下来 3 个字母相等。现在，s = "abcdabc"。
- 删除全部字母。
一共用了 2 步操作，所以返回 2 。可以证明 2 是所需的最大操作数。
注意，在第二步操作中无法再次删除 "abc" ，因为 "abc" 的下一次出现并不是位于接下来的 3 个字母。
```

**示例 2：**

```
输入：s = "aaabaab"
输出：4
解释：
- 删除第一个字母（"a"），因为它和接下来的字母相等。现在，s = "aabaab"。
- 删除前 3 个字母（"aab"），因为它们和接下来 3 个字母相等。现在，s = "aab"。 
- 删除第一个字母（"a"），因为它和接下来的字母相等。现在，s = "ab"。
- 删除全部字母。
一共用了 4 步操作，所以返回 4 。可以证明 4 是所需的最大操作数。
```

**示例 3：**

```
输入：s = "aaaaa"
输出：5
解释：在每一步操作中，都可以仅删除 s 的第一个字母。
```

 

**提示：**

- `1 <= s.length <= 4000`
- `s` 仅由小写英文字母组成

### 解题思路

![image-20221006205621018](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20221006205621018.png)

### 代码

```c++
class Solution {
public:
    int deleteString(string s) {
        int n = s.size();
        int lcp[n][n];
        //lcp[i][j]  s[i..n]与s[j..n]的最长公共前缀
        memset(lcp,0,sizeof(lcp));
        for(int i=0;i<n;i++){
            lcp[n-1][i] = s[n-1]==s[i]?1:0;
            lcp[i][n-1] = s[i]==s[n-1]?1:0;
        }
        for(int i=n-2;i>=0;i--){
            for(int j=n-2;j>=0;j--){
                if(s[i]==s[j]) lcp[i][j] = max(lcp[i+1][j+1]+1, 1);
                else lcp[i][j] = 0;
            }
        }
        
        vector<int> dp(n,1);//dp[i]:  删除s[i..n]所需的最大操作数
        dp[n-1] = 1;
        for(int i=n-2;i>=0;i--){
            for(int len=1;i+len<n;len++){
                if(lcp[i][i+len]>=len){//有至少len个字母相等
                    dp[i] = max(dp[i], dp[i+len]+1);
                }
            }
        }
        // for(int i=0;i<n;i++) cout<<dp[i]<<' ';
        return dp[0];
        
    }
};
```

