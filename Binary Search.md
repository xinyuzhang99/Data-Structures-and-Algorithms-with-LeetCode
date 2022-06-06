# Binary Search 二分搜索

<img src="https://labuladong.github.io/algo/images/%e4%ba%8c%e5%88%86%e6%9f%a5%e6%89%be/poem.png" alt="img" style="zoom: 67%;" />

- **搜索一个元素** (LeetCode 704)

  ```python
  def search(self, nums: List[int], target: int) -> int:
          if nums is None or len(nums) == 0:
              return -1
  
          l = 0
          r = len(nums) - 1
          while l <= r:               # 搜索区间为空的时候终止
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
      return l
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
          # S1: convert the 2D matrix to 1D array
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

  Time Complexity: O(logN)

  Space Complexity: O(1)