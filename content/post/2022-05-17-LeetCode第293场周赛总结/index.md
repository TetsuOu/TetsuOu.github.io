---
title:      LeetCode第293场周赛总结
description:   363/7356
date:       2022-05-17 00:00:00+0000
image: post-bg-20220517.png
categories: 
    - leetcode contest
tags:
    
---

![image-20220515152619420](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220515152619420.png)

第一次AK力扣周赛，上大分了。继上次双周赛刷新最好名次后，周赛再度刷新。

## 【模拟】移除字母异位词后的结果数组

### 题目

给你一个下标从 **0** 开始的字符串 `words` ，其中 `words[i]` 由小写英文字符组成。

在一步操作中，需要选出任一下标 `i` ，从 `words` 中 **删除** `words[i]` 。其中下标 `i` 需要同时满足下述两个条件：

1. `0 < i < words.length`
2. `words[i - 1]` 和 `words[i]` 是 **字母异位词** 。

只要可以选出满足条件的下标，就一直执行这个操作。

在执行所有操作后，返回 `words` 。可以证明，按任意顺序为每步操作选择下标都会得到相同的结果。

**字母异位词** 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。例如，`"dacb"` 是 `"abdc"` 的一个字母异位词。

 

**示例 1：**

```
输入：words = ["abba","baba","bbaa","cd","cd"]
输出：["abba","cd"]
解释：
获取结果数组的方法之一是执行下述步骤：
- 由于 words[2] = "bbaa" 和 words[1] = "baba" 是字母异位词，选择下标 2 并删除 words[2] 。
  现在 words = ["abba","baba","cd","cd"] 。
- 由于 words[1] = "baba" 和 words[0] = "abba" 是字母异位词，选择下标 1 并删除 words[1] 。
  现在 words = ["abba","cd","cd"] 。
- 由于 words[2] = "cd" 和 words[1] = "cd" 是字母异位词，选择下标 2 并删除 words[2] 。
  现在 words = ["abba","cd"] 。
无法再执行任何操作，所以 ["abba","cd"] 是最终答案。
```

**示例 2：**

```
输入：words = ["a","b","c","d","e"]
输出：["a","b","c","d","e"]
解释：
words 中不存在互为字母异位词的两个相邻字符串，所以无需执行任何操作。
```

 

**提示：**

- `1 <= words.length <= 100`
- `1 <= words[i].length <= 10`
- `words[i]` 由小写英文字母组成

### 解题思路

按题意模拟即可

### 代码

```c++
class Solution {
public:
    bool isok(string& a, string& b){//判断是否是字母异位词
        if(a.size()!=b.size()) return false;
        map<char,int> ma,mb;
        for(auto &x: a) ma[x]++;
        for(auto &x: b) mb[x]++;
        return ma==mb;
    }
    vector<string> removeAnagrams(vector<string>& words) {
        int n = words.size();
        if(n==1) return words;
        vector<bool> del(n,0);
        vector<string> res;
        for(int i=1;i<n;i++){
            if(isok(words[i-1],words[i])) del[i]=1;
        }
        for(int i=0;i<n;i++){
            if(!del[i]) res.push_back(words[i]);
        }
        return res;
    }
};
```

## 【排序】不含特殊楼层的最大连续楼层数

### 题目

Alice 管理着一家公司，并租用大楼的部分楼层作为办公空间。Alice 决定将一些楼层作为 **特殊楼层** ，仅用于放松。

给你两个整数 `bottom` 和 `top` ，表示 Alice 租用了从 `bottom` 到 `top`（含 `bottom` 和 `top` 在内）的所有楼层。另给你一个整数数组 `special` ，其中 `special[i]` 表示 Alice 指定用于放松的特殊楼层。

返回不含特殊楼层的 **最大** 连续楼层数。

 

**示例 1：**

```
输入：bottom = 2, top = 9, special = [4,6]
输出：3
解释：下面列出的是不含特殊楼层的连续楼层范围：
- (2, 3) ，楼层数为 2 。
- (5, 5) ，楼层数为 1 。
- (7, 9) ，楼层数为 3 。
因此，返回最大连续楼层数 3 。
```

**示例 2：**

```
输入：bottom = 6, top = 8, special = [7,6,8]
输出：0
解释：每层楼都被规划为特殊楼层，所以返回 0 。
```

 

**提示**

- `1 <= special.length <= 10^5`
- `1 <= bottom <= special[i] <= top <= 10^9`
- `special` 中的所有值 **互不相同**

### 解题思路

将special数组排序后，遍历一遍找最大连续层数即可

### 代码

```c++
class Solution {
public:
    int maxConsecutive(int bottom, int top, vector<int>& special) {
        int n = special.size();
        sort(special.begin(), special.end());
        int res = special[0]-bottom;
        for(int i=1;i<n;i++){
            res = max(res, special[i]-special[i-1]-1);
        }
        res = max(res, top-special[n-1]);
        return res;
    }
};
```

## 【枚举】按位与结果大于零的最长组合

### 题目

对数组 `nums` 执行 **按位与** 相当于对数组 `nums` 中的所有整数执行 **按位与** 。

- 例如，对 `nums = [1, 5, 3]` 来说，按位与等于 `1 & 5 & 3 = 1` 。
- 同样，对 `nums = [7]` 而言，按位与等于 `7` 。

给你一个正整数数组 `candidates` 。计算 `candidates` 中的数字每种组合下 **按位与** 的结果。 `candidates` 中的每个数字在每种组合中只能使用 **一次** 。

返回按位与结果大于 `0` 的 **最长** 组合的长度*。*

 

**示例 1：**

```
输入：candidates = [16,17,71,62,12,24,14]
输出：4
解释：组合 [16,17,62,24] 的按位与结果是 16 & 17 & 62 & 24 = 16 > 0 。
组合长度是 4 。
可以证明不存在按位与结果大于 0 且长度大于 4 的组合。
注意，符合长度最大的组合可能不止一种。
例如，组合 [62,12,24,14] 的按位与结果是 62 & 12 & 24 & 14 = 8 > 0 。
```

**示例 2：**

```
输入：candidates = [8,8]
输出：2
解释：最长组合是 [8,8] ，按位与结果 8 & 8 = 8 > 0 。
组合长度是 2 ，所以返回 2 。
```

 

**提示：**

- `1 <= candidates.length <= 10^5`
- `1 <= candidates[i] <= 10^7`

### 解题思路

看完题后第一时间还没想好思路，但看了下一开始通过率高的吓人，就冷静下来想了一下

如果考虑每一种数字组合的话，复杂度会过高，不可能的。换个角度想

按位与结果最后要大于0，说明它们的与运算结果的二进制表示中至少有1位不能为0

由于数的范围是10^7，没有超过int，那么最多可以考虑32位，枚举每一位，使得该位不为0，为了数字组合长度最长，只要不使得该位按位与运算后变为0，那么可以尽量的将数字选进来。

### 代码

```c++
class Solution {
public:
    int largestCombination(vector<int>& candidates) {
        int n = candidates.size();
        int res=0;
        for(int i=0;i<32;i++){//枚举第i位不为0
            int cur=0;
            for(auto &x: candidates){
                if((x>>i)&1) cur++;//只要第i位不为0，就将其组合起来
            }
            res=max(res,cur);
        }
        return res;
    }
};
```

## 【动态开点线段树】统计区间中的整数数目

### 题目

给你区间的 **空** 集，请你设计并实现满足要求的数据结构：

- **新增**：添加一个区间到这个区间集合中。
- **统计**：计算出现在 **至少一个** 区间中的整数个数。

实现 `CountIntervals` 类：

- `CountIntervals()` 使用区间的空集初始化对象
- `void add(int left, int right)` 添加区间 `[left, right]` 到区间集合之中。
- `int count()` 返回出现在 **至少一个** 区间中的整数个数。

**注意**：区间 `[left, right]` 表示满足 `left <= x <= right` 的所有整数 `x` 。

 

**示例 1**：

```
输入
["CountIntervals", "add", "add", "count", "add", "count"]
[[], [2, 3], [7, 10], [], [5, 8], []]
输出
[null, null, null, 6, null, 8]

解释
CountIntervals countIntervals = new CountIntervals(); // 用一个区间空集初始化对象
countIntervals.add(2, 3);  // 将 [2, 3] 添加到区间集合中
countIntervals.add(7, 10); // 将 [7, 10] 添加到区间集合中
countIntervals.count();    // 返回 6
                           // 整数 2 和 3 出现在区间 [2, 3] 中
                           // 整数 7、8、9、10 出现在区间 [7, 10] 中
countIntervals.add(5, 8);  // 将 [5, 8] 添加到区间集合中
countIntervals.count();    // 返回 8
                           // 整数 2 和 3 出现在区间 [2, 3] 中
                           // 整数 5 和 6 出现在区间 [5, 8] 中
                           // 整数 7 和 8 出现在区间 [5, 8] 和区间 [7, 10] 中
                           // 整数 9 和 10 出现在区间 [7, 10] 中
```

 

**提示：**

- `1 <= left <= right <= 10^9`
- 最多调用 `add` 和 `count` 方法 **总计** `10^5` 次
- 调用 `count` 方法至少一次

### 动态开点线段树

读完题就鉴定为可以用线段树做。

由于值域范围达10\^9，但询问次数为10\^5，所以采用动态开点线段树

节点维护区间中整数数目即可。

本来代码很快就码好了，但是忽略了对tot，root的初始化，并且一开始将它们放在了类里面，造成了奇奇怪怪的错误。找了好久才发现，然后将它们移到类外，并进行显示初始化。（如果不进行显示初始化是不行的，力扣可能会测试多组用例，后面再初始化需要重置才行）。

```c++
const int INF = 1e9+2;
const int MAXM = 4e6+6;
int tot,root;
struct node{
        int sum;//区间中整数的数目
        int tag;
        int ls,rs;
}T[MAXM];
class CountIntervals {
public:
    
    int minv,maxv;
    void push_up(int &p){
       T[p].sum = T[T[p].ls].sum + T[T[p].rs].sum;
    }
    void push_down(int &p, int l, int r){
        if(!T[p].tag) return;
        if(!T[p].ls){
            T[p].ls=++tot;
            T[T[p].ls] = {0,0,0,0};
        }
        if(!T[p].rs){
            T[p].rs=++tot;
            T[T[p].rs] = {0,0,0,0};
        }
        int mid=(l+r)>>1;
        T[T[p].ls].tag+=T[p].tag;
        T[T[p].rs].tag+=T[p].tag;
        T[T[p].ls].sum=(mid-l+1);
        T[T[p].rs].sum=r-mid;
        T[p].tag=0;
    }
    void update(int &p, int l, int r, int nl, int nr, int k){
        if(!p){
            p=++tot;
            T[p]={0,0,0,0};
        }
        if(nl<=l&&nr>=r){
            T[p].sum = r-l+1;
            T[p].tag = 1;
            return;
        }
        int mid = (l+r)>>1;
        push_down(p,l,r);
        if(nl<=mid) update(T[p].ls,l,mid,nl,nr,k);
        if(nr>mid) update(T[p].rs,mid+1,r,nl,nr,k);
        push_up(p);
    }
    int query(int p, int l, int r, int nl, int nr){
        if(nl<=l&&nr>=r) return T[p].sum;
        push_down(p,l,r);
        int res=0,mid=(l+r)>>1;
        if(nl<=mid) res+=query(T[p].ls,l,mid,nl,nr);
        if(nr>mid) res+=query(T[p].rs,mid+1,r,nl,nr);
        return res;
    }
    CountIntervals() {
        minv=0,maxv=INF;
        tot=0,root=0;
    }
    
    void add(int left, int right) {
        update(root,1,INF,left,right,1);
        minv=min(minv,left);
        maxv=max(maxv,right);
    }
    
    int count() {
        return query(root,1,INF,minv,maxv);
    }
};

/**
 * Your CountIntervals object will be instantiated and called as such:
 * CountIntervals* obj = new CountIntervals();
 * obj->add(left,right);
 * int param_2 = obj->count();
 */
```

### 模拟 求区间并集

> 求区间并集模板题。用一个 set 有序地维护所有不相交的区间，当加入区间 [left, right] 时，通过 lower_bound 快速找到第一个右端点大于等于 left - 1 的区间，然后不断用接下来的区间和 [left, right] 合并，直到当前区间的左端点大于 right + 1。由于每个区间只会加入以及离开 set 一次，复杂度O(nlogn)。
>

```c++
class CountIntervals {
    typedef pair<int, int> pii;//first：右端点   second：左端点

    int ans = 0;
    set<pii> st;

public:
    CountIntervals() {
    }
    
    void add(int left, int right) {
        int L = left, R = right;
        auto it = st.lower_bound(pii(left - 1, -2e9));//在旧区间中找到第一个右端点大于等于新加入区间left-1的区间索引
        while (it != st.end()) {//找到了，开始合并
            if (it->second > right + 1) break;
            L = min(L, it->second);
            R = max(R, it->first);
            ans -= it->first - it->second + 1;
            st.erase(it++);
        }
        ans += R - L + 1;
        st.insert(pii(R, L));
    }
    
    int count() {
        return ans;
    }
};

/**
 * Your CountIntervals object will be instantiated and called as such:
 * CountIntervals* obj = new CountIntervals();
 * obj->add(left,right);
 * int param_2 = obj->count();
 */


// 作者：TsReaper
// 链接：https://leetcode.cn/circle/discuss/sKLBSg/
// 来源：力扣（LeetCode）
// 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

