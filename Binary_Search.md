# Binary Search 二分搜索

<img src="https://labuladong.github.io/algo/images/%e4%ba%8c%e5%88%86%e6%9f%a5%e6%89%be/poem.png" alt="img" style="zoom: 67%;" />

- **搜索一个元素** (LeetCode 704)

  ```python
  def search(self, nums: List[int], target: int) -> int:
          if nums is None or len(nums) == 0:
              return -1
  
          l = 0
          r = len(nums) - 1
          while l <= r:             # 搜索区间为空的时候终止
              # m = l + (r - l)//2  # same as (l + r)//2 !!Important in Java / C... to avoid overflow
              m = (l + r)//2        # No integer overflow problems in Python
              if nums[m] == target:
                  return m
              elif nums[m] < target:
                  l = m + 1
              elif nums[m] > target:
                  r = m - 1         
          return -1
  ```

​	**分析二分查找的一个技巧是：不要出现 else，而是把所有情况用 else if 写清楚，这样可以清楚地展现所有细节**

​	**计算 `mid` 时需要防止溢出**，代码中 `left + (right - left) / 2` 就和 `(left + right) / 2` 的结果相同，但是有效防止了 `left` 和 `right` 太大，直接相加导致溢出的情况。--> <font color=red>**In C / Java / C++...**</font>

​	<font color=red>**However, integer overflow is not a problem in Python!**</font> --> When the value is small, Python can just use the computer's underlying int representation and operations. When the values gets larger, Python ***automatically converts to a representation using more bits***.

- **寻找左侧/右侧边界的二分搜索**

  - <u>寻找左侧边界</u>

    ```python
    def leftBound(self, nums, target):
      if nums is None or len(nums) == 0:
        return -1
      
      l = 0
      r = len(nums) - 1
      while (l <= r):# 搜索区间为 [left, right]
        m = (l + r)//2
        if (nums[m] == target):
          # 收缩右侧边界！！
          r = m - 1  # 收缩「搜索区间」的上界 right，在区间 [left, mid) 中继续搜索，即不断向左收缩，达到锁定左侧边界的目的
        elif (nums[m] < target):
          l = m + 1	 # 搜索区间变为 [mid+1, right]
        elif (nums[m] > target):
          r = m - 1	 # 搜索区间变为 [left, mid-1]
      
      # 检查索引是否出界
      if (l >= len(nums) or nums[l] != target):
        return -1
      return l
    ```

    由于 while 的退出条件是 `left == right + 1`，所以当 `target` 比 `nums` 中所有元素都大时，会存在以下情况使得索引越界：

    <img src="https://labuladong.github.io/algo/images/%e4%ba%8c%e5%88%86%e6%9f%a5%e6%89%be/2.jpg" alt="img" style="zoom:33%;" />

  - <u>寻找右侧边界</u>

    ```python
    def rightBound(self, nums, target):
      if nums is None or len(nums) == 0:
        return -1
      
      l = 0
      r = len(nums) - 1
      while (l <= r):# 搜索区间为 [left, right]
        m = (l + r)//2
        if (nums[m] == target):
          l = m + 1  # 收缩左侧边界！！
        elif (nums[m] < target):
          l = m + 1	 # 搜索区间变为 [mid+1, right]
        elif (nums[m] > target):
          r = m - 1	 # 搜索区间变为 [left, mid-1]
      
      # 检查索引是否出界
      if (r < 0 or nums[r] != target):
        return -1
      return r
    ```

    当 `target` 比所有元素都小时，`right` 会被减到 -1，所以需要在最后防止越界：

    <img src="https://labuladong.github.io/algo/images/%e4%ba%8c%e5%88%86%e6%9f%a5%e6%89%be/4.jpg" alt="img" style="zoom:33%;" />

- **注意！**

  1、分析二分查找代码时，不要出现 else，全部展开成 **else if** 方便理解。

  2、注意「搜索区间」和 while 的终止条件，如果存在漏掉的元素，记得在最后检查。

  3、如需定义左闭右开的「搜索区间」搜索左右边界，只要在 `nums[mid] == target` 时做修改即可，搜索右侧时需要减一。

  4、如果将「搜索区间」全都统一成两端都闭，好记，只要稍改 `nums[mid] == target` 条件处的代码和返回的逻辑即可，**推荐拿小本本记下，作为二分搜索模板**。

## 1. 34 [Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (40.08%) | [`array`](https://leetcode.com/tag/array); [`binary-search`](https://leetcode.com/tag/binary-search) |

Given an array of integers `nums` sorted in non-decreasing order, find the starting and ending position of a given `target` value.

If `target` is not found in the array, return `[-1, -1]`.

You must write an algorithm with `O(log n)` runtime complexity.

**Example 1:**

```
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

**Example 2:**

```
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

**Example 3:**

```
Input: nums = [], target = 0
Output: [-1,-1] 
```

- **Constraints:**

  - `0 <= nums.length <= 105`

  - `-109 <= nums[i] <= 109`

  - `nums` is a non-decreasing array.

  - `-109 <= target <= 109`

- **Thoughts**

  The array is in non-decreasing order + `O(log n)` runtime complexity --> 题目指定了方法为二分搜索 --> 套用左边界和右边界模板

- **Solution**

  直接套用模板

  ```python
  from typing import List
  class Solution:
      def searchRange(self, nums: List[int], target: int) -> List[int]:
          res = []
          if nums is None or len(nums) == 0:
              return [-1, -1]
          
          # left bound
          l = 0
          r = len(nums) - 1
          while (l <= r):
              m = (l + r)//2
              if nums[m] == target:
                  r = m - 1
              elif nums[m] < target:
                  l = m + 1
              elif nums[m] > target:
                  r = m - 1
          if l >= len(nums) or nums[l] != target:
              res.append(-1)
          else:
              res.append(l)
  
          # right bound
          l = 0
          r = len(nums) - 1
          while (l <= r):
              m = (l + r)//2
              if nums[m] == target:
                  l = m + 1
              elif nums[m] < target:
                  l = m + 1
              elif nums[m] > target:
                  r = m - 1
          if r < 0 or nums[r] != target:
              res.append(-1)
          else:
              res.append(r)
          
          return res
  ```

  Time Complexity: O(logn) --> 其中 *n* 为数组的长度。二分查找的时间复杂度为 O*(log*n*)，一共会执行两次，因此总时间复杂度为 O*(log*n*)。

  Space Complexity: O(1)

## 2. 35 [Search Insert Position](https://leetcode.com/problems/search-insert-position/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (42.32%) | [`array`](https://leetcode.com/tag/array); [`binary-search`](https://leetcode.com/tag/binary-search) |

Given a sorted array of distinct integers and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You must write an algorithm with `O(log n)` runtime complexity. 

**Example 1:**

```
Input: nums = [1,3,5,6], target = 5
Output: 2
```

**Example 2:**

```
Input: nums = [1,3,5,6], target = 2
Output: 1
```

**Example 3:**

```
Input: nums = [1,3,5,6], target = 7
Output: 4
```

- **Constraints:**

  - `1 <= nums.length <= 104`

  - `-104 <= nums[i] <= 104`

  - `nums` contains **distinct** values sorted in **ascending** order.

  - `-104 <= target <= 104`

- **Solution**

  该题和基本的二分搜索一个元素的模板一样，除了如果没有找到该元素，返回该元素应该在的位置。通过画图推导，可发现该位置为`left`所指位

  ```python
  from typing import List
  class Solution:
      def searchInsert(self, nums: List[int], target: int) -> int:
          if (nums is None or len(nums) == 0):
              return 0
          
          l = 0
          r = len(nums) - 1
          while (l <= r):
              m = (l + r)//2
              if nums[m] == target:
                  return m
              elif nums[m] < target:
                  l = m + 1
              elif nums[m] > target:
                  r = m - 1
          return l
  ```

  Time Complexity: O(logn)

  Space Complexity: O(1)

## 3. 162 [Find Peak Element](https://leetcode.com/problems/find-peak-element/description/)

|  Category  |   Difficulty    |                            Likes                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (45.99%) | [`array`](https://leetcode.com/tag/array); [`binary-search`](https://leetcode.com/tag/binary-search) |

A peak element is an element that is strictly greater than its neighbors.

Given an integer array `nums`, find a peak element, and return its index. If the array contains multiple peaks, return the index to **any of the peaks**.

You may imagine that `nums[-1] = nums[n] = -∞`.

You must write an algorithm that runs in `O(log n)` time.

**Example 1:**

```
Input: nums = [1,2,3,1]
Output: 2
Explanation: 3 is a peak element and your function should return the index number 2.
```

**Example 2:**

```
Input: nums = [1,2,1,3,5,6,4]
Output: 5
Explanation: Your function can return either index number 1 where the peak element is 2, or index number 5 where the peak element is 6.
```

- **Constraints:**

  - `1 <= nums.length <= 1000`

  - `-231 <= nums[i] <= 231 - 1`

  - `nums[i] != nums[i + 1]` for all valid `i`.

- **Thoughts**

  从`O(logn)`可看出，该题目应用二分搜索解，然而二分搜索解的前提为有序数组

  - 首先要注意题目条件，在题目描述中出现了 nums[-1] = nums[n] = -∞，这就代表着 只要数组中存在一个元素比相邻元素大，那么沿着它一定可以找到一个峰值 --> 根据上述结论，我们就可以使用二分查找找到峰值

  - 查找时，左指针 l，右指针 r，以其保持左右顺序为循环条件
    根据左右指针计算中间位置 m，并比较 m 与 m+1 的值，如果 m 较大，则左侧存在峰值，r = m，如果 m + 1 较大，则右侧存在峰值，l = m + 1

    对于一个满足 `nums[x]>nums[x−1]` 的位置，x 的右边一定存在峰值；或对于一个满足 `nums[x] > nums[x + 1]`的位置，x 的左边一定存在峰值。


- **Solution**

  ```python
  from typing import List
  class Solution:
      def findPeakElement(self, nums: List[int]) -> int:
          if (nums is None or len(nums) == 0):
              return -1
          
          l = 0
          r = len(nums) - 1
          while (l < r):
              m = (l + r)//2
              if nums[m] > nums[m+1]:  # 峰值一定在nums[l]~nums[m]之间(前面元素)，故把r设定为m
                  r = m
              else:                    # nums[m] <= nums[m+1]
                  l = m + 1            # 峰值一定在nums[m+1]~nums[r]之间(后面元素)
          return l
  ```

​		Time Complexity: O(logN)

​		Space Complexity: O(1)

## 4. 74 [Search a 2D Matrix](https://leetcode.com/problems/search-a-2d-matrix/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (44.53%) | [`array`](https://leetcode.com/tag/array); [`binary-search`](https://leetcode.com/tag/binary-search) |

Write an ==efficient== algorithm that searches for a value `target` in an `m x n` integer matrix `matrix`. This matrix has the following properties:

- Integers in each row are ==sorted== from left to right.
- The first integer of each row is greater than the last integer of the previous row.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/05/mat.jpg)

```
Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 3
Output: true
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/10/05/mat2.jpg)

```
Input: matrix = [[1,3,5,7],[10,11,16,20],[23,30,34,60]], target = 13
Output: false
```

- **Constraints:**

  - `m == matrix.length`

  - `n == matrix[i].length`

  - `1 <= m, n <= 100`

  - `-104 <= matrix[i][j], target <= 104`

- **Thought**
  - 从该题目描述的矩阵性质可得，该矩阵可以展开为一个sorted array，接着使用二分搜索模板
  - 难点在于如何把二维矩阵转换成一维数组
    - Convert from 1D array to 2D matrix: `x = index//col`; `y = index%col`
    - Convert from 2D matrix to 1D array: `index = x*col + y`

- **Solution**

  ```python
  class Solution:
      def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
          # S1: suppose there is an original 1D array and convert to 2D array: x = index//col; y = index%col
          # S2: apply binary search to find the target value   
  
          if (matrix is None or len(matrix) == 0):
              return False
          
          row = len(matrix)
          col = len(matrix[0])
          l = 0
          r = row*col - 1
  
          while (l <= r):
              m = (l + r)//2
              element = matrix[m//col][m%col]    # convert from 2D to 1D
              if element == target:
                  return True
              elif element < target:
                  l = m + 1
              elif element > target:
                  r = m - 1
          return False
  ```

  - Time Complexity: O(logN)

    Space Complexity: O(1)

## 5. 875 [Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/description/)

|  Category  |   Difficulty    |                          Tags                           |
| :--------: | :-------------: | :-----------------------------------------------------: |
| algorithms | Medium (54.48%) | [`two-pointers`](https://leetcode.com/tag/two-pointers) |

Koko loves to eat bananas. There are `n` piles of bananas, the `ith` pile has `piles[i]` bananas. The guards have gone and will come back in `h` hours.

Koko can decide her bananas-per-hour eating speed of `k`. Each hour, she chooses some pile of bananas and eats `k` bananas from that pile. If the pile has less than `k` bananas, she eats all of them instead and will not eat any more bananas during this hour.

Koko likes to eat slowly but still wants to finish eating all the bananas before the guards return.

Return *the minimum integer* `k` *such that she can eat all the bananas within* `h` *hours*.

**Example 1:**

```
Input: piles = [3,6,7,11], h = 8
Output: 4
```

**Example 2:**

```
Input: piles = [30,11,23,4,20], h = 5
Output: 30
```

**Example 3:**

```
Input: piles = [30,11,23,4,20], h = 6
Output: 23
```

- **Constraints:**

  - `1 <= piles.length <= 104`

  - `piles.length <= h <= 109`

  - `1 <= piles[i] <= 109`

- **Thoughts**

  - 由于吃香蕉的速度和是否可以在规定时间内吃掉所有香蕉之间存在单调性，因此可以使用二分查找的方法得到最小速度 k。
  - 这道题的重点是确定吃香蕉的速度范围 --> 由于每小时都要吃香蕉，即每小时至少吃 1 个香蕉，因此二分查找的下界是 1；由于每小时最多吃一堆香蕉，即每小时吃的香蕉数目不会超过最多的一堆中的香蕉数目，因此二分查找的上界是最多的一堆中的香蕉数目 `max(piles)`。
  - <u>Procedures:</u>
    - For each speed, count the total hour for Koko eating all bananas
    - If the total hour satisfies the condition (<= h), set the result to be the minimum of current speed k and the previous res; As the current k satisfies the condition, decrease the speed to the left portion--> check to see if can find a smaller k --> `r = k - 1`
    - Not satisfy the condition --> increase speed to the right portion `l = k + 1`

- **Solution**

  ```python
  def minEatingSpeed(self, piles: List[int], h: int) -> int:
    l, r = 0, max(piles)     # boundaries for searching the speed [1, 2, ..., max(piles)]
    res = max(piles)
    
    while l <= r:
      k = (l + r)//2
      hour = 0      				# the total hour for Koko eating all bananas
      for p in piles:
        hour += math.ceil(p / k)  # Koco can only eat one pile at a time
      
      if hour <= h:					# satisfy the condition
        res = min(res, k)
        r = k - 1
      else:
        l = k + 1
      
   return res
  ```

  - Time Complexity: $O(n + nlogm) = O(nlogm) = O(len(piles) * log(max(piles)))$，其中 n 是数组 piles 的长度，m 是数组 piles 中的最大值。需要 O(n) 的时间遍历数组找到最大值 m，二分查找需要执行 O(logm) 轮，每一轮二分查找需要 O(n) 的时间，因此总时间复杂度是 O(nlogm)。

    Space Complexity: O(1)

## 6. 33 [Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (37.90%) | [`array`](https://leetcode.com/tag/array);[`binary-search`](https://leetcode.com/tag/binary-search) |

There is an integer array `nums` sorted in ascending order (with **distinct** values).

Prior to being passed to your function, `nums` is **possibly rotated** at an unknown pivot index `k` (`1 <= k < nums.length`) such that the resulting array is `[nums[k], nums[k+1], ..., nums[n-1], nums[0], nums[1], ..., nums[k-1]]` (**0-indexed**). For example, `[0,1,2,4,5,6,7]` might be rotated at pivot index `3` and become `[4,5,6,7,0,1,2]`.

Given the array `nums` **after** the possible rotation and an integer `target`, return *the index of* `target` *if it is in* `nums`*, or* `-1` *if it is not in* `nums`.

You must write an algorithm with `O(log n)` runtime complexity.

**Example 1:**

```
Input: nums = [4,5,6,7,0,1,2], target = 0
Output: 4
```

**Example 2:**

```
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
```

**Example 3:**

```
Input: nums = [1], target = 0
Output: -1
```

- **Constraints:**

  - `1 <= nums.length <= 5000`

  - `-104 <= nums[i] <= 104`

  - All values of `nums` are **unique**.

  - `nums` is an ascending array that is possibly rotated.

  - `-104 <= target <= 104`

- **Thoughts**

  - 从题干的`sorted`和`O(logn)`可知，该题目应使用二分搜索方法。但是这道题中，数组本身不是有序的，进行旋转后只保证了数组的局部是有序的，这还能进行二分查找吗？答案是可以的。

    可以发现的是，<font color=blue>**我们将数组从中间分开成左右两部分的时候，一定有一部分的数组是有序的**</font>。拿示例来看，我们从 6 这个位置分开以后数组变成了 [4, 5, 6] 和 [7, 0, 1, 2] 两个部分，其中左边 [4, 5, 6] 这个部分的数组是有序的，其他也是如此。

    这启示我们可以在常规二分查找的时候查看当前 mid 为分割位置分割出来的两个部分 [l, mid] 和 [mid + 1, r] 哪个部分是有序的，并根据有序的那个部分确定我们该如何改变二分查找的上下界，因为我们能够根据有序的那部分判断出 target 在不在这个部分：

    - 如果` [l, mid - 1]`是有序数组，且 target 的大小满足 [nums[l],nums[mid])，则我们应该将搜索范围缩小至 `[l, mid - 1]`，否则在 `[mid + 1, r]` 中寻找。
    - 如果 `[mid, r]` 是有序数组，且 target 的大小满足 [nums[mid+1],nums[r]]，则我们应该将搜索范围缩小至 `[mid + 1, r]`，否则在 `[l, mid - 1]` 中寻找。

  - 总结：旋转数组就是一个有序数组切分为两个数组，只要用一次二分找到最小值min，再将min与taeget判断在哪一个数组，最后再二分一次就找到了。这道题和平常二分法查找的不同就在于,把一个有序递增的数组分成了,两个递增的数组,我们需要做的就是**判断这个数在哪一个递增的数组中,然后再去用常规的二分法去解决**

- **Solution**

  ```python
  def search(self, nums: List[int], target: int) -> int:
    # check which portion the target is and use binary search
    l, r = 0, len(nums) - 1
  
    while l <= r:
      m = (l + r)//2
      if target == nums[m]:
        return m
  
      if nums[l] <= nums[m]:    # left portion是有序数组 --> nums[l] <= target <= nums[m]
        if nums[l] <= target < nums[m]:   # target is in the left portion 
          r = m - 1             # search in the left portion
        else:
          l = m + 1             # search in the right portion
      else:                     # right portion是有序数组 --> nums[m] <= target <= nums[r]
        if nums[m] < target <= nums[r]:
          l = m + 1             # search in the right portion
        else:
          r = m - 1             # search in the left portion
    return -1
  ```

  - Time Complexity: O(logn)

    Space Complexity: O(1)

## 6. 981 [Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store/description/)

|  Category  |   Difficulty    |                    Tags                     |
| :--------: | :-------------: | :-----------------------------------------: |
| algorithms | Medium (52.68%) | [`greedy`](https://leetcode.com/tag/greedy) |

Design a time-based key-value data structure that can store multiple values for the same key at different time stamps and retrieve the key's value at a certain timestamp.

Implement the `TimeMap` class:

- `TimeMap()` Initializes the object of the data structure.
- `void set(String key, String value, int timestamp)` Stores the key `key` with the value `value `at the given time `timestamp`.
- `String get(String key, int timestamp)` Returns a value such that `set` was called previously, with `timestamp_prev <= timestamp`. If there are multiple such values, it returns the value associated with the largest `timestamp_prev`. If there are no values, it returns `""`. 

**Example 1:**

```java
Input
["TimeMap", "set", "get", "get", "set", "get", "get"]
[[], ["foo", "bar", 1], ["foo", 1], ["foo", 3], ["foo", "bar2", 4], ["foo", 4], ["foo", 5]]
Output
[null, null, "bar", "bar", null, "bar2", "bar2"]

Explanation
TimeMap timeMap = new TimeMap();
timeMap.set("foo", "bar", 1);  // store the key "foo" and value "bar" along with timestamp = 1.
timeMap.get("foo", 1);         // return "bar"
timeMap.get("foo", 3);         // return "bar", since there is no value corresponding to foo at timestamp 3 and timestamp 2, then the only value is at timestamp 1 is "bar".
timeMap.set("foo", "bar2", 4); // store the key "foo" and value "bar2" along with timestamp = 4.
timeMap.get("foo", 4);         // return "bar2"
timeMap.get("foo", 5);         // return "bar2"
```

- **Constraints:**

  - `1 <= key.length, value.length <= 100`

  - `key` and `value` consist of lowercase English letters and digits.

  - `1 <= timestamp <= 107`

  - ==All the timestamps `timestamp` of `set` are strictly increasing.==

  - At most `2 * 105` calls will be made to `set` and `get`.

- **Thoughts**

  - 为实现 `get` 操作，我们需要用一个哈希表存储 `set` 操作传入的数据。具体地，哈希表的键为字符串 `key`，值为一个二元组列表，二元组中存储的是时间戳 `timestamp` 和值 `value`。
  
  
    - 关键点：由于set 操作中的时间戳都是严格递增的，因此二元组列表中保存的时间戳也是严格递增的 --> sorted array --> binary search
  
    - We use hash map to lookup ordered `{timestamp, value}` pairs by key in O(1). Then, we use binary search to find the value with a timestamp less or equal than the requested one.
  


- **Solution**

  ```python
  from collections import defaultdict
  class TimeMap:
  
      def __init__(self):
        self.timeMap = {}
        # self.timeMap = defaultdict(list)
  
      def set(self, key: str, value: str, timestamp: int) -> None:
        if key not in self.timeMap:
          self.timeMap[key] = []
        self.timeMap[key].append([timemap, value])
  
      # find the maximum number less than timestamp --> sorted --> binary search 
      def get(self, key: str, timestamp: int) -> str:
        if key not in self.timeMap:
          return ''
        
        res = ''
        values = self.timeMap[key]      # get the list of values
        l, r = 0, len(values) - 1
        while l <= r:
          m = (l + r)//2
          if values[m][0] <= timestamp: 
            res = values[m][1]
            l = m + 1										# find a bigger value
          else:
            r = m - 1										# find a smaller value
        return res
  ```

  - Time Complexity: O(1) for `init__` and `set`; O(logn) for `get` (binary search)

    Space Complexity: O(n) --> create a hashmap