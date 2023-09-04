---
title:      数位DP
description:   动态规划
date:       2022-08-19
image: post-bg-20220819.png
categories:
    - data structure and algorithm
tags:
    - 数位DP
---

## 前言

先将数转为字符串s，记忆化搜索，枚举s的每一位

一般有4个参数：pos, state, is_limit, is_num

- pos 表示当前枚举到哪一位，必须的
- state 表示枚举到当前位时，之前的位的一些状态，可以是已选取数字集合等等，不是必须的，视题中的限制而决定
- is_limit 表示枚举到当前位时，之前的位是否达到了上限，比如n=1234，pos=3，此时is_limit=true表示前面枚举的是123。这一项决定枚举当前位的上限数字
- is_num 表示枚举到当前位时，之前的位是否表示一个合法的数，用来处理前导0的情况。如果is_num=true，那么当前位可从0开始枚举；如果is_num=false，那么当前位要从1开始枚举(后续is_num为true），或者跳过（后续is_num仍为false)

```c++
//is_limit要初始化为true，不然后面都会为false

//is_num初始化为false

//is_limit为true时不会存在重复运算，不会重复访问一个状态

//is_num为false时前面都为空，也不会存在重复运算，也不需要记忆化
```

## 至少有1位重复的数字

### 题目

给定正整数 n，返回在 [1, n] 范围内具有 至少 1 位 重复数字的正整数的个数。

 

示例 1：

输入：n = 20
输出：1
解释：具有至少 1 位重复数字的正数（<= 20）只有 11 。
示例 2：

输入：n = 100
输出：10
解释：具有至少 1 位重复数字的正数（<= 100）有 11，22，33，44，55，66，77，88，99 和 100 。
示例 3：

输入：n = 1000
输出：262


提示：

1 <= n <= 10^9

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/numbers-with-repeated-digits
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

求含至少1位重复数字的正整数的个数，从反面更容易求，即求**不含重复数字的正整数的个数x**，那么结果即为n-x

state表示前面所填数字的集合，用位来记录

### 代码

```c++
class Solution {
public:
    int numDupDigitsAtMostN(int n) {
        string num = to_string(n);
        int m = num.size(), dp[m][1<<10];
        memset(dp,-1,sizeof(dp));
        //从pos开始填数字，pos前面填的数字的集合是state，能构造出的不含重复数字的正整数的个数
        //is_limit 表示前面填的数字是否都是n对应位上的，如果为true，那么当前位至多为int(num[i])，否则至多为9
        //is_num 表示前面是否填了数字（是否跳过），如果为true，那么当前位可以从0开始，如果为false，那么可以跳过，或者从1开始填数字（不能前导0）

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
        return n - dfs(0,0,true,false);
        //is_limit要初始化为true，不然后面都会为false
        //is_num初始化为false
        //is_limit为true时不会存在重复运算，不会重复访问一个状态
        //is_num为false时前面都为空，也不会存在重复运算，也不需要记忆化
    }
};
```

## 最大为 N 的数字组合

### 题目

给定一个按 非递减顺序 排列的数字数组 digits 。你可以用任意次数 digits[i] 来写的数字。例如，如果 digits = ['1','3','5']，我们可以写数字，如 '13', '551', 和 '1351315'。

返回 可以生成的小于或等于给定整数 n 的正整数的个数 。

 

示例 1：

输入：digits = ["1","3","5","7"], n = 100
输出：20
解释：
可写出的 20 个数字是：
1, 3, 5, 7, 11, 13, 15, 17, 31, 33, 35, 37, 51, 53, 55, 57, 71, 73, 75, 77.
示例 2：

输入：digits = ["1","4","9"], n = 1000000000
输出：29523
解释：
我们可以写 3 个一位数字，9 个两位数字，27 个三位数字，
81 个四位数字，243 个五位数字，729 个六位数字，
2187 个七位数字，6561 个八位数字和 19683 个九位数字。
总共，可以使用D中的数字写出 29523 个整数。
示例 3:

输入：digits = ["7"], n = 8
输出：1


提示：

1 <= digits.length <= 9

digits[i].length == 1

digits[i] 是从 '1' 到 '9' 的数

digits 中的所有值都 不同 

digits 按 非递减顺序 排列

1 <= n <= 10^9

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/numbers-at-most-n-given-digit-set
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

限制条件为**只能用给定的数字**

用不到state

注意，这题是需要is_num的，虽然给定的数字中不会出现0，如果没有is_num，**将没办法跳过高位数字**，若n=2345，那么千位必须要填一个数，实际上是可以跳过的，所以需要is_num位标记

### 代码

```c++
class Solution {
public:
    int atMostNGivenDigitSet(vector<string>& digits, int n) {
        auto s = to_string(n);
        int m = s.size();
        vector<int> dp(m,-1);
        set<int> ok;
        for(auto &x: digits) ok.insert(stoi(x));
        function<int(int,bool,bool)> f = [&](int pos, bool is_limit, bool is_num)->int{
            if(pos == m) return is_num;
            if(!is_limit && is_num && dp[pos]>=0) return dp[pos];
            int res = 0;
            if(!is_num) res = f(pos+1, false, false);
            for(int d=1-is_num,up=is_limit?s[pos]-'0':9;d<=up;d++){
                if(ok.count(d)){//只用给定的数字
                    res += f(pos+1, is_limit && d==up, true);
                }
            }
            if(!is_limit && is_num) dp[pos] = res;
            return res;
        };


        return f(0,true,false);
    }
};
```

## 2出现的次数

### 题目

编写一个方法，计算从 0 到 n (含 n) 中数字 2 出现的次数。

示例:

输入: 25
输出: 9
解释: (2, 12, 20, 21, 22, 23, 24, 25)(注意 22 应该算作两次)
提示：

n <= 10^9

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/number-of-2s-in-range-lcci
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

state位需要，**记录前面2出现的次数**即可

is_num位不必须，就算考虑了前导0的情况，也不影响2出现的次数

### 代码

```c++
class Solution {
public:
    int numberOf2sInRange(int n) {
        auto s = to_string(n);
        int m = s.size();
        int dp[m][m];
        memset(dp,-1,sizeof(dp));
        function<int(int,int,bool)> f = [&](int pos, int cnt2, bool is_limit)->int{
            if(pos == m) return cnt2;
            if(!is_limit && dp[pos][cnt2]>=0) return dp[pos][cnt2];
            int res = 0;
            
            int up = is_limit?s[pos]-'0':9;
            for(int d=0;d<=up;d++){
                res += f(pos+1, cnt2 + (d==2), is_limit&&d==up);//只有当前位是2时，2出现的次数+1
            }
            if(!is_limit) dp[pos][cnt2] = res;
            return res;
        };
        return f(0,0,true);
    }
};
```

## 不含连续1的非负整数

### 题目

给定一个正整数 n ，返回范围在 [0, n] 都非负整数中，其二进制表示不包含 连续的 1 的个数。

 

示例 1:

输入: n = 5
输出: 5
解释: 
下面是带有相应二进制表示的非负整数<= 5：
0 : 0
1 : 1
2 : 10
3 : 11
4 : 100
5 : 101
其中，只有整数3违反规则（有两个连续的1），其他5个满足规则。
示例 2:

输入: n = 1
输出: 2
示例 3:

输入: n = 2
输出: 3


提示:

1 <= n <= 10^9

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/non-negative-integers-without-consecutive-ones
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

注意题中限制是**二进制表示**中的限制，故先求出其二进制表示

求不包含连续1的情况

可以先求包含连续1的情况，再拿n+1减

state是需要的，记录出现的连续的1的情况

is_num不必需，不影响连续的1

### 代码

```c++
class Solution {
public:
    int findIntegers(int n) {
        string s;
        //高位到低位的二进制表示
        for(int i=31;i>=0;i--) s.push_back(((n>>i)&1)+'0');
        
        int m = s.size();
        int dp[m][4];
        //state==2 前面的数中已经出现了连续的1
        //state==1 前面的数中还没出现连续的1，但前一个数正好是1
        //state==0 前面的数中还没出现连续的1，且前一个数也不是1
        memset(dp,-1,sizeof(dp));
        function<int(int,int,bool)> f = [&](int pos, int state, bool is_limit)->int{
            if(pos == m) {
                return state == 2;
            };
            if(!is_limit &&dp[pos][state]>=0) return dp[pos][state];
            int res = 0;
            
            int up = is_limit?s[pos]-'0':1;
            for(int d = 0; d<=up; d++){
                int nxt;
                //根据之前状态与当前位是否是1，决定下一状态
                if(state==2 || (state==1 && d==1)) nxt = 2;
                else if(d == 1) nxt = 1;
                else nxt = 0;
                res += f(pos+1, nxt, is_limit && d==up);
            }
            if(!is_limit) dp[pos][state] = res;
            return res;
        };
        //二进制表示中含连续的1的个数为f，所求即为n+1-f
        return n+1-f(0, 0, true);
    }
};
```

## 数字1的个数1

### 题目

给定一个整数 n，计算所有小于等于 n 的非负整数中数字 1 出现的个数。

 

示例 1：

输入：n = 13
输出：6
示例 2：

输入：n = 0
输出：0


提示：

0 <= n <= 10^9

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/number-of-digit-one
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

state记录数字1出现的个数即可

is_num不必须，不影响1出现的个数

### 代码

```c++
class Solution {
public:
    int countDigitOne(int n) {
        auto s = to_string(n);
        int m = s.size();
        int dp[m][m];//cnt: 数字1出现的个数
        memset(dp,-1,sizeof(dp));
        function<int(int,int,bool)> f = [&](int pos,int cnt,int is_limit)->int{
        	if(pos == m) return cnt;
        	if(!is_limit && dp[pos][cnt]>=0) return dp[pos][cnt];
        	int res = 0;
        	int up = is_limit?s[pos]-'0':9;
        	for(int d=0;d<=up;d++){
            	res += f(pos+1, cnt+(d==1), is_limit&&d==up);
        	}
        	if(!is_limit) dp[pos][cnt]=res;
        	return res;
    		};
        return f(0,0,true);
    }
};
```

