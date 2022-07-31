---
title: 剑指 Offer 22. 链表中倒数第k个节点
date: 2022-07-31 21:41:52
tags:
- 链表
- 双指针
- 剑指Offer
- Leetcode
---

### 1.题目

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。

**示例：**

```shell
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```

### 2.思路

- 递归

递归的特点是从后往前打印此时我们创建index=0 当index==k那么就表示找到了

- 快慢指针

快指针先走到k位置，让快指针与慢指针之间间隔的距离始终保持K，当快指针走完时慢指针所在的位置就是k的位置

- 迭代索引

这个最简单得出链表长度 再用链表长度length-k得出我们需要k的位置

### 3.代码实现

#### 3.1递归

```java
		int index = 0;
    public ListNode getKthFromEnd2(ListNode head, int k) {
        if(head==null){
            return head;
        }
        // res会存储 index==k时断开的节点
        ListNode res = getKthFromEnd2(head.next,k);
        index++;
        return index == k ? head : res;
    }
```

上述代码我个人觉得不好理解的地方在 index==K返回哪个，因为递归的特点如果每次都返回head，那么会导致这个链表最终返回时这个链表是完整的返回 没有在我们想要k地方断开，所以我用了一个res去存储 index==k的情况这样 递归在其他情况时返回的结果都是index==k的结果直到返回最外层。

#### 3.2快慢指针

```java
public ListNode getKthFromEnd4(ListNode head, int k) {
        ListNode fastNode = head;
        ListNode slowNode = head;
        while(fastNode!=null && k > 0){
            fastNode=fastNode.next;
            k--;
        }
        while(fastNode!=null){
            fastNode=fastNode.next;
            slowNode=slowNode.next;
        }
        return slowNode;
    }
```

时间复杂度O(n) 三种中效率最高

#### 3.3迭代

```java
public ListNode getKthFromEnd3(ListNode head, int k) {
        int len = 0;
        ListNode curNode = head;
        while (curNode != null) {
            curNode = curNode.next;
            len++;
        }
        for (int i = 0; i < len && head != null; ++i) {
            if (i == (len - k)) {
                return head;
            }
            head = head.next;
        }
        return null;
    }


// 优化得到如下
public ListNode getKthFromEnd3(ListNode head, int k) {
        int len = 0;
        ListNode curNode = head;
        while(curNode != null){
            curNode = curNode.next;
            len++;
        }
        for(;head!= null;--len){
            if(len<=k){
                return head;
            }
            head = head.next;
        }
        return null;
    }
```

