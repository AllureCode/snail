---
title: leetcode-question_23
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
 * 给你一个链表数组，每个链表都已经按升序排列。
 * <p>
 * 请你将所有链表合并到一个升序链表中，返回合并后的链表。
 * <p>
 * 输入：lists = [[1,4,5],[1,3,4],[2,6]]
 * 输出：[1,1,2,3,4,4,5,6]
 * 解释：链表数组如下：
 * [
 * 1->4->5,
 * 1->3->4,
 * 2->6
 * ]
 * 将它们合并到一个有序链表中得到。
 * 1->1->2->3->4->4->5->6
 * <p>
 * 来源：力扣（LeetCode）
 * 链接：https://leetcode.cn/problems/merge-k-sorted-lists
 * 著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
 *
 * @author xianhu.wang
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 题解{% endnote %}

```java
/**
/**
     * 使用二叉堆优先级队列实现
     * 使用最小堆存放元素 确保每次出队元素都是最小的
     *
     * @param lists
     * @return
     */
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists.length == 0) {
            return null;
        }
        PriorityQueue<ListNode> queue = new PriorityQueue<>(
            lists.length, (a, b) -> a.val - b.val);
        // 遍历链表放入queue中
        for (ListNode listNode : lists) {
            if (null != listNode) {
                queue.add(listNode);
            }
        }
        //从队列中取出元素组建新链表
        ListNode head = new ListNode(-1);
        ListNode temp = head;

        while (!queue.isEmpty()) {
            ListNode node = queue.poll();
            temp.next = node;
            if (node.next != null) {
                // 链表后续元素
                queue.add(node.next);
            }
            temp = temp.next;
        }
        return head.next;
    }
```
