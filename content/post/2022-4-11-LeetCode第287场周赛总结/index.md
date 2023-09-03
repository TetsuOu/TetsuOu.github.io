---
title:      LeetCode第287场周赛总结
description:   1958/6811
date:       2022-04-11 12:00:00+0000
math: true
image: post-bg-20220411_1.png
categories:
    - leetcode contest
tags:
    
---
![image-20220411191644801](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220411191644801.png)

## 【贪心】转化时间需要的最少操作数

### 题目

给你两个字符串 `current` 和 `correct` ，表示两个 **24 小时制时间** 。

**24 小时制时间** 按 `"HH:MM"` 进行格式化，其中 `HH` 在 `00` 和 `23` 之间，而 `MM` 在 `00` 和 `59` 之间。最早的 24 小时制时间为 `00:00` ，最晚的是 `23:59` 。

在一步操作中，你可以将 `current` 这个时间增加 `1`、`5`、`15` 或 `60` 分钟。你可以执行这一操作 **任意** 次数。

返回将 `current` 转化为 `correct` 需要的 **最少操作数** 。

 

**示例 1：**

```
输入：current = "02:30", correct = "04:35"
输出：3
解释：
可以按下述 3 步操作将 current 转换为 correct ：
- 为 current 加 60 分钟，current 变为 "03:30" 。
- 为 current 加 60 分钟，current 变为 "04:30" 。 
- 为 current 加 5 分钟，current 变为 "04:35" 。
可以证明，无法用少于 3 步操作将 current 转化为 correct 。
```

**示例 2：**

```
输入：current = "11:00", correct = "11:01"
输出：1
解释：只需要为 current 加一分钟，所以最小操作数是 1 。
```

 

**提示：**

- `current` 和 `correct` 都符合 `"HH:MM"` 格式
- `current <= correct`

### 解题思路

先加60的，加不了再加15，加不了再加5，加不了再加1

没想到可以先直接都转为分钟

### 代码

写的很繁琐

```c++
class Solution {
public:
    vector<int> covert(string time){
        int i=0;
        int a=0,b=0;
        for(i=0;i<time.size();i++){
            if(time[i]==':') break;
            a = a*10+(time[i]-'0');
        }
        for(i++;i<time.size();i++){
            b = b*10+(time[i]-'0');
        }
        return {a,b};
    }
    int convertTime(string current, string correct) {
        
        int cura = covert(current)[0], curb=covert(current)[1];
        int fina = covert(correct)[0], finb=covert(correct)[1];
        
        int step=0;
        
        while(1){
            
            if(cura==fina && curb==finb) break;
            
            if(cura==fina){
                int tmp = finb - curb;
                if(tmp>=15) curb+=15;
                else if(tmp>=5) curb+=5;
                else curb+=1;
                
                step++;
            }
            
            if(cura<fina && curb<=finb){
                step+=fina-cura;
                cura = fina;            
            }
            
            if(cura<fina && curb>finb){
                int tmp = finb+60-curb;
                
                if(tmp>=15){
                    curb+=15;
                    if(curb>=60) curb-=60,cura++;
                }
                else if(tmp>=5){
                    curb+=5;
                    if(curb>=60) curb-=60,cura++;
                }
                else {
                    curb+=1;
                    if(curb>=60) curb-=60,cura++;
                }
                
                step++;
            }
        }
        return step;
    }
};
```

### 偷学代码

```c++
class Solution {
public:
    int convertTime(string current, string correct) {
        int a = stoi(current.substr(0, 2)) * 60 + stoi(current.substr(3, 2));
        int b = stoi(correct.substr(0, 2)) * 60 + stoi(correct.substr(3, 2));
        if (b < a) b += 24 * 60;
        int ans = 0;
        while (b - a >= 60) a += 60, ans++;
        while (b - a >= 15) a += 15, ans++;
        while (b - a >= 5) a += 5, ans++;
        return ans + b - a;
    }
};
```

## 【模拟】找出输掉零场或一场比赛的玩家

### 题目

给你一个整数数组 `matches` 其中 `matches[i] = [winneri, loseri]` 表示在一场比赛中 `winneri` 击败了 `loseri` 。

返回一个长度为 2 的列表 `answer` ：

- `answer[0]` 是所有 **没有** 输掉任何比赛的玩家列表。
- `answer[1]` 是所有恰好输掉 **一场** 比赛的玩家列表。

两个列表中的值都应该按 **递增** 顺序返回。

**注意：**

- 只考虑那些参与 **至少一场** 比赛的玩家。
- 生成的测试用例保证 **不存在** 两场比赛结果 **相同** 。

 

**示例 1：**

```
输入：matches = [[1,3],[2,3],[3,6],[5,6],[5,7],[4,5],[4,8],[4,9],[10,4],[10,9]]
输出：[[1,2,10],[4,5,7,8]]
解释：
玩家 1、2 和 10 都没有输掉任何比赛。
玩家 4、5、7 和 8 每个都输掉一场比赛。
玩家 3、6 和 9 每个都输掉两场比赛。
因此，answer[0] = [1,2,10] 和 answer[1] = [4,5,7,8] 。
```

**示例 2：**

```
输入：matches = [[2,3],[1,3],[5,4],[6,4]]
输出：[[1,2,5,6],[]]
解释：
玩家 1、2、5 和 6 都没有输掉任何比赛。
玩家 3 和 4 每个都输掉两场比赛。
因此，answer[0] = [1,2,5,6] 和 answer[1] = [] 。
```

 

**提示：**

- `1 <= matches.length <= 105`
- `matches[i].length == 2`
- `1 <= winneri, loseri <= 105`
- `winneri != loseri`
- 所有 `matches[i]` **互不相同**

### 解题思路

模拟即可

### 代码

```c++
class Solution {
public:
    vector<vector<int>> findWinners(vector<vector<int>>& matches) {
        vector<int> a,b;
        
        unordered_map<int,int> mapa,mapb;
        
        for(auto v: matches){
            int winnum = v[0];
            int losenum = v[1];
            mapa[winnum]++;
            mapb[losenum]++;
        }
        
        for(auto x: mapa){
            if(!mapb.count(x.first)) a.push_back(x.first);
        }
        
        for(auto x:mapb){
            if(x.second == 1) b.push_back(x.first);
        }
        
        sort(a.begin(),a.end());
        sort(b.begin(),b.end());
        return {a,b};
        
        
    }
};
```

## 【二分】每个小孩最多能分到多少糖果

### 题目

给你一个 **下标从 0 开始** 的整数数组 `candies` 。数组中的每个元素表示大小为 `candies[i]` 的一堆糖果。你可以将每堆糖果分成任意数量的 **子堆** ，但 **无法** 再将两堆合并到一起。

另给你一个整数 `k` 。你需要将这些糖果分配给 `k` 个小孩，使每个小孩分到 **相同** 数量的糖果。每个小孩可以拿走 **至多一堆** 糖果，有些糖果可能会不被分配。

返回每个小孩可以拿走的 **最大糖果数目** 。

 

**示例 1：**

```
输入：candies = [5,8,6], k = 3
输出：5
解释：可以将 candies[1] 分成大小分别为 5 和 3 的两堆，然后把 candies[2] 分成大小分别为 5 和 1 的两堆。现在就有五堆大小分别为 5、5、3、5 和 1 的糖果。可以把 3 堆大小为 5 的糖果分给 3 个小孩。可以证明无法让每个小孩得到超过 5 颗糖果。
```

**示例 2：**

```
输入：candies = [2,5], k = 11
输出：0
解释：总共有 11 个小孩，但只有 7 颗糖果，但如果要分配糖果的话，必须保证每个小孩至少能得到 1 颗糖果。因此，最后每个小孩都没有得到糖果，答案是 0 。
```

 

**提示：**

- `1 <= candies.length <= 105`
- `1 <= candies[i] <= 107`
- `1 <= k <= 1012`

### 解题思路

如果每个小孩都能分到至少 x 个糖果，那么也能分到至少 (x - 1)个糖果。因此满足二分性。

经典二分了

一开始二分判定函数里有个变量写错了，WA了一发

### 代码

```c++
class Solution {
public:
    long long num;//孩子个数
    vector<int> candie;
    long long len,sum;//糖果堆数 糖果总数
    bool isok(long long c){//每人c颗是否可行
        if(c*num>sum) return false;
        long long  total = 0;
        for(auto x:candie){
            total+=x/c;
            // cout<<total<<endl;
        }
        return total>=num;
    }
    int maximumCandies(vector<int>& candies, long long k) {
        num = k;
        candie = candies;
        len = candies.size();
        sum=0;
        int maxc = -1;
        for(auto x:candies){
            sum+=x;
            maxc=max(maxc,x);
        }
        
        if(sum<k) return 0;
        if(k==1) return maxc;
        
        // cout<<isok(6)<<endl;
        
        long long l = 0, r = 1e7+5;//左开右开
        
        while(l+1!=r){
            long long mid = (l+r)/2;
            
            if(isok(mid)) l=mid;
            else r = mid;
        }
        
        // cout<<l<<' '<<r<<' '<<endl;
        
        return l;
        
    }
};
```

## 【模拟+逆向思考】加密解密字符串

### 题目

给你一个字符数组 `keys` ，由若干 **互不相同** 的字符组成。还有一个字符串数组 `values` ，内含若干长度为 2 的字符串。另给你一个字符串数组 `dictionary` ，包含解密后所有允许的原字符串。请你设计并实现一个支持加密及解密下标从 **0** 开始字符串的数据结构。

字符串 **加密** 按下述步骤进行：

1. 对字符串中的每个字符 `c` ，先从 `keys` 中找出满足 `keys[i] == c` 的下标 `i` 。
2. 在字符串中，用 `values[i]` 替换字符 `c` 。

字符串 **解密** 按下述步骤进行：

1. 将字符串每相邻 2 个字符划分为一个子字符串，对于每个子字符串 `s` ，找出满足 `values[i] == s` 的一个下标 `i` 。如果存在多个有效的 `i` ，从中选择 **任意** 一个。这意味着一个字符串解密可能得到多个解密字符串。
2. 在字符串中，用 `keys[i]` 替换 `s` 。

实现 `Encrypter` 类：

- `Encrypter(char[] keys, String[] values, String[] dictionary)` 用 `keys`、`values` 和 `dictionary` 初始化 `Encrypter` 类。
- `String encrypt(String word1)` 按上述加密过程完成对 `word1` 的加密，并返回加密后的字符串。
- `int decrypt(String word2)` 统计并返回可以由 `word2` 解密得到且出现在 `dictionary` 中的字符串数目。

 

**示例：**

```
输入：
["Encrypter", "encrypt", "decrypt"]
[[['a', 'b', 'c', 'd'], ["ei", "zf", "ei", "am"], ["abcd", "acbd", "adbc", "badc", "dacb", "cadb", "cbda", "abad"]], ["abcd"], ["eizfeiam"]]
输出：
[null, "eizfeiam", 2]

解释：
Encrypter encrypter = new Encrypter([['a', 'b', 'c', 'd'], ["ei", "zf", "ei", "am"], ["abcd", "acbd", "adbc", "badc", "dacb", "cadb", "cbda", "abad"]);
encrypter.encrypt("abcd"); // 返回 "eizfeiam"。 
                           // 'a' 映射为 "ei"，'b' 映射为 "zf"，'c' 映射为 "ei"，'d' 映射为 "am"。
encrypter.decrypt("eizfeiam"); // return 2. 
                              // "ei" 可以映射为 'a' 或 'c'，"zf" 映射为 'b'，"am" 映射为 'd'。 
                              // 因此，解密后可以得到的字符串是 "abad"，"cbad"，"abcd" 和 "cbcd"。 
                              // 其中 2 个字符串，"abad" 和 "abcd"，在 dictionary 中出现，所以答案是 2 。
```

 

**提示：**

- `1 <= keys.length == values.length <= 26`
- `values[i].length == 2`
- `1 <= dictionary.length <= 100`
- `1 <= dictionary[i].length <= 100`
- 所有 `keys[i]` 和 `dictionary[i]` **互不相同**
- `1 <= word1.length <= 2000`
- `1 <= word2.length <= 200`
- 所有 `word1[i]` 都出现在 `keys` 中
- `word2.length` 是偶数
- `keys`、`values[i]`、`dictionary[i]`、`word1` 和 `word2` 只含小写英文字母
- 至多调用 `encrypt` 和 `decrypt` **总计** `200` 次

### 解题思路

这种类型题目，对于力扣的输入都有点搞不懂，所以当时就没写了



加密，按题意模拟即可

解密，倒过来思考，利用前面实现的加密即可

### 代码

```c++
class Encrypter {
public:
    vector<char> key;
    vector<string> value;
    vector<string> dic;
    Encrypter(vector<char>& keys, vector<string>& values, vector<string>& dictionary) {
        key = keys;
        value = values;
        dic = dictionary;
    }
    
    string encrypt(string word1) {
        string res;
        for(auto c:word1){
            int idx = find(key.begin(),key.end(),c)-key.begin();
            if(idx<key.size()) res += value[idx];
            else res += '?';
        }
        // cout<<res<<endl;
        return res;
    }
    
    int decrypt(string word2) {
        int res=0;
        for(auto x:dic){
           if(encrypt(x)==word2) res++;
        }
        return res;
    }
};

/**
 * Your Encrypter object will be instantiated and called as such:
 * Encrypter* obj = new Encrypter(keys, values, dictionary);
 * string param_1 = obj->encrypt(word1);
 * int param_2 = obj->decrypt(word2);
 */
```

