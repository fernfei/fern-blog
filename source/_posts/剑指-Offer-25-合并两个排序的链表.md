---
title: 剑指 Offer 25. 合并两个排序的链表
date: 2022-07-30 20:59:03
tags:
- 链表
- Leetcode
- 剑指Offer
---

### 1.题目

输入两个递增排序的链表，合并这两个链表并使新链表中的节点仍然是递增排序的。

**示例1：**

```shell
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

**限制：**

`0 <= 链表长度 <= 1000`

### 2.思路

- 递归

假设有这样两个有序链表`[1,1,1]`,`[2,3,4]`;先考虑递归边界假设如果是这样两种情况的链表

情况1: `[]`、`[1]`此时边界就是 如果`l1链表为空`则不需要排序直接返回`链表2`

情况2:同情况1

那么此时边界就得出了 `if(l1==null)return l2` 、`if(l2==null) return l1`

我先说说我之前设计这个递归算法时走的误区，那就是`mergeTwoLists(l1.next,l2.next)`这种写法的问题没有考虑的`[1,1,1]`的情况如果`[1]、[2]`同时向后移动那么此时`l1下个元素可能还会小于[2]`所以应该是 `mergeTwoLists(l1.next,l2)`让l1的每个元素都与l2链表进行比较，如果l1>l2那么此时`mergeTwoLists(l1,l2.next)`。阅读递归算法应该从后向前阅读，for example : `[1]、[4]` ;1<4  1.next==null return l2链表 此时会一层一层往上触发 1.next = l2

- 迭代

引用leetcode的图，迭代时 l1<l2比较 如果小于 则 l1指针向后移动，反之l2向后移 

`[1,1,6]、[2,3,4,5]`  preHead头节点 -1本身无含义只是占位 prev存储 l1和l2两个节点移动的位置



![img](http://image.hi-hufei.com/typora/35.png)

### 3.实现

#### 3.1递归实现

```java
    public ListNode mergeTwoLists2(ListNode l1, ListNode l2) {
        //[1,1,1]
        //[2,3,4]
        if (l1 == null) {
            return l2;
        } else if (l2 == null) {
            return l1;
        } else if (l1.val < l2.val) {
            l1.next = mergeTwoLists2(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists2(l1, l2.next);
            return l2;
        }
    }
```

#### 3.2迭代实现

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        //[1,1,6]
        //[2,3,4,5]
        ListNode headNode = new ListNode(-1);
        ListNode preNode = headNode;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                preNode.next = l1;
                l1 = l1.next;
            } else {
                preNode.next = l2;
                l2 = l2.next;
            }
            preNode = preNode.next;
        }
  			// 5<6 此时6没有被存储preNode，只需要判断l1和l2没遍历完就把其元素存入
        preNode.next = l1 != null ? l1 : l2;
        return headNode.next;
    }
```

