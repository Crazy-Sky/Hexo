---
title: 剑指 Offer 03. 数组中重复的数字
date: 2021-04-07 18:07:00
tag: [剑指 Offer,搜索]
categories: LeetCode
toc: true
thumbnail: /thumbnails/LeetCode/leetcode.png
---

leetcode 剑指 Offer 03. 数组中重复的数字

<!--more-->

---

**题目：[剑指 Offer 03. 数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)**

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

**示例1:**

> 输入: [2, 3, 1, 0, 2, 5, 3]
> 输出: 2 或 3

**限制:**

* 2 <= n <= 100000

## 方法一：利用Set的特性

**思路：**利用Set的特性。

**运行数据：**执行用时：5 ms，内存消耗：48.2 MB

**复杂度分析：**

* 时间复杂度：O(n)，n为数组长度。
* 空间复杂度：O(n)，n为数组长度。

```java
// LeetCode指定调用方法 
public int findRepeatNumber(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int num : nums) {
        if (!set.add(num)) {
            return num;
        }
    }
    return -1;
}
```

## 方法二：排序

**思路：**排序后比较前后的元素。

**运行数据：**执行用时：3 ms，内存消耗：46.1 MB

**复杂度分析：**

* 时间复杂度：O(n)，n为数组长度。
* 空间复杂度：O(n)，n为数组长度。

```java
// LeetCode指定调用方法 
public int findRepeatNumber(int[] nums) {
    Arrays.sort(nums);
    for (int i = 1; i < nums.length; i++) {
        if (nums[i] == nums[i - 1]) {
            return nums[i];
        }
    }
    return -1;
}
```

## 方法三：原地交换

**思路：**原地交换。根据题目可知如果不存在重复的数字，则nums[i] == i。遍历nuns如果i位置的数字nums[i]不等于i，则将数字nums[i]与它对应位置的数nums[nums[i]]交换，如果nums[i] == nums[nums[i]]，则找到了重复的数字，如果nums[i] == i，则i++，继续遍历找到下一个数字nums[i]不等于i的。

**运行数据：**执行用时：0 ms，内存消耗：46.4 MB

**复杂度分析：**

* 时间复杂度：O(n)，n为数组长度。
* 空间复杂度：O(1)。

```java
// LeetCode指定调用方法 
public int findRepeatNumber(int[] nums) {
    int i = 0;
    while (i < nums.length) {
        if (nums[i] == i) {
            i++;
        } else {
            if (nums[i] == nums[nums[i]]) {
                return nums[i];
            }
            int temp = nums[i];
            nums[i] = nums[temp];
            nums[temp] = temp;
        }
    }
    return -1;
}
```

---

**学习所得，资料、图片部分来源于网络，如有侵权，请联系本人删除。**

**才疏学浅，若有错误或不当之处，可批评指正，还请见谅！**