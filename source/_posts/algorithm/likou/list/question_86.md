---
title: leetcode-question_86
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
 * 给你一个链表的头节点 head 和一个特定值 x ，
 * 请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。
 * <p>
 * 你应当 保留 两个分区中每个节点的初始相对位置。
 * <p>
 * 来源：力扣（LeetCode）
 * 链接：https://leetcode.cn/problems/partition-list
 * 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
 * <p>
 * 输入：head = [1,4,3,2,5,2], x = 3
 * 输出：[1,2,2,4,3,5]
 * 输入：head = [2,1], x = 2
 * 输出：[1,2]
 *
 * @author xianhu.wang
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 题解{% endnote %}

```java
public ListNode partition(ListNode head, int x) {
        // p1 记录大于等于x之前的原始
        ListNode p1 = new ListNode(-1);
        // p2 记录小于x之前的原始
        ListNode p2 = new ListNode(-1);

        ListNode p11 = p1;
        ListNode p22 = p2;

        // 指向头节点 遍历原始链表
        ListNode p = head;

        while (p != null) {
            if (p.val < x) {
                p11.next = p;
                p11 = p11.next;
            } else {
                p22.next = p;
                p22 = p22.next;
            }
//            p = p.next;
            // 断开原始节点 否则合并链表时会出现环
            ListNode temp = p.next;
            p.next = null;
            p = temp;
        }
        p22.next = p1.next;
        return p2.next;
    }
```
