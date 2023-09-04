---
title:      二叉树（二）
description:   二叉树的建立
date:       2022-04-13 00:00:00+0000
image:      post-bg-20220413.png
categories:
    - data structure and algorithm
tags:
    - 二叉树
---
## 前言

再啰嗦一下，明白递归函数的作用并相信它能完成。

由前序遍历和中序遍历能确定一棵二叉树

由后序遍历和中序遍历能确定一棵二叉树

但由前序遍历和后序遍历不能确定一棵二叉树

## 从中序与后序遍历序列构造二叉树

### 题目

给定两个整数数组 inorder 和 postorder ，其中 inorder 是二叉树的中序遍历， postorder 是同一棵树的后序遍历，请你构造并返回这颗 二叉树 。

 

示例 1:

![img](https://assets.leetcode.com/uploads/2021/02/19/tree.jpg)


输入：inorder = [9,3,15,20,7], postorder = [9,15,7,20,3]
输出：[3,9,20,null,null,15,7]
示例 2:

输入：inorder = [-1], postorder = [-1]
输出：[-1]


提示:

1 <= inorder.length <= 3000
postorder.length == inorder.length
-3000 <= inorder[i], postorder[i] <= 3000
inorder 和 postorder 都由 不同 的值组成
postorder 中每一个值都在 inorder 中
inorder 保证是树的中序遍历
postorder 保证是树的后序遍历

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 递归V1.0

根据中序与后序遍历序列构造二叉树，首先根据后序遍历序列可以确定根节点，根据这个根节点可以在中序遍历序列中分成左右子树，反过来可以在后序遍历序列中分割左右子树，...，递归的做这件事

1）如果两个序列都为空，说明是空节点；（如果两个序列大小都为1，将该节点作为根节点返回即可）

2.1）如果两个序列不为空，取后序序列最后一个元素rootval作为根节点

2.2）找到rootval在中序序列中的索引，作为切割点

2.3）将中序序列切割成左中序序列（左子树）和右中序序列（右子树）

2.4）得到上述左右子树的信息后，将后序序列切割成左后序序列（左子树）和右后序序列（右子树）

3）递归处理左右区间

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
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {

        // if(inorder.size()==1 && postorder.size()==1) return new TreeNode(inorder[0]);
        if(inorder.size()==0 && postorder.size()==0) return nullptr;

        int rootval = postorder.back();
        TreeNode* root = new TreeNode(rootval);
        postorder.pop_back();//删除最后一个根节点

        auto idx = find(inorder.begin(),inorder.end(),rootval);//找到分割点

        vector<int> leftInorder(inorder.begin(),idx);
        vector<int> rightInorder(idx+1,inorder.end());

        vector<int> leftPostorder(postorder.begin(),postorder.begin()+leftInorder.size());
        vector<int> rightPostorder(postorder.begin()+leftInorder.size(),postorder.end());

        root->left = buildTree(leftInorder,leftPostorder);
        root->right = buildTree(rightInorder,rightPostorder);

        return root;
    }
};
```

### 递归V2.0

上述版本每层递归都定义了新的vector，耗时耗力，可以考虑**增添索引作为参数**，就不用每次都创建新的vector

下面区间采取了左闭右闭，其他方式也一样

这个遍历找分割点，可以先用一个哈希表存储[值，索引]，就可以O(1)查询了

```c++
class Solution {
public:
    TreeNode* build(vector<int>& inorder,int lin,int rin,vector<int>& postorder,int lpo,int rpo){//[]
        if(lin>rin || lpo>rpo) return nullptr;
        TreeNode* root;
        int rootval = postorder[rpo];
        root = new TreeNode(rootval);
        rpo--;

        int idx = -1;
        for(int i=lin;i<=rin;i++){
            if(inorder[i]==rootval){
                idx = i;
                break;
            }
        }
        root->left=build(inorder,lin,idx-1,postorder,lpo,lpo+idx-1-lin);

        root->right=build(inorder,idx+1,rin,postorder,lpo+idx-lin,rpo);

        return root;


    }
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        return build(inorder,0,inorder.size()-1,postorder,0,postorder.size()-1);
    }
};
```

## 从前序与中序遍历序列构造二叉树

### 题目

给定两个整数数组 preorder 和 inorder ，其中 preorder 是二叉树的先序遍历， inorder 是同一棵树的中序遍历，请构造二叉树并返回其根节点。

 

示例 1:

![img](https://assets.leetcode.com/uploads/2021/02/19/tree.jpg)

输入: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
输出: [3,9,20,null,null,15,7]
示例 2:

输入: preorder = [-1], inorder = [-1]
输出: [-1]


提示:

1 <= preorder.length <= 3000
inorder.length == preorder.length
-3000 <= preorder[i], inorder[i] <= 3000
preorder 和 inorder 均 无重复 元素
inorder 均出现在 preorder
preorder 保证 为二叉树的前序遍历序列
inorder 保证 为二叉树的中序遍历序列

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 递归题解

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
    unordered_map<int,int> table;
    TreeNode* build(vector<int>& preorder,int Lpre,int Rpre,int Lin,int Rin){
        if(Lpre>Rpre || Lin>Rin) return nullptr;
        int rootval = preorder[Lpre];
        TreeNode* root = new TreeNode(rootval);

        int idx = table[rootval];
    
        root->left = build(preorder,Lpre+1,Lpre+idx-Lin,Lin, idx-1);
        root->right = build(preorder,Lpre+idx-Lin+1,Rpre,idx+1,Rin);
        return root;
    }
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int count=0;
        for(auto x:inorder){
            table[x] = count++;
        }
        return build(preorder,0,count-1,0,count-1);
    }
};
```

## 合并二叉树

### 题目

给你两棵二叉树： root1 和 root2 。

想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，不为 null 的节点将直接作为新二叉树的节点。

返回合并后的二叉树。

注意: 合并过程必须从两个树的根节点开始。

 

示例 1：


输入：root1 = [1,3,2,5], root2 = [2,1,3,null,4,null,7]
输出：[3,4,5,5,4,null,7]
示例 2：

输入：root1 = [1], root2 = [1,2]
输出：[2,2]


提示：

两棵树中的节点数目在范围 [0, 2000] 内
-10^4 <= Node.val <= 10^4

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/merge-two-binary-trees
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 递归题解

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
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if(!root1) return root2;
        if(!root2) return root1;
        TreeNode* root = new TreeNode(root1->val+root2->val);
        root->left = mergeTrees(root1->left,root2->left);
        root->right = mergeTrees(root1->right,root2->right);
        return root;
    }
};
```

