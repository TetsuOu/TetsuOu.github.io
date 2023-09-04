---
title:      LeetCode第83场双周赛总结
description:   1122/4437
date:       2022-07-28
image: post-bg-20220728.png
categories: 
    - leetcode contest
tags:
---

![image-20220728220923041](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220728220923041.png)

## 【模拟】最好的扑克手牌

### 题目

给你一个整数数组 `ranks` 和一个字符数组 `suit` 。你有 `5` 张扑克牌，第 `i` 张牌大小为 `ranks[i]` ，花色为 `suits[i]` 。

下述是从好到坏你可能持有的 **手牌类型** ：

1. `"Flush"`：同花，五张相同花色的扑克牌。
2. `"Three of a Kind"`：三条，有 3 张大小相同的扑克牌。
3. `"Pair"`：对子，两张大小一样的扑克牌。
4. `"High Card"`：高牌，五张大小互不相同的扑克牌。

请你返回一个字符串，表示给定的 5 张牌中，你能组成的 **最好手牌类型** 。

**注意：**返回的字符串 **大小写** 需与题目描述相同。

 

**示例 1：**

```
输入：ranks = [13,2,3,1,9], suits = ["a","a","a","a","a"]
输出："Flush"
解释：5 张扑克牌的花色相同，所以返回 "Flush" 。
```

**示例 2：**

```
输入：ranks = [4,4,2,4,4], suits = ["d","a","a","b","c"]
输出："Three of a Kind"
解释：第一、二和四张牌组成三张相同大小的扑克牌，所以得到 "Three of a Kind" 。
注意我们也可以得到 "Pair" ，但是 "Three of a Kind" 是更好的手牌类型。
有其他的 3 张牌也可以组成 "Three of a Kind" 手牌类型。
```

**示例 3：**

```
输入：ranks = [10,10,2,12,9], suits = ["a","b","c","a","d"]
输出："Pair"
解释：第一和第二张牌大小相同，所以得到 "Pair" 。
我们无法得到 "Flush" 或者 "Three of a Kind" 。
```

 

**提示：**

- `ranks.length == suits.length == 5`
- `1 <= ranks[i] <= 13`
- `'a' <= suits[i] <= 'd'`
- 任意两张扑克牌不会同时有相同的大小和花色。

### 解题思路

简单模拟即可

### 代码

```c++
class Solution {
public:
    string bestHand(vector<int>& ranks, vector<char>& suits) {
        map<char,int> s;
        map<int,int> r;
        for(auto &x: ranks) r[x]++;
        for(auto &x: suits) s[x]++;
        
        if(s.size()==1) return "Flush";
        
        for(auto &x: r){
            if(x.second>=3) return "Three of a Kind";
        }
        
        for(auto &x: r){
            if(x.second==2) return "Pair";
        }
        
        return "High Card";
        
    }
};
```

## 【枚举】全 0 子数组的数目

### 题目

给你一个整数数组 `nums` ，返回全部为 `0` 的 **子数组** 数目。

**子数组** 是一个数组中一段连续非空元素组成的序列。

 

**示例 1：**

```
输入：nums = [1,3,0,0,2,0,0,4]
输出：6
解释：
子数组 [0] 出现了 4 次。
子数组 [0,0] 出现了 2 次。
不存在长度大于 2 的全 0 子数组，所以我们返回 6 。
```

**示例 2：**

```
输入：nums = [0,0,0,2,0,0]
输出：9
解释：
子数组 [0] 出现了 5 次。
子数组 [0,0] 出现了 3 次。
子数组 [0,0,0] 出现了 1 次。
不存在长度大于 3 的全 0 子数组，所以我们返回 9 。
```

**示例 3：**

```
输入：nums = [2,10,2019]
输出：0
解释：没有全 0 子数组，所以我们返回 0 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `-109 <= nums[i] <= 10^9`

### 解题思路

一段长度为n的全部元素为0的数组，拥有全部元素为0的子数组数目为：$\frac{n(n+1)}{2}$

所以统计全部元素为0的数组即可

WA的一发是统计0段的时候出错了

### 代码

```c++
#define ll long long
class Solution {
public:
    long long zeroFilledSubarray(vector<int>& nums) {
        int n = nums.size();
        ll res=0;
        
        int i,l=-1,r=0;
        for(i=0;i<n;i++){
           if(nums[i]==0){
               if(l==-1) l=i,r=i;
               else r=i;
           }else{
               if(l!=-1){
                   int len=r-l+1;
                   res += 1ll*len*(len+1)/2;
                   l=-1;
               }
           }
        }
        
        if(nums[n-1]==0){
            int len=n-1-l+1;
            res += 1ll*len*(len+1)/2;
        }
        
        return res;
    }
};
```

### 偷学代码

考虑每个以 0 结尾的子数组的个数。

统计连续 0 组成的长度 c，每个 c 可以贡献 c 个子数组。

0：以最后一个0为右端点的子数组，有1个

00：以最后一个0为右端点的子数组，有2个

000：以最后一个0为右端点的子数组，有3个

...

```c++
class Solution {
public:
    long long zeroFilledSubarray(vector<int> &nums) {
        long ans = 0L;
        int c = 0;
        for (int num : nums)
            if (num) c = 0;
            else ans += ++c;
        return ans;
    }
};

作者：endlesscheng
链接：https://leetcode.cn/problems/number-of-zero-filled-subarrays/solution/by-endlesscheng-men8/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

## 【模拟】设计数字容器系统

### 题目

设计一个数字容器系统，可以实现以下功能：

- 在系统中给定下标处 **插入** 或者 **替换** 一个数字。
- **返回** 系统中给定数字的最小下标。

请你实现一个 `NumberContainers` 类：

- `NumberContainers()` 初始化数字容器系统。
- `void change(int index, int number)` 在下标 `index` 处填入 `number` 。如果该下标 `index` 处已经有数字了，那么用 `number` 替换该数字。
- `int find(int number)` 返回给定数字 `number` 在系统中的最小下标。如果系统中没有 `number` ，那么返回 `-1` 。

 

**示例：**

```
输入：
["NumberContainers", "find", "change", "change", "change", "change", "find", "change", "find"]
[[], [10], [2, 10], [1, 10], [3, 10], [5, 10], [10], [1, 20], [10]]
输出：
[null, -1, null, null, null, null, 1, null, 2]

解释：
NumberContainers nc = new NumberContainers();
nc.find(10); // 没有数字 10 ，所以返回 -1 。
nc.change(2, 10); // 容器中下标为 2 处填入数字 10 。
nc.change(1, 10); // 容器中下标为 1 处填入数字 10 。
nc.change(3, 10); // 容器中下标为 3 处填入数字 10 。
nc.change(5, 10); // 容器中下标为 5 处填入数字 10 。
nc.find(10); // 数字 10 所在的下标为 1 ，2 ，3 和 5 。因为最小下标为 1 ，所以返回 1 。
nc.change(1, 20); // 容器中下标为 1 处填入数字 20 。注意，下标 1 处之前为 10 ，现在被替换为 20 。
nc.find(10); // 数字 10 所在下标为 2 ，3 和 5 。最小下标为 2 ，所以返回 2 。
```

 

**提示：**

- `1 <= index, number <= 10^9`
- 调用 `change` 和 `find` 的 **总次数** 不超过 `10^5` 次。

### 解题思路

每个下标不同，数字可能相同

map<int, set<int\>>  tab;   其key表示数字的值，value表示对应下标的集合

map<int, int>  a;  其key表示下标，value表示对应下标的值

WA了一发是由于tab的key对应集合为空的话，没有删除该键值对。

### 代码

```c++
class NumberContainers {
public:
    map<int,set<int>> tab;
    map<int,int> a;
    NumberContainers() {

    }
    
    void change(int index, int number) {
        if(!a.count(index)){
            a[index] = number;
            tab[number].insert(index);
        }else{
            int bef = a[index];
            a[index] = number;
            tab[bef].erase(index);
            tab[number].insert(index);
        }
    }
    
    int find(int number) {
        if(!tab.count(number)) return -1;
        if(tab[number].empty()) return -1;
        return *tab[number].begin();
    }
};

/**
 * Your NumberContainers object will be instantiated and called as such:
 * NumberContainers* obj = new NumberContainers();
 * obj->change(index,number);
 * int param_2 = obj->find(number);
 */
```

## 【思维】不可能得到的最短骰子序列

### 题目

给你一个长度为 `n` 的整数数组 `rolls` 和一个整数 `k` 。你扔一个 `k` 面的骰子 `n` 次，骰子的每个面分别是 `1` 到 `k` ，其中第 `i` 次扔得到的数字是 `rolls[i]` 。

请你返回 **无法** 从 `rolls` 中得到的 **最短** 骰子子序列的长度。

扔一个 `k` 面的骰子 `len` 次得到的是一个长度为 `len` 的 **骰子子序列** 。

**注意** ，子序列只需要保持在原数组中的顺序，不需要连续。

 

**示例 1：**

```
输入：rolls = [4,2,1,2,3,3,2,4,1], k = 4
输出：3
解释：所有长度为 1 的骰子子序列 [1] ，[2] ，[3] ，[4] 都可以从原数组中得到。
所有长度为 2 的骰子子序列 [1, 1] ，[1, 2] ，... ，[4, 4] 都可以从原数组中得到。
子序列 [1, 4, 2] 无法从原数组中得到，所以我们返回 3 。
还有别的子序列也无法从原数组中得到。
```

**示例 2：**

```
输入：rolls = [1,1,2,2], k = 2
输出：2
解释：所有长度为 1 的子序列 [1] ，[2] 都可以从原数组中得到。
子序列 [2, 1] 无法从原数组中得到，所以我们返回 2 。
还有别的子序列也无法从原数组中得到，但 [2, 1] 是最短的子序列。
```

**示例 3：**

```
输入：rolls = [1,1,3,2,2,2,3,3], k = 4
输出：1
解释：子序列 [4] 无法从原数组中得到，所以我们返回 1 。
还有别的子序列也无法从原数组中得到，但 [4] 是最短的子序列。
```

 

**提示：**

- `n == rolls.length`
- `1 <= n <= 10^5`
- `1 <= rolls[i] <= k <= 10^5`

### 解题思路

遍历元素过程中维护一个set，每当set中元素种类数达到k时，说明可以得到的骰子子序列长度+1

就是看可以产生res个这样的set，可以得到的骰子子序列最长长度就是res

那么不能得到的最短骰子子序列长度即是res+1

注意：当输入数据为[1,2,3,2,1]，3时，输出应该为2。此时，长度为2的骰子序列中，[3,3]未出现过

### 代码

```c++
class Solution {
public:
    int shortestSequence(vector<int>& rolls, int k) {
        set<int> st;
        int res=0;
        for(auto &x: rolls){
            st.insert(x);
            if(st.size()==k){
                st.clear();
                res++;
            }
        }
        return res+1;
    }
};
```

