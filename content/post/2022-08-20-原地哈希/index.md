---
title:      原地哈希
description:   哈希表
date:       2022-08-20
image: post-bg-20220820.png
categories:
    - data structure and algorithm
tags:
    
---
## 前言

即将**原数组本身当作哈希表**，位置i对应值i或者i+1等

在原数组上进行修改

第i个数有两种情况进行位置交换，一个是主动比较与位置i的关系，另一个是被动比较，即后续的数主动比较引起的第i个数的位置交换。

当主动比较有限制时，还可以有被动比较的机会进行更改。

> 虽然第一个循环中嵌套了`while`，但是每一个`while`中就会有一个元素确定位置，在后续的遍历中确定位置元素的`while`就会被跳过。平均下来，每个数只需要看一次就可以了，`while` 循环体被执行很多次的情况不会每次都发生。这样的复杂度分析的方法叫做**均摊复杂度分析**，所以时间复杂度为*O*(*N*)。

## 寻找重复数

### 题目

给定一个包含 n + 1 个整数的数组 nums ，其数字都在 [1, n] 范围内（包括 1 和 n），可知至少存在一个重复的整数。

假设 nums 只有 一个重复的整数 ，返回 这个重复的数 。

你设计的解决方案必须 不修改 数组 nums 且只用常量级 O(1) 的额外空间。

 

示例 1：

输入：nums = [1,3,4,2,2]
输出：2
示例 2：

输入：nums = [3,1,3,4,2]
输出：3


提示：

1 <= n <= 10^5

nums.length == n + 1

1 <= nums[i] <= n

nums 中 只有一个整数 出现 两次或多次 ，其余整数均只出现 一次


进阶：

如何证明 nums 中至少存在一个重复的数字?
你可以设计一个线性级时间复杂度 O(n) 的解决方案吗？

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/find-the-duplicate-number
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

数组长度为n+1，下标为[0,n]，数字范围为[1,n]，设定哈希函数为f(x) = x + 1 ，即**位置i处的数字应为i+1**

对于元素nums[i]，如果nums[i]==i+1，说明满足要求，不用修改

否则，将nums[i]放到其应该待的位置：nums[i]-1，直到第i处的值也正确，或者已经找到了重复的数

注意，这里采用的原地哈希，需要修改原数组，不符合题目要求，但时间复杂度为O(n)，以及O(1)的空间

### 代码

```c++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int n = nums.size();
        for(int i=0;i<n;i++){
            //if(nums[i] == i+1) continue;
            while(nums[i]!=i+1){
                if(nums[i] == nums[nums[i]-1]) return nums[i];//重复了
                swap(nums[i], nums[nums[i]-1]);//x应该被换到下标x-1处
            }
        }
        // for(int i=0;i<n;i++){
        //     // cout<<nums[i]<<' ';
        //     if(nums[i]!=i+1) return nums[i];
        // }
        return -1;
    }
};
```

## 丢失的数字

### 题目

给定一个包含 [0, n] 中 n 个数的数组 nums ，找出 [0, n] 这个范围内没有出现在数组中的那个数。

 

示例 1：

输入：nums = [3,0,1]
输出：2
解释：n = 3，因为有 3 个数字，所以所有的数字都在范围 [0,3] 内。2 是丢失的数字，因为它没有出现在 nums 中。
示例 2：

输入：nums = [0,1]
输出：2
解释：n = 2，因为有 2 个数字，所以所有的数字都在范围 [0,2] 内。2 是丢失的数字，因为它没有出现在 nums 中。
示例 3：

输入：nums = [9,6,4,2,3,5,7,0,1]
输出：8
解释：n = 9，因为有 9 个数字，所以所有的数字都在范围 [0,9] 内。8 是丢失的数字，因为它没有出现在 nums 中。
示例 4：

输入：nums = [0]
输出：1
解释：n = 1，因为有 1 个数字，所以所有的数字都在范围 [0,1] 内。1 是丢失的数字，因为它没有出现在 nums 中。


提示：

n == nums.length
1 <= n <= 10^4
0 <= nums[i] <= n
nums 中的所有数字都 独一无二


进阶：你能否实现线性时间复杂度、仅使用额外常数空间的算法解决此问题?

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/missing-number
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

数组长度为n，数组下标为[0,n-1]，数字范围为[0,n]，设定哈希函数为f(x) = x，即位置i处的数字应为i

如果nums[i]==i，肯定不用再改了；如果nums[i]==n，也不用改，因为原数组中没有它对应的位置，等待后续其他位置和它进行调换

即如果nums[i]!=i 且 nums[i]<n 的话，就一直替换，将x放到位置x处

最后[0,n-1]的位置上第一个没有对应数字的即是丢失的数字。如果都对应上了，说明丢了n。

### 代码

```c++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int n = nums.size();
        //x 应该放的下标为x ，如果x为n，则先不动它，动其他的
        for(int i=0;i<n;i++){
            while(nums[i]!=i && nums[i]<n){
                swap(nums[i], nums[nums[i]]);//将x放到位置 x 处
            }
        }
        for(int i=0;i<n;i++){//第一个位置上没有对应数字的即是丢失的数字
            if(nums[i]!=i) return i;
        }
        return n;

    }
};
```

## 缺失的第一个正数

### 题目

给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。

请你实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案。


示例 1：

输入：nums = [1,2,0]
输出：3
示例 2：

输入：nums = [3,4,-1,1]
输出：2
示例 3：

输入：nums = [7,8,9,11,12]
输出：1


提示：

1 <= nums.length <= 5 * 10^5
-2^31 <= nums[i] <= 2^31 - 1

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/first-missing-positive
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

数组长度为n，数组下标为[0,n-1]，数的范围为[1,~],设定哈希函数为f(x) = x + 1 ，即**位置i处的数字应为i+1**

对于大于等于n的数x，在数组中没有其正确的容身之处，先不改，等待其它正确的数将它替换过去。

### 代码

```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();
        //对于小于n的值x，放在位置x-1处
        //即位置x，放的数值应该为x+1
        for(int i=0;i<n;i++){
            while(i+1 != nums[i] && nums[i]<n && nums[i]>0){
                if(nums[i]==nums[nums[i]-1]) break;
                swap(nums[i], nums[nums[i]-1]);
            }
        }
        for(int i=0;i<n;i++){
            if(i+1!=nums[i]) return i+1;
        }
        return n+1;
    }
};
```

## 找到所有数组中消失的数字

### 题目

给你一个含 n 个整数的数组 nums ，其中 nums[i] 在区间 [1, n] 内。请你找出所有在 [1, n] 范围内但没有出现在 nums 中的数字，并以数组的形式返回结果。

 

示例 1：

输入：nums = [4,3,2,7,8,2,3,1]
输出：[5,6]
示例 2：

输入：nums = [1,1]
输出：[2]


提示：

n == nums.length
1 <= n <= 10^5
1 <= nums[i] <= n
进阶：你能在不使用额外空间且时间复杂度为 O(n) 的情况下解决这个问题吗? 你可以假定返回的数组不算在额外空间内。

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

数组长度为n，数组下标为[0,n-1]，数的范围为[1,~],设定哈希函数为f(x) = x + 1 ，即**位置i处的数字应为i+1**

最后位置上没有对应数字即缺少该数字

### 代码

```c++
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        int n = nums.size();
        vector<int> res;
        for(int i=0;i<n;i++){
            while(nums[i]!=i+1){
                if(nums[i] == nums[nums[i]-1]) break;
                swap(nums[i], nums[nums[i]-1]);
            }
        }
        for(int i=0;i<n;i++){
            if(nums[i]!=i+1) res.push_back(i+1);
        }
        return res;
    }
};
```

