# Difference Array 差分数组

**差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减 --> range update query in O(1)** 

比如说，我给你输入一个数组 `nums`，然后又要求给区间 `nums[2..6]` 全部加 1，再给 `nums[3..9]` 全部减 3，再给 `nums[0..4]` 全部加 2，再给… 一通操作猛如虎，然后问你，最后 `nums` 数组的值是什么？

常规的思路很容易，你让我给区间 `nums[i..j]` 加上 `val`，那我就一个 for 循环给它们都加上呗，还能咋样？这种思路的时间复杂度是 O(N)，由于这个场景下对 `nums` 的修改非常频繁，所以效率会很低下。

这里就需要差分数组的技巧，类似前缀和技巧构造的 `prefix` 数组，我们先对 `nums` 数组构造一个 `diff` 差分数组，**`diff[i]` 就是 `nums[i]` 和 `nums[i-1]` 之差**：

```python
diff = [0] * len(nums)
# 构造差分数组
diff[0] = nums[0]
for i in range(1, len(nums)):
  diff[i] = nums[i] - nums[i - 1]
```

<img src="https://labuladong.github.io/algo/images/%e5%b7%ae%e5%88%86%e6%95%b0%e7%bb%84/2.jpeg" alt="img" style="zoom:50%;" />

通过这个 `diff` 差分数组是可以反推出原始数组 `nums` 的，代码逻辑如下：

```python
res = [0] * len(diff)
# 根据差分数组构造结果数组
res[0] = diff[0]
for i in range(1, len(diff)):
  res[i] = res[i - 1] + diff[i]
```

**这样构造差分数组 `diff`，就可以快速进行区间增减的操作**，如果你想对区间 `nums[i..j]` 的元素全部加 3，那么只需要让 `diff[i] += 3`，然后再让 `diff[j+1] -= 3` 即可

```python
# 给闭区间 [i, j] 增加 val（可以是负数）
def increament(i, j, val):
  diff[i] += val
  if j + 1 < len(diff):     # Important! 当 j+1 >= len(diff) 时，说明是对 nums[i] 及以后的整个数组都进行修改，那么就不需要再给 diff 数组减 val 了。
    diff[j + 1] -= val
```

<img src="https://labuladong.github.io/algo/images/%e5%b7%ae%e5%88%86%e6%95%b0%e7%bb%84/3.jpeg" alt="img" style="zoom:50%;" />

**原理很简单，回想 `diff` 数组反推 `nums` 数组的过程，`diff[i] += 3` 意味着给 `nums[i..]` 所有的元素都加了 3，然后 `diff[j+1] -= 3` 又意味着对于 `nums[j+1..]` 所有元素再减 3，那综合起来，是不是就是对 `nums[i..j]` 中的所有元素都加 3 了**？

只要花费 O(1) 的时间修改 `diff` 数组，就相当于给 `nums` 的整个区间做了修改。多次修改 `diff`，然后通过 `diff` 数组反推，即可得到 `nums` 修改后的结果。

## 1. 370. Range Addition

Medium

You are given an integer `length` and an array `updates` where `updates[i] = [startIdxi, endIdxi, inci]`.

You have an array `arr` of length `length` with all zeros, and you have some operation to apply on `arr`. In the `ith` operation, you should increment all the elements `arr[startIdxi], arr[startIdxi + 1], ..., arr[endIdxi]` by `inci`.

Return `arr` *after applying all the* `updates`.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/03/27/rangeadd-grid.jpg" alt="img" style="zoom:50%;" />

```
Input: length = 5, updates = [[1,3,2],[2,4,3],[0,2,-2]]
Output: [-2,0,3,5,3]
```

**Example 2:**

```
Input: length = 10, updates = [[2,4,6],[5,6,8],[1,9,-4]]
Output: [0,-4,2,2,2,4,4,-4,-4,-4] 
```

- **Constraints:**

  - `1 <= length <= 105`

  - `0 <= updates.length <= 104`

  - `0 <= startIdxi <= endIdxi < length`

  - `-1000 <= inci <= 1000`

- **Solution**

  - <u>Method 1: Naive Approach</u>

    For each time, operate as the question shows.

    ```python
    def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
      arr = [0] * length
      for update in updates:
        l, r, val = update[0], update[1], update[2]
        arr[l:r+1] = [arr[i] + val for i in range(l, r + 1)]
      return arr
    ```

    - Time complexity: $O(k * n)$, where k is the number of updates and n is the length of the array 
    - Space complexity: O(1)

  - <u>Method 2: Difference Array</u>

    --> idea: use a diff array to record the difference between nums[i] and nums[i - 1]

     S1: build an diff array (initial all zeros) and update with updates

    S2: based on the diff array, get the original array

    ```python
    def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
            arr = [0] * length
            diff = [0] * length
            
            for l, r, val in updates:
                # l, r, val = update[0], update[1], update[2]
                diff[l] += val
                if r + 1 < length:
                    diff[r + 1] -= val
            
            arr[0] = diff[0]
            for i in range(1, length):
                arr[i] = arr[i - 1] + diff[i]
            return arr
    ```

    - Time complexity: $O(k + n)$

    - Space complexity: O(N)

    - Further Optimization: use only O(1) extra memory (only have `arr`)

      --> At the end, we iterate the array from index 1 (0-based indexing), and set: `ans[i] += ans[i-1]`. This is basically prefix-sum

      ```python
      def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
              arr = [0] * length
              
              for l, r, val in updates:
                  arr[l] += val
                  if r + 1 < length:
                      arr[r + 1] -= val
              
              for i in range(1, length):
                  arr[i] += arr[i - 1]
              return arr
      ```

      - Time complexity: $O(k + n)$
      - Space complexity: O(1)







