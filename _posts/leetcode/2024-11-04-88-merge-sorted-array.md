---
layout: post
title: 88. 合并两个有序数组
categories: blog
---

## 问题描述

给你两个按非递减顺序排列的整数数组 `nums1` 和 `nums2`，另有两个整数 `m` 和 `n`，分别表示 `nums1` 和 `nums2` 中的元素数目。请你合并 `nums2` 到 `nums1` 中，使合并后的数组同样按 非递减顺序 排列。
注意：最终，合并后数组不应由函数返回，而是存储在数组 `nums1` 中。为了应对这种情况，`nums1` 的初始长度为 `m + n`，其中前 `m` 个元素表示应合并的元素，后 `n` 个元素为 `0`，应忽略。`nums2` 的长度为 `n`。

原始题目连接：[合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/description/)。

## 方法一

```javascript
function merge(nums1, m, nums2, n) {
  nums1.splice(m, nums1.length - m, ...nums2);
  nums1.sort((a, b) => a - b);
}
```

## 方法 2

```javascript
function merge(nums1, m, nums2, n) {
  let idx = 0, p1 = 0, p2 = 0;
  const sortedArr = Array(m + n).fill(0);
  while (p1 + p2 < m + n) {
    if (p1 < m && p2 >= n) {
      sortedArr[idx] = nums1[p1++];
    } else if (p1 >= m && p2 < n) {
      sortedArr[idx] = nums2[p2++];
    } else {
      sortedArr[idx] = nums1[p1] <= nums2[p2] ? nums1[p1++] : nums2[p2++];
    }
    idx++;
  }

  for (let i = 0; i < sortedArr.length; i++) {
    nums1[i] = sortedArr[i];
  }
}
```
