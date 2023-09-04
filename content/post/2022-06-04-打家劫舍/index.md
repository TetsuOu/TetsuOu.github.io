---
title:      打家劫舍
description:   动态规划
date:       2022-06-04 00:00:00+0000
image: post-bg-20220604.png
categories:
    - data structure and algorithm
tags:
    - 动态规划
    - 树型DP
---



打家劫舍I、II是经典的序列型DP

打家劫舍III是树型的，但主要思想是一样的。

## 打家劫舍

### 题目

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

 

示例 1：

输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
示例 2：

输入：[2,7,9,3,1]
输出：12
解释：偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。


提示：

1 <= nums.length <= 100

0 <= nums[i] <= 400

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/house-robber
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

序列DP。

每个房屋有偷窃和不偷窃两个状态，如果当前房屋偷窃，那么前一个房屋必定不能偷窃；

如果当前房屋不偷窃，那么前一个房屋可以选择偷窃，也可以选择不偷窃。

### 代码

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n==1) return nums[0];
        auto dp = vector(n,vector<int>(2,0));
        //dp[i][j] 前i个,j=1偷第i个，j=0不偷第i个，的最高金额
        dp[0][0]=0,dp[0][1]=nums[0];
        dp[1][0]=dp[0][1],dp[1][1]=nums[1];
        for(int i=2;i<n;i++){
            dp[i][0]=dp[i-1][1];
            dp[i][1]=max(dp[i-2][0],dp[i-2][1])+nums[i];
        }
        return max(dp[n-1][0],dp[n-1][1]);
    }
};
```

也可以只用一个数组

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n==1) return nums[0];
        vector<int> dp(n,0);
        //dp[i] 前i个房屋能偷窃的最高金额
        dp[0] = nums[0];
        dp[1] = max(nums[0],nums[1]);
        for(int i=2;i<n;i++){
            dp[i] = max(dp[i-1],dp[i-2]+nums[i]);//第i个要么偷，要么不偷
        }
        return dp[n-1];
    }
};
```

## 打家劫舍II

### 题目

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，今晚能够偷窃到的最高金额。

 

示例 1：

输入：nums = [2,3,2]
输出：3
解释：你不能先偷窃 1 号房屋（金额 = 2），然后偷窃 3 号房屋（金额 = 2）, 因为他们是相邻的。
示例 2：

输入：nums = [1,2,3,1]
输出：4
解释：你可以先偷窃 1 号房屋（金额 = 1），然后偷窃 3 号房屋（金额 = 3）。
     偷窃到的最高金额 = 1 + 3 = 4 。
示例 3：

输入：nums = [1,2,3]
输出：3


提示：

1 <= nums.length <= 100

0 <= nums[i] <= 1000

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/house-robber-ii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

和前一题的区别是变成了环形。

受影响的是，选第0个时，最后一个也不能选。

分情况讨论即可。

### 代码

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if(n==1) return nums[0];
        vector<int> dp(n,0);
        int res=0;
        //选第0个时, dp[i]前i个的最大偷窃金额
        dp[0]=nums[0];
        dp[1]=max(nums[1],dp[0]);
        for(int i=2;i<n;i++){
            dp[i]=max(dp[i-1],dp[i-2]+nums[i]);
        }
        res = dp[n-2];//选第0个，则第n-1个不能选
        //下面计算不选第0个时的情况
        dp[0]=0;
        dp[1]=nums[1];
        for(int i=2;i<n;i++){
            dp[i]=max(dp[i-1],dp[i-2]+nums[i]);
        }
        res = max(res,dp[n-1]);
        return res;
    }
};
```

## 打家劫舍III

### 题目

小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为 root 。

除了 root 之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果 两个直接相连的房子在同一天晚上被打劫 ，房屋将自动报警。

给定二叉树的 root 。返回 在不触动警报的情况下 ，小偷能够盗取的最高金额 。

 

示例 1:



输入: root = [3,2,3,null,3,null,1]
输出: 7 
解释: 小偷一晚能够盗取的最高金额 3 + 3 + 1 = 7
示例 2:



输入: root = [3,4,5,1,3,null,1]
输出: 9
解释: 小偷一晚能够盗取的最高金额 4 + 5 = 9


提示：

树的节点数在 [1, 10^4] 范围内

0 <= Node.val <= 10^4

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/house-robber-iii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

变成了树型，主要思路还是一样的，偷还是不偷两个状态。

如果偷了当前节点，那么其左右两个子节点必定不能偷

如果没有偷当前节点，那么其左右两个子节点都是可以偷或者不偷的，取较大值

### 代码

官方式的记忆化搜索:

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
class Solution {
public:
    unordered_map<TreeNode*,int> f,g;
    //f[root] 偷当前节点  最大值
    //g[root] 不偷当前节点 最大值
    void dfs(TreeNode* root){
        if(root==nullptr) return;
        dfs(root->left);
        dfs(root->right);
        int fval = root->val+g[root->left]+g[root->right];
        int gval = max(f[root->left],g[root->left])+max(f[root->right],g[root->right]);
        f[root]=fval;
        g[root]=gval;
        return;
    }
    int rob(TreeNode* root) {
        f[nullptr]=0,g[nullptr]=0;
        dfs(root);
        return max(f[root],g[root]);
    }
};
```

但我觉得这样更像记忆化搜索：

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
 #define P pair<TreeNode*,int>
class Solution {
public:
    map<P,int> dp;
    //dp[root][0] 当前根节点为root，且不偷当前节点 的最大值
    //dp[root][1] 当前根节点为root，且偷当前节点 的最大值 
    int dfs(P node){
        if(node.first==nullptr) return 0;
        if(dp[node]) return dp[node];
        TreeNode* root = node.first;
        //偷当前节点
        int res=root->val+dfs(make_pair(root->left,0))+dfs(make_pair(root->right,0));
        dp[make_pair(root,1)]=res;

        //不偷当前节点
        int res2 = max(dfs(make_pair(root->left,0)),dfs(make_pair(root->left,1)))     +max(dfs(make_pair(root->right,0)),dfs(make_pair(root->right,1)));
        dp[make_pair(root,0)] = res2;
        if(node.second==0) return res2;
        else return res;
    }
    int rob(TreeNode* root) {
        return max(dfs(make_pair(root,0)),dfs(make_pair(root,1)));
    }
};
```

树型DP：

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
 #define P pair<int,int> 
 //first: 不偷  second： 偷
class Solution {
public:
    P dfs(TreeNode* root){
        if(root==nullptr) return P(0,0);
        P left = dfs(root->left);
        P right = dfs(root->right);
        int val = max(left.first,left.second)+max(right.first,right.second);//不偷当前节点
        int val2 = root->val+left.first+right.first;//偷当前节点
        return P(val,val2);
    }
    int rob(TreeNode* root) {
        P res = dfs(root);
        return max(res.first,res.second);
    }
};
```

