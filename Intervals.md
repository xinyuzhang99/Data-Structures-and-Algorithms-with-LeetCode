# Intervals

所谓区间问题，就是线段问题，让你合并所有线段、找出线段的交集等等。主要有两个技巧：

**1、排序**。常见的排序方法就是按照区间起点排序，或者<u><font color=blue>**先按照起点升序排序，若起点相同，则按照终点降序排序**</font></u>。当然，如果你非要按照终点排序，无非对称操作，本质都是一样的。

**2、画图**。就是说不要偷懒，勤动手，两个区间的相对位置到底有几种可能，不同的相对位置我们的代码应该怎么去处理。

- **区间调度问题的思路**

  1. **第一个场景**，假设现在只有一个会议室，还有若干会议，你如何将尽可能多的会议安排到这个会议室里？

     这个问题需要将这些会议（区间）按结束时间（右端点）排序，然后进行处理，详见前文 [贪心算法做时间管理](https://labuladong.github.io/algo/di-er-zhan-a01c6/tan-xin-le-9bedf/tan-xin-su-c41e8/)。

  2. **第二个场景**，给你若干较短的视频片段，和一个较长的视频片段，请你从较短的片段中尽可能少地挑出一些片段，拼接出较长的这个片段。

     这个问题需要将这些视频片段（区间）按开始时间（左端点）排序，然后进行处理，详见后文 [剪视频剪出一个贪心算法](https://labuladong.github.io/algo/di-er-zhan-a01c6/tan-xin-le-9bedf/jian-shi-p-4302c/)。

  3. **第三个场景**，给你若干区间，其中可能有些区间比较短，被其他区间完全覆盖住了，请你删除这些被覆盖的区间。

     这个问题需要将这些区间按左端点排序，然后就能找到并删除那些被完全覆盖的区间了，详见后文 [删除覆盖区间](https://labuladong.github.io/algo/di-san-zha-24031/jing-dian--a94a0/yi-ge-fang-93124/)。

  4. **第四个场景**，给你若干区间，请你将所有有重叠部分的区间进行合并。

     这个问题需要将这些区间按左端点排序，方便找出存在重叠的区间，详见后文 [合并重叠区间](https://labuladong.github.io/algo/di-san-zha-24031/jing-dian--a94a0/yi-ge-fang-93124/)。

  5. **第五个场景**，有两个部门同时预约了同一个会议室的若干时间段，请你计算会议室的冲突时段。

     这个问题就是给你两组区间列表，请你找出这两组区间的交集，这需要你将这些区间按左端点排序，详见后文 [区间交集问题](https://labuladong.github.io/algo/di-san-zha-24031/jing-dian--a94a0/yi-ge-fang-93124/)。

  6. **第六个场景**，假设现在只有一个会议室，还有若干会议，如何安排会议才能使这个会议室的闲置时间最少？

     这个问题需要动动脑筋，说白了这就是个 0-1 背包问题的变形：

     会议室可以看做一个背包，每个会议可以看做一个物品，物品的价值就是会议的时长，请问你如何选择物品（会议）才能最大化背包中的价值（会议室的使用时长）？

     当然，这里背包的约束不是一个最大重量，而是各个物品（会议）不能互相冲突。把各个会议按照结束时间进行排序，然后参考前文 [0-1 背包问题详解](https://labuladong.github.io/algo/di-er-zhan-a01c6/bei-bao-le-34bd4/jing-dian--28f3c/) 的思路即可解决，等我以后有机会可以写一写这个问题。

  7. **第七个场景**，就是本文想讲的场景，给你若干会议，让你合理申请会议室。**给你输入若干时间区间，让你计算同一时刻「最多」有几个区间重叠**。题目的关键点在于，给你任意一个时刻，你是否能够说出这个时刻有几个会议？如果可以做到，那我遍历所有的时刻，找个最大值，就是需要申请的会议室数量。

- **使用贪心算法解决区间调度问题**

  思路其实很简单，可以分为以下三步：

  1、从区间集合 `intvs` 中选择一个区间 `x`，这个 `x` 是在当前所有区间中**结束最早的**（`end` 最小）。（按每个区间的 `end` 数值升序排序）

  2、把所有与 `x` 区间相交的区间从区间集合 `intvs` 中删除。

  3、重复步骤 1 和 2，直到 `intvs` 为空为止。之前选出的那些 `x` 就是最大不相交子集。

  <img src="https://labuladong.github.io/algo/images/interval/1.gif" alt="img" style="zoom:50%;" />

  关键在于，如何去除与 `x` 相交的区间，选择下一轮循环的 `x` 呢？

  **由于我们事先排了序**，不难发现所有与 `x` 相交的区间必然会与 `x` 的 `end` 相交；如果一个区间不想与 `x` 的 `end` 相交，它的 `start` 必须要大于（或等于）`x` 的 `end`：

  <img src="https://labuladong.github.io/algo/images/interval/2.jpg" alt="img" style="zoom:50%;" />

  ```python
  ## find the maximum number of overlapping intervals
  from typing import List
  def intervalSchedule(intvs: List[List[int]]) -> int:
      if len(intvs) == 0:
          return 0
      # 按 end 升序排序
      intvs.sort(key=lambda x: x[1])
      # 至少有一个区间不相交
      count = 0
      lastEnd = float('-inf')
      for interval in intvs:
          if interval[0] >= lastEnd:  # or start > lastEnd
              # 找到下一个选择的区间了（去除与x相交的空间实际上是略过，不是真正的去除）
              count += 1
              lastEnd = interval[1]
      return count
  ```

## 1. 57 [Insert Interval](https://leetcode.com/problems/insert-interval/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (37.47%) | [`array`](https://leetcode.com/tag/array); [`sort`](https://leetcode.com/tag/sort) |

You are given an array of non-overlapping intervals `intervals` where `intervals[i] = [starti, endi]` represent the start and the end of the `ith` interval and `intervals` is sorted in ascending order by `starti`. You are also given an interval `newInterval = [start, end]` that represents the start and end of another interval.

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
      intervals.sort(key=lambda u: (u[0], -u[1]))   # 起点升序排列，终点降序排列，这样只用考虑终点即可判断区间是否覆盖 --> O(NlogN)
      res = n
      rMax = intervals[0][1]
      
      for i in range(1, n):													# O(N)
        if intervals[i][1] <= rMax:									# the interval is covered 
          res -= 1
        else:
          rMax = intervals[i][1]										# the interval is not covered --> update the right boundary
      return res
    ```

    - Time complexity: $O(NlogN + N) = O(NlogN)$

      Space complexity: $O(logN)$ --> the sorting needs to take $O(logN)$ space

## 3. 56 [Merge Intervals](https://leetcode.com/problems/merge-intervals/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (45.05%) | [`array`](https://leetcode.com/tag/array); [`sort`](https://leetcode.com/tag/sort) |

Given an array of `intervals` where `intervals[i] = [starti, endi]`, merge all overlapping intervals, and return *an array of the non-overlapping intervals that cover all the intervals in the input*.

**Example 1:**

```
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlap, merge them into [1,6].
```

**Example 2:**

```
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping. 
```

- **Constraints:**

  - `1 <= intervals.length <= 104`

  - `intervals[i].length == 2`

  - `0 <= starti <= endi <= 104`

- **Thoughts**

  我们解决区间问题的一般思路是先排序，然后观察规律。一个区间可以表示为`[start, end]`，对于区间合并问题，按`start`升序排序。这道题的关键点在于，当merge了两个重叠区间并放在result里后，可能下一个区间和这个融合后的区间依旧重叠，所以每一次都要将区间和result里的最后一个区间的end进行比较，如果重叠了要进行融合（改变区间的右边界end）。

- **Solution**

  ```python
  def merge(self, intervals: List[List[int]]) -> List[List[int]]:
    intervals.sort()      # =: intervals.sort(key=lambda x:x[0])
    res = [intervals[0]]
    
    for start, end in intervals[1:]:
      lastEnd = res[-1][1]
      if end <= lastEnd:  # overlap
        res[-1][1] = end
      else:								# not overlap
        res.append([start, end])
    return res
  ```

  - Time complexity: $O(NlogN + N) = O(NlogN)$

    Space complexity: $O(N)$ / $O(logN)$ --> the sorting needs to take $O(logN)$ space

## 4. 986 [Interval List Intersections](https://leetcode.com/problems/interval-list-intersections/description/)

|  Category  |   Difficulty    |                  Tags                   |
| :--------: | :-------------: | :-------------------------------------: |
| algorithms | Medium (71.21%) | [`math`](https://leetcode.com/tag/math) |

You are given two lists of closed intervals, `firstList` and `secondList`, where `firstList[i] = [starti, endi]` and `secondList[j] = [startj, endj]`. Each list of intervals is pairwise **disjoint** and in **sorted order**.

Return *the intersection of these two interval lists*.

A **closed interval** `[a, b]` (with `a <= b`) denotes the set of real numbers `x` with `a <= x <= b`.

The **intersection** of two closed intervals is a set of real numbers that are either empty or represented as a closed interval. For example, the intersection of `[1, 3]` and `[2, 4]` is `[2, 3]`.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2019/01/30/interval1.png" alt="img" style="zoom: 50%;" />

```
Input: firstList = [[0,2],[5,10],[13,23],[24,25]], secondList = [[1,5],[8,12],[15,24],[25,26]]
Output: [[1,2],[5,5],[8,10],[15,23],[24,24],[25,25]]
```

**Example 2:**

```
Input: firstList = [[1,3],[5,9]], secondList = []
Output: []
```

- **Constraints:**

  - `0 <= firstList.length, secondList.length <= 1000`

  - `firstList.length + secondList.length >= 1`

  - `0 <= starti < endi <= 109`

  - `endi < starti+1`

  - `0 <= startj < endj <= 109 `

  - `endj < startj+1`

- **Thought**

  - 由于题目说给定的两个区间列表都是排好序的，所以可以考虑使用双指针 --> 一个指针扫描 A 数组，一个指针扫描 B 数组，根据子区间的两端，求出一个交集区间 --> 指针移动，直至指针越界，得到由交集区间组成的数组。

    求出交集区间方法：

    <img src="/Users/xinyuzhang/Downloads/IMG_AC935F477DCE-1.jpeg" alt="IMG_AC935F477DCE-1" style="zoom: 33%;" />

  -  <font color=red>**关键点：双指针移动的时机**</font> --> 

    - 求完一个交集区间后，较早结束的子区间，不可能再和别的子区间重叠，它的指针要移动。

    - 较长的子区间还有可能和别人重叠，它的指针暂时不动。

      --> A ends early: `if aend < bend: i += 1`

      ​	  B ends early: `if bend < aend: j += 1` 

- **Solution**

  ```python
  def intervalIntersection(self, firstList: List[List[int]], secondList: List[List[int]]) -> List[List[int]]:
          res = []
          i = j = 0
  
          while i < len(firstList) and j < len(secondList):
              astart, aend = firstList[i][0], firstList[i][1]
              bstart, bend = secondList[j][0], secondList[j][1]
              
              # overlapping --> find intersection
              if bend >= astart and aend >= bstart:          
                  res.append([max(astart, bstart), min(aend, bend)])
  
              # move the pointer 
              # 求完一个交集区间后，较早结束的子区间，不可能再和别的子区间重叠，它的指针要移动。
              # 较长的子区间还有可能和别人重叠，它的指针暂时不动。
              if aend < bend:  
                  i += 1
              else:
                  j += 1           
          return res
  ```

  - Time complexity: $O(M + N)$ --> traverse through two lists

    Space complexity: $O(M + N)$ for the maximum number of elements in  `res` array

## 区间调度问题 (Interval Scheduling)

### 5. 435 [Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/description/)

|  Category  |   Difficulty    |                    Tags                     |
| :--------: | :-------------: | :-----------------------------------------: |
| algorithms | Medium (48.44%) | [`greedy`](https://leetcode.com/tag/greedy) |

Given an array of intervals `intervals` where `intervals[i] = [starti, endi]`, return *the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping*.

**Example 1:**

```
Input: intervals = [[1,2],[2,3],[3,4],[1,3]]
Output: 1
Explanation: [1,3] can be removed and the rest of the intervals are non-overlapping.
```

**Example 2:**

```
Input: intervals = [[1,2],[1,2],[1,2]]
Output: 2
Explanation: You need to remove two [1,2] to make the rest of the intervals non-overlapping.
```

**Example 3:**

```
Input: intervals = [[1,2],[2,3]]
Output: 0
Explanation: You don't need to remove any of the intervals since they're already non-overlapping.
```

- **Constraints:**

  - `1 <= intervals.length <= 105`

  - `intervals[i].length == 2`

  - `-5 * 104 <= starti < endi <= 5 * 104`

- **Thoughts**

  这道题为区间调度问题，要求minimum，所以使用贪心算法。思路为：

  1、从区间集合 `intvs` 中选择一个区间 `x`，这个 `x` 是在当前所有区间中**结束最早的**（`end` 最小）。（按每个区间的 `end` 数值升序排序）

  2、把所有与 `x` 区间相交的区间从区间集合 `intvs` 中删除。

  3、重复步骤 1 和 2，直到 `intvs` 为空为止。之前选出的那些 `x` 就是最大不相交子集。

- **Solution**

  ```python
  # 1. sort the intervals based on the end (ascending)
  # 2. check if interval_start < last_end, remove --> count += 1; if interval_start >= last_end, update last_end
  def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
    count = 0  # number of intervals needed to be removed
    intervals.sort(key=lambda x: x[1])
  
    lastEnd = float('-inf')
    for interval in intervals:
      if interval[0] < lastEnd:
        count += 1
      else:
        lastEnd = interval[1]
    return count      
  ```

  - Time complexity: $NO(logN) + O(N) = NO(logN)$ 

    Space complexity: O(1)

### 6. 452 [Minimum Number of Arrows to Burst Balloons](https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/description/)

|  Category  |   Difficulty    |                    Tags                     |
| :--------: | :-------------: | :-----------------------------------------: |
| algorithms | Medium (52.96%) | [`greedy`](https://leetcode.com/tag/greedy) |

There are some spherical balloons taped onto a flat wall that represents the XY-plane. The balloons are represented as a 2D integer array `points` where `points[i] = [xstart, xend]` denotes a balloon whose **horizontal diameter** stretches between `xstart` and `xend`. You do not know the exact y-coordinates of the balloons.

Arrows can be shot up **directly vertically** (in the positive y-direction) from different points along the x-axis. A balloon with `xstart` and `xend` is **burst** by an arrow shot at `x` if `xstart <= x <= xend`. There is **no limit** to the number of arrows that can be shot. A shot arrow keeps traveling up infinitely, bursting any balloons in its path.

Given the array `points`, return *the **minimum** number of arrows that must be shot to burst all balloons*. 

**Example 1:**

```
Input: points = [[10,16],[2,8],[1,6],[7,12]]
Output: 2
Explanation: The balloons can be burst by 2 arrows:
- Shoot an arrow at x = 6, bursting the balloons [2,8] and [1,6].
- Shoot an arrow at x = 11, bursting the balloons [10,16] and [7,12].
```

**Example 2:**

```
Input: points = [[1,2],[3,4],[5,6],[7,8]]
Output: 4
Explanation: One arrow needs to be shot for each balloon for a total of 4 arrows.
```

**Example 3:**

```
Input: points = [[1,2],[2,3],[3,4],[4,5]]
Output: 2
Explanation: The balloons can be burst by 2 arrows:
- Shoot an arrow at x = 2, bursting the balloons [1,2] and [2,3].
- Shoot an arrow at x = 4, bursting the balloons [3,4] and [4,5].
```

- **Constraints:**

  - `1 <= points.length <= 105`

  - `points[i].length == 2`

  - `-231 <= xstart < xend <= 231 - 1`

- **Thoughts**

  这个问题和区间调度算法基本一模一样！如果最多有 `n` 个不重叠的区间，那么就至少需要 `n` 个箭头穿透所有区间：

  <img src="https://labuladong.github.io/algo/images/interval/3.jpg" alt="img" style="zoom:50%;" />

  只是有一点不一样，在 `intervalSchedule` 算法中，如果两个区间的边界触碰，不算重叠；而按照这道题目的描述，箭头如果碰到气球的边界气球也会爆炸，所以说相当于区间的边界触碰也算重叠：

  <img src="https://labuladong.github.io/algo/images/interval/4.jpg" alt="img" style="zoom:50%;" />

  --> 这道题和模板的区别仅在于判断时当`start > lastEnd`，才能判定为non-overlapping interval

- **Solution**

  ```python
  # minimum number of arrows that must be shot to burst all balloons --> number of non-overlapping intervals --> similar to LeetCode 435
  # if start >= lastEnd: count += 1, lastEnd = p[1]
      def findMinArrowShots(self, points: List[List[int]]) -> int:
          count = 0
          points.sort(key=lambda x:x[1])
  
          lastEnd = float('-inf')
          for p in points:
              if p[0] > lastEnd:
                  count += 1
                  lastEnd = p[1]
          return count
  ```

  - Time complexity: $NO(logN) + O(N) = NO(logN)$ 

    Space complexity: O(1)

### 7. 252 Meeting Rooms

Given an array of meeting time `intervals` where `intervals[i] = [starti, endi]`, determine if a person could attend all meetings.

**Example 1:**

```
Input: intervals = [[0,30],[5,10],[15,20]]
Output: false
```

**Example 2:**

```
Input: intervals = [[7,10],[2,4]]
Output: true
```

- **Constraints:**

  - `0 <= intervals.length <= 104`

  - `intervals[i].length == 2`

  - `0 <= starti < endi <= 106`

- **Thoughts**

  这道题目问是否能参加所有会议，其实就是要确认**非重叠会议**的数量等于所有会议的数量 --> 区间调度问题，使用贪心算法解决，直接使用模板即可

- **Solution**

  ```python
  # attend all meetings: number of non-overlapping intervals = n
  class Solution:
      def canAttendMeetings(self, intervals: List[List[int]]) -> bool:
          n = len(intervals)
          intervals.sort(key=lambda x:x[1])
          availableMeeting = 0
          lastEnd = 0
  
          for interval in intervals:
              if interval[0] >= lastEnd:
                  availableMeeting += 1
                  lastEnd = interval[1]
          return availableMeeting == n
  ```

  - Time complexity: $NO(logN) + O(N) = NO(logN)$ 

    Space complexity: O(1)

### 8. 253 Meeting Rooms II

Given an array of meeting time intervals `intervals` where `intervals[i] = [starti, endi]`, return *the minimum number of conference rooms required*.

**Example 1:**

```
Input: intervals = [[0,30],[5,10],[15,20]]
Output: 2
```

**Example 2:**

```
Input: intervals = [[7,10],[2,4]]
Output: 1
```

- **Constraints:**

  - `1 <= intervals.length <= 104`

  - `0 <= starti < endi <= 106`

- **Thoughts**

  这道题乍看和之前的题目类似，但实际不同。<font color=blue>**遇见区间问题时要仔细分析具体题意是什么！**</font>

  如果会议之间的时间有重叠，那就得额外申请会议室来开会，想求至少需要多少间会议室，就是让你计算同一时刻最多有多少会议在同时进行。换句话说，**如果把每个会议的起始时间看做一个线段区间，那么题目就是让你求最多有几个重叠区间**，仅此而已。--> <font color=blue>**给你输入若干时间区间，让你计算同一时刻「最多」有几个区间重叠**。</font>

  红色的点代表每个会议的开始时间点，绿色的点代表每个会议的结束时间点。

  现在假想有一条带着计数器的线，在时间线上从左至右进行扫描，每遇到红色的点，计数器 `count` 加一，每遇到绿色的点，计数器 `count` 减一：

  <img src="https://labuladong.github.io/algo/images/%e5%ae%89%e6%8e%92%e4%bc%9a%e8%ae%ae%e5%ae%a4/2.jpeg" alt="img" style="zoom:50%;" />

  **这样一来，每个时刻有多少个会议在同时进行，就是计数器 `count` 的值，`count` 的最大值，就是需要申请的会议室数量**。

- **Solution**

  ```python
  # the minimum number of conference rooms required --> the maximum number of overlapping intervals at any given time point --> 给你输入若干时间区间，让你计算同一时刻「最多」有几个区间重叠。
  # for each interval, if start < lastEnd, conference room += 1
      def minMeetingRooms(self, intervals: List[List[int]]) -> int:
          res = 0
          start = sorted([interval[0] for interval in intervals])
          end = sorted([interval[1] for interval in intervals])
          # start = [interval[0] for interval in intervals]
          # end = [interval[1] for interval in intervals]
          # start.sort()
          # end.sort()
  
          i, j = 0, 0
          count = 0
          while i < len(start):  # or: while i < len(start) and j < len(end) --> if i = len(start), no need to check j's status as end is always behind the start
              # case 1: add a meeting room
              if start[i] < end[j]:
                  count += 1
                  i += 1
              # case 2: remove a meeting room
              elif start[i] > end[j]:
                  count -= 1
                  j += 1
              # case 3: keep meeting room number unchanged, move the two pointers to the next start and next end --> this step can be ignored and merge into `elif start[i] >= end[j]`
              elif start[i] == end[j]:    # remain the same
                  i += 1
                  j += 1
              
              res = max(res, count)
  
          return res
  ```

  - Time complexity: NO(logN) + O(N) = NO(logN) --> `sort` + `while`

    Space complexity: O(N) --> build `start` and `end` lists

