---
title: 剑指 Offer 06. 从尾到头打印链表
date: 2022-07-28 14:25:03
tags:
- 链表
- Leetcode
---

### 1.题目

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

**示例 1：**

```shell
输入：head = [1,3,2]
输出：[2,3,1]
```

### 2.思路

大体思路其实就是先反转链表再输入进数组返回，反转链表之前写过

> http://www.hi-hufei.com/post/%E5%8F%8D%E8%BD%AC%E9%93%BE%E8%A1%A8

### 3.实现

```java
    public static int[] reversePrint(ListNode head) {
        ListNode curNode = head;
        ListNode preNode = null;
        int len = 0 ;
        // 1 3 2
        while(curNode!=null){
            ListNode nextNode = curNode.next;
            curNode.next=preNode;
            preNode = curNode;
            curNode = nextNode;
            len++;
        }
        int[]ints = new int[len];
        int index = 0;
        while(preNode!=null){
            ints[index] = preNode.val;
            preNode = preNode.next;
            index++;
        }
        return ints;
    }
```

