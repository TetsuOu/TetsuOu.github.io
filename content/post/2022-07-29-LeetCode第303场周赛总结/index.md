---
title:      LeetCode第303场周赛总结
description:   775/7032
date:       2022-07-29
image: post-bg-20220729.png
categories:
    - leetcode contest
tags:
    
---

![image-20220729191735861](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220729191735861.png)

## 【模拟】第一个出现两次的字母

### 题目

给你一个由小写英文字母组成的字符串 `s` ，请你找出并返回第一个出现 **两次** 的字母。

**注意：**

- 如果 `a` 的 **第二次** 出现比 `b` 的 **第二次** 出现在字符串中的位置更靠前，则认为字母 `a` 在字母 `b` 之前出现两次。
- `s` 包含至少一个出现两次的字母。

 

**示例 1：**

```
输入：s = "abccbaacz"
输出："c"
解释：
字母 'a' 在下标 0 、5 和 6 处出现。
字母 'b' 在下标 1 和 4 处出现。
字母 'c' 在下标 2 、3 和 7 处出现。
字母 'z' 在下标 8 处出现。
字母 'c' 是第一个出现两次的字母，因为在所有字母中，'c' 第二次出现的下标是最小的。
```

**示例 2：**

```
输入：s = "abcdd"
输出："d"
解释：
只有字母 'd' 出现两次，所以返回 'd' 。
```

 

**提示：**

- `2 <= s.length <= 100`
- `s` 由小写英文字母组成
- `s` 包含至少一个重复字母

### 解题思路

简单模拟即可

### 代码

```c++
class Solution {
public:
    char repeatedCharacter(string s) {
        vector<int> cnt(26,0);
        for(auto &x: s){
            cnt[x-'a']++;
            if(cnt[x-'a']>=2) return x;
        }
        return '?';
    }
};
```

## 【枚举+哈希】相等行列对

### 题目

给你一个下标从 **0** 开始、大小为 `n x n` 的整数矩阵 `grid` ，返回满足 `Ri` 行和 `Cj` 列相等的行列对 `(Ri, Cj)` 的数目*。*

如果行和列以相同的顺序包含相同的元素（即相等的数组），则认为二者是相等的。

 

**示例 1：**

<img src="https://assets.leetcode.com/uploads/2022/06/01/ex1.jpg" alt="img" style="zoom:50%;" />

```
输入：grid = [[3,2,1],[1,7,6],[2,7,7]]
输出：1
解释：存在一对相等行列对：
- (第 2 行，第 1 列)：[2,7,7]
```

**示例 2：**

<img src="https://assets.leetcode.com/uploads/2022/06/01/ex2.jpg" alt="img" style="zoom:50%;" />

```
输入：grid = [[3,1,2,2],[1,4,4,5],[2,4,2,2],[2,4,2,2]]
输出：3
解释：存在三对相等行列对：
- (第 0 行，第 0 列)：[3,1,2,2]
- (第 2 行, 第 2 列)：[2,4,2,2]
- (第 3 行, 第 2 列)：[2,4,2,2]
```

 

**提示：**

- `n == grid.length == grid[i].length`
- `1 <= n <= 200`
- `1 <= grid[i][j] <= 10^5`

### 解题思路

数据范围不大，枚举比较

利用哈希降时间复杂度

### 代码

```c++
#define ull unsigned long long
const int B = 1e9+7;
class Solution {
public:
    int equalPairs(vector<vector<int>>& grid) {
        int n = grid.size();
        unordered_map<ull,int> row,col;
        for(int i=0;i<n;i++){
            ull ha = 0;
            for(int j=0;j<n;j++){
                ha = ha*B+grid[i][j];
            }
            row[ha]++;
        }
        for(int i=0;i<n;i++){
            ull ha=0;
            for(int j=0;j<n;j++){
                ha = ha*B+grid[j][i];
            }
            col[ha]++;
        }
        int res = 0;
        
        for(auto &[ha,cnt]: row){
            if(col.count(ha)) res += cnt*col[ha];
        }
        return res;
        
    }
};
```

## 【模拟】设计食物评分系统

### 题目

设计一个支持下述操作的食物评分系统：

- **修改** 系统中列出的某种食物的评分。
- 返回系统中某一类烹饪方式下评分最高的食物。

实现 `FoodRatings` 类：

- `FoodRatings(String[] foods, String[] cuisines, int[] ratings)`初始化系统。食物由
  `foods` 、`cuisines`  和`ratings`描述，长度均为`n`

  - `foods[i]` 是第 `i` 种食物的名字。
  - `cuisines[i]` 是第 `i` 种食物的烹饪方式。
  - `ratings[i]` 是第 `i` 种食物的最初评分。

- `void changeRating(String food, int newRating)` 修改名字为 `food` 的食物的评分。

- `String highestRated(String cuisine)` 返回指定烹饪方式 `cuisine` 下评分最高的食物的名字。如果存在并列，返回 **字典序较小** 的名字。

注意，字符串 `x` 的字典序比字符串 `y` 更小的前提是：`x` 在字典中出现的位置在 `y` 之前，也就是说，要么 `x` 是 `y` 的前缀，或者在满足 `x[i] != y[i]` 的第一个位置 `i` 处，`x[i]` 在字母表中出现的位置在 `y[i]` 之前。

 

**示例：**

```
输入
["FoodRatings", "highestRated", "highestRated", "changeRating", "highestRated", "changeRating", "highestRated"]
[[["kimchi", "miso", "sushi", "moussaka", "ramen", "bulgogi"], ["korean", "japanese", "japanese", "greek", "japanese", "korean"], [9, 12, 8, 15, 14, 7]], ["korean"], ["japanese"], ["sushi", 16], ["japanese"], ["ramen", 16], ["japanese"]]
输出
[null, "kimchi", "ramen", null, "sushi", null, "ramen"]

解释
FoodRatings foodRatings = new FoodRatings(["kimchi", "miso", "sushi", "moussaka", "ramen", "bulgogi"], ["korean", "japanese", "japanese", "greek", "japanese", "korean"], [9, 12, 8, 15, 14, 7]);
foodRatings.highestRated("korean"); // 返回 "kimchi"
                                    // "kimchi" 是分数最高的韩式料理，评分为 9 。
foodRatings.highestRated("japanese"); // 返回 "ramen"
                                      // "ramen" 是分数最高的日式料理，评分为 14 。
foodRatings.changeRating("sushi", 16); // "sushi" 现在评分变更为 16 。
foodRatings.highestRated("japanese"); // 返回 "sushi"
                                      // "sushi" 是分数最高的日式料理，评分为 16 。
foodRatings.changeRating("ramen", 16); // "ramen" 现在评分变更为 16 。
foodRatings.highestRated("japanese"); // 返回 "ramen"
                                      // "sushi" 和 "ramen" 的评分都是 16 。
                                      // 但是，"ramen" 的字典序比 "sushi" 更小。
```

 

**提示：**

- `1 <= n <= 2 * 10^4`
- `n == foods.length == cuisines.length == ratings.length`
- `1 <= foods[i].length, cuisines[i].length <= 10`
- `foods[i]`、`cuisines[i]` 由小写英文字母组成
- `1 <= ratings[i] <= 10^8`
- `foods` 中的所有字符串 **互不相同**
- 在对 `changeRating` 的所有调用中，`food` 是系统中食物的名字。
- 在对 `highestRated` 的所有调用中，`cuisine` 是系统中 **至少一种** 食物的烹饪方式。
- 最多调用 `changeRating` 和 `highestRated` **总计** `2 * 10^4` 次

### 解题思路

和这周双周赛T3类似

WA了一发和上次同样的原因，val为空时，没有将该键值对erase掉

### 代码

```c++
class FoodRatings {
public:
    map<string,map<string,int>> tab;
    map<string,map<int,set<string>>> res;
    map<string,string> type;
   
    FoodRatings(vector<string>& foods, vector<string>& cuisines, vector<int>& ratings) {
        int n = foods.size();
        for(int i=0;i<n;i++){
            type[foods[i]] = cuisines[i];
            tab[cuisines[i]][foods[i]] = ratings[i];
            res[cuisines[i]][ratings[i]].insert(foods[i]);
        }
    }
    
    void changeRating(string food, int newRating) {
        string typ = type[food];
        int bef = tab[typ][food];
        tab[typ][food] = newRating;
        res[typ][bef].erase(food);
        if(res[typ][bef].empty()) res[typ].erase(bef);
        res[typ][newRating].insert(food);
    }
    
    string highestRated(string cuisine) {
        return *res[cuisine].rbegin()->second.begin();
    }
};

/**
 * Your FoodRatings object will be instantiated and called as such:
 * FoodRatings* obj = new FoodRatings(foods, cuisines, ratings);
 * obj->changeRating(food,newRating);
 * string param_2 = obj->highestRated(cuisine);
 */
```

## 【思维】优质数对的数目

### 题目

给你一个下标从 **0** 开始的正整数数组 `nums` 和一个正整数 `k` 。

如果满足下述条件，则数对 `(num1, num2)` 是 **优质数对** ：

- `num1` 和 `num2` **都** 在数组 `nums` 中存在。
- `num1 OR num2` 和 `num1 AND num2` 的二进制表示中值为 **1** 的位数之和大于等于 `k` ，其中 `OR` 是按位 **或** 操作，而 `AND` 是按位 **与** 操作。

返回 **不同** 优质数对的数目。

如果 `a != c` 或者 `b != d` ，则认为 `(a, b)` 和 `(c, d)` 是不同的两个数对。例如，`(1, 2)` 和 `(2, 1)` 不同。

**注意：** 如果 `num1` 在数组中至少出现 **一次** ，则满足 `num1 == num2` 的数对 `(num1, num2)` 也可以是优质数对。

 

**示例 1：**

```
输入：nums = [1,2,3,1], k = 3
输出：5
解释：有如下几个优质数对：
- (3, 3)：(3 AND 3) 和 (3 OR 3) 的二进制表示都等于 (11) 。值为 1 的位数和等于 2 + 2 = 4 ，大于等于 k = 3 。
- (2, 3) 和 (3, 2)： (2 AND 3) 的二进制表示等于 (10) ，(2 OR 3) 的二进制表示等于 (11) 。值为 1 的位数和等于 1 + 2 = 3 。
- (1, 3) 和 (3, 1)： (1 AND 3) 的二进制表示等于 (01) ，(1 OR 3) 的二进制表示等于 (11) 。值为 1 的位数和等于 1 + 2 = 3 。
所以优质数对的数目是 5 。
```

**示例 2：**

```
输入：nums = [5,1,1], k = 10
输出：0
解释：该数组中不存在优质数对。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`
- `1 <= k <= 60`

### 解题思路

`num1 or num2  `和`num1 and num2`的二进制中值为1的位数之和 与

`num1` 和`num2`的二进制中值为1的位数之和 等价

把二进制数看成集合，根据容斥原理$|A\cup B|=|A|+|B|-|A\cap B|$得

$|A\cup B|+|A\cap B|=|A|+|B|$

### 代码

```c++
class Solution {
public:
    long long countExcellentPairs(vector<int>& nums, int k) {
        vector<int> tab(66,0);
        sort(nums.begin(),nums.end());
        int n = unique(nums.begin(),nums.end())-nums.begin();
        for(int i=0;i<n;i++){
            int cnt = __builtin_popcount(nums[i]);
            tab[cnt]++;
        }
        long long res = 0;
        vector<long long> sum(66,0);
        sum[0] = tab[0];
        for(int i=1;i<66;i++) sum[i] = sum[i-1]+tab[i];
        
        for(int i=0;i<66;i++){
            int tar = k-i;
            int cn = sum[65];
            if(tar>0) cn-=sum[tar-1];
            res += 1ll*tab[i]*cn;
        }
        return res;
    }
};
```

### 偷学代码

```c++
class Solution {
public:
    int cnt[64];
    long long countExcellentPairs(vector<int>& nums, int k) {
        sort(nums.begin(),nums.end());
        nums.resize(unique(nums.begin(),nums.end())-nums.begin());
        for(auto x:nums)cnt[__builtin_popcountll(x)]++;
        long long ans=0;
        for(int i=0;i<64;i++)for(int j=0;j<64;j++)
            if(i+j>=k)ans+=1ll*cnt[i]*cnt[j];
        return ans;
    }
};
```

