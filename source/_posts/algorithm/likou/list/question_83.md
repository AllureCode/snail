---
title: leetcode-question_83
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
  * 给定一个已排序的链表的头 head ， 删除所有重复的元素，使每个元素只出现一次 。
 * 返回 已排序的链表
 * 输入：head = [1,1,2]
 * 输出：[1,2]
 *
 * @author xianhu.wang
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 题解{% endnote %}

```java
public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }
        ListNode slow = head;
        ListNode fast = head;

        while (fast != null) {
            if (fast.val != slow.val) {
                slow.next = fast;
                slow = slow.next;
            }
            fast = fast.next;
        }
        slow.next = null;
        return head;
    }
```
