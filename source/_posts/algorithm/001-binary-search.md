---
title: 通关leetcode(一)：二分法
tags:
  - algorithm
categories:
  - 算法知识点
---

## 二分法的使用条件

当题目里出现了数组，且为**有序数组**，并强调**数组中无重复元素**时，就要考虑是否能够使用二分法了。因为若数组中有重复元素，二分法返回的下标就会出现不唯一的情况。（但也有一种情况特殊，就是直接让你求出重复元素的起始位置和终止位置，比如leetcode [34.在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/description/)）

如果题目对时间复杂度有要求，规定在<b>O(logN)</b>的时间复杂度内解决问题，也可以考虑二分。

还有一种类型的题目可以首先考虑二分，那就是和**平方根**相关的题目。不允许使用运算符，但需要求一个值的平方根，那么就考虑二分没跑了。比如leetcode [69.x 的平方根](https://leetcode.cn/problems/sqrtx/description/), [367.有效的完全平方数](https://leetcode.cn/problems/valid-perfect-square/description/)。

{% note info flat %}
因此要牢记，二分法的使用场景关键词：
1. **有序数组**
2. **数组中无重复元素**
3. 时间复杂度**(OlogN)**
4. **平方根**
{% endnote %}

## 怎么写二分？

二分的原理非常简单，就是不断地将数组分为两半，最终找到我们需要的值。但我们可能会为二分法许多的边界感到疑惑：到底是`left < right`，还是`left <= right`；是`right = mid - 1 `，怎么也有人写`right = mid`呢？

其实所有的二分法都只属于两种情况：左闭右闭，`[left, right]`，或者左闭右开，`[left, right)`。这两种情况的本质都是要**维持区间的定义不变**。要在二分查找的过程中保持不变量，就是在while寻找中每一次边界的处理都要**坚持根据区间的定义来操作**，这就是**循环不变量规则**。

### 第一种写法 - 左闭右闭区间 [left, right]

左闭右闭区间，顾名思义，在这个区间中查找mid，mid的值可以取到left，也可以取到right。这就是左闭右闭区间的核心，之后写条件都要维持这个核心不变。

因此，我们一开始定义区间的时候，就要写为`let left = 0, right = nums.length - 1`，左和右都可以取到。

并且，循环结束条件（找不到mid的条件）就应该为`while(left <= right)`，因为`left = right` 在   `[left, right]`区间里是有可能的。

当`nums[mid] > target`时，设置边界条件`right = mid + 1`。因为mid是有可能取到right的值的，所以right要更新为`mid - 1`，才能剔除已经找过的上一个mid。

同理，当`nums[mid] < target`时，设置边界条件`left = mid + 1`。

#### 核心代码

```js
function search(nums, target) {
  let left = 0, right = nums.length - 1
  let mid
  while(left <= right) {
    if(nums[mid] < target) {
      left = mid + 1
    } else if (nums[mid] > target) {
      right = mid - 1
    } else {
      return mid
    }
  }
}
```

### 第二种写法 - 左闭右开区间 [left, right)

左闭右开区间的核心：在这个区间中查找mid，mid的值可以取到left，但不能取到right。

根据这个核心，一开始定义区间的时候：`let left = 0, right = nums.length`

循环结束条件：`while(left < right)`，因为`left = right`在`[left, right)`区间里不可能。

当`nums[mid] > target`时，设置边界条件`right = mid`，因为mid取不到right，所以下一个区间在`[left, mid)`就会不包含mid。

当`nums[mid] < target`时，同样设置边界条件`left = mid + 1`。

#### 核心代码

```js
function search(nums, target) {
  let left = 0, right = nums.length
  let mid
  while(left < right) {
    if(nums[mid] < target) {
      left = mid + 1
    } else if (nums[mid] > target) {
      right = mid
    } else {
      return mid
    }
  }
}
```

### 刷点题吧

#### [704.二分查找](https://leetcode.cn/problems/binary-search/description/)
给定一个 `n` 个元素有序的（升序）整型数组 `nums` 和一个目标值 `target`  ，写一个函数搜索 `nums` 中的 `target`，如果目标值存在返回下标，否则返回 `-1`。
```
示例 1:
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
示例 2:
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```
提示：
1. 你可以假设 `nums` 中的所有元素是不重复的。
2. `n` 将在 `[1, 10000]`之间。
3. `nums` 的每个元素都将在 `[-9999, 9999]`之间。

##### 解法
```js
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
```
没什么好说的，二分基础题

#### [35.搜索插入位置](https://leetcode.cn/problems/search-insert-position/description/)
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
请必须使用时间复杂度为 `O(log n)` 的算法。
 ```
示例 1:
输入: nums = [1,3,5,6], target = 5
输出: 2
示例 2:
输入: nums = [1,3,5,6], target = 2
输出: 1
示例 3:
输入: nums = [1,3,5,6], target = 7
输出: 4
 ```

提示:
* 1 <= `nums.length` <= 104
* -104 <= `nums[i]` <= 104
* `nums` 为 无重复元素 的 升序 排列数组
* -104 <= `target` <= 104

##### 解法
```js
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
```

为什么直接`return left`：

因为如果上面的没有返回`return middle`，说明最后一定是`left > right`从而跳出循环的，在此之前是`left = right`，

如果最后是`right - 1`导致的`left > right`，说明原来的`right`位置是大于`target`的，`target`加入之后占用的是原来`right`的下标位置，所以返回原来的`right`位置即`left`位置；

如果最后是`left + 1`导致的`left > right`,说明是原来的的`left = right`这个位置小于`target`，而`right`能移动到这个位置，说明此位置右侧是大于`target`的，`target`应加在这个位置的后面，`left`现在加`1`就移动到了`target`应该加入的位置，返回`left`即可

#### [34.在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/description/)
给你一个按照非递减顺序排列的整数数组 `nums`，和一个目标值 `target`。请你找出给定目标值在数组中的开始位置和结束位置。
如果数组中不存在目标值 `target`，返回 `[-1, -1]`。
你必须设计并实现时间复杂度为 `O(log n)` 的算法解决此问题。
```
示例 1：
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
示例 2：
输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]
示例 3：
输入：nums = [], target = 0
输出：[-1,-1]
```
提示：
* 0 <= `nums.length` <= 105
* -109 <= `nums[i]` <= 109
* `nums` 是一个非递减数组
* -109 <= `target` <= 109

##### 解法
```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var searchRange = function(nums, target) {
  let left_target_arr = -1,
    right_target_arr = -1
  let l = 0,
    r = nums.length
  let mid
  // 先找left: 如果相等 就更新left_target_arr，并向左找最终的left_target_arr
  while (l < r) {
    mid = Math.floor(l + (r - l) / 2)
    if (nums[mid] < target) {
      l = mid + 1
    } else if (nums[mid] > target) {
      r = mid
    } else {
      left_target_arr = mid
      r = mid
    }
  }
  l = 0
  r = nums.length
  // 再找right: 如果相等 就更新right_target_arr，并向右找最终的right_target_arr
  while (l < r) {
    mid = Math.floor(l + (r - l) / 2)
    if (nums[mid] < target) {
      l = mid + 1
    } else if (nums[mid] > target) {
      r = mid
    } else {
      right_target_arr = mid
      l = mid + 1
    }
  }
  return [left_target_arr, right_target_arr]
};
```
这道题的核心就是找两遍，先找最左边的边界，再找最右边的边界。找边界其实就是改变`target = nums[mid]`时的处理逻辑，找左边的边界就继续再左区间中找，找右边的边界就继续在右区间中找。

##### 20240903补充
之前一直没有尝试写过简化版的代码，今天试了一下，可以使用一个函数去简化两个二分的写法，本质还是先左边界后右边界，需要特别注意的是不同区间定义下，**跳出循环时`l`和`r`代表的意义**。

首先是不管哪个区间，左边永远是闭合的，也就是说`mid`永远可以取到`l`，因此当不断寻找左边界的时候，最后跳出循环一定是当`left = mid`时，执行了`right = mid / right = mid + 1`跳出的，所以最终，`l` 会指向第一个等于目标值的位置。

当不断寻找右边界的时候，最后跳出循环一定是执行了`left = mid + 1`跳出的，最终`l`一定会指向第一个大于目标值的位置。

我们再来看右边界，**左闭右闭**区间时，mid可以取到r，当不断寻找左边界的时候，最后跳出循环是执行了`right = mid - 1`，最终`r`会指向第一个小于目标值的位置。

当不断寻找右边界的时候，最后跳出循环是当`right = mid`时，执行了`left = mid + 1`跳出的，最终`r`会指向最后一个等于目标值的位置。

**左闭右开**区间时，`mid`不能取到`r`，当不断寻找左边界的时候，最后跳出循环是执行了`right = mid`，最终`r`会指向第一个目标值的位置。

当不断寻找右边界的时候，最后跳出循环是当`right = mid + 1`时，执行了`left = mid + 1`跳出的，最终`r`会指向第一个大于目标值的位置。

（文字描述很绕，之后可以补个图）
```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var searchRange = function (nums, target) {
  const binarySearch = (left) => {
    let l = 0, r = nums.length - 1
    let mid
    while (l <= r) {
      mid = Math.floor(l + (r - l) / 2)
      if (nums[mid] > target || (left && nums[mid] === target)) r = mid - 1
      else l = mid + 1
      // 这个else包含：nums[mid] < target, !left && nums[mid] === target
    }
    return left ? l : r
  }

  const leftIndex = binarySearch(true)
  const rightIndex = binarySearch(false)

  if (nums[leftIndex] !== target || nums[rightIndex] !== target) {
    return [-1, -1]
  }
  return [leftIndex, rightIndex]
};
```

#### [69.x 的平方根](https://leetcode.cn/problems/sqrtx/description/)
给你一个非负整数 `x` ，计算并返回 `x` 的 算术平方根 。
由于返回类型是整数，结果只保留 **整数部分** ，小数部分将被 **舍去** 。
注意：不允许使用任何内置指数函数和算符，例如 `pow(x, 0.5)` 或者 `x ** 0.5` 。
```
示例 1：
输入：x = 4
输出：2
示例 2：
输入：x = 8
输出：2
解释：8 的算术平方根是 2.82842..., 由于返回类型是整数，小数部分将被舍去。
```
提示：
* 0 <= `x` <= 231 - 1

##### 解法

```js
/**
 * @param {number} x
 * @return {number}
 */
var mySqrt = function(x) {
  let l = 0, r = x
  let mid, mid2
  let ans
  while (l <= r) {
    mid = Math.floor(l + (r - l) / 2) 
    mid2 = mid * mid
    if (mid2 > x) {
      r = mid - 1
    } else if (mid2 < x) {
      ans = mid
      l = mid + 1
    } else {
      return mid
    }
  }
  return ans
};
```

这道题是利用二分解决平方根问题，当然还有其他两种解法，数学思想较多，暂时没看，之后可以再学习一下。

由于 x 平方根的整数部分 ans 是满足 `k^2 ≤ x` 的最大 k 值，因此我们可以对 k 进行二分查找，从而得到答案。

二分查找的下界为 `0`，上界可以粗略地设定为 `x`（要细分的话，可以优化到`x/2`，但必须在`x >= 4`的时候。当然，在本题中只保留整数，因此`x > 2`的时候边界`x/2`就成立）。在二分查找的每一步中，我们只需要比较中间元素 `mid` 的平方与 `x` 的大小关系，并通过比较的结果调整上下界的范围。由于我们所有的运算都是整数运算，不会存在误差，因此在得到最终的答案 `ans` 后，也就不需要再去尝试 `ans+1` 了。

#### [367.有效的完全平方数](https://leetcode.cn/problems/valid-perfect-square/description/)
给你一个正整数 `num` 。如果 `num` 是一个完全平方数，则返回 `true` ，否则返回 `false` 。
完全平方数 是一个可以写成某个整数的平方的整数。换句话说，它可以写成某个整数和自身的乘积。
不能使用任何内置的库函数，如  `sqrt` 。
```
示例 1：
输入：num = 16
输出：true
解释：返回 true ，因为 4 * 4 = 16 且 4 是一个整数。
示例 2：
输入：num = 14
输出：false
解释：返回 false ，因为 3.742 * 3.742 = 14 但 3.742 不是一个整数。
```
提示：
* 1 <= num <= 231 - 1

##### 解法
```js
/**
 * @param {number} num
 * @return {boolean}
 */
var isPerfectSquare = function(num) {
  let l = 0, r = num
  let mid
  while (l <= r) {
    mid = Math.floor(l + (r - l) / 2)
    mid2 = mid * mid
    if (mid2 < num) {
      l = mid + 1
    } else if (mid2 > num) {
      r = mid - 1
    } else {
      return true
    }
  }
  return false
};
```

有了69题的基础，这道题更简单了，话不多说