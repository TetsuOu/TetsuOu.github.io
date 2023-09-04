---
title:      LeetCode第88场双周赛总结
description:   681/3905
date:       2022-10-05
image: post-bg-20221005.png
categories:
    - leetcode contest
tags:
    - 线段树
---

![image-20221005155518586](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20221005155518586.png)

第一题好像被rejudge掉了，麻了。

## 【枚举】删除字符使频率相同

### 题目

给你一个下标从 **0** 开始的字符串 `word` ，字符串只包含小写英文字母。你需要选择 **一个** 下标并 **删除** 下标处的字符，使得 `word` 中剩余每个字母出现 **频率** 相同。

如果删除一个字母后，`word` 中剩余所有字母的出现频率都相同，那么返回 `true` ，否则返回 `false` 。

**注意：**

- 字母 `x` 的 **频率** 是这个字母在字符串中出现的次数。
- 你 **必须** 恰好删除一个字母，不能一个字母都不删除。

 

**示例 1：**

```
输入：word = "abcc"
输出：true
解释：选择下标 3 并删除该字母，word 变成 "abc" 且每个字母出现频率都为 1 。
```

**示例 2：**

```
输入：word = "aazz"
输出：false
解释：我们必须删除一个字母，所以要么 "a" 的频率变为 1 且 "z" 的频率为 2 ，要么两个字母频率反过来。所以不可能让剩余所有字母出现频率相同。
```

 

**提示：**

- `2 <= word.length <= 100`
- `word` 只包含小写英文字母。

### 解题思路

这题情况有点多，没考虑全就容易WA

赛时WA了两发才过。今天看竟然被rejudge掉了。

赛后看其实暴力枚举就行了呀，也就不用考虑那么多情况了。

枚举删除第i种字符，再看剩余的是否合题意就行。

### 代码

```c++
class Solution {
public:
    bool equalFrequency(string word) {
        vector<int> cnt(26,0);
        for(auto &x: word){
            cnt[x-'a']++;
        }
        for(int i=0;i<26;i++){//枚举删除字符 i+'a'
            if(cnt[i]==0) continue;
            cnt[i]--;
            int last = -1;
            bool ok = true;
            for(int j=0;j<26&&ok;j++){//考虑删除字符i+'a'后，剩余字符分布情况是否合题意
                if(cnt[j]==0) continue;
                if(last==-1) last = cnt[j];
                else {
                    if(last!=cnt[j]) ok=false;
                }
            }
            cnt[i]++;
            if(ok) return true;
        }
        return false;
    }
};
```

## 【模拟】最长上传前缀

### 题目

给你一个 `n` 个视频的上传序列，每个视频编号为 `1` 到 `n` 之间的 **不同** 数字，你需要依次将这些视频上传到服务器。请你实现一个数据结构，在上传的过程中计算 **最长上传前缀** 。

如果 **闭区间** `1` 到 `i` 之间的视频全部都已经被上传到服务器，那么我们称 `i` 是上传前缀。最长上传前缀指的是符合定义的 `i` 中的 **最大值** 。

请你实现 `LUPrefix` 类：

- `LUPrefix(int n)` 初始化一个 `n` 个视频的流对象。
- `void upload(int video)` 上传 `video` 到服务器。
- `int longest()` 返回上述定义的 **最长上传前缀** 的长度。

 

**示例 1：**

```
输入：
["LUPrefix", "upload", "longest", "upload", "longest", "upload", "longest"]
[[4], [3], [], [1], [], [2], []]
输出：
[null, null, 0, null, 1, null, 3]

解释：
LUPrefix server = new LUPrefix(4);   // 初始化 4个视频的上传流
server.upload(3);                    // 上传视频 3 。
server.longest();                    // 由于视频 1 还没有被上传，最长上传前缀是 0 。
server.upload(1);                    // 上传视频 1 。
server.longest();                    // 前缀 [1] 是最长上传前缀，所以我们返回 1 。
server.upload(2);                    // 上传视频 2 。
server.longest();                    // 前缀 [1,2,3] 是最长上传前缀，所以我们返回 3 。
```

 

**提示：**

- `1 <= n <= 10^5`
- `1 <= video <= 10^5`
- `video` 中所有值 **互不相同** 。
- `upload` 和 `longest` **总调用** 次数至多不超过 `2 * 10^5` 次。
- 至少会调用 `longest` 一次。

### 解题思路

乐。赛时又用了线段树。

其实用一个数组一个指针就可以维护这个最长前缀了，这个最长前缀不会减小。

### 代码

```c++
class LUPrefix {
public:
    int a[100005];
    int cur;
    LUPrefix(int n) {
        memset(a,0,sizeof(a));
        cur = 0;
    }
    
    void upload(int video) {
        a[video] = 1;
    }
    
    int longest() {
        while(a[cur+1]==1){
            cur++;
        }
        return cur;
    }
};

/**
 * Your LUPrefix object will be instantiated and called as such:
 * LUPrefix* obj = new LUPrefix(n);
 * obj->upload(video);
 * int param_2 = obj->longest();
 */
```

#### 线段树

```c++
#define ll long long
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
const int MAXN = 2e5+5;
struct node{
    int cnt;
}T[MAXN<<2];
void push_up(int p,int l,int r){
    int mid=(l+r)>>1;
    if(T[ls(p)].cnt==(mid-l+1)) T[p].cnt = T[ls(p)].cnt+T[rs(p)].cnt;
    else T[p].cnt = T[ls(p)].cnt;
}
void build(int p, int l, int r){
    if(l==r){
        T[p].cnt = 0;
        return;
    }
    int mid = (l+r)>>1;
    build(ls(p),l,mid);
    build(rs(p),mid+1,r);
    push_up(p,l,r);
}
void update(int p,int l,int r,int lr){
    if(l==r && l==lr){
        T[p].cnt =1;
        return;
    }
    int mid=(l+r)>>1;
    if(lr<=mid) update(ls(p),l,mid,lr);
    else update(rs(p),mid+1,r,lr);
    push_up(p,l,r);
}

class LUPrefix {
public:
    int N;
    LUPrefix(int n) {
        N = n;
        build(1,1,N);
    }
    
    void upload(int video) {
        update(1,1,N,video);
    }
    
    int longest() {
        return T[1].cnt;
    }
};
```

## 【思维+位运算】所有数对的异或和

### 题目

给你两个下标从 **0** 开始的数组 `nums1` 和 `nums2` ，两个数组都只包含非负整数。请你求出另外一个数组 `nums3` ，包含 `nums1` 和 `nums2` 中 **所有数对** 的异或和（`nums1` 中每个整数都跟 `nums2` 中每个整数 **恰好** 匹配一次）。

请你返回 `nums3` 中所有整数的 **异或和** 。

 

**示例 1：**

```
输入：nums1 = [2,1,3], nums2 = [10,2,5,0]
输出：13
解释：
一个可能的 nums3 数组是 [8,0,7,2,11,3,4,1,9,1,6,3] 。
所有这些数字的异或和是 13 ，所以我们返回 13 。
```

**示例 2：**

```
输入：nums1 = [1,2], nums2 = [3,4]
输出：0
解释：
所有数对异或和的结果分别为 nums1[0] ^ nums2[0] ，nums1[0] ^ nums2[1] ，nums1[1] ^ nums2[0] 和 nums1[1] ^ nums2[1] 。
所以，一个可能的 nums3 数组是 [2,5,1,6] 。
2 ^ 5 ^ 1 ^ 6 = 0 ，所以我们返回 0 。
```

 

**提示：**

- `1 <= nums1.length, nums2.length <= 10^5`
- `0 <= nums1[i], nums2[j] <= 10^9`

### 解题思路

主要利用异或的性质： a^a = 0

这题的答案与两个数组的奇偶性有关

两个数组都是偶数个时，答案为0

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
class Solution {
public:
    int xorAllNums(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size(), m = nums2.size();
        int res1 = 0, res2 = 0;
        for(auto &x: nums1) res1 = res1 ^ x;
        for(auto &x: nums2) res2 = res2 ^ x;
        if((n&1) && (m&1)) return res1^res2;
        else if(!(n&1) && !(m&1)) return 0;
        else if((n&1) && !(m&1)) return res2;
        else return res1;
    }
};
```

## 【线段树+类逆序对】满足不等式的数对数目

### 题目

给你两个下标从 **0** 开始的整数数组 `nums1` 和 `nums2` ，两个数组的大小都为 `n` ，同时给你一个整数 `diff` ，统计满足以下条件的 **数对** `(i, j)` ：

- `0 <= i < j <= n - 1` **且**
- `nums1[i] - nums1[j] <= nums2[i] - nums2[j] + diff`.

请你返回满足条件的 **数对数目** 。

 

**示例 1：**

```
输入：nums1 = [3,2,5], nums2 = [2,2,1], diff = 1
输出：3
解释：
总共有 3 个满足条件的数对：
1. i = 0, j = 1：3 - 2 <= 2 - 2 + 1 。因为 i < j 且 1 <= 1 ，这个数对满足条件。
2. i = 0, j = 2：3 - 5 <= 2 - 1 + 1 。因为 i < j 且 -2 <= 2 ，这个数对满足条件。
3. i = 1, j = 2：2 - 5 <= 2 - 1 + 1 。因为 i < j 且 -3 <= 2 ，这个数对满足条件。
所以，我们返回 3 。
```

**示例 2：**

```
输入：nums1 = [3,-1], nums2 = [-2,2], diff = -1
输出：0
解释：
没有满足条件的任何数对，所以我们返回 0 。
```

 

**提示：**

- `n == nums1.length == nums2.length`
- `2 <= n <= 10^5`
- `-10^4 <= nums1[i], nums2[i] <= 10^4`
- `-10^4 <= diff <= 10^4`

### 解题思路

根据`nums1[i] - nums1[j] <= nums2[i] - nums2[j] + diff`移项得：`nums1[i] - nums2[i] <= nums1[j]- nums2[j] + diff`

这样研究对象就变成一个数组了

令`a[i] = nums1[i] - nums2[i]`，要求`a[i] <= a[j]+diff`且`i<j`的数对个数。

和逆序对的思路类似。

倒序遍历数组，考虑a[i]时，就不会受下标小于i的数的影响。

再利用线段树/树状数组等结构维护`a[j] + diff`，（其中$j\in[i+1,n]$）的值的分布情况，可在`log(n)`时间复杂度内求得以i开头的数对(i,j)数目。

由于值可能出现负数，离散化一下即可。

总时间复杂度$O(n\log n)$

![微信图片_20221002003919.jpg](https://pic.leetcode-cn.com/1664642377-PawZNH-%E5%BE%AE%E4%BF%A1%E5%9B%BE%E7%89%87_20221002003919.jpg)

### 代码

```c++
#include<bits/stdc++.h>
using namespace std;
#define ll long long
#define ls(p) (p<<1)
#define rs(p) (p<<1|1)
const int MAXN = 2e5+5;
struct node{
    int cnt;
}T[MAXN<<2];
void push_up(int p){
    T[p].cnt  = T[ls(p)].cnt + T[rs(p)].cnt;
}
void build(int p, int l, int r){
    if(l==r){
        T[p].cnt = 0;
        return;
    }
    int mid = (l+r)>>1;
    build(ls(p),l,mid);
    build(rs(p),mid+1,r);
    push_up(p);
}
void update(int p,int l,int r,int lr){
    if(l==r && l==lr){
        T[p].cnt += 1;
        return ;
    }
    int mid=(l+r)>>1;
    if(lr<=mid) update(ls(p),l,mid,lr);
    else update(rs(p),mid+1,r,lr);
    push_up(p);
}
int query(int p,int l,int r,int nl,int nr){
    if(nl<=l && nr>=r) return T[p].cnt;
    int mid=(l+r)>>1;
    int res=0;
    if(nl<=mid) res+= query(ls(p),l,mid,nl,nr);
    if(nr>mid) res+=query(rs(p),mid+1,r,nl,nr);
    return res;
}
class Solution {
public:
    long long numberOfPairs(vector<int>& nums1, vector<int>& nums2, int diff) {
        int n = nums1.size();
        vector<int> a(n,0);
        vector<int> tab;
        for(int i=0;i<n;i++) {
            a[i] = nums1[i] - nums2[i];
            tab.push_back(a[i]);
            tab.push_back(a[i]+diff);
        }
        ll res=0;
        sort(tab.begin(),tab.end());
        // int m = unique(tab.begin(),tab.end())-tab.begin()+1;
        int m = n*2;
        function<int(int)> getid = [&](int val)->int{
            int idx = lower_bound(tab.begin(),tab.end(),val)-tab.begin()+1;
            return idx;
        } ;
        
        build(1,1,m);
        for(int i=n-1;i>=0;i--){
            int u = getid(a[i]);
            int num = query(1,1,m,u,m);
            res += num;
            int v = getid(a[i]+diff);
            update(1,1,m,v);
        }
        return res;
        
    }
};
```

