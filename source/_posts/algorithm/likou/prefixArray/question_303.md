---
title: leetcode-question_303
cover: https://images.xianhu.wang/2022081901.jpg
date:  2022-08-19 17:10:10
updated: 2022-08-19 17:15:10
tags:
  - 算法-leetcode
categories:
  - 算法-leetcode
---

{% note red 'fas fa-fan' disabled %} ## 题目{% endnote %}

{% note red  'fas -fa-fan' flat%} 
计算索left和right（包含 left 和 right）之间的 nums 元素的 和 ，其中left <= right
实现 NumArray 类：
NumArray(int[] nums) 使用数组 nums 初始化对象
int sumRange(int i, int j) 返回数组 nums中索引left和right之间的元素的 总和 ，包含left和right两点（也就是nums[left] + nums[left + 1] + ... + nums[right] )
来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/range-sum-query-immutable
 * 输入：
 * ["NumArray", "sumRange", "sumRange", "sumRange"]
 * [[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]
 * 输出：
 * [null, 1, -1, -3]
 * 解释：
 * NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
 * numArray.sumRange(0, 2); // return 1 ((-2) + 0 + 3)
 * numArray.sumRange(2, 5); // return -1 (3 + (-5) + 2 + (-1))
 * numArray.sumRange(0, 5); // return -3 ((-2) + 0 + 3 + (-5) + 2 + (-1))
{% endnote %}

{% note red 'fas fa-fan' disabled %} ## 题解{% endnote %}

```java
public class question_303 {
    public static void main(String[] args) {
        int[] num = {-2, 0, 3, -5, 2, -1};
        NumArray numArray = new NumArray(num);
        int result = numArray.sumRange(2, 5);
        System.out.println("result=" + result);

        NumArray2 numArray2 = new NumArray2(num);
        int result2 = numArray2.sumRange(2, 5);
        System.out.println("result2=" + result2);

        NumArray3 numArray3 = new NumArray3(num);
        int result3 = numArray3.sumRange(2, 5);
        System.out.println("result3=" + result3);
    }

}

/**
 * 解法三
 */
class NumArray3 {

    private int[] tempNum;

    public NumArray3(int[] num) {
        tempNum = new int[num.length];
        tempNum[0] = num[0];
        for (int i = 1; i < num.length; i++) {
            tempNum[i] = tempNum[i - 1] + num[i];
        }
        System.out.println(Arrays.toString(tempNum));
    }

    public int sumRange(int left, int right) {
        if (left == 0) {
            return tempNum[right];
        } else {
            return tempNum[right] - tempNum[left - 1];
        }
    }
}

/**
 * 解法二
 */
class NumArray2 {

    private int[] tempNum;

    public NumArray2(int[] num) {
        tempNum = new int[num.length + 1];
        for (int i = 1; i <= num.length; i++) {
            tempNum[i] = tempNum[i - 1] + num[i - 1];
        }
        System.out.println(Arrays.toString(tempNum));
    }

    public int sumRange(int left, int right) {
        return tempNum[right + 1] - tempNum[left];
    }
}


/**
 * 解法一
 */
class NumArray {
    private int[] num;

    public NumArray(int[] num) {
        this.num = num;
    }

    public int sumRange(int left, int right) {
        int result = 0;
        for (int i = left; i <= right; i++) {
            result += num[i];
        }
        return result;
    }
}
```
