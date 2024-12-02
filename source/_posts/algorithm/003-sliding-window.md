---
title: 通关leetcode(三)：滑动窗口 以及一点前缀和+单调队列
date: 2024-11-29 10:03:52
tags:
  - algorithm
categories:
  - 算法知识点
---

## 滑动窗口是什么

滑动窗口其实也可以理解为双指针法的一种，只不过这种解法更像是一个窗口的移动，所以叫做滑动窗口更适合一些。

所谓滑动窗口，就是**不断的调节子序列的起始位置和终止位置**，从而得出我们要想的结果。

{% note primary %}
实现滑动窗口，主要确定如下三点：
* 窗口内是什么？
* 如何移动窗口的起始位置？
* 如何移动窗口的结束位置？
{% endnote %}

如果采用暴力解法，那么一般都是一个for循环滑动窗口的起始位置，一个for循环为滑动窗口的终止位置，用两个for循环完成了一个不断搜索区间的过程。采用滑动窗口，只需要循环滑动窗口的终止位置，然后找到滑动窗口起始位置的移动策略就可以了。<b>滑动窗口的精妙之处在于根据当前子序列和大小的情况，不断调节子序列的起始位置。从而将O(n^2)暴力解法降为O(n)。</b>

### 刷点题吧

#### [209.长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)

给定一个含有 n 个正整数的数组和一个正整数 target 。
找出该数组中满足其总和大于等于 target 的长度最小的 子数组 [numsl, numsl+1, ..., numsr-1, numsr] ，并返回其长度。如果不存在符合条件的子数组，返回 0 。
 
```
示例 1：
输入：target = 7, nums = [2,3,1,2,4,3]
输出：2
解释：子数组 [4,3] 是该条件下的长度最小的子数组。
示例 2：
输入：target = 4, nums = [1,4,4]
输出：1
示例 3：
输入：target = 11, nums = [1,1,1,1,1,1,1,1]
输出：0
```
 
提示：
- 1 <= target <= 109
- 1 <= nums.length <= 105
- 1 <= nums[i] <= 105
 

进阶：
- 如果你已经实现 O(n) 时间复杂度的解法, 请尝试设计一个 O(n log(n)) 时间复杂度的解法。

##### 解法
```js
/**
 * @param {number} target
 * @param {number[]} nums
 * @return {number}
 */
var minSubArrayLen = function(target, nums) {
  let l = 0, r = 0
  let sum = nums[0], len = 999999
  // r代表滑动窗口的结束位置
  while (r < nums.length) {
    // sum小了，继续后移窗口的结束位置，添加元素
    // 否则更新len，并移动窗口的起始位置，移除元素，缩小窗口，找最小的区间
    if (sum < target) {
      sum += nums[++r]
    } else {
      len = Math.min(r - l + 1, len)
      sum -= nums[l++]
    }
  }
  return len === 999999 ? 0 : len
};
```

利用滑动窗口，用一个循环来表示**滑动窗口的终止位置**。我们来看一下这道题的滑动窗口三要素：
- **窗口**就是 满足其和 ≥ s 的长度最小的 连续 子数组。
- **窗口的起始位置如何移动**：如果当前窗口的值大于等于s了，窗口就要向前移动了（也就是该缩小了）。
- **窗口的结束位置如何移动**：窗口的结束位置就是遍历数组的指针，也就是循环里的索引。


##### 进阶解法

进阶需要时间复杂度 O(nlogn) ，想半天没想出来，看题解原来是用**二分 + 前缀和**（二分的学习还是有用的，至少看到 O(nlogn) 想到是一个循环套一个二分了，但不熟悉前缀和，没想出来怎么二分）

我们先来简单介绍一下前缀和（详细的前缀和专题，我们在之后的文章里总结）：

{% note green no-icon %}
「前缀和」 是从 nums 数组中的第 0 位置开始累加，到第 i 位置的累加结果，我们常把这个结果保存到数组 preSum 中，记为 preSum[i]。
我们常常把「前缀和」数组 preSum 的长度定义为 原数组的长度 + 1。preSum 的第 0 个位置，相当于一个占位符，置为 0。
那么就可以把 preSum 的公式统一为 `preSum[i] = preSum[i - 1] + nums[i - 1]`，此时的 preSum[i] 表示 nums 中 i 元素左边所有元素之和（不包含当前元素 i）。
{% endnote %}

这道题如果不使用滑动窗口，纯暴力解法的话，两层循环，一层 i 遍历子数组的起始位置，一层 j 遍历子数组的结束位置，如果 ij 之间的值相加大于 target ，就找到了以 i 开头的总和大于等于 target 的**长度最小的子数组**（为什么是长度最小的？因为 j 是从 i 开始遍历的，第一个找到的 ij 之间的值相加大于 target 的 j 值，就是离 i 最近的符合条件的 j）。

这个暴力解法需要 O(n^2) ，其实就是因为确定数组的起始位置之后，找到符合条件的数组的终止位置需要 O(n) ，我们可以采用二分查找将 O(n) 优化为 O(logn) 。

找j实际上是需要找 `sum(nums[i], nums[i+1], nums[i+2], …., nums[j-1], nums[j]) >= target` ，我们可以通过一个数组 sum 存储每个下标 i 的前缀和（nums[0] 到 nums[i-1] 之间的和）。`sum(nums[i], nums[i+1], nums[i+2], …., nums[j-1], nums[j])` 可以转化为 `(sum(nums[0], nums[1], …., nums[i], nums[i+1], …., nums[j-1], nums[j], …., nums[j])) - (sum(nums[0], nums[1], ..., nums[i-1]))`，也就是 `sum[j-1] - sum[i] >= target`。

用图来举个例子：
{% asset_img sumArr.png %}

那么使用前缀和的公式就是：`sum[j-1] - sum[i] >= target`，移项变成：`sum[j-1] >= target + sum[i]`。找终止元素的时候，使用二分在 sum 数组里找第一个大于等于 target + sum[i] 的 sun[j-1] 的值就好。<b>（重要：因为题目里说了 nums 的所有元素都是正整数，由此保证了前缀和数组 sum 一定是一个非递减的数组，可以使用二分。如果 nums 有负数，就不能使用二分法了。）</b>

代码如下：
```js
/**
 * @param {number} target
 * @param {number[]} nums
 * @return {number}
 */
var minSubArrayLen = function (target, nums) {
  // 二分法：在单调递增的前缀和中找到第一个大于等于target+sum[i]的sum[j]
  const search = (arr, t) => {
    let l = 0, r = arr.length
    let mid
    while (l < r) {
      mid = Math.floor(l + (r - l) / 2)
      if (arr[mid] < t) {
        l = mid + 1
      } else {
        r = mid
      }
    }
    return l
  }
  // 建立前缀和数组
  let sum = [0]
  for (let i = 1; i <= nums.length; i++) {
    sum[i] = sum[i-1] + nums[i-1]
  }
  let minLen = 999999
  // 开始遍历，使用一个循环N套一个二分查找logN找最小长度
  for (let i = 0; i < nums.length; i++) {
    const bound = search(sum, target + sum[i])
    // bound - 1 = j, j 和 i 之间的长度: j - i + 1 = bound - i
    if (bound < sum.length) {
      minLen = Math.min(minLen, bound - i) 
    }
  }
  return minLen === 999999 ? 0 : minLen
};
```

##### 拓展：如果 nums 不是正整数数组呢？

当 nums 可以为负数的时候，滑动窗口和二分法都不可以使用了（不能维持状态的统一性，增加一个元素的和有可能是增加也可能是减少）。既然问题的根本在于没有一个规律性的状态，我们就可以使用<b>前缀和+单调队列</b>，手动创建一个非递减序列使用。

我们先看这道题，核心公式其实就是`sum[j-1]-sum[i] >= target`（sum是前缀和数组）。移项，得`sum[i] <= sum[j-1] - target`，对于每个子数组的终止位置 j ，在满足这个公式的情况下，i 要尽可能大。也就是说**如果后面的前缀和比前面的小，其实只用记录后面的前缀和+索引就行了**。

因此我们可以用 j 遍历前缀和数组，代表子数组的终止位置，并维护一个单调双端队列 deque ，遇到 sum[j] 时，首先从单调队列的队头开始，如果 **当前前缀和(sum[j])** 与 **队列中最小的前缀和(sum[i])** 之差**大于等于 target**，就更新最小子数组长度，并移除这个元素（因为这个 i 已经遇到了第一个 j ，即使再后面的 j 索引也满足公式，也不可能比这个 j 的长度小了，所以直接剔除），直到不满足`sum[i] <= sum[j] - target`为止。之后要把 sum[j] 也放进这个单调队列中，从单调队列的队尾开始，可以**直接把比当前前缀和大的元素全都扔掉**。原因就是前面提到的：如果后面的前缀和比前面的小，只用记录后面的前缀和+索引就行了。

其实这个单调队列，不止维护了单调的前缀和，同时也通过了“直接把比当前前缀和大的元素全都扔掉”维护了索引的单调性，队列里的索引一定也是递增的。通过这个单调递增队列，就可以通过 O(n) 的时间复杂度来解决问题。

```js
/**
 * @param {number} target
 * @param {number[]} nums
 * @return {number}
 */
var minSubArrayLen = function (target, nums) {
  let sum = [0]
  for (let i = 1; i <= nums.length; i++) {
    sum[i] = sum[i - 1] + nums[i - 1]
  }
  let deque = []
  let len = 999999
  for (let j = 0; j < sum.length; j++) {
    // 检查当前前缀和与队列中最小的前缀和之差是否大于等于 target
    while (deque.length > 0 && sum[j] >= target + sum[deque[0]]) {
      len = Math.min(len, j - deque.shift())
    }
    // 保持队列的单调性，如果后面的前缀和比前面的小，只用记录后面的前缀和+索引就行了
    while (deque.length > 0 && sum[j] <= sum[deque[deque.length - 1]]) {
      deque.pop()
    }
    // 把索引 j 加入队列
    deque.push(j)
  }
  return len === 999999 ? 0 : len
};
```

虽然是 for 循环里套 while ，但是在队列中，每个元素只会被加入/移出一次，所以这两个 while 循环的总操作次数不会超过 2n 次。因此，整体时间复杂度是 O(n)。

这个题目是自己拓展的，没有 leetcode 程序可以验证，对代码有疑问可以随时提出交流。

#### [904.水果成篮](https://leetcode.cn/problems/fruit-into-baskets/description/)
你正在探访一家农场，农场从左到右种植了一排果树。这些树用一个整数数组 fruits 表示，其中 fruits[i] 是第 i 棵树上的水果 **种类** 。

你想要尽可能多地收集水果。然而，农场的主人设定了一些严格的规矩，你必须按照要求采摘水果：
- 你只有 **两个** 篮子，并且每个篮子只能装 单一类型 的水果。每个篮子能够装的水果总量没有限制。
- 你可以选择任意一棵树开始采摘，你必须从 **每棵** 树（包括开始采摘的树）上 **恰好摘一个水果** 。采摘的水果应当符合篮子中的水果类型。每采摘一次，你将会向右移动到下一棵树，并继续采摘。
- 一旦你走到某棵树前，但水果不符合篮子的水果类型，那么就必须停止采摘。

给你一个整数数组 fruits ，返回你可以收集的水果的 最大 数目。
```
示例 1：
输入：fruits = [1,2,1]
输出：3
解释：可以采摘全部 3 棵树。
示例 2：
输入：fruits = [0,1,2,2]
输出：3
解释：可以采摘 [1,2,2] 这三棵树。
如果从第一棵树开始采摘，则只能采摘 [0,1] 这两棵树。
示例 3：
输入：fruits = [1,2,3,2,2]
输出：4
解释：可以采摘 [2,3,2,2] 这四棵树。
如果从第一棵树开始采摘，则只能采摘 [1,2] 这两棵树。
示例 4：
输入：fruits = [3,3,3,1,2,1,1,2,3,3,4]
输出：5
解释：可以采摘 [1,2,1,1,2] 这五棵树。
```
提示：
- 1 <= fruits.length <= 105
- 0 <= fruits[i] < fruits.length

##### 解法
```js
/**
 * @param {number[]} fruits
 * @return {number}
 */
var totalFruit = function (fruits) {
  let l = 0, r = 0
  const types = new Set();
  let maxlen = 0
  for (; r < fruits.length; r++) {
    // 如果不在types里
    if (!types.has(fruits[r])) {
      if (types.size >= 2) {
        // 如果超出了2种类型，要去除相距较远的那种类型，从r的前一个开始向前找l的位置
        l = r - 1
        while (fruits[l] === fruits[r-1]) {
          l--
        }
        l++
        types.clear()
        types.add(fruits[r-1])
      }
      types.add(fruits[r])
    }
    maxlen = Math.max(maxlen, r-l+1)
  }
  return maxlen
};
```
“返回你可以收集的水果的 **最大** 数目”其实还是找最大区间的问题，用滑动窗口三要素来分析这道题：
- **窗口**就是 满足只有两种水果类型的 连续 子数组。
- **窗口的起始位置如何移动**：如果当前窗口的水果类型超过了两个，起始位置要移动来使水果类型始终在 2 个以内。
- **窗口的结束位置如何移动**：窗口的结束位置就是遍历数组的指针，也就是 for 循环里的索引。

这道题的重点在于：当窗口的水果类型超过 2 个的时候，窗口起始位置应该怎么移动？首先想到直接从起始位置往结束位置去移动，但是这样无法达到我们的效果，起始位置和结束位置之间的数有可能是乱序的，比如题目中示例的第4条：
```
[3,3,3,1,2,1,1,2,3,3,4]
```
{% asset_img moveType.png %}

从 l 向 r 移动是无法将多余类型剔除干净的。种类超过3个一定是因为 r 的移动所引起的，也就是说 r 当前的位置一定是第三种水果类型，r 的前一个位置一定是要保留的水果类型。 所以我们可以直接让 l = r - 1 ，向左移动，如果 l 的值不等于 r - 1了就停下，说明已经剔除干净了，当前区间的水果类型只有 r 对应的类型和 r - 1 对应的类型了。

#### [560.和为 k 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/description/)
给你一个整数数组 nums 和一个整数 k ，请你统计并返回 该数组中和为 k 的子数组的个数 。
子数组是数组中元素的连续非空序列。
```
示例 1：
输入：nums = [1,1,1], k = 2
输出：2
示例 2：
输入：nums = [1,2,3], k = 3
输出：2
```
提示：
- 1 <= nums.length <= 2 * 104
- -1000 <= nums[i] <= 1000
- -107 <= k <= 107

##### 解法
```js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number}
 */
var subarraySum = function (nums, k) {
  // 获取前缀和数组
  let sum = [0]
  for (let i = 1; i <= nums.length; i++) {
    sum[i] = sum[i - 1] + nums[i - 1]
  }
  let map = {}
  let num = 0
  // sum[j] - sum[i] = k
  for (let i = 0; i < sum.length; i++) {
    if (map[sum[i] - k]) {
      num += map[sum[i] - k]
    }
    if (map[sum[i]]) {
      map[sum[i]]++
    } else {
      map[sum[i]] = 1
    }
  }
  return num
};
```

这道题也是一个前缀和，核心公式是 `sum[j]-sum[i] = k` ，其实也就是找，当子数组是以下标 i 开始时，有几个 j 可以满足 `sum[j] = k + sum[i]` ，或者当子数组是以下标 j+1 结束时，有几个 i 可以满足 `sum[i] = sum[j] - k`。我们从左向右遍历的话，找以下标 j+1 结束时有几个 i 比较方便，一次遍历即可。维护一个 map ，用来记录前缀和的值出现了几次。遇到 sum[j] 时，在 map 里找一下 map[sum[j] - k] 有几个，就说明以下标 j 结束时，和等于 k 的子数组有几个。之后再把 sum[j] 也更新进 map 即可。

不能用滑动窗口的原因是这道题和 209 不同， 209 表明了数组里的元素全为正整数，所以如果区间和大了就可以移动滑动窗口起始位置，区间和小了就移动滑动窗口终止位置。这道题元素有可能为负数，无法根据区间大小移动滑动窗口了，因为扩大终止位置可能让区间和减少，缩小起始位置也有可能让区间和增大，无法控制。