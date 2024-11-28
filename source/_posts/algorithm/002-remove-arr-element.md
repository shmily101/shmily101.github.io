---
title: 通关leetcode(二)：移除数组元素-快慢指针
tags:
  - algorithm
categories:
  - 算法知识点
---

## 移除数组元素

移除数组元素这类的题目，关键字就是“**原地**”。这种题目要求使用`O(1)`的空间复杂度更改数组元素，一般都是使用<b>快慢指针(双指针)</b>法。

{% link 原地算法,百度百科,https://baike.baidu.com/item/%E5%8E%9F%E5%9C%B0%E7%AE%97%E6%B3%95 %}

{% note info flat %}
双指针法（快慢指针法）： 通过一个快指针和慢指针在一个for循环下完成两个for循环的工作。
{% endnote %}

双指针法的关键是要找到**快、慢指针分别代表什么意义**，找到了之后题目自然就解决了。

### 刷点题吧

#### [27.移除元素](https://leetcode.cn/problems/remove-element/description/)
给你一个数组 nums 和一个值 val，你需要 **原地** 移除所有数值等于 val 的元素。元素的顺序可能发生改变。然后返回 nums 中与 val 不同的元素的数量。
假设 nums 中不等于 val 的元素数量为 k，要通过此题，您需要执行以下操作：
* 更改 nums 数组，使 nums 的前 k 个元素包含不等于 val 的元素。nums 的其余元素和 nums 的大小并不重要。
* 返回 k。

用户评测：

评测机将使用以下代码测试您的解决方案：
```js
int[] nums = [...]; // 输入数组
int val = ...; // 要移除的值
int[] expectedNums = [...]; // 长度正确的预期答案。
                            // 它以不等于 val 的值排序。

int k = removeElement(nums, val); // 调用你的实现

assert k == expectedNums.length;
sort(nums, 0, k); // 排序 nums 的前 k 个元素
for (int i = 0; i < actualLength; i++) {
    assert nums[i] == expectedNums[i];
}
```
如果所有的断言都通过，你的解决方案将会通过。

```
示例 1：
输入：nums = [3,2,2,3], val = 3
输出：2, nums = [2,2,_,_]
解释：你的函数函数应该返回 k = 2, 并且 nums 中的前两个元素均为 2。
你在返回的 k 个元素之外留下了什么并不重要（因此它们并不计入评测）。
示例 2：
输入：nums = [0,1,2,2,3,0,4,2], val = 2
输出：5, nums = [0,1,4,0,3,_,_,_]
解释：你的函数应该返回 k = 5，并且 nums 中的前五个元素为 0,0,1,3,4。
注意这五个元素可以任意顺序返回。
你在返回的 k 个元素之外留下了什么并不重要（因此它们并不计入评测）。
```

提示：
* 0 <= nums.length <= 100
* 0 <= nums[i] <= 50
* 0 <= val <= 100

##### 解法
```js
/**
 * @param {number[]} nums
 * @param {number} val
 * @return {number}
 */
var removeElement = function(nums, val) {
  let slow = 0, fast = 0
  for (; fast < nums.length; fast++) {
    if(nums[fast] !== val) nums[slow++] = nums[fast]
  }
  return slow
};
```

就是定义快慢指针，在本题中，快慢指针的意义分别是：
* 快指针：寻找新数组的元素 ，新数组就是**不含有目标元素的数组**
* 慢指针：指向更新的**新数组下标的位置**

#### [26.删除有序数组中的重复项](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/)
给你一个 非严格递增排列 的数组 nums ，请你 **原地** 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。然后返回 nums 中唯一元素的个数。
考虑 nums 的唯一元素的数量为 k ，你需要做以下事情确保你的题解可以被通过：
* 更改数组 nums ，使 nums 的前 k 个元素包含唯一元素，并按照它们最初在 nums 中出现的顺序排列。nums 的其余元素与 nums 的大小不重要。
* 返回 k 。

判题标准:

系统会用下面的代码来测试你的题解:
```js
int[] nums = [...]; // 输入数组
int[] expectedNums = [...]; // 长度正确的期望答案

int k = removeDuplicates(nums); // 调用

assert k == expectedNums.length;
for (int i = 0; i < k; i++) {
    assert nums[i] == expectedNums[i];
}
```
如果所有断言都通过，那么您的题解将被 通过。

```
示例 1：
输入：nums = [1,1,2]
输出：2, nums = [1,2,_]
解释：函数应该返回新的长度 2 ，并且原数组 nums 的前两个元素被修改为 1, 2 。不需要考虑数组中超出新长度后面的元素。
示例 2：
输入：nums = [0,0,1,1,1,2,2,3,3,4]
输出：5, nums = [0,1,2,3,4]
解释：函数应该返回新的长度 5 ， 并且原数组 nums 的前五个元素被修改为 0, 1, 2, 3, 4 。不需要考虑数组中超出新长度后面的元素。
```

提示：
* 1 <= nums.length <= 3 * 104
* -104 <= nums[i] <= 104
* nums 已按 非严格递增 排列

##### 解法
```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function (nums) {
  let l = 0, r = 0
  for (; r < nums.length; r++) {
    if (nums[r] !== nums[l]) nums[++l] = nums[r]
  }
  // 唯一元素个数
  return l+1
};
```
上一道题的变种，快慢指针的定义和上一题相同：
* 快指针：寻找新数组的元素 ，新数组就是**不含有重复元素的数组**
* 慢指针：指向更新的**新数组下标的位置**

假如快指针和慢指针的值相同，说明快指针的这个值不应该被记录到新数组中，应当跳过；

快指针的值和慢指针不同，说明这个值没有被记录过，应该记录，所以慢指针向后移1位并且记录这个值。

还有另外一种解法：快指针寻找不重复的元素，当快指针指向的值和上一个元素相同的时候，就说明是重复元素，需要跳过。
```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var removeDuplicates = function (nums) {
  if(nums.length === 1) return 1
  let slow = 1, fast = 1
  for (; fast < nums.length; fast++) {
    // 如果当前的值和上一个相同，这一个需要被跳过
    if (nums[fast] !== nums[fast - 1]) nums[slow++] = nums[fast]
  }
  return slow
};
```

#### [283.移动0](https://leetcode.cn/problems/move-zeroes/)
给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。
请注意 ，必须在不复制数组的情况下原地对数组进行操作。
 
```
示例 1:
输入: nums = [0,1,0,3,12]
输出: [1,3,12,0,0]
示例 2:
输入: nums = [0]
输出: [0]
```

提示:
* 1 <= nums.length <= 104
* -231 <= nums[i] <= 231 - 1


进阶：你能尽量减少完成的操作次数吗？

#### 解法
```js
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function (nums) {
  let l = 0, r = 0 
  for (; r < nums.length; r++) {
    if (nums[r] !== 0) {
      [nums[l], nums[r]] = [nums[r], nums[l]]
      l++
    }
  }
}
```
快慢指针定义：
* 快指针：寻找新数组的元素 ，新数组就是**非0元素组成的数组**
* 慢指针：指向更新的**新数组下标的位置**
和27题基本相同，快指针去寻找非0的元素，但因为题目要求把所有的0放在后面，所以需要在给慢指针赋值的时候，同时给快指针赋值0。

进阶要求我们尽可能减少操作次数，我们观察两个指针的行进逻辑，如果 l 和 r 一直没有遇到 0 ，那么它们始终会指向同一个位置。如果 l 和 r 分开了，那么它们一定是遇到 0 了才导致 l 不和 r 一起向后移动了，它们之间间隔的一定是0。当 l 和 r 指向同一个位置的时候（也就是 l 不指向 0 的时候），其实是不需要进行交换的。
```js
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function (nums) {
  let l = 0, r = 0 
  for (; r < nums.length; r++) {
    if (nums[r] !== 0) {
      if(l !== r) [nums[l], nums[r]] = [nums[r], nums[l]]
      l++
    }
  }
}
```


#### [844.比较含退格的字符串](https://leetcode.cn/problems/backspace-string-compare/)
给定 s 和 t 两个字符串，当它们分别被输入到空白的文本编辑器后，如果两者相等，返回 true 。# 代表退格字符。
注意：如果对空文本输入退格字符，文本继续为空。
 
```
示例 1：
输入：s = "ab#c", t = "ad#c"
输出：true
解释：s 和 t 都会变成 "ac"。
示例 2：
输入：s = "ab##", t = "c#d#"
输出：true
解释：s 和 t 都会变成 ""。
示例 3：
输入：s = "a#c", t = "b"
输出：false
解释：s 会变成 "c"，但 t 仍然是 "b"。
```

提示：
* 1 <= s.length, t.length <= 200
* s 和 t 只含有小写字母以及字符 '#'
 

进阶：
* 你可以用 O(n) 的时间复杂度和 O(1) 的空间复杂度解决该问题吗？

#### 解法
```js
/**
 * @param {string} s
 * @param {string} t
 * @return {boolean}
 */
var backspaceCompare = function (s, t) {
  const filterBackSpace = (str) => {
    let l = 0, r = 0
    for (; r < str.length; r++) {
      // 如果快指针遇到#，慢指针需要退1格，但不会超过0
      if (str[r] === '#') {
        l = Math.max(0, l-1)
      } else {
        str[l++] = str[r]
      }
    }
    return str.slice(0, l).join('')
  }
  const s_new = filterBackSpace(s.split(""))
  const t_new = filterBackSpace(t.split(""))
  if (s_new !== t_new) return false
  return true
};
```
这道题使用双指针的解法就是他的进阶解法。暴力的话很简单，遍历 s ，不是 # 就 push 进新数组，遇到 # 就 pop 出一个来，再同样遍历 t 即可，这样的时间复杂度是 O(n) ，空间复杂度也是 O(n) 

进阶还是双指针，因为遇到 # 需要退格，所以代表**更新新数组下标的位置**的慢指针就需要在遇到 # 的时候回退 1 个（对空文本输入退格字符，文本继续为空，慢指针在 0 位置的时候除外）。其余和上面的题目都相同。


#### [977.有序数组的平方](https://leetcode.cn/problems/squares-of-a-sorted-array/)
给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。
 
```
示例 1：
输入：nums = [-4,-1,0,3,10]
输出：[0,1,9,16,100]
解释：平方后，数组变为 [16,1,0,9,100]
排序后，数组变为 [0,1,9,16,100]
示例 2：
输入：nums = [-7,-3,2,3,11]
输出：[4,9,9,49,121]
```

提示：
* 1 <= nums.length <= 104
* -104 <= nums[i] <= 104
* nums 已按 非递减顺序 排序
 


进阶：
* 请你设计时间复杂度为 O(n) 的算法解决本问题

#### 解法
```js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var sortedSquares = function (nums) {
  const firstPositive = () => {
    for (let i = 0; i < nums.length; i++) {
      if (nums[i] >= 0) return i
    }
    return nums.length
  }
  const ans = []
  // 先找到第一个正数
  let positive = firstPositive()
  let navigator = positive - 1
  // 当前正数小于负数绝对值，把正数平方放进数组，正数移动
  // 当前正数大于负数绝对值，把负数平方放进数组，负数移动
  while (positive < nums.length && navigator >= 0) {
    if (nums[positive] < Math.abs(nums[navigator])) {
      ans.push(nums[positive] * nums[positive++])
    } else {
      ans.push(nums[navigator] * nums[navigator--])
    }
  }

  // 把剩余的正数/负数放进数组
  if (navigator < 0) {
    while (positive < nums.length) {
      ans.push(nums[positive] * nums[positive++])
    }
  } else if (positive >= nums.length) {
    while (navigator >= 0) {
      ans.push(nums[navigator] * nums[navigator--])
    }
  }

  return ans
};
```
暴力的话就是先遍历+平方，再排序。进阶的话，采用双指针，要保证平方数组是非递减顺序，在添加平方的时候就要按绝对值大小添加。所以就不是快慢指针了，一个指针遍历负数，一个指针遍历正数，从中间向两边发散，比较类似于合并两个有序数组了，时间复杂度O(n)。

当然其实也可以从左右两边向中间遍历，负数指针初始化为0，正数指针初始化为nums.length-1，当正负指针相遇的时候跳出。这样就是谁大添加谁，相对来说简单一点。
```js
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var sortedSquares = function (nums) {
  let l = 0, r = nums.length - 1
  let arr = []
  while (l <= r) {
    const l_2 = nums[l] * nums[l]
    const r_2 = nums[r] * nums[r]
    if (l_2 > r_2) {
      arr.unshift(l_2)
      l++
    } else {
      arr.unshift(r_2)
      r--
    }
  }
  return arr
};
```