# Intervals

所谓区间问题，就是线段问题，让你合并所有线段、找出线段的交集等等。主要有两个技巧：

**1、排序**。常见的排序方法就是按照区间起点排序，或者先按照起点升序排序，若起点相同，则按照终点降序排序。当然，如果你非要按照终点排序，无非对称操作，本质都是一样的。

**2、画图**。就是说不要偷懒，勤动手，两个区间的相对位置到底有几种可能，不同的相对位置我们的代码应该怎么去处理。

## 1. 57 [Insert Interval](https://leetcode.com/problems/insert-interval/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (37.47%) | [`array`](https://leetcode.com/tag/array); [`sort`](https://leetcode.com/tag/sort) |

ou are given an array of non-overlapping intervals `intervals` where `intervals[i] = [starti, endi]` represent the start and the end of the `ith` interval and `intervals` is sorted in ascending order by `starti`. You are also given an interval `newInterval = [start, end]` that represents the start and end of another interval.

Insert `newInterval` into `intervals` such that `intervals` is still sorted in ascending order by `starti` and `intervals` still does not have any overlapping intervals (merge overlapping intervals if necessary).

Return `intervals` *after the insertion*.

**Example 1:**

```
Input: intervals = [[1,3],[6,9]], newInterval = [2,5]
Output: [[1,5],[6,9]]
```

**Example 2:**

```
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps with [3,5],[6,7],[8,10].
```

- **Constraints:**

  - `0 <= intervals.length <= 104`

  - `intervals[i].length == 2`

  - `0 <= starti <= endi <= 105`

  - `intervals` is sorted by `starti` in **ascending** order.

  - `newInterval.length == 2`

  - `0 <= start <= end <= 105`

- **Thoughts**

  用指针去扫 `intervals`，最多可能有三个阶段：

  1. 不重叠的绿区间，在蓝区间的左边
  2. 有重叠的绿区间
  3. 不重叠的绿区间，在蓝区间的右边

  ![image.png](https://pic.leetcode-cn.com/1604465027-kDWfBc-image.png)

  idea: iterate through all intervals and find the insertion point (check if overlapping)

  check overlapping: check if the new interval is before or after an interval

  --> left_new <= right_old and right_new >= left_old

  --> then take the minimum of left then maximum of right

  next step: check if the new merging interval overlaps with the next interval

  <img src="/Users/xinyuzhang/Downloads/IMG_4FFCA484A217-1.jpeg" alt="IMG_4FFCA484A217-1" style="zoom:50%;" />

- **Solution**

  ```python
  def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
    res = []
    for i in range(len(intervals)):
      # 1. non-overlapping: before
      if newInterval[1] < intervals[i][0]:
        res.append(newInterval)
        return res + intervals[i:]
      # 2. non-overlapping: after
    elif intervals[i][1] < newInterval[0]:
      res.append(intervals[i])
      # 3. overlapping
    else:
      newInterval = [min(intervals[i][0], newInterval[0]), max(intervals[i][1], newInterval[1])]
  
    res.append(newInterval)
    return res
  ```

  - Time complexity: O(N)

    Space complexity: O(1)

## 2. 1288 [Remove Covered Intervals](https://leetcode.com/problems/remove-covered-intervals/description/)

|  Category  |   Difficulty    |  Tags   |
| :--------: | :-------------: | :-----: |
| algorithms | Medium (57.43%) | Unknown |

Given an array `intervals` where `intervals[i] = [li, ri]` represent the interval `[li, ri)`, remove all intervals that are covered by another interval in the list.

The interval `[a, b)` is covered by the interval `[c, d)` if and only if `c <= a` and `b <= d`.

Return *the number of remaining intervals*.

**Example 1:**

```
Input: intervals = [[1,4],[3,6],[2,8]]
Output: 2
Explanation: Interval [3,6] is covered by [2,8], therefore it is removed.
```

**Example 2:**

```
Input: intervals = [[1,4],[2,3]]
Output: 1
```

- **Constraints:**

  - `1 <= intervals.length <= 1000`

  - `intervals[i].length == 2`

  - `0 <= li < ri <= 105`

  - All the given intervals are **unique**.

- **Solution**

  - <u>Method 1: Brute-force</u>

    用for循环遍历所有区间，对每个区间再使用一次for循环遍历其余所有区间，如果有覆盖区间则结果减1

    ```python
    def removeCoveredIntervals(self, intervals: List[List[int]]) -> int:
      # Method 1: Brute-force
      res = len(intervals)
    
      for i in range(len(intervals)):
        for j in range(len(intervals)):
          if i != j and intervals[i][0] >= intervals[j][0] and intervals[i][1] <= intervals[j][1]:
            res -= 1
            break  # 已经判断出这个区间是被某一个区间包围的了，就没必要再跟其他区间比较了，跳出内层for循环
      return res 
    ```

    - Time complexity: $O(N^2)$

      Space complexity: O(1)

  - <u>Method 2: Sort + traversal</u>

    为了减小时间复杂度 --> 减小一个for循环 --> 先将所有区间按左端点递增排序，右端点递减排序 --> 对于两个起点相同的区间，我们需要保证长的那个区间在上面（按照终点降序），这样才会被判定为覆盖，否则会被错误地判定为相交，少算一个覆盖区间。

    这道题目思考的关键还是在画图，画图才能分析出：**当区间左端点相同的时候，右端点靠后的应该放在前面**。

    <img src="https://pic.leetcode-cn.com/595511c4b95ff7234aba27ec4836bc5cc09d41ed12ffb44906aa78f602cf18d6-image.png" alt="image.png" style="zoom:50%;" />

    ```python
    def removeCoveredIntervals(self, intervals: List[List[int]]) -> int:
      n = len(intervals)
      intervals.sort(key=lambda u: (u[0], -u[1]))   # 起点升序排列，终点降序排列
      res = n
      rMax = intervals[0][1]
      
      for i in range(1, n):
        if intervals[i][1] <= rMax:
          res -= 1
        else:
          rMax = intervals[i][1]
      return res
    ```

    - Time complexity: $O(NlogN + N) = O(NlogN)$

      Space complexity: $O(logN)$