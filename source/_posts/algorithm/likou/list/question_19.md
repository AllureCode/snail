---
title: leetcode-question_19
cover: https://images.xianhu.wang/20220824.jpg
date:  2022-08-24 14:25:00
updated: 2022-08-24 17:25:10
tags:
  - 算法-leetcode
categories:
  - 算法-leetcode
---

{% note red 'fas fa-fan' disabled %} ## 题目{% endnote %}

{% note red  'fas -fa-fan' flat%} 
 * 给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。
 * <p>
 * 输入：head = [1,2,3,4,5], n = 2
 * 输出：[1,2,3,5]
 * 输入：head = [1], n = 1
 * 输出：[]
 * 输入：head = [1,2], n = 1
 * 输出：[1]
 *
 * @author xianhu.wang
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 题解{% endnote %}

```java
/**
     * 先找到倒数第n个节点 使得该节点前一个节点指向该节点后一个节点
     *
     * @param head
     * @param n
     * @return
     */
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if (head == null) {
            return null;
        }

        ListNode p2 = head;
        ListNode p1 = head;
        
        for (int i = 0; i < n; i++) {
            p2 = p2.next;
        }
        if (p2 == null) {
            return head.next;
        }
        while (p2.next != null) {
            p2 = p2.next;
            p1 = p1.next;
        }
        p1.next = p1.next.next;

        return head;
    }
```
