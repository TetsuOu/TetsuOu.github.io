---
title:      LeetCode第321场周赛总结
description:   ---/5115
date:       2022-12-13
image: post-bg-20221213.png
categories:
    - leetcode contest
tags:
    - 单调栈
---

## 【枚举】找出中枢整数

### 题目

给你一个正整数 `n` ，找出满足下述条件的 **中枢整数** `x` ：

- `1` 和 `x` 之间的所有元素之和等于 `x` 和 `n` 之间所有元素之和。

返回中枢整数 `x` 。如果不存在中枢整数，则返回 `-1` 。题目保证对于给定的输入，至多存在一个中枢整数。

 

**示例 1：**

```
输入：n = 8
输出：6
解释：6 是中枢整数，因为 1 + 2 + 3 + 4 + 5 + 6 = 6 + 7 + 8 = 21 。
```

**示例 2：**

```
输入：n = 1
输出：1
解释：1 是中枢整数，因为 1 = 1 。
```

**示例 3：**

```
输入：n = 4
输出：-1
解释：可以证明不存在满足题目要求的整数。
```

 

**提示：**

- `1 <= n <= 1000`

### 解题思路

枚举即可

### 代码

```c++
class Solution {
public:
    int pivotInteger(int n) {
        vector<int> sum(n+1,0);
        for(int i=1;i<=n;i++) sum[i] = sum[i-1] + i;
        for(int i=1;i<=n;i++){
            if(sum[i]== sum[n]-sum[i-1]){
                return i;
            }
        }
        return -1;
    }
};
```

## 【贪心】追加字符以获得子序列

### 题目

给你两个仅由小写英文字母组成的字符串 `s` 和 `t` 。

现在需要通过向 `s` 末尾追加字符的方式使 `t` 变成 `s` 的一个 **子序列** ，返回需要追加的最少字符数。

子序列是一个可以由其他字符串删除部分（或不删除）字符但不改变剩下字符顺序得到的字符串。

 

**示例 1：**

```
输入：s = "coaching", t = "coding"
输出：4
解释：向 s 末尾追加字符串 "ding" ，s = "coachingding" 。
现在，t 是 s ("coachingding") 的一个子序列。
可以证明向 s 末尾追加任何 3 个字符都无法使 t 成为 s 的一个子序列。
```

**示例 2：**

```
输入：s = "abcde", t = "a"
输出：0
解释：t 已经是 s ("abcde") 的一个子序列。
```

**示例 3：**

```
输入：s = "z", t = "abcde"
输出：5
解释：向 s 末尾追加字符串 "abcde" ，s = "zabcde" 。
现在，t 是 s ("zabcde") 的一个子序列。 
可以证明向 s 末尾追加任何 4 个字符都无法使 t 成为 s 的一个子序列。
```

 

**提示：**

- `1 <= s.length, t.length <= 10^5`
- `s` 和 `t` 仅由小写英文字母组成

### 解题思路

只需要使用s对t进行贪心匹配

### 代码

```c++
class Solution {
public:
    int appendCharacters(string s, string t) {
        int j=0;
        for(int i=0;i<s.size();i++){
            if(j<t.size() && s[i] == t[j]) j++;
        }
        return t.size()-j;
    }
};
```

## 【单调栈/递归】从链表中移除节点

### 题目

给你一个链表的头节点 `head` 。

对于列表中的每个节点 `node` ，如果其右侧存在一个具有 **严格更大** 值的节点，则移除 `node` 。

返回修改后链表的头节点 `head` 。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/10/02/drawio.png)

```
输入：head = [5,2,13,3,8]
输出：[13,8]
解释：需要移除的节点是 5 ，2 和 3 。
- 节点 13 在节点 5 右侧。
- 节点 13 在节点 2 右侧。
- 节点 8 在节点 3 右侧。
```

**示例 2：**

```
输入：head = [1,1,1,1]
输出：[1,1,1,1]
解释：每个节点的值都是 1 ，所以没有需要移除的节点。
```

 

**提示：**

- 给定列表中的节点数目在范围 `[1, 10^5]` 内
- `1 <= Node.val <= 105`

### 解题思路

可以将其转换成数组，利用单调栈求解是否有右侧更大值，再变回链表

或者加个哑结点，直接在链表上用单调栈

### 代码

#### 递归

```c++
class Solution {
public:
    ListNode* remove(ListNode* node) {
        if(!node->next)
            return node;

        ListNode* tmp = remove(node->next);
        
        if (tmp->val > node->val) {
            return tmp;
        }
        node->next = tmp;
        return node;
    }

    ListNode* removeNodes(ListNode* head) {
        return remove(head);
    }
};
```

#### 单调栈

```c++
class Solution {
public:
    ListNode* removeNodes(ListNode* head) {
        ListNode* ans = new ListNode(1e6);
        stack<ListNode*> st;
        st.push(ans);
        while(head) {
            while(head->val > st.top()->val) st.pop();
            st.top()->next = head;
            st.push(head);
            head = head->next;
        }
        return ans->next;        
    }
};
```



## 【前缀和+哈希表】统计中位数为 K 的子数组

### 题目

给你一个长度为 `n` 的数组 `nums` ，该数组由从 `1` 到 `n` 的 **不同** 整数组成。另给你一个正整数 `k` 。

统计并返回 `num` 中的 **中位数** 等于 `k` 的非空子数组的数目。

**注意：**

- 数组的中位数是按递增顺序排列后位于中间的那个元素，如果数组长度为偶数，则中位数是位于中间靠左的那个元素。

   - 例如，`[2,3,1,4]` 的中位数是 `2` ，`[8,4,3,5,1]` 的中位数是 `4` 。

- 子数组是数组中的一个连续部分。



**示例 1：**

```
输入：nums = [3,2,1,4,5], k = 4
输出：3
解释：中位数等于 4 的子数组有：[4]、[4,5] 和 [1,4,5] 。
```

**示例 2：**

```
输入：nums = [2,3,1], k = 3
输出：1
解释：[3] 是唯一一个中位数等于 3 的子数组。
```

 

**提示：**

- `n == nums.length`
- `1 <= n <= 10^5`
- `1 <= nums[i], k <= n`
- `nums` 中的整数互不相同

### 解题思路

注意到k只会出现一次

将k视作0，将小于k的数视作-1，将大于k的数视作+1

那么中位数为k则子数组和为0，可能是奇数长度和偶数长度。

子数组求和利用前缀和相减



### 代码

```c++
class Solution {
public:
    int countSubarrays(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> a;
        a.push_back(-1);
        for(auto &x: nums){
            if(x==k) a.push_back(0);
            else if(x<k) a.push_back(-1);
            else a.push_back(1);
        }
       

        unordered_map<int,int> tab1,tab2;//存奇数长度和偶数长度的前缀和
        tab2[0] = 1;

        int u = 0, res = 0;
        for(int i=1;i<=n;i++){
            u += a[i];
            int tar_one = u - 0; //奇数长度要为0
            int tar_two = u - 1; //偶数长度要为1
            if(i&1){
                res += tab2[tar_one];
                res += tab1[tar_two];
            }else{
                res += tab1[tar_one];
                res += tab2[tar_two];
            }
            if(i&1){
                tab1[u] ++;
            }else{
                tab2[u] ++;
            }
        }
        return res;
    }
};
```

