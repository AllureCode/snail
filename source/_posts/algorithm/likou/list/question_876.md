---
title: leetcode-question_876
cover: https://images.xianhu.wang/20220824.jpg
date:  2022-08-24 19:28:00
updated: 2022-08-24 19:28:10
tags:
  - 算法-leetcode
categories:
  - 算法-leetcode
---

{% note red 'fas fa-fan' disabled %} ## 题目{% endnote %}

{% note red  'fas -fa-fan' flat%} 
 * 给定一个头结点为 head 的非空单链表，返回链表的中间结点。
 * 如果有两个中间结点，则返回第二个中间结点。
 * <p>
 * 输入：[1,2,3,4,5]
 * 输出：此列表中的结点 3 (序列化形式：[3,4,5])
 * 返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
 * 注意，我们返回了一个 ListNode 类型的对象 ans，这样：
 * ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
 * <p>
 * 来源：力扣（LeetCode）
 * 链接：https://leetcode.cn/problems/middle-of-the-linked-list
 * 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
 *
 * @author xianhu.wang
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 题解{% endnote %}

```java
public ListNode middleNode(ListNode head) {
        // 快慢指针
        ListNode fast = head;
        ListNode slow = head;

        // 慢指针移动一位 快指针移动两位
        // 当快指针移动到链表尾部，则慢指针指向的节点就是满足要求的节点（中间节点）
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
```
