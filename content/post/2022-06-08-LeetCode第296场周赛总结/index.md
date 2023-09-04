---
title:      LeetCode第296场周赛总结
description:   1256/5721
date:       2022-06-08
image: post-bg-20220608.png
categories: 
    - leetcode contest
tags:
    
---
![image-20220608134559750](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220608134559750.png)

## 【模拟】极大极小游戏

### 题目

给你一个下标从 **0** 开始的整数数组 `nums` ，其长度是 `2` 的幂。

对 `nums` 执行下述算法：

1. 设 `n` 等于 `nums` 的长度，如果 `n == 1` ，**终止** 算法过程。否则，**创建** 一个新的整数数组 `newNums` ，新数组长度为 `n / 2` ，下标从 **0** 开始。
2. 对于满足 `0 <= i < n / 2` 的每个 **偶数** 下标 `i` ，将 `newNums[i]` **赋值** 为 `min(nums[2 * i], nums[2 * i + 1])` 。
3. 对于满足 `0 <= i < n / 2` 的每个 **奇数** 下标 `i` ，将 `newNums[i]` **赋值** 为 `max(nums[2 * i], nums[2 * i + 1])` 。
4. 用 `newNums` 替换 `nums` 。
5. 从步骤 1 开始 **重复** 整个过程。

执行算法后，返回 `nums` 中剩下的那个数字。

 

**示例 1：**

![img](https://assets.leetcode.com/uploads/2022/04/13/example1drawio-1.png)

```
输入：nums = [1,3,5,2,4,8,2,2]
输出：1
解释：重复执行算法会得到下述数组。
第一轮：nums = [1,5,4,2]
第二轮：nums = [1,4]
第三轮：nums = [1]
1 是最后剩下的那个数字，返回 1 。
```

**示例 2：**

```
输入：nums = [3]
输出：3
解释：3 就是最后剩下的数字，返回 3 。
```

 

**提示：**

- `1 <= nums.length <= 1024`
- `1 <= nums[i] <= 109`
- `nums.length` 是 `2` 的幂

### 解题思路

按题意模拟即可

### 代码

```c++
class Solution {
public:
    int minMaxGame(vector<int>& nums) {
        int n = nums.size();
        while(n>1){
            for(int i=0;i<n/2;i++){
                if(i%2==0) nums[i]=min(nums[i*2],nums[i*2+1]);
                else nums[i]=max(nums[i*2],nums[i*2+1]);
            }
            n=n/2;
        }
        return nums[0];
    }
};
```

## 【排序+贪心】划分数组使最大差为 K

### 题目

给你一个整数数组 `nums` 和一个整数 `k` 。你可以将 `nums` 划分成一个或多个 **子序列** ，使 `nums` 中的每个元素都 **恰好** 出现在一个子序列中。

在满足每个子序列中最大值和最小值之间的差值最多为 `k` 的前提下，返回需要划分的 **最少** 子序列数目。

**子序列** 本质是一个序列，可以通过删除另一个序列中的某些元素（或者不删除）但不改变剩下元素的顺序得到。

 

**示例 1：**

```
输入：nums = [3,6,1,2,5], k = 2
输出：2
解释：
可以将 nums 划分为两个子序列 [3,1,2] 和 [6,5] 。
第一个子序列中最大值和最小值的差值是 3 - 1 = 2 。
第二个子序列中最大值和最小值的差值是 6 - 5 = 1 。
由于创建了两个子序列，返回 2 。可以证明需要划分的最少子序列数目就是 2 。
```

**示例 2：**

```
输入：nums = [1,2,3], k = 1
输出：2
解释：
可以将 nums 划分为两个子序列 [1,2] 和 [3] 。
第一个子序列中最大值和最小值的差值是 2 - 1 = 1 。
第二个子序列中最大值和最小值的差值是 3 - 3 = 0 。
由于创建了两个子序列，返回 2 。注意，另一种最优解法是将 nums 划分成子序列 [1] 和 [2,3] 。
```

**示例 3：**

```
输入：nums = [2,2,4,5], k = 0
输出：3
解释：
可以将 nums 划分为三个子序列 [2,2]、[4] 和 [5] 。
第一个子序列中最大值和最小值的差值是 2 - 2 = 0 。
第二个子序列中最大值和最小值的差值是 4 - 4 = 0 。
第三个子序列中最大值和最小值的差值是 5 - 5 = 0 。
由于创建了三个子序列，返回 3 。可以证明需要划分的最少子序列数目就是 3 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^5`
- `0 <= k <= 105`

### 解题思路

要求是子序列，不用连续，位置不重要，排序贪心就好了。

从小到大排序后，每次找差值为K的一组。

### 代码

```c++
class Solution {
public:
    int partitionArray(vector<int>& nums, int k) {
        int n = nums.size();
        sort(nums.begin(),nums.end());
        int res = 0 ;
        int curmin=-1;
        for(int i=0;i<n;i++){
            if(curmin==-1){
                curmin = nums[i];
            }else{
                if(nums[i]-curmin<=k){//差值没有超过K，可继续添加元素
                }else{//否则，另起一组
                    curmin = nums[i];
                    res++;
                }
            }
        }
        return res+1;//最后的一组统计进来
    }
};
```

## 【模拟】替换数组中的元素

### 题目

给你一个下标从 **0** 开始的数组 `nums` ，它包含 `n` 个 **互不相同** 的正整数。请你对这个数组执行 `m` 个操作，在第 `i` 个操作中，你需要将数字 `operations[i][0]` 替换成 `operations[i][1]` 。

题目保证在第 `i` 个操作中：

- `operations[i][0]` 在 `nums` 中存在。
- `operations[i][1]` 在 `nums` 中不存在。

请你返回执行完所有操作后的数组。

 

**示例 1：**

```
输入：nums = [1,2,4,6], operations = [[1,3],[4,7],[6,1]]
输出：[3,2,7,1]
解释：我们对 nums 执行以下操作：
- 将数字 1 替换为 3 。nums 变为 [3,2,4,6] 。
- 将数字 4 替换为 7 。nums 变为 [3,2,7,6] 。
- 将数字 6 替换为 1 。nums 变为 [3,2,7,1] 。
返回最终数组 [3,2,7,1] 。
```

**示例 2：**

```
输入：nums = [1,2], operations = [[1,3],[2,1],[3,2]]
输出：[2,1]
解释：我们对 nums 执行以下操作：
- 将数字 1 替换为 3 。nums 变为 [3,2] 。
- 将数字 2 替换为 1 。nums 变为 [3,1] 。
- 将数字 3 替换为 2 。nums 变为 [2,1] 。
返回最终数组 [2,1] 。
```

 

**提示：**

- `n == nums.length`
- `m == operations.length`
- `1 <= n, m <= 10^5`
- `nums` 中所有数字 **互不相同** 。
- `operations[i].length == 2`
- `1 <= nums[i], operations[i][0], operations[i][1] <= 10^6`
- 在执行第 `i` 个操作时，`operations[i][0]` 在 `nums` 中存在。
- 在执行第 `i` 个操作时，`operations[i][1]` 在 `nums` 中不存在。

### 解题思路

要替换数字，先找到该数字的位置，然后再替换

元素都不同，且每次操作也不冲突

利用数据结构存储数的位置。当时看数据范围没那么大就用数组了，值的范围大一点的话用map或unordered_map就行。

### 代码

```c++
const int MAXN = 1e6+5;
class Solution {
public:
    vector<int> arrayChange(vector<int>& nums, vector<vector<int>>& operations) {
        int n = nums.size();
        vector<int> pos(MAXN,-1);
        for(int i=0;i<n;i++) pos[nums[i]]=i;
        for(auto &x: operations){
            int before = x[0];
            int after = x[1];
            pos[after] = pos[before];
            pos[before] = -1;
        }
        vector<int> res(n,0);
        for(int i=0;i<MAXN;i++){
            if(pos[i]>-1) res[pos[i]]=i;
        }
        return res;
    }
};
```

### 题目进阶

如果含有重复元素呢

考虑将操作倒过来，记录每个数最终要变成的数

令mp[i] = j 表示数i最后会被修改成j

如果j也会被修改，即mp[i] = mp[j]

```c++
class Solution {
public:
    vector<int> arrayChange(vector<int>& nums, vector<vector<int>>& operations) {
        unordered_map<int, int> mp;
        reverse(operations.begin(), operations.end());
        for (auto &op:operations) {   // 建立数与数之间的映射关系
            if (mp.count(op[1])) mp[op[0]] = mp[op[1]];
            else mp[op[0]] = op[1];         
        }
        for (int &x:nums) if (mp.count(x)) x = mp[x];
        return nums;
    }
};
```

## 【模拟】设计一个文本编辑器

### 题目

请你设计一个带光标的文本编辑器，它可以实现以下功能：

- **添加：**在光标所在处添加文本。
- **删除：**在光标所在处删除文本（模拟键盘的删除键）。
- **移动：**将光标往左或者往右移动。

当删除文本时，只有光标左边的字符会被删除。光标会留在文本内，也就是说任意时候 `0 <= cursor.position <= currentText.length` 都成立。

请你实现 `TextEditor` 类：

- `TextEditor()` 用空文本初始化对象。
- `void addText(string text)` 将 `text` 添加到光标所在位置。添加完后光标在 `text` 的右边。
- `int deleteText(int k)` 删除光标左边 `k` 个字符。返回实际删除的字符数目。
- `string cursorLeft(int k)` 将光标向左移动 `k` 次。返回移动后光标左边 `min(10, len)` 个字符，其中 `len` 是光标左边的字符数目。
- `string cursorRight(int k)` 将光标向右移动 `k` 次。返回移动后光标左边 `min(10, len)` 个字符，其中 `len` 是光标左边的字符数目。

 

**示例 1：**

```
输入：
["TextEditor", "addText", "deleteText", "addText", "cursorRight", "cursorLeft", "deleteText", "cursorLeft", "cursorRight"]
[[], ["leetcode"], [4], ["practice"], [3], [8], [10], [2], [6]]
输出：
[null, null, 4, null, "etpractice", "leet", 4, "", "practi"]

解释：
TextEditor textEditor = new TextEditor(); // 当前 text 为 "|" 。（'|' 字符表示光标）
textEditor.addText("leetcode"); // 当前文本为 "leetcode|" 。
textEditor.deleteText(4); // 返回 4
                          // 当前文本为 "leet|" 。
                          // 删除了 4 个字符。
textEditor.addText("practice"); // 当前文本为 "leetpractice|" 。
textEditor.cursorRight(3); // 返回 "etpractice"
                           // 当前文本为 "leetpractice|". 
                           // 光标无法移动到文本以外，所以无法移动。
                           // "etpractice" 是光标左边的 10 个字符。
textEditor.cursorLeft(8); // 返回 "leet"
                          // 当前文本为 "leet|practice" 。
                          // "leet" 是光标左边的 min(10, 4) = 4 个字符。
textEditor.deleteText(10); // 返回 4
                           // 当前文本为 "|practice" 。
                           // 只有 4 个字符被删除了。
textEditor.cursorLeft(2); // 返回 ""
                          // 当前文本为 "|practice" 。
                          // 光标无法移动到文本以外，所以无法移动。
                          // "" 是光标左边的 min(10, 0) = 0 个字符。
textEditor.cursorRight(6); // 返回 "practi"
                           // 当前文本为 "practi|ce" 。
                           // "practi" 是光标左边的 min(10, 6) = 6 个字符。
```

 

**提示**：

- `1 <= text.length, k <= 40`
- `text` 只含有小写英文字母。
- 调用 `addText` ，`deleteText` ，`cursorLeft` 和 `cursorRight` 的 **总** 次数不超过 `2 * 10^4` 次。

 

**进阶**：你能设计并实现一个每次调用时间复杂度为 `O(k)` 的解决方案吗？

### 解题思路

一开始看数据范围很小，直接string暴力开码，保存当前光标位置

没想到74/75，超时了

果断换Java StringBuilder过了，但它的函数和C++的有区别，一直在调，人麻了

### 代码

```java
class TextEditor {
    StringBuffer str;
    int p;
    public TextEditor() {
        str = new StringBuffer("|");
        p = 0;
    }
    public void addText(String text) {
        str.insert(p,text);
        p = p+text.length();
    }
    
    public int deleteText(int k) {
        int start = Math.max(0,p-k);
        str.delete(start,p);
        int res=p-Math.max(0,p-k);
        p = Math.max(0,p-k);
        return res;
    }
    
    public String cursorLeft(int k) {
        str.delete(p,p+1);
        str.insert(Math.max(0,p-k),"|");
        p=Math.max(0,p-k);
        int start=Math.max(0,p-10);
        int len=Math.min(10,p-start);
        return str.substring(start,start+len);
    }
    
    public String cursorRight(int k) {
        int ma = str.length()-1;
        str.delete(p,p+1);
        str.insert(Math.min(p+k,ma),"|");
        p=Math.min(p+k,ma);
        int start=Math.max(0,p-10);
        int len=Math.min(10,p-start);
        return str.substring(start,start+len);
    }
}
```

### 偷学代码

#### 两个string维护

一个string维护光标前的字符串，一个string维护光标后的字符串

```c++
class TextEditor {
    string s,t;
public:
    TextEditor() {
        s=t="";
    }
    
    void addText(string text) {
        s+=text;
    }
    
    int deleteText(int k) {
        if(s.size()<k)k=s.size();
        int i;
        for(i=0;i<k;i++)s.pop_back();
        return k;
    }
    
    string cursorLeft(int k) {
        if(s.size()<k)k=s.size();
        int i;
        for(i=0;i<k;i++)
        {
            t.push_back(s.back());
            s.pop_back();
        }
        string ans(s.end()-min(10,int(s.size())),s.end());
        return ans;
    }
    
    string cursorRight(int k) {
        if(t.size()<k)k=t.size();
        int i;
        for(i=0;i<k;i++)
        {
            s.push_back(t.back());
            t.pop_back();
        }
        string ans(s.end()-min(10,int(s.size())),s.end());
        return ans;
    }
};
```

#### 双向链表

```c++
class TextEditor {
    list<char> l;
    list<char>::iterator cur = l.begin();

public:
    TextEditor() {}

    void addText(string text) {
        for (char ch : text)
            l.insert(cur, ch);
    }

    int deleteText(int k) {
        int k0 = k;
        for (; k && cur != l.begin(); --k)
            cur = l.erase(prev(cur));
        return k0 - k;
    }

    string text() {
        string s;
        auto it = cur;
        for (int k = 10; k && it != l.begin(); --k) {
            it = prev(it);
            s += *it;
        }
        reverse(s.begin(), s.end());
        return s;
    }

    string cursorLeft(int k) {
        for (; k && cur != l.begin(); --k)
            cur = prev(cur);
        return text();
    }

    string cursorRight(int k) {
        for (; k && cur != l.end(); --k)
            cur = next(cur);
        return text();
    }
};
```

#### 对顶栈

和两个string维护的差不多

```c++
class TextEditor {
    vector<char> left, right;

public:
    TextEditor() {}

    void addText(string text) {
        left.insert(left.end(), text.begin(), text.end());
    }

    int deleteText(int k) {
        int k0 = k;
        for (; k && !left.empty(); --k)
            left.pop_back();
        return k0 - k;
    }

    string text() {
        return string(next(left.begin(), max((int) left.size() - 10, 0)), left.end());
    }

    string cursorLeft(int k) {
        for (; k && !left.empty(); --k) {
            right.emplace_back(left.back());
            left.pop_back();
        }
        return text();
    }

    string cursorRight(int k) {
        for (; k && !right.empty(); --k) {
            left.emplace_back(right.back());
            right.pop_back();
        }
        return text();
    }
};

```

