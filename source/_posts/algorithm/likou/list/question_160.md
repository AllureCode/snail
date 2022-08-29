---
title: leetcode-question_160
cover: https://images.xianhu.wang/20220824.jpg
date:  2022-08-29 14:09:00
updated: 2022-08-29 14:09:10
tags:
  - 算法-leetcode
categories:
  - 算法-leetcode
---

{% note red 'fas fa-fan' disabled %} ## 题目{% endnote %}

{% note red  'fas -fa-fan' flat%} 
 * 给你两个单链表的头节点headA 和 headB ，请你找出并返回两个单链表相交的起始节点。
 * 如果两个链表不存在相交节点，返回 null 。
 * 输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,6,1,8,4,5], skipA = 2, skipB = 3
 * 输出：Intersected at '8'
 * 解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。
 * 从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,6,1,8,4,5]。
 * 在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
 * <p>
 * 来源：力扣（LeetCode）
 * 链接：https://leetcode.cn/problems/intersection-of-two-linked-lists
 * 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
 *
 * @author xianhu.wang
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 题解{% endnote %}

```java
    /**
     * 遍历两个链表 为了使得两个链表会最终在相交点相遇
     * 则同时遍历两个链表
     * 第一个链表遍历完成后遍历第二个链表
     * 第二个链表遍历完成后遍历第一个链表
     * 这样做可以使得两个链表在相交点相遇（如果存在相交点）
     * listA = [4,1,8,4,5], listB = [5,6,1,8,4,5]
     * 4 1 8 4 5 5 6 1 8 4 5
     * 5 6 1 8 4 5 4 1 8 4 5
     *
     * @param headA
     * @param headB
     * @return
     */
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode p1 = headA;
        ListNode p2 = headB;

        while (p1 != p2) {
            if (p1 == null) {
                p1 = headB;
            } else {
                p1 = p1.next;
            }
            if (p2 == null) {
                p2 = headA;
            } else {
                p2 = p2.next;
            }
        }
        // 循环结束如果p1!=null 则相交 否则 不相交
        return p1;
    }
```
