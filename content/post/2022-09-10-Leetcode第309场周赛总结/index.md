---
title:      LeetCode第309场周赛总结
description:   274/7972
date:       2022-09-10
image: post-bg-20220910.png
categories:
    - leetcode contest
tags:
    - 动态规划
    - 二分
    - 组合数学
    - 线段树
---

![image-20220910165621743](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220910165621743.png)

## 【模拟】检查相同字母间的距离

### 题目

给你一个下标从 **0** 开始的字符串 `s` ，该字符串仅由小写英文字母组成，`s` 中的每个字母都 **恰好** 出现 **两次** 。另给你一个下标从 **0** 开始、长度为 `26` 的的整数数组 `distance` 。

字母表中的每个字母按从 `0` 到 `25` 依次编号（即，`'a' -> 0`, `'b' -> 1`, `'c' -> 2`, ... , `'z' -> 25`）。

在一个 **匀整** 字符串中，第 `i` 个字母的两次出现之间的字母数量是 `distance[i]` 。如果第 `i` 个字母没有在 `s` 中出现，那么 `distance[i]` 可以 **忽略** 。

如果 `s` 是一个 **匀整** 字符串，返回 `true` ；否则，返回 `false` 。

 

**示例 1：**

```
输入：s = "abaccb", distance = [1,3,0,5,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
输出：true
解释：
- 'a' 在下标 0 和下标 2 处出现，所以满足 distance[0] = 1 。
- 'b' 在下标 1 和下标 5 处出现，所以满足 distance[1] = 3 。
- 'c' 在下标 3 和下标 4 处出现，所以满足 distance[2] = 0 。
注意 distance[3] = 5 ，但是由于 'd' 没有在 s 中出现，可以忽略。
因为 s 是一个匀整字符串，返回 true 。
```

**示例 2：**

```
输入：s = "aa", distance = [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0]
输出：false
解释：
- 'a' 在下标 0 和 1 处出现，所以两次出现之间的字母数量为 0 。
但是 distance[0] = 1 ，s 不是一个匀整字符串。
```

 

**提示：**

- `2 <= s.length <= 52`
- `s` 仅由小写英文字母组成
- `s` 中的每个字母恰好出现两次
- `distance.length == 26`
- `0 <= distance[i] <= 50`

### 解题思路

按题意模拟即可

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    bool checkDistances(string s, vector<int>& distance) {
        int n = s.size();
        map<int,int> tab;
        for(int i = 0; i<n;i++){
            if(!tab.count(s[i]-'a')) tab[s[i]-'a'] = i;
            else{
                if(i-tab[s[i]-'a']-1!=distance[s[i]-'a']) return false;
            }
        }
        return true;
    }
};
```

## 【动态规划/组合数学】恰好移动 k 步到达某一位置的方法数目

### 题目

给你两个 **正** 整数 `startPos` 和 `endPos` 。最初，你站在 **无限** 数轴上位置 `startPos` 处。在一步移动中，你可以向左或者向右移动一个位置。

给你一个正整数 `k` ，返回从 `startPos` 出发、**恰好** 移动 `k` 步并到达 `endPos` 的 **不同** 方法数目。由于答案可能会很大，返回对 `109 + 7` **取余** 的结果。

如果所执行移动的顺序不完全相同，则认为两种方法不同。

**注意：**数轴包含负整数**。**

 

**示例 1：**

```
输入：startPos = 1, endPos = 2, k = 3
输出：3
解释：存在 3 种从 1 到 2 且恰好移动 3 步的方法：
- 1 -> 2 -> 3 -> 2.
- 1 -> 2 -> 1 -> 2.
- 1 -> 0 -> 1 -> 2.
可以证明不存在其他方法，所以返回 3 。
```

**示例 2：**

```
输入：startPos = 2, endPos = 5, k = 10
输出：0
解释：不存在从 2 到 5 且恰好移动 10 步的方法。
```

 

**提示：**

- `1 <= startPos, endPos, k <= 1000`

### 解题思路

一开始想用记忆化搜索，没调对，赶快直接动规了

由于可能出现负数坐标，做个向右的偏移

令dp[i]\[j]表示到达坐标i，且走的步数为j的方案数

状态转移有：dp[i]\[j] = dp[i-1]\[j-1]+dp[i+1]\[j-1]



其实也可以直接用组合数学进行求解：

经过k步从startPos到endPos，最短距离为x = endPos-startPos

如果k<x，那么肯定走不到

如果(k-x)&1，即超过x奇数步，同样也肯定走不到

以组合数学的角度上看，需要x+(k-x)/2步是正方向，需要(k-x)/2步是反方向，如此才能保证恰好走到目的地

即$C_k^{k-(endPos-startPos)/2}$



### 代码

#### 动态规划

```c++
#include<bits/stdc++.h>
#define ll long long
const int MOD = 1e9+7;
class Solution {
public:
    int numberOfWays(int startPos, int endPos, int k) {
        //向右偏移2000
        ll dp[5000][1005];
        memset(dp,0,sizeof(dp));
        dp[startPos+2000][0] = 1;
        
        for(int i=1;i<=k;i++){
            for(int j=1;j<5000;j++){
                if(dp[j][i-1]){
                    dp[j-1][i] = (dp[j][i-1]+ dp[j-1][i])%MOD;
                    dp[j+1][i] = (dp[j][i-1]+ dp[j+1][i])%MOD;
                }
            }
        }
        
        return dp[endPos+2000][k]%MOD;
        
    }
};
```

#### 组合数学

```c++
#include<bits/stdc++.h>
#define ll long long
const int MOD = 1e9+7;
ll c[1005][1005];
int tmp=[]()->int{
    c[0][0]=c[1][0]=c[1][1]=1;
    for(int i=1;i<=1000;i++){
        c[i][0]=1;
        for(int j=1;j<=1000;j++)
            c[i][j] = (c[i-1][j]+c[i-1][j-1])%MOD;
    }
    return 0;
}();
class Solution {
public:
    int numberOfWays(int startPos, int endPos, int k) {
        int x = endPos-startPos;
        if(k<x) return 0;
        if((k-x)&1) return 0;
        
        return c[k][(k-x)/2];
    }
};
```

## 【二分/滑动窗口】最长优雅子数组

### 题目

给你一个由 **正** 整数组成的数组 `nums` 。

如果 `nums` 的子数组中位于 **不同** 位置的每对元素按位 **与（AND）**运算的结果等于 `0` ，则称该子数组为 **优雅** 子数组。

返回 **最长** 的优雅子数组的长度。

**子数组** 是数组中的一个 **连续** 部分。

**注意：**长度为 `1` 的子数组始终视作优雅子数组。

 

**示例 1：**

```
输入：nums = [1,3,8,48,10]
输出：3
解释：最长的优雅子数组是 [3,8,48] 。子数组满足题目条件：
- 3 AND 8 = 0
- 3 AND 48 = 0
- 8 AND 48 = 0
可以证明不存在更长的优雅子数组，所以返回 3 。
```

**示例 2：**

```
输入：nums = [3,1,5,11,13]
输出：1
解释：最长的优雅子数组长度为 1 ，任何长度为 1 的子数组都满足题目条件。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`

### 解题思路

赛时用的二分

利用前缀和判断是否存在长度为k的优雅子数组

（其实优雅子数组的长度不会超过30）

优雅数组中的所有数要满足32位上，每一位最多只能有1个1





### 代码

#### 二分

```c++
#include<bits/stdc++.h>
#define ll long long
const int MOD = 1e9+7;
using namespace std;
class Solution {
public:
    int longestNiceSubarray(vector<int>& nums) {
        int n = nums.size();
        int sum[n+1][32];
        memset(sum,0,sizeof(sum));
        for(int i=1;i<=n;i++){
            for(int j=0;j<32;j++){
                if((nums[i-1]>>j)&1) sum[i][j] = sum[i-1][j]+1;
                else sum[i][j] = sum[i-1][j];
            }
        }
        function<bool(int)> C =[&](int k)->bool{
            for(int i=1;i+k-1<=n;i++){
                bool res = true;
                for(int j=0;j<32;j++){
                    if(sum[i+k-1][j]-sum[i-1][j]>1) {
                        res = false;break;
                    }
                }
                if(res) return true;
            }
            return false;
        };
        int l=1,r=n+1;
        while(l<r){
            int mid=(l+r)>>1;
            if(C(mid)) l=mid+1;
            else r=mid;
        }
        return l-1;
    }
};
```

### 偷学代码

#### 滑动窗口

> 如果一个区间是优雅的，那么它的子区间肯定也是优雅的（因为二进制位中出现 11 的次数不会增多），因此可以使用 two pointers 解决。
>
> right每次右移都判断下都用nums[right]和[left, right)之间的数字从right - 1开始到left做相与判断，如果不为0，则让left记录最后一个相与为0的位置。记录下此次right - left + 1的值，如此类推，用res记录每次循环的最大窗口值

```c++
int longestNiceSubarray(int* nums, int numsSize)
{
	int left = 0;
	int right = 0;
	int res = 0;
	while (right < numsSize) {
		int cur = nums[right];
		for (int i = right - 1; i >= left; --i) {
			if ((nums[i] & cur) != 0) {
				left = i  + 1;
			}
		}
		res = fmax(res, right - left + 1);
		right++;
	}
	
	return res;
}

```

## 【模拟】会议室III

### 题目

给你一个整数 `n` ，共有编号从 `0` 到 `n - 1` 的 `n` 个会议室。

给你一个二维整数数组 `meetings` ，其中 `meetings[i] = [starti, endi]` 表示一场会议将会在 **半闭** 时间区间 `[starti, endi)` 举办。所有 `starti` 的值 **互不相同** 。

会议将会按以下方式分配给会议室：

1. 每场会议都会在未占用且编号 **最小** 的会议室举办。
2. 如果没有可用的会议室，会议将会延期，直到存在空闲的会议室。延期会议的持续时间和原会议持续时间 **相同** 。
3. 当会议室处于未占用状态时，将会优先提供给原 **开始** 时间更早的会议。

返回举办最多次会议的房间 **编号** 。如果存在多个房间满足此条件，则返回编号 **最小** 的房间。

**半闭区间** `[a, b)` 是 `a` 和 `b` 之间的区间，**包括** `a` 但 **不包括** `b` 。

 

**示例 1：**

```
输入：n = 2, meetings = [[0,10],[1,5],[2,7],[3,4]]
输出：0
解释：
- 在时间 0 ，两个会议室都未占用，第一场会议在会议室 0 举办。
- 在时间 1 ，只有会议室 1 未占用，第二场会议在会议室 1 举办。
- 在时间 2 ，两个会议室都被占用，第三场会议延期举办。
- 在时间 3 ，两个会议室都被占用，第四场会议延期举办。
- 在时间 5 ，会议室 1 的会议结束。第三场会议在会议室 1 举办，时间周期为 [5,10) 。
- 在时间 10 ，两个会议室的会议都结束。第四场会议在会议室 0 举办，时间周期为 [10,11) 。
会议室 0 和会议室 1 都举办了 2 场会议，所以返回 0 。 
```

**示例 2：**

```
输入：n = 3, meetings = [[1,20],[2,10],[3,5],[4,9],[6,8]]
输出：1
解释：
- 在时间 1 ，所有三个会议室都未占用，第一场会议在会议室 0 举办。
- 在时间 2 ，会议室 1 和 2 未占用，第二场会议在会议室 1 举办。
- 在时间 3 ，只有会议室 2 未占用，第三场会议在会议室 2 举办。
- 在时间 4 ，所有三个会议室都被占用，第四场会议延期举办。 
- 在时间 5 ，会议室 2 的会议结束。第四场会议在会议室 2 举办，时间周期为 [5,10) 。
- 在时间 6 ，所有三个会议室都被占用，第五场会议延期举办。 
- 在时间 10 ，会议室 1 和 2 的会议结束。第五场会议在会议室 1 举办，时间周期为 [10,12) 。 
会议室 1 和会议室 2 都举办了 2 场会议，所以返回 1 。 
```

 

**提示：**

- `1 <= n <= 100`
- `1 <= meetings.length <= 10^5`
- `meetings[i].length == 2`
- `0 <= starti < endi <= 5 * 10^5`
- `starti` 的所有值 **互不相同**

### 解题思路

由于n比较小，可直接遍历一遍找满足条件的会议室

WA了一发是因为一开始题意没理解好

如果n比较大，可以利用线段树

### 代码

#### 暴力

```c++
#include<bits/stdc++.h>
#define ll long long
const int MOD = 1e9+7;
using namespace std;
class Solution {
public:
    int mostBooked(int n, vector<vector<int>>& meetings) {
        vector<ll> a(n, 0);//a[i] 第i个会议室的结束时间
        vector<int> cnt(n, 0);
        sort(meetings.begin(),meetings.end(),[&](vector<int>& x,vector<int>& y){
           return x[0]<y[0];
        });
        int m = meetings.size();
        for(int i=0;i<m;i++){
            int idx=-1;
            ll minv = LONG_LONG_MAX;
            for(int j=0;j<n;j++){
                if(a[j]<minv){
                    minv = a[j];
                    idx = j;
                }
            }//可以先找一轮结束时间最早的
            for(int j=0;j<n;j++){
                if(a[j]<=meetings[i][0]){
                    idx = j;break;
                }
            }//如果前面有结束时间小于当前时间的话，取代
            if(a[idx]<=meetings[i][0]){
                a[idx] = meetings[i][1];
            }else{
                a[idx] = a[idx]+(meetings[i][1]-meetings[i][0]);
            }
            cnt[idx]++;
            // cout<<idx<<' '<<a[idx]<<endl;
        }
        int idx=-1,maxv=INT_MIN;
        for(int i=0;i<n;i++){
            if(cnt[i]>maxv){
                maxv = cnt[i];
                idx=i;
            }
        }
        return idx;
    }
};
```

#### 线段树

```c++
const int MAXN = 1e2+5;
#define ll long long
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
struct node{
    ll minv;
    int idx;
}T[MAXN<<2];
void push_up(int p){
    T[p].minv = min(T[ls(p)].minv, T[rs(p)].minv);
    T[p].idx = T[ls(p)].minv<=T[rs(p)].minv?T[ls(p)].idx:T[rs(p)].idx;
}
void build(int p, int l, int r){
    if(l==r){
        T[p].minv = 0;
        T[p].idx = l;
        return;
    }
    int mid=(l+r)>>1;
    build(ls(p),l,mid);
    build(rs(p),mid+1,r);
    push_up(p);
}
void update(int p, int l, int r, int lr, pair<int,int> k){
    if(l==r && l==lr){
        if(k.first<=T[p].minv) T[p].minv += k.second-k.first;
        else T[p].minv = k.second;
        return;
    }
    int mid=(l+r)>>1;
    if(lr<=mid) update(ls(p),l,mid,lr,k);
    else update(rs(p),mid+1,r,lr,k);
    push_up(p);
}
int query(int p, int l, int r, int k){//找第一个值小于等于k的位置
    if(T[p].minv>k) return -1;
    if(l==r) return l;
    int mid = (l+r)>>1;
    if(T[ls(p)].minv<=k){
        return query(ls(p),l,mid,k);
    }else return query(rs(p),mid+1,r,k);
}
class Solution {
public:
    int mostBooked(int n, vector<vector<int>>& meetings) {
        int m = meetings.size();
        vector<int> cnt(n+1,0);
        sort(meetings.begin(),meetings.end(),[&](vector<int>& x,vector<int>& y){
           return x[0]<y[0];
        });
        build(1,1,n);
        
        for(int i=0;i<m;i++){
            int idx=-1;
            if(T[1].minv>meetings[i][0]){
                idx=T[1].idx;
            }else{
                idx = query(1,1,n,meetings[i][0]);
            }
            cnt[idx]++;
            update(1,1,n,idx,make_pair(meetings[i][0],meetings[i][1]));
        }
        int res=0,id=0;
        for(int i=1;i<=n;i++){
            if(cnt[i]>res){
                res=cnt[i];
                id=i-1;
            }
            // cout<<i<<' '<<cnt[i]<<endl;
        }
        return id;
    }
};
```

