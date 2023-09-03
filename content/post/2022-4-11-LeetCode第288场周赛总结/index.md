---
title:      LeetCode第288场周赛总结
description:   1352/6926
date:       2022-04-11 13:00:00+0000
image: post-bg-20220411_2.png
math:   true   
categories:
    - leetcode contest
tags:
 
---
![image-20220410221308514](https://raw.githubusercontent.com/zezezeking/picturebed/main/image-20220410221308514.png)


## 【贪心】按奇偶性交换后的最大数字

### 题目

给你一个正整数 `num` 。你可以交换 `num` 中 **奇偶性** 相同的任意两位数字（即，都是奇数或者偶数）。

返回交换 **任意** 次之后 `num` 的 **最大** 可能值*。*

 

**示例 1：**

```
输入：num = 1234
输出：3412
解释：交换数字 3 和数字 1 ，结果得到 3214 。
交换数字 2 和数字 4 ，结果得到 3412 。
注意，可能存在其他交换序列，但是可以证明 3412 是最大可能值。
注意，不能交换数字 4 和数字 1 ，因为它们奇偶性不同。
```

**示例 2：**

```
输入：num = 65875
输出：87655
解释：交换数字 8 和数字 6 ，结果得到 85675 。
交换数字 5 和数字 7 ，结果得到 87655 。
注意，可能存在其他交换序列，但是可以证明 87655 是最大可能值。
```

 

**提示：**

- `1 <= num <= 10^9`

### 解题思路

贪心。奇偶性相同的位置从大到小排序。

打比赛的时候写的太繁琐了。

### 代码

```c++
class Solution {
public:
    int largestInteger(int num) {
        vector<int> nums;
        while(num){
            nums.push_back(num%10);
            num/=10;
        }
        reverse(nums.begin(),nums.end());
        int len = nums.size();
        vector<int> iseven(len);
        vector<int> even;
        for(int i=0;i<len;i++){
            if(nums[i]%2==0) {
                iseven[i] = 1;
                even.push_back(nums[i]);
            }
        }
        sort(even.begin(),even.end());
        
        for(int i=0;i<len;i++){
            
            if(iseven[i]) {
                nums[i]=even.back(),even.pop_back();
            }
        }
        
        vector<int> isodd(len);
        vector<int> odd;
        for(int i=0;i<len;i++){
            if(nums[i]%2==1) {
                isodd[i] = 1;
    
                odd.push_back(nums[i]);
            }
        }
        sort(odd.begin(),odd.end());
        
        for(int i=0;i<len;i++){
            
            if(isodd[i]) {
                
                nums[i]=odd.back(),odd.pop_back();
            }
        }
        
        
        int res = 0;
        
        for(auto x:nums){
            res = res*10+x;
        }
        
        return res;
    }
};
```

### 偷学代码

我前面花了一定代码量处理int转string以及string转int

直接使用\<string>里的to_string函数方便的多

```c++
class Solution {
public:
    int largestInteger(int num) {
        auto s = std::to_string(num);
        for (int i = 0; i < int(s.length()); i++) {
            for (int j = 0; j < int(s.length()); j++) {
                if (s[i] % 2 == s[j] % 2 && s[i] > s[j]) {
                    std::swap(s[i], s[j]);
                }
            }
        }
        return std::atoi(s.c_str());
    }
};
```

```c++
class Solution {
public:
    int largestInteger(int num) {
        string str = to_string(num);
        for(int i=0; i<(int)str.size(); i++)
            for(int j=i+1; j<(int)str.size(); j++)
                if((str[i] ^ str[j]) % 2 == 0 && str[i] < str[j])
                    swap(str[i], str[j]);
        return stoi(str);
    }
};
```

#### atoi和stoi函数的区别

都是C++的字符处理函数，把数字字符串转换成int输出

头文件都是#include\<cstring>

atoi()的参数是 const char* ,因此对于一个字符串str我们必须调用 c\_str()的方法把这个string转换成 const char*类型的,而stoi()的参数是const string*,不需要转化为 const char*；

stoi()会做范围检查，默认范围是在int的范围内的，如果超出范围的话则会runtime error！而atoi()不会做范围检查，如果超出范围的话，超出上界，则输出上界，超出下界，则输出下界；

当输入的字符串不是整型，且前半部分都是数字时，stoi()与atoi()都会取前面为整型的部分。如string testFloat = "12.34";时，两者输出值为12。

当输入的字符串不是整型，且前半部分不是数字时，stoi()会报错，但atoi()会输出0。

## 【暴力】向表达式添加括号后的最小结果

### 题目

给你一个下标从 **0** 开始的字符串 `expression` ，格式为 `"<num1>+<num2>"` ，其中 `<num1>` 和 `<num2>` 表示正整数。

请你向 `expression` 中添加一对括号，使得在添加之后， `expression` 仍然是一个有效的数学表达式，并且计算后可以得到 **最小** 可能值。左括号 **必须** 添加在 `'+'` 的左侧，而右括号必须添加在 `'+'` 的右侧。

返回添加一对括号后形成的表达式 `expression` ，且满足 `expression` 计算得到 **最小** 可能值*。*如果存在多个答案都能产生相同结果，返回任意一个答案。

生成的输入满足：`expression` 的原始值和添加满足要求的任一对括号之后 `expression` 的值，都符合 32-bit 带符号整数范围。

 

**示例 1：**

```
输入：expression = "247+38"
输出："2(47+38)"
解释：表达式计算得到 2 * (47 + 38) = 2 * 85 = 170 。
注意 "2(4)7+38" 不是有效的结果，因为右括号必须添加在 '+' 的右侧。
可以证明 170 是最小可能值。
```

**示例 2：**

```
输入：expression = "12+34"
输出："1(2+3)4"
解释：表达式计算得到 1 * (2 + 3) * 4 = 1 * 5 * 4 = 20 。
```

**示例 3：**

```
输入：expression = "999+999"
输出："(999+999)"
解释：表达式计算得到 999 + 999 = 1998 。
```

 

**提示：**

- `3 <= expression.length <= 10`
- `expression` 仅由数字 `'1'` 到 `'9'` 和 `'+'` 组成
- `expression` 由数字开始和结束
- `expression` 恰好仅含有一个 `'+'`.
- `expression` 的原始值和添加满足要求的任一对括号之后 `expression` 的值，都符合 32-bit 带符号整数范围

### 解题思路

一开始看到表达式的题以为是要用动态规划，想run的，后来看数据范围，这么小，暴力就好

### 代码

```c++
class Solution {
public:
    string minimizeResult(string expression) {
        vector<int> left,right;
        bool flag = true;
        for(auto x: expression){
            if(x=='+') flag=false;
            else{
                if(flag) left.push_back(x-'0');
                else right.push_back(x-'0');
            }        
        }
        
        int lenL = left.size(),lenR=right.size();
        right.push_back(0);
        int res = 1<<30;
        int idxL,idxR;
        for(int i=0;i<lenL;i++){
            int leftL = 0,leftR=0;
            for(int p=0;p<i;p++){
                leftL = leftL*10 + left[p];
            }
            for(int q=i;q<lenL;q++){
                leftR = leftR*10 + left[q];
            }
            // cout<<leftL<<' '<<leftR<<endl;
            for(int j=1;j<=lenR;j++){
                int rightL=0,rightR=0;
                for(int p=0;p<j;p++)
                    rightL = rightL*10 + right[p];
                for(int q=j;q<lenR;q++)
                    rightR = rightR*10 + right[q];
                
                // cout<<rightL<<' '<<rightR<<endl;
                if(leftL == 0) leftL =1;
                if(rightR == 0) rightR = 1;
                int tmp = leftL*(leftR+rightL);
                tmp*=rightR;
                if(tmp<res){
                    res = tmp;
                    idxL = i;
                    idxR = j;
                }
            }
            
        }
        
        
        // for(auto x:left){
        //     cout<<x<<' ';
        // }cout<<endl;
        // for(auto x:right){
        //     cout<<x<<' ';
        // }
        // cout<<res<<endl<<idxL<<' '<<idxR<<endl;
        
        expression.insert(idxL,"(");
        
        idxR = idxR+lenL+2;
        
        expression.insert(idxR,")");
        
        return expression;
    }
};
```

### 偷学代码

```c++
class Solution {
public:
    string minimizeResult(string s) {
        int ans = std::numeric_limits<int>::max();
        int x = s.find('+');
        int n = s.length();
        int I = 0, J = 0;
        for (int i = 0; i < x; i++) {
            for (int j = x + 2; j <= n; j++) {
                int v = (i == 0 ? 1 : std::atoi(s.substr(0, i).c_str())) *
                            (std::atoi(s.substr(i, x - i).c_str()) + std::atoi(s.substr(x + 1, j - x - 1).c_str())) *
                               (j == n ? 1 : std::atoi(s.substr(j, n - j).c_str()));
                if (v < ans) {
                    ans = v;
                    I = i;
                    J = j;
                }
            }
        }
        s.insert(s.begin() + J, ')');
        s.insert(s.begin() + I, '(');
        return s;
    }
};
```

## 【贪心】K 次增加后的最大乘积

### 题目

给你一个非负整数数组 `nums` 和一个整数 `k` 。每次操作，你可以选择 `nums` 中 **任一** 元素并将它 **增加** `1` 。

请你返回 **至多** `k` 次操作后，能得到的 `nums`的 **最大乘积** 。由于答案可能很大，请你将答案对 `10^9 + 7` 取余后返回。

 

**示例 1：**

```
输入：nums = [0,4], k = 5
输出：20
解释：将第一个数增加 5 次。
得到 nums = [5, 4] ，乘积为 5 * 4 = 20 。
可以证明 20 是能得到的最大乘积，所以我们返回 20 。
存在其他增加 nums 的方法，也能得到最大乘积。
```

**示例 2：**

```
输入：nums = [6,3,3,2], k = 2
输出：216
解释：将第二个数增加 1 次，将第四个数增加 1 次。
得到 nums = [6, 4, 3, 3] ，乘积为 6 * 4 * 3 * 3 = 216 。
可以证明 216 是能得到的最大乘积，所以我们返回 216 。
存在其他增加 nums 的方法，也能得到最大乘积。
```

 

**提示：**

- `1 <= nums.length, k <= 10^5`
- `0 <= nums[i] <= 10^6`

### 解题思路

一开始也以为要用动态规划，其实细想一下没那么难

考虑$a\*b\*c\*d$，各个数加1后分别为：

$(a+1)\*b\*c\*d=abcd+bcd$，

$a\*(b+1)\*c\*d=abcd+acd$，

$a\*b\*(c+1)\*d=abcd+abd$，

$a\*b\*c\*(d+1)=abcd+abc$。

很明显，这个1加在最小的数上收益最大（这里的数都为非负数

那么，维护一个最小堆即可

### 代码

```c++
class Solution {
public:
    int maximumProduct(vector<int>& nums, int k) {
        if(nums.size()==1) return nums[0]+k;
        priority_queue<int,vector<int>,greater<int>> q;
        for(auto x:nums) q.push(x);
        int sum =0;
        while(1){
            if(sum==k) break;
            int cur = q.top();q.pop();
            int diff = q.top()-cur;
            if(diff+sum+1<=k) cur = cur + diff +1, sum += diff+1;
            else cur += k-sum, sum = k;
            q.push(cur);
            
        }
        long long res = 1;
        long long mod = 1e9+7;
        while(q.size()){
            res = res*q.top()%mod;
            q.pop();
        }
        return res;
        return 0;
    }
};
```



## 【枚举+二分】花园的最大总美丽值

### 题目

Alice 是 `n` 个花园的园丁，她想通过种花，最大化她所有花园的总美丽值。

给你一个下标从 **0** 开始大小为 `n` 的整数数组 `flowers` ，其中 `flowers[i]` 是第 `i` 个花园里已经种的花的数目。已经种了的花 **不能** 移走。同时给你 `newFlowers` ，表示 Alice 额外可以种花的 **最大数目** 。同时给你的还有整数 `target` ，`full` 和 `partial` 。

如果一个花园有 **至少** `target` 朵花，那么这个花园称为 **完善的** ，花园的 **总美丽值** 为以下分数之 **和** ：

- **完善** 花园数目乘以 `full`.
- 剩余 **不完善** 花园里，花的 **最少数目** 乘以 `partial` 。如果没有不完善花园，那么这一部分的值为 `0` 。

请你返回 Alice 种最多 `newFlowers` 朵花以后，能得到的 **最大** 总美丽值。

 

**示例 1：**

```
输入：flowers = [1,3,1,1], newFlowers = 7, target = 6, full = 12, partial = 1
输出：14
解释：Alice 可以按以下方案种花
- 在第 0 个花园种 2 朵花
- 在第 1 个花园种 3 朵花
- 在第 2 个花园种 1 朵花
- 在第 3 个花园种 1 朵花
花园里花的数目为 [3,6,2,2] 。总共种了 2 + 3 + 1 + 1 = 7 朵花。
只有 1 个花园是完善的。
不完善花园里花的最少数目是 2 。
所以总美丽值为 1 * 12 + 2 * 1 = 12 + 2 = 14 。
没有其他方案可以让花园总美丽值超过 14 。
```

**示例 2：**

```
输入：flowers = [2,4,5,3], newFlowers = 10, target = 5, full = 2, partial = 6
输出：30
解释：Alice 可以按以下方案种花
- 在第 0 个花园种 3 朵花
- 在第 1 个花园种 0 朵花
- 在第 2 个花园种 0 朵花
- 在第 3 个花园种 2 朵花
花园里花的数目为 [5,4,5,5] 。总共种了 3 + 0 + 0 + 2 = 5 朵花。
有 3 个花园是完善的。
不完善花园里花的最少数目为 4 。
所以总美丽值为 3 * 2 + 4 * 6 = 6 + 24 = 30 。
没有其他方案可以让花园总美丽值超过 30 。
注意，Alice可以让所有花园都变成完善的，但这样她的总美丽值反而更小。
```

 

**提示：**

- `1 <= flowers.length <= 10^5`
- `1 <= flowers[i], target <= 10^5`
- `1 <= newFlowers <= 10^10`
- `1 <= full, partial <= 10^5`

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/maximum-total-beauty-of-the-gardens
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路

一开始看题目是求最大值，再看数据范围，有想到用二分，但是想到可能有多种情况，比较麻烦。所以当时没做。

令能得到的最大总美丽值为res，完善花园数目为fullcount，剩下不完善花园中花的最少数目为leastnum，那么有

$$
res = fullcount\*full+least\*partial
$$

其中共有两个未知变量，考虑固定一个值后，从而更方便得到另一个值。

设开始的完善花园个数为count。

这里固定完善花园数目，即枚举完善花园数目（可知范围为[count, length]，然后要最大化least值，即剩下newFlowers数目下不完善花园中最大的最少花数目（经典的最大化最小值问题，可用二分）。

在所有的情况中，选出最大值即可。

#### 最大化最小值

总体思路是这样的，但实现起来有蛮多细节的。

```c++
	bool isok(int C, vector<int>& flowers, long long newFlowers,int target){
        for(auto x:flowers){
            if(x>=target) continue;;
            if(x>=C) continue;
            int need = C-x;
            newFlowers -= need;
            if(newFlowers<0) return false;
        }
        return true;
    }
	
	long long L = flowers.back(), R = target-1;//[]
       
    while(L<=R){
    	int mid = (L+R)>>1;
        if(isok(mid,flowers,newFlowers,target)) L = mid+1;
        else R = mid-1;
    }
	//R 
```

一开始实现的二分里的判定函数如上，isok(C,flowers,newFlowers,target)表示额外可以种花数量为newFlowers时，所有不完善花园的最少花数目为C时是否可行。

正确性应该没错，后面执行时发现了问题——超时，在isok函数里对flowers数组进行了一组遍历，消耗了O(n)时间。所以判定函数需要更换思路。

——利用前缀和。

判定思路其实也就是**将不完善花园中少于C的花园中花的数目全部补充到C时，需要额外增添花的数目是否小于newFlowers**

所以先将flowers排序后，计算好前缀和，以方便计算。

```c++
bool isok(int C,vector<int>& flowers,long long newFlowers){
        int idx = upper_bound(flowers.begin(),flowers.end(),C,greater<int>())-flowers.begin();//first less pos
        if(idx==presum.size()) return true;
        long long cur = presum.back();
        if(idx>0) cur-=presum[idx-1];
        long long need = C*(flowers.size()-idx);
        if(cur+newFlowers>=need) return true;
        else return false;
}
```

上述代码中presum数组为flowers数组非递增排序后的前缀和数组。

首先在flowers数组中找到第一个小于C的位置idx（upper_bound默认是找第一个大于的值，所以需要增添第4个参数），如果没找到这样的位置，即flowers数组中的值都大于C，所以满足条件，返回true；

否则的话需要计算额外补充的花的数目是否足够，即cur+newFlowers是否大于等于need。

#### 注意数据范围！

注意数据范围，int的最大值约为$2*10^9$，而题中的数据范围最大值达到了$10^5$，两项相乘是可能溢出的，所以将需要的地方都定义成long long。

特别注意：

```c++
long long a;
int b,c;
a = b*c;//No!! may overflow
a = 1LL*b*c;//Yes!
```

### 代码

修修改改，写的挺繁琐的。

主要代码逻辑是：

1）将flowers非递增排序并计算其前缀和数组presum，以方便二分判定。

2）计算未额为补充花时，已经完善花园的数目，并且特判，计算当全部为完善花园数目时的答案（此时就不用再枚举未完善花园中的最少花的数目）

3）接下来需要枚举新增完善花园的数目，我在这里也特判了一下，即当新增完善花园数目为0时；其余就在for循环里枚举。还有个特判是枚举到将所有花园都完善后，未完善部分就是0。

4）固定完善花园数目后，就开始二分得到未完善花园中最少花数目的最大值

5）所有情况中取最大的res

```c++
class Solution {
public:
    vector<long long> presum;
    bool isok(int C,vector<int>& flowers,long long newFlowers){
        int idx = upper_bound(flowers.begin(),flowers.end(),C,greater<int>())-flowers.begin();//first less pos
        if(idx==presum.size()) return true;
        long long cur = presum.back();
        if(idx>0) cur-=presum[idx-1];
        long long need = C*(flowers.size()-idx);
        if(cur+newFlowers>=need) return true;
        else return false;
    }
    
    long long maximumBeauty(vector<int>& flowers, long long newFlowers, int target, int full, int partial) {
        int length = flowers.size();
        sort(flowers.begin(),flowers.end(),greater<int>());//descend
        int count = 0;
        for(int i=0;i<length;i++){
            if(flowers[i]>=target) count++;
        }
        presum.push_back(flowers[0]);
        for(int i=1;i<length;i++) presum.push_back(flowers[i]+presum.back());

        long long res;
        if(count==length) {
            res = 1LL*length*full;//all full initially
            return res;
        }
        
        res = count*full;
        long long L = flowers.back(), R = target-1;//[]
       
        while(L<=R){
            int mid = (L+R)>>1;
            if(isok(mid,flowers,newFlowers)) L=mid+1;
            else R=mid-1;
        }
        
        res += R*partial;
        
        for(int i=1;i<=length-count;i++){//add new full garden
            //enumerate the count of full garden
            int fullcount = count+i;
            int need = target-flowers[count+i-1];
            newFlowers -= need;
            flowers[count+i-1] = target;
            if(newFlowers<0) break;
            if(i==length-count){
                res=res<1LL*length*full?1LL*length*full:res;
                break;
            }
            
            long long left=flowers.back(),right=target-1;
            while(left<=right){
                int mid=(left+right)>>1;
                if(isok(mid,flowers,newFlowers)) left=mid+1;
                else right=mid-1;
            }
            
            long long cur = 1LL*fullcount*full+right*partial;
            res = max(res,cur);
        }

        return res;
    }
};
```

### 偷学代码

该场比赛第1的代码，短小精炼

一开始有个预处理，即flowers数组中大于target的部分零其等于target。

```c++
using i64 = long long;
class Solution {
public:
    long long maximumBeauty(vector<int>& a, long long k, int T, int F, int P) {
        int n = a.size();
        std::sort(a.begin(), a.end());
        for (int i = 0; i < n; i++) {
            a[i] = std::min(a[i], T);
        }
        
        i64 ans = 0;
        std::vector<i64> sum(n + 1);
        for (int i = 0; i < n; i++) {
            sum[i + 1] = sum[i] + a[i];
        }
        
        for (int i = 0, j = 0; i <= n; i++) {
            i64 K = k - (1LL * T * (n - i) - (sum[n] - sum[i]));
            if (K >= 0) {
                while (j < i && K >= 1LL * a[j] * j - sum[j]) {
                    j++;
                }
                ans = std::max(ans, 1LL * F * (n - i) + (j == 0 ? 0LL : 1LL * P * std::min(T - 1LL, (K + sum[j]) / j)));
            }
            
            if (i < n && a[i] == T) {
                break;
            }
        }
        
        return ans;
    }
};
```

