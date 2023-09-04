---
title:      LeetCode第301场周赛总结
description:   464/7133
date:       2022-07-15
image: post-bg-20220715.png
categories:
    - leetcode contest
tags:
    - 组合数学
---

![image-20220715182212283](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220715182212283.png)

## 【贪心】装满杯子需要的最短总时长

### 题目

现有一台饮水机，可以制备冷水、温水和热水。每秒钟，可以装满 `2` 杯 **不同** 类型的水或者 `1` 杯任意类型的水。

给你一个下标从 **0** 开始、长度为 `3` 的整数数组 `amount` ，其中 `amount[0]`、`amount[1]` 和 `amount[2]` 分别表示需要装满冷水、温水和热水的杯子数量。返回装满所有杯子所需的 **最少** 秒数。

 

**示例 1：**

```
输入：amount = [1,4,2]
输出：4
解释：下面给出一种方案：
第 1 秒：装满一杯冷水和一杯温水。
第 2 秒：装满一杯温水和一杯热水。
第 3 秒：装满一杯温水和一杯热水。
第 4 秒：装满一杯温水。
可以证明最少需要 4 秒才能装满所有杯子。
```

**示例 2：**

```
输入：amount = [5,4,4]
输出：7
解释：下面给出一种方案：
第 1 秒：装满一杯冷水和一杯热水。
第 2 秒：装满一杯冷水和一杯温水。
第 3 秒：装满一杯冷水和一杯温水。
第 4 秒：装满一杯温水和一杯热水。
第 5 秒：装满一杯冷水和一杯热水。
第 6 秒：装满一杯冷水和一杯温水。
第 7 秒：装满一杯热水。
```

**示例 3：**

```
输入：amount = [5,0,0]
输出：5
解释：每秒装满一杯冷水。
```

 

**提示：**

- `amount.length == 3`
- `0 <= amount[i] <= 100`

### 解题思路

赛时看数据规模直接模拟了，每次装待装容量最多的两种水即可。



### 代码

```c++
class Solution {
public:
    int fillCups(vector<int>& amount) {
        int a=amount[0],b=amount[1],c=amount[2];
        int res=0;
        while(1){
            if(a==0&&b==0&&c==0) break;
            int maxv = max({a,b,c});
            int minv = min({a,b,c});
            int mid = (a+b+c)-maxv-minv;
            res++;
            maxv--;
            if(mid>0) mid--;
            a=maxv,b=mid,c=minv;
        }
        return res;
    }
};
```

### 偷学代码

> 肯定是最好每次都选两个。
> 分两种情况，一种是有一种水特别多，那么答案就是这种水的数量。
>
> 否则，一定可以匹配到只剩一杯，或匹配完。

```c++
class Solution {
public:
    int fillCups(vector<int>& a) {
        sort(a.begin(),a.end());
        int sum=a[0]+a[1]+a[2];
        if(a[1]+a[0]>=a[2])sum=(sum+1)/2;
        else sum=a[2];return sum;
    }
};
```

## 【模拟】无限集中的最小数字

### 题目

现有一个包含所有正整数的集合 `[1, 2, 3, 4, 5, ...]` 。

实现 `SmallestInfiniteSet` 类：

- `SmallestInfiniteSet()` 初始化 **SmallestInfiniteSet** 对象以包含 **所有** 正整数。
- `int popSmallest()` **移除** 并返回该无限集中的最小整数。
- `void addBack(int num)` 如果正整数 `num` **不** 存在于无限集中，则将一个 `num` **添加** 到该无限集中。

 

**示例：**

```
输入
["SmallestInfiniteSet", "addBack", "popSmallest", "popSmallest", "popSmallest", "addBack", "popSmallest", "popSmallest", "popSmallest"]
[[], [2], [], [], [], [1], [], [], []]
输出
[null, null, 1, 2, 3, null, 1, 4, 5]

解释
SmallestInfiniteSet smallestInfiniteSet = new SmallestInfiniteSet();
smallestInfiniteSet.addBack(2);    // 2 已经在集合中，所以不做任何变更。
smallestInfiniteSet.popSmallest(); // 返回 1 ，因为 1 是最小的整数，并将其从集合中移除。
smallestInfiniteSet.popSmallest(); // 返回 2 ，并将其从集合中移除。
smallestInfiniteSet.popSmallest(); // 返回 3 ，并将其从集合中移除。
smallestInfiniteSet.addBack(1);    // 将 1 添加到该集合中。
smallestInfiniteSet.popSmallest(); // 返回 1 ，因为 1 在上一步中被添加到集合中，
                                   // 且 1 是最小的整数，并将其从集合中移除。
smallestInfiniteSet.popSmallest(); // 返回 4 ，并将其从集合中移除。
smallestInfiniteSet.popSmallest(); // 返回 5 ，并将其从集合中移除。
```

 

**提示：**

- `1 <= num <= 1000`
- 最多调用 `popSmallest` 和 `addBack` 方法 **共计** `1000` 次

### 解题思路

模拟。大水题

### 代码

```c++
class SmallestInfiniteSet {
public:
    set<int> tab;
    SmallestInfiniteSet() {
        for(int i=1;i<=2333;i++){
            tab.insert(i);
        }
    }
    
    int popSmallest() {
        int res = *tab.begin();
        tab.erase(res);
        return res;
        
    }
    
    void addBack(int num) {
        if(!tab.count(num)) tab.insert(num);
    }
};

/**
 * Your SmallestInfiniteSet object will be instantiated and called as such:
 * SmallestInfiniteSet* obj = new SmallestInfiniteSet();
 * int param_1 = obj->popSmallest();
 * obj->addBack(num);
 */
```

## 【思维】移动片段得到字符串

### 题目

给你两个字符串 `start` 和 `target` ，长度均为 `n` 。每个字符串 **仅** 由字符 `'L'`、`'R'` 和 `'_'` 组成，其中：

- 字符 `'L'` 和 `'R'` 表示片段，其中片段 `'L'` 只有在其左侧直接存在一个 **空位** 时才能向 **左** 移动，而片段 `'R'` 只有在其右侧直接存在一个 **空位** 时才能向 **右** 移动。
- 字符 `'_'` 表示可以被 **任意** `'L'` 或 `'R'` 片段占据的空位。

如果在移动字符串 `start` 中的片段任意次之后可以得到字符串 `target` ，返回 `true` ；否则，返回 `false` 。

 

**示例 1：**

```
输入：start = "_L__R__R_", target = "L______RR"
输出：true
解释：可以从字符串 start 获得 target ，需要进行下面的移动：
- 将第一个片段向左移动一步，字符串现在变为 "L___R__R_" 。
- 将最后一个片段向右移动一步，字符串现在变为 "L___R___R" 。
- 将第二个片段向右移动散步，字符串现在变为 "L______RR" 。
可以从字符串 start 得到 target ，所以返回 true 。
```

**示例 2：**

```
输入：start = "R_L_", target = "__LR"
输出：false
解释：字符串 start 中的 'R' 片段可以向右移动一步得到 "_RL_" 。
但是，在这一步之后，不存在可以移动的片段，所以无法从字符串 start 得到 target 。
```

**示例 3：**

```
输入：start = "_R", target = "R_"
输出：false
解释：字符串 start 中的片段只能向右移动，所以无法从字符串 start 得到 target 。
```

 

**提示：**

- `n == start.length == target.length`
- `1 <= n <= 10^5`
- `start` 和 `target` 由字符 `'L'`、`'R'` 和 `'_'` 组成

### 解题思路

首先L和R的个数要分别相等

由于L和R不能互相穿过，所以相对位置要相等

L只能往左移，R只能往右移

相对位置相等时，start的`L`绝对位置一定不能小于target的`L`绝对位置，start的`R`绝对位置一定不能大于target的`R`绝对位置

### 代码

```c++
class Solution {
public:
    bool canChange(string start, string target) {
        int n = start.size();
        vector<pair<char,int>> s,t;
        for(int i=0;i<n;i++){
            char ch=start[i];
            if(ch=='L'){
                s.push_back({'L', i});
            }else if(ch=='R'){
                s.push_back({'R', i});
            }
        }
        for(int i=0;i<n;i++){
            char ch=target[i];
            if(ch=='L'){
                t.push_back({'L', i});
            }else if(ch=='R'){
                t.push_back({'R', i});
            }
        }
        int m = s.size();
        if(t.size()!=m) return false;
        for(int i=0;i<m;i++){
            if(s[i].first != t[i].first) return false;
            if(s[i].first=='L'){
                if(s[i].second < t[i].second) return false;
            }
            else if(s[i].first=='R'){
                if(s[i].second > t[i].second) return false;
            }
        }
        return true;
        
    }
};
```



## 【组合数学】统计理想数组的数目

### 题目

给你两个整数 `n` 和 `maxValue` ，用于描述一个 **理想数组** 。

对于下标从 **0** 开始、长度为 `n` 的整数数组 `arr` ，如果满足以下条件，则认为该数组是一个 **理想数组** ：

- 每个 `arr[i]` 都是从 `1` 到 `maxValue` 范围内的一个值，其中 `0 <= i < n` 。
- 每个 `arr[i]` 都可以被 `arr[i - 1]` 整除，其中 `0 < i < n` 。

返回长度为 `n` 的 **不同** 理想数组的数目。由于答案可能很大，返回对 `10^9 + 7` 取余的结果。

 

**示例 1：**

```
输入：n = 2, maxValue = 5
输出：10
解释：存在以下理想数组：
- 以 1 开头的数组（5 个）：[1,1]、[1,2]、[1,3]、[1,4]、[1,5]
- 以 2 开头的数组（2 个）：[2,2]、[2,4]
- 以 3 开头的数组（1 个）：[3,3]
- 以 4 开头的数组（1 个）：[4,4]
- 以 5 开头的数组（1 个）：[5,5]
共计 5 + 2 + 1 + 1 + 1 = 10 个不同理想数组。
```

**示例 2：**

```
输入：n = 5, maxValue = 3
输出：11
解释：存在以下理想数组：
- 以 1 开头的数组（9 个）：
   - 不含其他不同值（1 个）：[1,1,1,1,1] 
   - 含一个不同值 2（4 个）：[1,1,1,1,2], [1,1,1,2,2], [1,1,2,2,2], [1,2,2,2,2]
   - 含一个不同值 3（4 个）：[1,1,1,1,3], [1,1,1,3,3], [1,1,3,3,3], [1,3,3,3,3]
- 以 2 开头的数组（1 个）：[2,2,2,2,2]
- 以 3 开头的数组（1 个）：[3,3,3,3,3]
共计 9 + 1 + 1 = 11 个不同理想数组。
```

 

**提示：**

- `2 <= n <= 10^4`
- `1 <= maxValue <= 10^4`

### 解题思路

考虑以x结尾的理想数组，x可以进行质因数分解 $x = q_1^{r_1}q_2^{r_2} \cdots q_m^{r_m}$

构造出一个理想数组等价为，先确定结尾x，再每次将$r_i$个$q_i$因子可重复地放入n个'盒子'中（长为n的数组的n个位置上）

可重复组合$H_n^r = C_{n+r-1}^r$，可利用隔板法思考，即r个球和n-1个隔板进行组合，共C(r+n-1, r)种方案

总共有m种小球，由乘法原理求最终结果

### 代码

```c++
#define ll long long
const int MAXN = 1e4+16;
const int MOD = 1e9+7;
int c[MAXN][16];
vector<int> prime[MAXN];
void initc(){
    c[0][0]=1;
    for(int i=1;i<MAXN;i++){
        c[i][0]=1;
        for(int j=1;j<16;j++){
            c[i][j] = (c[i-1][j]+c[i-1][j-1])%MOD;
        }
    }
}
void getprime(){//质因数分解 近似为O(maxn sqrt(maxn) sqrt(maxn))
    prime[2].push_back(1);//2^1
    prime[3].push_back(1);//3^1
    for(int i=4;i<MAXN;i++){
        int c=i;
        for(int j=2;j*j<=i;j++){
            int cnt=0;
            while(c%j==0){
                cnt++;
                c=c/j;
            }
            if(cnt>0) prime[i].push_back(cnt);
        }
        if(c>1) prime[i].push_back(1);
    }
}
int init = [](){
    initc();
    getprime();
    return 0;
}();//用lambda函数的方式在main函数外 执行一次
class Solution {
public:
    int idealArrays(int n, int maxValue) {
        ll res=1;//all 1
        for(int i=2;i<=maxValue;i++){
            ll cur = 1;
            for(auto &r: prime[i]){//每个不同因子的数目为r个
                cur = (cur*c[n-1+r][r])%MOD;//隔板法 n-1个板 r个小球
            }//乘法原理乘起来
            res = (res + cur)%MOD;
        }
        return res;
    }
};
```

