---
title: 通关leetcode(一)：二分法
tags:
  - algorithm
categories:
  - 算法知识点
---

## 二分法的使用条件

当题目里出现了数组，且为**有序数组**，并强调**数组中无重复元素**时，就要考虑是否能够使用二分法了。因为若数组中有重复元素，二分法返回的下标就会出现不唯一的情况。

:::tip

因此要牢记，二分法的使用场景：
1. **有序数组**
2. **数组中无重复元素**

:::

## 怎么写二分？

二分的原理非常简单，就是不断地将数组分为两半，最终找到我们需要的值。但我们可能会为二分法许多的边界感到疑惑：到底是`left < right`，还是`left <= right`，是`right = mid - 1 `，怎么也有人写`right = mid`呢？

其实所有的二分法都只属于两种情况：左闭右闭，`[left, right]`，或者左闭右开，`[left, right)`





二分法条件：1. 有序数组 2. 无重复元素（有重复元素会导致结果不唯一）
区间的定义：1. 左闭右闭[left, right] 2. 左闭右开[left, right)
1. 左闭右闭[left, right]
找不到的条件：while(left <= right) 因为left = right 在[left, right]区间里是有可能的
if (arr[mid] > target) { right = mid - 1 } 
if (arr[mid] < target) { left = mid + 1 }
因为[left, right] mid不等于target下一个区间里就不应该包含mid
2. 左闭右开
找不到的条件：while(left < right) 因为left = right 在[left, right)区间里不可能
if (arr[mid] > target) { right = mid  }  因为[left, right) 所以下一个区间在[left, mid)就会不包含mid
if (arr[mid] < target) { left = mid + 1 }


704
给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。
示例 1:
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
示例 2:
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
 
提示：
1. 你可以假设 nums 中的所有元素是不重复的。
2. n 将在 [1, 10000]之间。
3. nums 的每个元素都将在 [-9999, 9999]之间。

/*
 * @lc app=leetcode.cn id=704 lang=javascript
 * @lcpr version=30204
 *
 * [704] 二分查找
 */

// @lcpr-template-start

// @lcpr-template-end
// @lc code=start
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function (nums, target) {
  // [left, right]
  // let left = 0,
  //   right = nums.length - 1
  // let mid
  // while (left <= right) {
  //   mid = Math.floor((left + right) / 2)
  //   if (nums[mid] < target) {
  //     left = mid + 1
  //   } else if (nums[mid] > target) {
  //     right = mid - 1
  //   } else {
  //     return mid
  //   }
  // }
  // return -1

  // [left, right)
  let left = 0,
    right = nums.length
  let mid
  while (left < right) {
    mid = Math.floor((left + right) / 2)
    if (nums[mid] < target) {
      left = mid + 1
    } else if (nums[mid] > target) {
      right = mid
    } else {
      return mid
    }
  }
  return -1
}
// @lc code=end

/*
// @lcpr case=start
// [-1,0,3,5,9,12]\n9\n
// @lcpr case=end

// @lcpr case=start
// [-1,0,3,5,9,12]\n2\n
// @lcpr case=end

 */


*35
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
请必须使用时间复杂度为 O(log n) 的算法。
 
示例 1:
输入: nums = [1,3,5,6], target = 5
输出: 2
示例 2:
输入: nums = [1,3,5,6], target = 2
输出: 1
示例 3:
输入: nums = [1,3,5,6], target = 7
输出: 4
 
提示:
* 1 <= nums.length <= 104
* -104 <= nums[i] <= 104
* nums 为 无重复元素 的 升序 排列数组
* -104 <= target <= 104


/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var searchInsert = function (nums, target) {
  let left = 0,
    right = nums.length - 1
  let mid
  while (left <= right) {
    mid = Math.floor((left + right) / 2)
    if (nums[mid] > target) {
      right = mid - 1
    } else if (nums[mid] < target) {
      left = mid + 1
    } else {
      return mid
    }
  }
  return left
}

为什么直接return left：
因为如果上面的没有返回return middle，说明最后一定是，left>right从而跳出循环的，在此之前是left=right，如果最后是right-1导致的left>right，说明原来的right位置是大于target的，target加入之后占用的是原来right的下标位置，所以返回原来的right位置即left位置；
如果最后是left+1导致的left>right,说明是原来的的left=right这个位置小于target，而right能移动到这个位置，说明此位置右侧是大于target的，target应加在这个位置的后面，left现在加1就移动到了target应该加入的位置，返回left即可

**34
给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。
如果数组中不存在目标值 target，返回 [-1, -1]。
你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题。

const binarySearch = (nums, target, lower) => {
    let left = 0, right = nums.length - 1, ans = nums.length;
    while (left <= right) {
        const mid = Math.floor((left + right) / 2);
        if (nums[mid] > target || (lower && nums[mid] >= target)) {
            right = mid - 1;
            ans = mid;
        } else {
            left = mid + 1;
        }
    }
    return ans;
}

var searchRange = function(nums, target) {
    let ans = [-1, -1];
    const leftIdx = binarySearch(nums, target, true);
    const rightIdx = binarySearch(nums, target, false) - 1;
    if (leftIdx <= rightIdx && rightIdx < nums.length && nums[leftIdx] === target && nums[rightIdx] === target) {
        ans = [leftIdx, rightIdx];
    } 
    return ans;
};

作者：力扣官方题解
链接：https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/solutions/504484/zai-pai-xu-shu-zu-zhong-cha-zhao-yuan-su-de-di-3-4/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
 
示例 1：
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
示例 2：
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
示例 3：
输入：nums = [], target = 0
输出：[-1,-1]
 
提示：
* 0 <= nums.length <= 105
* -109 <= nums[i] <= 109
* nums 是一个非递减数组
* -109 <= target <= 109
