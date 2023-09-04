---
title:      LeetCode第307场周赛总结
description:   466/7064
date:       2022-08-27
image: post-bg-20220827.png
categories:
    - leetcode contest
tags:
    - 第K大和
---

![image-20220827125439087](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220827125439087.png)

## 【模拟】赢得比赛需要的最少训练时长

### 题目

你正在参加一场比赛，给你两个 **正** 整数 `initialEnergy` 和 `initialExperience` 分别表示你的初始精力和初始经验。

另给你两个下标从 **0** 开始的整数数组 `energy` 和 `experience`，长度均为 `n` 。

你将会 **依次** 对上 `n` 个对手。第 `i` 个对手的精力和经验分别用 `energy[i]` 和 `experience[i]` 表示。当你对上对手时，需要在经验和精力上都 **严格** 超过对手才能击败他们，然后在可能的情况下继续对上下一个对手。

击败第 `i` 个对手会使你的经验 **增加** `experience[i]`，但会将你的精力 **减少** `energy[i]` 。

在开始比赛前，你可以训练几个小时。每训练一个小时，你可以选择将增加经验增加 1 **或者** 将精力增加 1 。

返回击败全部 `n` 个对手需要训练的 **最少** 小时数目。

 

**示例 1：**

```
输入：initialEnergy = 5, initialExperience = 3, energy = [1,4,3,2], experience = [2,6,3,1]
输出：8
解释：在 6 小时训练后，你可以将精力提高到 11 ，并且再训练 2 个小时将经验提高到 5 。
按以下顺序与对手比赛：
- 你的精力与经验都超过第 0 个对手，所以获胜。
  精力变为：11 - 1 = 10 ，经验变为：5 + 2 = 7 。
- 你的精力与经验都超过第 1 个对手，所以获胜。
  精力变为：10 - 4 = 6 ，经验变为：7 + 6 = 13 。
- 你的精力与经验都超过第 2 个对手，所以获胜。
  精力变为：6 - 3 = 3 ，经验变为：13 + 3 = 16 。
- 你的精力与经验都超过第 3 个对手，所以获胜。
  精力变为：3 - 2 = 1 ，经验变为：16 + 1 = 17 。
在比赛前进行了 8 小时训练，所以返回 8 。
可以证明不存在更小的答案。
```

**示例 2：**

```
输入：initialEnergy = 2, initialExperience = 4, energy = [1], experience = [3]
输出：0
解释：你不需要额外的精力和经验就可以赢得比赛，所以返回 0 。
```

 

**提示：**

- `n == energy.length == experience.length`
- `1 <= n <= 100`
- `1 <= initialEnergy, initialExperience, energy[i], experience[i] <= 100`

### 解题思路

对于精力来说，不可恢复，所以可预先求出来，看是否需要补充

对于经验来说，可以在战斗过程中增加，不够再补

WA了两发，细节没处理好

### 代码

```c++
class Solution {
public:
    int minNumberOfHours(int initialEnergy, int initialExperience, vector<int>& energy, vector<int>& experience) {
        int n = energy.size();
        int sum = accumulate(energy.begin(),energy.end(),0);
        int res = 0;
        if(initialEnergy <= sum) res+= sum+1-initialEnergy;
        // cout<<res<<endl;
        int s = 0,x = initialExperience;//需要补的经验 当前经验
        for(int i=0;i<n;i++){
            if(x > experience[i]){
                x += experience[i];
            }else{
                s += experience[i]+1-x;
                x += experience[i]+1-x + experience[i];
            }
        }
        // cout<<s<<endl;
        return res+s;
    }
};
```

## 【贪心】最大回文数字

### 题目

给你一个仅由数字（`0 - 9`）组成的字符串 `num` 。

请你找出能够使用 `num` 中数字形成的 **最大回文** 整数，并以字符串形式返回。该整数不含 **前导零** 。

**注意：**

- 你 **无需** 使用 `num` 中的所有数字，但你必须使用 **至少** 一个数字。
- 数字可以重新排序。

 

**示例 1：**

```
输入：num = "444947137"
输出："7449447"
解释：
从 "444947137" 中选用数字 "4449477"，可以形成回文整数 "7449447" 。
可以证明 "7449447" 是能够形成的最大回文整数。
```

**示例 2：**

```
输入：num = "00009"
输出："9"
解释：
可以证明 "9" 能够形成的最大回文整数。
注意返回的整数不应含前导零。
```

 

**提示：**

- `1 <= num.length <= 10^5`
- `num` 由数字（`0 - 9`）组成

### 解题思路

先获取每个数字的个数，然后从大到小构造



### 代码		

```c++
class Solution {
public:
    string largestPalindromic(string num) {
        int n = num.size();
        map<int,int> tab;
        for(auto &x: num){
            tab[x-'0']++;
        }
        string res;
        for(int i=9;i>=0;i--){
            while(tab[i]>=2){
                if(res.empty() && i==0) break;
                res+='0'+i;
                tab[i]-=2;
            }
        }
        string tmp = res;
        reverse(tmp.begin(),tmp.end());
        for(int i=9;i>=0;i--){
            if(tab[i]>=1){
                res+=i+'0';
                break;
            }
        }
        
        res += tmp;
        return res;
    }
};
```

## 【BFS】感染二叉树需要的总时间

### 题目

给你一棵二叉树的根节点 `root` ，二叉树中节点的值 **互不相同** 。另给你一个整数 `start` 。在第 `0` 分钟，**感染** 将会从值为 `start` 的节点开始爆发。

每分钟，如果节点满足以下全部条件，就会被感染：

- 节点此前还没有感染。
- 节点与一个已感染节点相邻。

返回感染整棵树需要的分钟数*。*

 

**示例 1：**

<img src="https://assets.leetcode.com/uploads/2022/06/25/image-20220625231744-1.png" alt="img" style="zoom:50%;" />

```
输入：root = [1,5,3,null,4,10,6,9,2], start = 3
输出：4
解释：节点按以下过程被感染：
- 第 0 分钟：节点 3
- 第 1 分钟：节点 1、10、6
- 第 2 分钟：节点5
- 第 3 分钟：节点 4
- 第 4 分钟：节点 9 和 2
感染整棵树需要 4 分钟，所以返回 4 。
```

**示例 2：**

<img src="https://assets.leetcode.com/uploads/2022/06/25/image-20220625231812-2.png" alt="img" style="zoom:50%;" />

```
输入：root = [1], start = 1
输出：0
解释：第 0 分钟，树中唯一一个节点处于感染状态，返回 0 。
```

 

**提示：**

- 树中节点的数目在范围 `[1, 10^5]` 内
- `1 <= Node.val <= 10^5`
- 每个节点的值 **互不相同**
- 树中必定存在值为 `start` 的节点

### 解题思路

相当于求图中到一点的最远距离，先构造图，再常规BFS即可

### 代码

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
const int MAXN = 1e5+5;

class Solution {
public:
    vector<int> G[MAXN];
    int amountOfTime(TreeNode* root, int start) {
        vector<int> d(MAXN,-1);
        queue<pair<int,int>> que;
        function<void(TreeNode*)> f =[&](TreeNode* t){
            if(t==nullptr) return;
            if(t->val==start){
                d[t->val]=0;
                que.emplace(0, t->val);
            }
            if(t->left){
                G[t->val].push_back(t->left->val);
                G[t->left->val].push_back(t->val);
                f(t->left);
            }
            if(t->right){
                G[t->val].push_back(t->right->val);
                G[t->right->val].push_back(t->val);
                f(t->right);
            }
        };
        f(root);
        
        while(que.size()){
            int sz = que.size();
            for(int i=0;i<sz;i++){
                auto [dis, u] = que.front();
                que.pop();
                for(auto &v: G[u]){
                    if(d[v]>-1) continue;
                    d[v] = dis+1;
                    que.emplace(d[v], v);
                }
            }
        }
        
        int res=0;
        for(int i=0;i<MAXN;i++){
            res = max(res, d[i]);
        }
        return res;
        
    }
};
```



## 【思维+最小堆】找出数组的第 K 大和

### 题目

给你一个整数数组 `nums` 和一个 **正** 整数 `k` 。你可以选择数组的任一 **子序列** 并且对其全部元素求和。

数组的 **第 k 大和** 定义为：可以获得的第 `k` 个 **最大** 子序列和（子序列和允许出现重复）

返回数组的 **第 k 大和** 。

子序列是一个可以由其他数组删除某些或不删除元素排生而来的数组，且派生过程不改变剩余元素的顺序。

**注意：** 空子序列的和视作 `0` 。

 

**示例 1：**

```
输入：nums = [2,4,-2], k = 5
输出：2
解释：所有可能获得的子序列和列出如下，按递减顺序排列：
- 6、4、4、2、2、0、0、-2
数组的第 5 大和是 2 。
```

**示例 2：**

```
输入：nums = [1,-2,3,4,-10,12], k = 16
输出：10
解释：数组的第 16 大和是 10 。
```

 

**提示：**

- `n == nums.length`
- `1 <= n <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `1 <= k <= min(2000, 2^n)`

### 解题思路

数组的第一大和显然是所有非负数的和sum。

注意到k的数据范围，最大不超过2000

那么求数组的第K大和，也就是求第K小和，然后sum减去即可

![image-20220827135539793](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220827135539793.png)

### 代码

```c++
#define ll long long
class Solution {
public:
    long long kSum(vector<int>& nums, int k) {
        int n = nums.size();
        ll sum = 0;
        for(auto &x: nums){
            if(x>=0) sum+=x;
            else x=-x;
        }
        if(k==1) return sum;//第一大，所有非负数的和
        sort(nums.begin(),nums.end());

        priority_queue< pair<ll,int>,vector<pair<ll,int>>,greater<pair<ll,int>>> pq;

        k-=2;//空子序列的和最大，什么都不用减。
        //求第k大和，即最大和减去前k-1小和
        pq.emplace(nums[0],0);
        while(k--){
            auto [su, id] = pq.top();
            pq.pop();
            if(id+1>=n) continue;
            pq.emplace(su-nums[id]+nums[id+1],id+1);
            pq.emplace(su+nums[id+1],id+1);
        }
        cout<<sum<<' '<<pq.top().first<<endl;
        return sum-pq.top().first;
    }
};
```

