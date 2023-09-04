---
title:      LeetCode第84场双周赛总结
description:   755/4574
date:       2022-08-11
image: post-bg-20220811.png
categories:
    - leetcode contest
tags:
    
---

![image-20220811101701727](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220811101701727.png)

## 【模拟】合并相似的物品

### 题目

给你两个二维整数数组 `items1` 和 `items2` ，表示两个物品集合。每个数组 `items` 有以下特质：

- `items[i] = [valuei, weighti]` 其中 `valuei` 表示第 `i` 件物品的 **价值** ，`weighti` 表示第 `i` 件物品的 **重量** 。
- `items` 中每件物品的价值都是 **唯一的** 。

请你返回一个二维数组 `ret`，其中 `ret[i] = [valuei, weighti]`， `weighti` 是所有价值为 `valuei` 物品的 **重量之和** 。

**注意：**`ret` 应该按价值 **升序** 排序后返回。

 

**示例 1：**

```
输入：items1 = [[1,1],[4,5],[3,8]], items2 = [[3,1],[1,5]]
输出：[[1,6],[3,9],[4,5]]
解释：
value = 1 的物品在 items1 中 weight = 1 ，在 items2 中 weight = 5 ，总重量为 1 + 5 = 6 。
value = 3 的物品再 items1 中 weight = 8 ，在 items2 中 weight = 1 ，总重量为 8 + 1 = 9 。
value = 4 的物品在 items1 中 weight = 5 ，总重量为 5 。
所以，我们返回 [[1,6],[3,9],[4,5]] 。
```

**示例 2：**

```
输入：items1 = [[1,1],[3,2],[2,3]], items2 = [[2,1],[3,2],[1,3]]
输出：[[1,4],[2,4],[3,4]]
解释：
value = 1 的物品在 items1 中 weight = 1 ，在 items2 中 weight = 3 ，总重量为 1 + 3 = 4 。
value = 2 的物品在 items1 中 weight = 3 ，在 items2 中 weight = 1 ，总重量为 3 + 1 = 4 。
value = 3 的物品在 items1 中 weight = 2 ，在 items2 中 weight = 2 ，总重量为 2 + 2 = 4 。
所以，我们返回 [[1,4],[2,4],[3,4]] 。
```

**示例 3：**

```
输入：items1 = [[1,3],[2,2]], items2 = [[7,1],[2,2],[1,4]]
输出：[[1,7],[2,4],[7,1]]
解释：
value = 1 的物品在 items1 中 weight = 3 ，在 items2 中 weight = 4 ，总重量为 3 + 4 = 7 。
value = 2 的物品在 items1 中 weight = 2 ，在 items2 中 weight = 2 ，总重量为 2 + 2 = 4 。
value = 7 的物品在 items2 中 weight = 1 ，总重量为 1 。
所以，我们返回 [[1,7],[2,4],[7,1]] 。
```

 

**提示：**

- `1 <= items1.length, items2.length <= 1000`
- `items1[i].length == items2[i].length == 2`
- `1 <= valuei, weighti <= 1000`
- `items1` 中每个 `valuei` 都是 **唯一的** 。
- `items2` 中每个 `valuei` 都是 **唯一的** 。

### 解题思路

简单模拟即可

### 代码

```c++
class Solution {
public:
    vector<vector<int>> mergeSimilarItems(vector<vector<int>>& items1, vector<vector<int>>& items2) {
        vector<vector<int>> res = items1;
        for(auto &x: items2){
            int v = x[0],w=x[1];
            int n = res.size();
            for(int i=0;i<n;i++){
                if(res[i][0]==v){
                    res[i][1]+=w;
                    break;
                }
                if(i==n-1){
                    res.push_back({v,w});
                }
            }
        }
        sort(res.begin(),res.end(),[&](vector<int>& a, vector<int> &b){
            return a[0]<b[0];
        });
        return res;
    }
};
```

## 【思维+枚举】统计坏数对的数目

### 题目

给你一个下标从 **0** 开始的整数数组 `nums` 。如果 `i < j` 且 `j - i != nums[j] - nums[i]` ，那么我们称 `(i, j)` 是一个 **坏数对** 。

请你返回 `nums` 中 **坏数对** 的总数目。

 

**示例 1：**

```
输入：nums = [4,1,3,3]
输出：5
解释：数对 (0, 1) 是坏数对，因为 1 - 0 != 1 - 4 。
数对 (0, 2) 是坏数对，因为 2 - 0 != 3 - 4, 2 != -1 。
数对 (0, 3) 是坏数对，因为 3 - 0 != 3 - 4, 3 != -1 。
数对 (1, 2) 是坏数对，因为 2 - 1 != 3 - 1, 1 != 2 。
数对 (2, 3) 是坏数对，因为 3 - 2 != 3 - 3, 1 != 0 。
总共有 5 个坏数对，所以我们返回 5 。
```

**示例 2：**

```
输入：nums = [1,2,3,4,5]
输出：0
解释：没有坏数对。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`

### 解题思路

如果按原式$j-i\neq nums[j]-nums[i]$想的话，不好想

考虑对原式进行变换：$j-nums[j] \neq i-nums[i]$

限定i<j，总数对数量为$n*(n-1)/2$

那么坏数对的数量即为 总数对的数量 减去 非坏数对

即统计每个$i-nums[i]$所对应的数目

### 代码

```c++
class Solution {
public:
    long long countBadPairs(vector<int>& nums) {
        int n = nums.size();
        long long res = 1ll*n*(n-1)/2;
        map<int,int> tab;
        for(int i=0;i<n;i++){
            tab[i-nums[i]]++;
        }
        for(auto &x: tab){
            res -= 1ll*x.second*(x.second-1)/2;
        }
        return res;
    }
};
```

## 【模拟】任务调度器 II

### 题目

给你一个下标从 **0** 开始的正整数数组 `tasks` ，表示需要 **按顺序** 完成的任务，其中 `tasks[i]` 表示第 `i` 件任务的 **类型** 。

同时给你一个正整数 `space` ，表示一个任务完成 **后** ，另一个 **相同** 类型任务完成前需要间隔的 **最少** 天数。

在所有任务完成前的每一天，你都必须进行以下两种操作中的一种：

- 完成 `tasks` 中的下一个任务
- 休息一天

请你返回完成所有任务所需的 **最少** 天数。

 

**示例 1：**

```
输入：tasks = [1,2,1,2,3,1], space = 3
输出：9
解释：
9 天完成所有任务的一种方法是：
第 1 天：完成任务 0 。
第 2 天：完成任务 1 。
第 3 天：休息。
第 4 天：休息。
第 5 天：完成任务 2 。
第 6 天：完成任务 3 。
第 7 天：休息。
第 8 天：完成任务 4 。
第 9 天：完成任务 5 。
可以证明无法少于 9 天完成所有任务。
```

**示例 2：**

```
输入：tasks = [5,8,8,5], space = 2
输出：6
解释：
6 天完成所有任务的一种方法是：
第 1 天：完成任务 0 。
第 2 天：完成任务 1 。
第 3 天：休息。
第 4 天：休息。
第 5 天：完成任务 2 。
第 6 天：完成任务 3 。
可以证明无法少于 6 天完成所有任务。
```

 

**提示：**

- `1 <= tasks.length <= 10^5`
- `1 <= tasks[i] <= 10^9`
- `1 <= space <= tasks.length`

### 解题思路

读懂题目这题就很简单，任务按tasks列表中的顺序完成，遍历一遍即可

注意数据范围可能超过int

### 代码

```c++
class Solution {
public:
    long long taskSchedulerII(vector<int>& tasks, int space) {
        int n = tasks.size();
        map<int,long long> tab;//tab[i]=j 工作i上一次完成的时间
        long long time = 0;
        
        for(int i=0;i<n;i++){
            if(!tab.count(tasks[i])){
                time++;
                tab[tasks[i]] = time;
            }else{
                time++;
                if(time-tab[tasks[i]]<=space){
                    time += space - (time-tab[tasks[i]]-1);
                    tab[tasks[i]] = time;
                }else{
                    tab[tasks[i]] = time;
                }
            }
            // cout<<tasks[i]<<' '<<time<<endl;
        }
        return time;
    }
};
```

## 【贪心】将数组排序的最少替换次数

### 题目

给你一个下表从 **0** 开始的整数数组 `nums` 。每次操作中，你可以将数组中任何一个元素替换为 **任意两个** 和为该元素的数字。

- 比方说，`nums = [5,6,7]` 。一次操作中，我们可以将 `nums[1]` 替换成 `2` 和 `4` ，将 `nums` 转变成 `[5,2,4,7]` 。

请你执行上述操作，将数组变成元素按 **非递减** 顺序排列的数组，并返回所需的最少操作次数。

 

**示例 1：**

```
输入：nums = [3,9,3]
输出：2
解释：以下是将数组变成非递减顺序的步骤：
- [3,9,3] ，将9 变成 3 和 6 ，得到数组 [3,3,6,3] 
- [3,3,6,3] ，将 6 变成 3 和 3 ，得到数组 [3,3,3,3,3] 
总共需要 2 步将数组变成非递减有序，所以我们返回 2 。
```

**示例 2：**

```
输入：nums = [1,2,3,4,5]
输出：0
解释：数组已经是非递减顺序，所以我们返回 0 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`

### 解题思路

贪心的想，肯定从最后一个元素开始

如果nums[i-1]<=nums[i] ，满足非递减顺序，第nums[i-1]个数不用拆分

如果nums[i-1]>nums[i]，不满足非递减顺序，需要进行拆分，考虑如何进行拆分

肯定是尽量使得拆分得到的最小数最大

令已经排列好的后一个数为cur，当前考虑的数为nums[i]，已知nums[i]>cur

如果nums[i]%cur==0，即nums[i]可以拆分成整数个cur，此时非递减数组中最小数仍然是cur

否则，nums[i]拆分出的数中的最小值minv一定小于cur，此时要考虑将minv最大化

nums[i]能拆分出nums[i]/cur个cur，还剩下一个值小于cur，即拆分出nums[i]/cur+1个数

此时直接将nums[i]平均拆分nums[i]/cur+1份，这个值就是最大化的最小值

### 代码

```c++
class Solution {
public:
    long long minimumReplacement(vector<int>& nums) {
        int n = nums.size();
        if(n==1) return 0;
        long long res = 0;
        int cur = 1e9+5;
        for(int i=n-1;i>=0;i--){
            if(nums[i]<=cur){
                cur = nums[i];
            }else{
                if(nums[i]%cur==0){
                    res += 1ll*nums[i]/cur-1;
                }else{
                    res += 1ll*nums[i]/cur;
                    int tmp = nums[i]/(nums[i]/cur+1);
                    cur=tmp;
                }
            }
            // cout<<nums[i]<<' '<<cur<<' '<<res<<endl;
        }
        return res;
    }
};
```

