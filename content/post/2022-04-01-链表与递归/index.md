---
title:      链表与递归
description:   链表递归——从后到前的迭代
date:       2022-04-01 00:00:00+0000
image: 20220401.jpg
categories:
    - data structure and algorithm
tags:
    - 链表
    - 递归
---

## 引言

链表的定义具有递归的性质，因此链表题目常可以用递归的方法求解。

链表给的通常是头节点，迭代通常是从头到尾，而递归则是从尾到头。

> **明白一个函数的作用并相信它能完成这个任务，千万不要跳进这个函数里面企图探究更多细节，** 否则就会陷入无穷的细节无法自拔，人脑能压几个栈啊。

写递归感觉和归纳法类似，注意边界，以及第n层到第n+1层间的传递关系。

## 移除链表元素

### 题目

给你一个链表的头节点 head 和一个整数 val ，请你删除链表中所有满足 Node.val == val 的节点，并返回 新的头节点 。

示例 1：

![img](https://assets.leetcode.com/uploads/2021/03/06/removelinked-list.jpg)

输入：head = [1,2,6,3,4,5,6], val = 6
输出：[1,2,3,4,5]

示例 2：

输入：head = [], val = 1
输出：[]

示例 3：

输入：head = [7,7,7,7], val = 7
输出：[]


提示：

列表中的节点数目在范围 [0, 104] 内
1 <= Node.val <= 50
0 <= val <= 50

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/remove-linked-list-elements
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路及代码

#### 递归

考虑边界，当头节点为空时，直接返回空；

考虑当前层和下一层的关系，这时需要明确该递归函数的作用，并且相信它能完成：返回移除完val值后链表的头节点。所以关系为当前层的头节点的next指针要指向下一层的头节点。

当前层要干什么呢，移除元素，当前层的值为val时，需要移除元素，所以返回下一个元素即head->next；否则不需要移除，返回原节点head。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        if(head==nullptr) return head;
        head->next = removeElements(head->next, val);
        if(head->val == val) return head->next;
        else return head;
    }
};
```

#### 迭代

新增一个哑节点，以避免对头节点的分类讨论

```c++
class Solution {
public:
    ListNode* removeElements(ListNode* head, int val) {
        struct ListNode* dummyHead = new ListNode(0, head);
        struct ListNode* temp = dummyHead;
        while (temp->next != NULL) {
            if (temp->next->val == val) {
                temp->next = temp->next->next;
            } else {
                temp = temp->next;
            }
        }
        return dummyHead->next;
    }
};
```



## 反转链表

### 题目

给你单链表的头节点 head ，请你反转链表，并返回反转后的链表。

示例 1：

![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg)

输入：head = [1,2,3,4,5]
输出：[5,4,3,2,1]
示例 2：

![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex2.jpg)


输入：head = [1,2]
输出：[2,1]
示例 3：

输入：head = []
输出：[]


提示：

链表中节点的数目范围是 [0, 5000]
-5000 <= Node.val <= 5000


进阶：链表可以选用迭代或递归方式完成反转。你能否用两种方法解决这道题？

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/reverse-linked-list
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路及代码

#### 递归

考虑边界，和上一题不同的是，也要判断一下head->next是否为空。可以这样想，后面出现了head->next->next，如果head>next为空的话，会报错。

考虑当前层和下一层的关系，需要将当前头节点和头节点下一节点的指向倒转过来，注意要将head->next置为NULL（考虑边界，如果不置为NULL，则会形成环）。

返回的是倒转后的新头节点。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(head==nullptr|| head->next==nullptr) return head;
        ListNode* newHead = reverseList(head->next);
        head->next->next = head;
        head->next = nullptr;
        return newHead;
    }
};
```

#### 迭代

此时头节点并没有特殊情况，所以不需要加哑节点。

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr) {
            ListNode* next = curr->next;
            curr->next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
};

```

## 两两交换链表中的节点

### 题目

给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。

 

示例 1：

![img](https://assets.leetcode.com/uploads/2020/10/03/swap_ex1.jpg)

输入：head = [1,2,3,4]
输出：[2,1,4,3]
示例 2：

输入：head = []
输出：[]
示例 3：

输入：head = [1]
输出：[1]


提示：

链表中节点的数目在范围 [0, 100] 内
0 <= Node.val <= 100

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/swap-nodes-in-pairs
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路及代码

#### 递归

考虑边界，0个节点或1个节点，原地返回。

考虑当前层和下一层的关系，由于要交换两个节点，所以先存下当前节点的下一个节点rev，倒转指针朝向，返回rev。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if(head==nullptr||head->next==nullptr) return head;
        ListNode* rev = head->next;
        head->next = swapPairs(rev->next);
        rev->next = head;
        return rev;
    }
};
```

#### 迭代



```c++
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        ListNode* dummyHead = new ListNode(0,head);
        ListNode* temp = dummyHead;
        while (temp->next != nullptr && temp->next->next != nullptr) {
            ListNode* node1 = temp->next;
            ListNode* node2 = temp->next->next;
            temp->next = node2;
            node1->next = node2->next;
            node2->next = node1;
            temp = node1;
        }
        return dummyHead->next;
    }
};
```

## 删除链表的倒数第N个节点

### 题目

给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

 

示例 1：

![img](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)


输入：head = [1,2,3,4,5], n = 2
输出：[1,2,3,5]
示例 2：

输入：head = [1], n = 1
输出：[]
示例 3：

输入：head = [1,2], n = 1
输出：[1]


提示：

链表中结点的数目为 sz
1 <= sz <= 30
0 <= Node.val <= 100
1 <= n <= sz

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路及代码

可以计算链表长度，以方便找出倒数第N个节点。

可以使用栈，先进先出，以方便找出倒数第N个节点。

可以使用双指针，一个指针先走N个节点，及它们间的距离为N，然后两个指针同时移动，当第一个指针到末尾的时候，说明第二个指针到了倒数第N个节点。

#### 迭代（双指针）

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummy = new ListNode(-1, head);
        ListNode *p = dummy, *q = dummy;
        for(int i=0;i<n;i++) {
            q = q->next;
        }
        while(q ->next!=nullptr){
            p = p->next;
            q = q->next;
        }

        p->next = p->next->next ;

        return dummy->next;

    }
};
```

#### 递归（全局变量）

还可以使用递归，不过与前面几题不同的是，需要一个全局变量，以表示倒数第几个节点。

当记录反向深度的变量re_dl为n时，返回head->next，说明删去了倒数第N个节点。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    int re_dl = 0;//记录反向深度
    ListNode* removeNthFromEnd(ListNode* head, int n) {
       if(head==nullptr) return head;
       head->next = removeNthFromEnd(head->next,n);
       re_dl++;//在递归调用后累加，这样才是反向记录
       if(re_dl==n) return head->next;
       else return head;
    }
};
```

## 回文链表

### 题目

编写一个函数，检查输入的链表是否是回文的。

 

示例 1：

输入： 1->2
输出： false 
示例 2：

输入： 1->2->2->1
输出： true 


进阶：
你能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/palindrome-linked-list-lcci
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路及代码

可以将值复制到数组中后用双指针法；

可以利用队列和栈进行比较；

#### 递归

递归反向迭代节点，同时使用递归函数外的变量向前迭代，就可以判断链表是否为回文

（为了更方便的初始化，可以另设一个递归函数）

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* front=nullptr;
    bool isPalindrome(ListNode* head) {
        if(!front) front = head;
        if(head==nullptr) return true;
        bool nxt = isPalindrome(head->next);
        if(front->val==head->val) {
            front = front->next;
            return nxt && true;
        }
        else return false;
    }
};
```

再看看官方的

```c++
class Solution {
    ListNode* frontPointer;
public:
    bool recursivelyCheck(ListNode* currentNode) {
        if (currentNode != nullptr) {
            if (!recursivelyCheck(currentNode->next)) {
                return false;
            }
            if (currentNode->val != frontPointer->val) {
                return false;
            }
            frontPointer = frontPointer->next;
        }
        return true;
    }

    bool isPalindrome(ListNode* head) {
        frontPointer = head;
        return recursivelyCheck(head);
    }
};
```

#### 快慢指针

避免使用 O(n)O(n) 额外空间的方法就是改变输入。

我们可以将链表的后半部分反转（修改链表结构），然后将前半部分和后半部分进行比较。比较完成后我们应该将链表恢复原样。虽然不需要恢复也能通过测试用例，但是使用该函数的人通常不希望链表结构被更改。


整个流程可以分为以下五个步骤：

1. 找到前半部分链表的尾节点。
2. 反转后半部分链表。
3. 判断是否回文。
4. 恢复链表。
5. 返回结果。

执行步骤一，我们可以计算链表节点的数量，然后遍历链表找到前半部分的尾节点。

我们也可以**使用快慢指针在一次遍历中找到：慢指针一次走一步，快指针一次走两步，快慢指针同时出发。当快指针移动到链表的末尾时，慢指针恰好到链表的中间。通过慢指针将链表分为两部分。**

若链表有奇数个节点，则中间的节点应该看作是前半部分。

作者：LeetCode-Solution
链接：https://leetcode-cn.com/problems/palindrome-linked-list-lcci/solution/hui-wen-lian-biao-by-leetcode-solution-6cp3/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```c++
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        if (head == nullptr) {
            return true;
        }

        // 找到前半部分链表的尾节点并反转后半部分链表
        ListNode* firstHalfEnd = endOfFirstHalf(head);
        ListNode* secondHalfStart = reverseList(firstHalfEnd->next);

        // 判断是否回文
        ListNode* p1 = head;
        ListNode* p2 = secondHalfStart;
        bool result = true;
        while (result && p2 != nullptr) {
            if (p1->val != p2->val) {
                result = false;
            }
            p1 = p1->next;
            p2 = p2->next;
        }        

        // 还原链表并返回结果
        firstHalfEnd->next = reverseList(secondHalfStart);
        return result;
    }

    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr != nullptr) {
            ListNode* nextTemp = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }

    ListNode* endOfFirstHalf(ListNode* head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast->next != nullptr && fast->next->next != nullptr) {
            fast = fast->next->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

## 反转链表II

### 题目

给你单链表的头指针 head 和两个整数 left 和 right ，其中 left <= right 。请你反转从位置 left 到位置 right 的链表节点，返回 反转后的链表 。

示例 1：

![img](https://assets.leetcode.com/uploads/2021/02/19/rev2ex2.jpg)


输入：head = [1,2,3,4,5], left = 2, right = 4
输出：[1,4,3,2,5]
示例 2：

输入：head = [5], left = 1, right = 1
输出：[5]


提示：

链表中节点数目为 n
1 <= n <= 500
-500 <= Node.val <= 500
1 <= left <= right <= n


进阶： 你可以使用一趟扫描完成反转吗？

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/reverse-linked-list-ii
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

### 解题思路及代码

一个简单的思路是先遍历一遍，找出要反转的链表区间，然后再使用反转链表的方法反转。

如果只遍历一遍的话，则需使用头插法

#### 头插法

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode *reverseBetween(ListNode *head, int left, int right) {
        
        ListNode *dummyNode = new ListNode(-1,head);
        ListNode *pre = dummyNode;
        for (int i = 0; i < left - 1; i++) {
            pre = pre->next;
        }//pre为待反转链表左区间的前驱节点，在后续头插法中保持不变
        ListNode *first = pre->next;//待反转链表的左区间
        ListNode *second;
        for (int i = 0; i < right - left; i++) {
            second = first->next;
            first->next = second->next;
            second->next = pre->next;//头插
            pre->next = second;
        }
        return dummyNode->next;
    }
};
```

#### 递归

 [如何递归反转链表-知乎](https://zhuanlan.zhihu.com/p/86745433)

##### 反转前N个节点

首先考虑当头节点为左边界的情况，此时就是反转前N个节点。

```c++
ListNode *reverseFN(ListNode *head, int n){
        if(n==1){//此处为边界，剩余的链表部分不会遍历到
            successor = head->next;//右边界的后驱节点
            return head;
        }
        ListNode* newHead = reverseFN(head->next,n-1);
        head->next->next = head;
        head->next = successor;//注意这里，递归是后向前迭代的，所以中间的赋值会被覆盖，只有最后会生效
        return newHead;
}
```

##### 由前N个节点到[left,right]

首先，left=1时，即上面这种情况。

left!=1时：

- 如果把head的索引视为1，从第left个元素开始反转；
- 如果把head.next的索引视为1，那么就是从第left-1个元素开始反转的。（此时，右边界也变为了right-1）

```c++
ListNode *reverseBetween(ListNode *head, int left, int right) {
        if(left==1) return reverseFN(head,right);
        head->next = reverseBetween(head->next,left-1,right-1);
        return head;
}
```









