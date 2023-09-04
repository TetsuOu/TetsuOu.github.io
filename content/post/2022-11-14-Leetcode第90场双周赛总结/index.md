---
title:      LeetCode第90场双周赛总结
description:   ---/3624
date:       2022-11-14
image: post-bg-20221114.png
categories:
    - leetcode contest
tags:
    - 单调栈
---

没打

[https://leetcode.cn/contest/biweekly-contest-90/](https://leetcode.cn/contest/biweekly-contest-90/)

## 【模拟】差值数组不同的字符串

### 题目

给你一个字符串数组 `words` ，每一个字符串长度都相同，令所有字符串的长度都为 `n` 。

每个字符串 `words[i]` 可以被转化为一个长度为 `n - 1` 的 **差值整数数组** `difference[i]` ，其中对于 `0 <= j <= n - 2` 有 `difference[i][j] = words[i][j+1] - words[i][j]` 。注意两个字母的差值定义为它们在字母表中 **位置** 之差，也就是说 `'a'` 的位置是 `0` ，`'b'` 的位置是 `1` ，`'z'` 的位置是 `25` 。

- 比方说，字符串 `"acb"` 的差值整数数组是 `[2 - 0, 1 - 2] = [2, -1]` 。

`words` 中所有字符串 **除了一个字符串以外** ，其他字符串的差值整数数组都相同。你需要找到那个不同的字符串。

请你返回 `words`中 **差值整数数组** 不同的字符串。

 

**示例 1：**

```
输入：words = ["adc","wzy","abc"]
输出："abc"
解释：
- "adc" 的差值整数数组是 [3 - 0, 2 - 3] = [3, -1] 。
- "wzy" 的差值整数数组是 [25 - 22, 24 - 25]= [3, -1] 。
- "abc" 的差值整数数组是 [1 - 0, 2 - 1] = [1, 1] 。
不同的数组是 [1, 1]，所以返回对应的字符串，"abc"。
```

**示例 2：**

```
输入：words = ["aaa","bob","ccc","ddd"]
输出："bob"
解释：除了 "bob" 的差值整数数组是 [13, -13] 以外，其他字符串的差值整数数组都是 [0, 0] 。
```

 

**提示：**

- `3 <= words.length <= 100`
- `n == words[i].length`
- `2 <= n <= 20`
- `words[i]` 只含有小写英文字母。

### 解题思路

按题意模拟即可

### 代码

```c++
class Solution {
public:
    string oddString(vector<string>& words) {
        int n = words.size();
        string res,res2;
        map<string,int> tab;
        for(auto &word: words){
            char minch = *min_element(word.begin(),word.end());
            string tmp = word;
            for(auto &ch: tmp){
                ch = ch-minch+'a';
            }
            // cout<<tmp<<endl;
            tab[tmp]++;
        }
        for(auto &word: words){
            char minch = *min_element(word.begin(),word.end());
            string tmp = word;
            for(auto &ch: tmp){
                ch = ch-minch+'a';
            }
            if(tab[tmp]==1){
                res = word;
                break;
            }
        }
        return res;
    }
    
};
```

## 【枚举】距离字典两次编辑以内的单词

### 题目

给你两个字符串数组 `queries` 和 `dictionary` 。数组中所有单词都只包含小写英文字母，且长度都相同。

一次 **编辑** 中，你可以从 `queries` 中选择一个单词，将任意一个字母修改成任何其他字母。从 `queries` 中找到所有满足以下条件的字符串：**不超过** 两次编辑内，字符串与 `dictionary` 中某个字符串相同。

请你返回 `queries` 中的单词列表，这些单词距离 `dictionary` 中的单词 **编辑次数** 不超过 **两次** 。单词返回的顺序需要与 `queries` 中原本顺序相同。

 

**示例 1：**

```
输入：queries = ["word","note","ants","wood"], dictionary = ["wood","joke","moat"]
输出：["word","note","wood"]
解释：
- 将 "word" 中的 'r' 换成 'o' ，得到 dictionary 中的单词 "wood" 。
- 将 "note" 中的 'n' 换成 'j' 且将 't' 换成 'k' ，得到 "joke" 。
- "ants" 需要超过 2 次编辑才能得到 dictionary 中的单词。
- "wood" 不需要修改（0 次编辑），就得到 dictionary 中相同的单词。
所以我们返回 ["word","note","wood"] 。
```

**示例 2：**

```
输入：queries = ["yes"], dictionary = ["not"]
输出：[]
解释：
"yes" 需要超过 2 次编辑才能得到 "not" 。
所以我们返回空数组。
```

 

**提示：**

- `1 <= queries.length, dictionary.length <= 100`
- `n == queries[i].length == dictionary[j].length`
- `1 <= n <= 100`
- 所有 `queries[i]` 和 `dictionary[j]` 都只包含小写英文字母。

### 解题思路

就是找不同字符的个数

### 代码

```c++
class Solution {
public:
    vector<string> twoEditWords(vector<string>& queries, vector<string>& dictionary) {
        vector<string> res;
        for(auto &query: queries){
            bool flag = false;
            for(auto &words: dictionary){
                if(query.size()!=words.size()) continue;
                int diff = 0;
                for(int i=0;i<query.size();i++){
                    if(query[i]!=words[i]) diff++;
                    if(diff>2) break;
                }
                if(diff<=2) {
                    flag = true;
                    break;
                }
            }
            if(flag) res.push_back(query);
        }
        return res;
    }
};
```

## 【枚举+数学】摧毁一系列目标

### 题目

给你一个下标从 **0** 开始的数组 `nums` ，它包含若干正整数，表示数轴上你需要摧毁的目标所在的位置。同时给你一个整数 `space` 。

你有一台机器可以摧毁目标。给机器 **输入** `nums[i]` ，这台机器会摧毁所有位置在 `nums[i] + c * space` 的目标，其中 `c` 是任意非负整数。你想摧毁 `nums` 中 **尽可能多** 的目标。

请你返回在摧毁数目最多的前提下，`nums[i]` 的 **最小值** 。

 

**示例 1：**

```
输入：nums = [3,7,8,1,1,5], space = 2
输出：1
解释：如果我们输入 nums[3] ，我们可以摧毁位于 1,3,5,7,9,... 这些位置的目标。
这种情况下， 我们总共可以摧毁 5 个目标（除了 nums[2]）。
没有办法摧毁多于 5 个目标，所以我们返回 nums[3] 。
```

**示例 2：**

```
输入：nums = [1,3,5,2,4,6], space = 2
输出：1
解释：输入 nums[0] 或者 nums[3] 都会摧毁 3 个目标。
没有办法摧毁多于 3 个目标。
由于 nums[0] 是最小的可以摧毁 3 个目标的整数，所以我们返回 1 。
```

**示例 3：**

```
输入：nums = [6,2,5], space = 100
输出：2
解释：无论我们输入哪个数字，都只能摧毁 1 个目标。输入的最小整数是 nums[1] 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`
- `1 <= space <= 10^9`

### 解题思路

将 `nums` 里的所有数按 `nums[i] % space` 分组。只要选择每一组里最小的数，就能摧毁整组。

和顺序无关，可以先对数组排序，第一次找到最多的即最小值

### 代码

```c++
class Solution {
public:
    int destroyTargets(vector<int>& nums, int space) {
        int n = nums.size();
        sort(nums.begin(),nums.end());
        unordered_map<int,int> tab;
        vector<int> a(nums.begin(),nums.end());
        for(auto &x: a) x = x%space;
        int maxcnt=0;
        for(int i=0;i<n;i++){
            tab[a[i]]++;
            if(tab[a[i]]>maxcnt){
                maxcnt = tab[a[i]];
            }
        }
        int res;
        for(int i=0;i<n;i++){
            if(maxcnt == tab[a[i]]){
                res = nums[i];
                break;
            }
        }
        return res;
    }
};
```

## 【单调栈+优先队列】下一个更大元素 IV

### 题目

给你一个下标从 **0** 开始的非负整数数组 `nums` 。对于 `nums` 中每一个整数，你必须找到对应元素的 **第二大** 整数。

如果 `nums[j]` 满足以下条件，那么我们称它为 `nums[i]` 的 **第二大** 整数：

- `j > i`
- `nums[j] > nums[i]`
- 恰好存在 **一个** `k` 满足 `i < k < j` 且 `nums[k] > nums[i]` 。

如果不存在 `nums[j]` ，那么第二大整数为 `-1` 。

- 比方说，数组 `[1, 2, 4, 3]` 中，`1` 的第二大整数是 `4` ，`2` 的第二大整数是 `3` ，`3` 和 `4` 的第二大整数是 `-1` 。

请你返回一个整数数组 `answer` ，其中 `answer[i]`是 `nums[i]` 的第二大整数。

 

**示例 1：**

```
输入：nums = [2,4,0,9,6]
输出：[9,6,6,-1,-1]
解释：
下标为 0 处：2 的右边，4 是大于 2 的第一个整数，9 是第二个大于 2 的整数。
下标为 1 处：4 的右边，9 是大于 4 的第一个整数，6 是第二个大于 4 的整数。
下标为 2 处：0 的右边，9 是大于 0 的第一个整数，6 是第二个大于 0 的整数。
下标为 3 处：右边不存在大于 9 的整数，所以第二大整数为 -1 。
下标为 4 处：右边不存在大于 6 的整数，所以第二大整数为 -1 。
所以我们返回 [9,6,6,-1,-1] 。
```

**示例 2：**

```
输入：nums = [3,3]
输出：[-1,-1]
解释：
由于每个数右边都没有更大的数，所以我们返回 [-1,-1] 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^9`

### 解题思路

如果是右侧第一个更大元素，很显然是单调栈的模板题。但之前写右侧第一个更大元素的单调栈，都是从右侧开始遍历。其实也可以从左侧遍历。从左侧遍历可能更好。

这题用单调栈的话，也需要从左侧开始遍历才好。

### 代码

```c++
class Solution {
public:
    vector<int> secondGreaterElement(vector<int>& nums) {
        int n = nums.size();
        stack<int> stk;
        vector<int> R(n, -1);
        //右侧第一个更大元素的位置
        // for(int i=0;i<n;i++){
        //     while(!stk.empty() && nums[i]>nums[stk.top()]){
        //         R[stk.top()] = i;
        //         stk.pop();
        //     }
        //     stk.push(i);
        // }

        //如果是右侧第二个更大元素的位置呢
        //pq中元素 first: 值  second: 索引  堆顶元素为最小值
        priority_queue<pair<int,int>,vector<pair<int,int>>,greater<pair<int,int>>> pq;
        for(int i=0;i<n;i++){
            while(!pq.empty() && nums[i]>pq.top().first){
                R[pq.top().second] = nums[i];
                pq.pop();
            }

            while(!stk.empty() && nums[i]>nums[stk.top()]){
                //nums[stk.top()]的右侧第一个更大元素为nums[i]
                //将nums[stk.top]存下来，可能有右侧第二个更大元素
                pq.emplace(nums[stk.top()],stk.top());
                stk.pop();
            }

            stk.push(i);
        }


        return R;

    }
};
```

