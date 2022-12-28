# Array

## 1. Prefix

### 1. 303. Range Sum Query - Immutable

Given an integer array `nums`, handle multiple queries of the following type:

1. Calculate the **sum** of the elements of `nums` between indices `left` and `right` **inclusive** where `left <= right`.

Implement the `NumArray` class:

- `NumArray(int[] nums)` Initializes the object with the integer array `nums`.
- `int sumRange(int left, int right)` Returns the **sum** of the elements of `nums` between indices `left` and `right` **inclusive** (i.e. `nums[left] + nums[left + 1] + ... + nums[right]`).

**Example 1:**

```java
Input
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]
Output
[null, 1, -1, -3]

Explanation
NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
numArray.sumRange(0, 2); // return (-2) + 0 + 3 = 1
numArray.sumRange(2, 5); // return 3 + (-5) + 2 + (-1) = -1
numArray.sumRange(0, 5); // return (-2) + 0 + 3 + (-5) + 2 + (-1) = -3 
```

- **Constraints:**

  - `1 <= nums.length <= 104`

  - `-105 <= nums[i] <= 105`

  - `0 <= left <= right < nums.length`

  - At most `104` calls will be made to `sumRange`.

- **Thoughts**

  - 最直观的方法为：

    ```python
    class NumArray:
    
        def __init__(self, nums: List[int]):
            self.nums = nums
            
        def sumRange(self, left: int, right: int) -> int:
            sum = 0
            for n in range(left, right+1):
              sum += self.nums[n]
            return sum
    ```

    这样可以达到效果，但是效率很差，因为 `sumRange` 方法会被频繁调用，而它的时间复杂度是 `O(N)`，其中 `N` 代表 `nums` 数组的长度 --> 每一次调用都会消耗`O(N)`的时间

    --> 最优解法是使用前缀和技巧，将 `sumRange` 函数的时间复杂度降为 `O(1)` --> 不要在 `sumRange` 里面用 for 循环

  - 方法：构造一个新的`preSum`数组， `preSum[i]` 记录 `nums[0..i-1]` 的累加和，看图 10 = 3 + 5 + 2：

    <img src="https://labuladong.github.io/algo/images/%e5%b7%ae%e5%88%86%e6%95%b0%e7%bb%84/1.jpeg" alt="img" style="zoom:50%;" />

- **Solution**

  ```python
  class NumArray:
  
      def __init__(self, nums: List[int]):
        self.nums = nums
        self.preSum = [0] * (len(nums) + 1)
        for i in range(1, len(self.preSum)):
          self.preSum[i] += nums[i-1]
        
      def sumRange(self, left: int, right: int) -> int:
        return self.preSum[right+1] - self.preSum[left]
  ```

  - Time complexity: 初始化 O(n)，每次检索 O(1)
  - Space complexity: O(N) --> 需要创建一个前缀和数组

### 2. 304 [Range Sum Query 2D - Immutable](https://leetcode.com/problems/range-sum-query-2d-immutable/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (47.94%) | [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given a 2D matrix `matrix`, handle multiple queries of the following type:

- Calculate the **sum** of the elements of `matrix` inside the rectangle defined by its **upper left corner** `(row1, col1)` and **lower right corner** `(row2, col2)`.

Implement the `NumMatrix` class:

- `NumMatrix(int[][] matrix)` Initializes the object with the integer matrix `matrix`.
- `int sumRegion(int row1, int col1, int row2, int col2)` Returns the **sum** of the elements of `matrix` inside the rectangle defined by its **upper left corner** `(row1, col1)` and **lower right corner** `(row2, col2)`.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/03/14/sum-grid.jpg" alt="img" style="zoom:50%;" />

```java
Input
["NumMatrix", "sumRegion", "sumRegion", "sumRegion"]
[[[[3, 0, 1, 4, 2], [5, 6, 3, 2, 1], [1, 2, 0, 1, 5], [4, 1, 0, 1, 7], [1, 0, 3, 0, 5]]], [2, 1, 4, 3], [1, 1, 2, 2], [1, 2, 2, 4]]
Output
[null, 8, 11, 12]

Explanation
NumMatrix numMatrix = new NumMatrix([[3, 0, 1, 4, 2], [5, 6, 3, 2, 1], [1, 2, 0, 1, 5], [4, 1, 0, 1, 7], [1, 0, 3, 0, 5]]);
numMatrix.sumRegion(2, 1, 4, 3); // return 8 (i.e sum of the red rectangle)
numMatrix.sumRegion(1, 1, 2, 2); // return 11 (i.e sum of the green rectangle)
numMatrix.sumRegion(1, 2, 2, 4); // return 12 (i.e sum of the blue rectangle)
```

- **Constraints:**

  - `m == matrix.length`

  - `n == matrix[i].length`

  - `1 <= m, n <= 200`

  - `-105 <= matrix[i][j] <= 105`

  - `-109 <= sum(matrix[i][j]) <= 109`

  - `0 <= row1 <= row2 < m`

  - `0 <= col1 <= col2 < n`

  - At most `104` calls will be made to `sumRegion`.

- **Thought**

  使用二维前缀和，在初始化的时候计算二维前缀和数组

  ![image](https://assets.leetcode.com/users/hiepit/image_1578762431.png)

- **Solution**

  ```python
  class NumMatrix:
  
      # S1: create a pre-sum matrix with an extra left column and top row
      # S2: each (i, j) element represents the pre-sum from (0,0) to (i, j)
      # S2: calculate each sum of all elements in squares (bottom_right - above - left + top_left)
      def __init__(self, matrix: List[List[int]]):
        row = len(matrix)
        col = len(matrix[0])
        self.preSum = [[0] * (col + 1) for _ in range(row + 1)]
        
        for r in range(row):
          prefix = 0
          for c in range(col):
            # 计算每个矩阵 [0, 0, r, c] 的元素和
            prefix += matrix[r][c]
            above = self.preSum[r][c+1]
            self.preSum[r+1][c+1] = prefix + above
          
      def sumRegion(self, row1: int, col1: int, row2: int, col2: int) -> int:
        bottom_right = self.preSum[row2+1][col2+1]
        above = self.preSum[row1][col2+1]
        left = self.preSum[row2+1][col1]
        top_left = self.preSum[row1][col1]
        return bottom_right - above - left + top_left
  ```

  - Time complexity: $O(row * column)$ 

    Space complexity: $O(row * column)$ --> create a `preSum` matrix

### 3. 238 [Product of Array Except Self](https://leetcode.com/problems/product-of-array-except-self/description/)

|  Category  |   Difficulty    |                   Tags                    |
| :--------: | :-------------: | :---------------------------------------: |
| algorithms | Medium (64.06%) | [`array`](https://leetcode.com/tag/array) |

Given an integer array `nums`, return *an array* `answer` *such that* `answer[i]` *is equal to the product of all the elements of* `nums` *except* `nums[i]`.

The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

You must write an algorithm that runs in `O(n)` time and without using the division operation.

**Example 1:**

```
Input: nums = [1,2,3,4]
Output: [24,12,8,6]
```

**Example 2:**

```
Input: nums = [-1,1,0,-3,3]
Output: [0,0,9,0,0]
```

**Constraints:**

- `2 <= nums.length <= 105`
- `-30 <= nums[i] <= 30`
- The product of any prefix or suffix of `nums` is **guaranteed** to fit in a **32-bit** integer.

**Follow up:** Can you solve the problem in `O(1) `extra space complexity? (The output array **does not** count as extra space for space complexity analysis.)

- **Thoughts**

  - 这道题和303有些类似 --> 构造一个 `prefix` 数组记录「前缀积」，再用一个 `postfix` 记录「后缀积」，根据前缀和后缀积就能计算除了当前元素之外其他元素的积。

- **Solution**

  ```python
  def productExceptSelf(self, nums: List[int]) -> List[int]:
          ## 1: O(N) extra space complexity
          pre = [1] * len(nums)
          post = [1] * len(nums)
  
          # prefix 
          for i in range(1, len(nums)):
              pre[i] = pre[i - 1] * nums[i - 1]
          
          # postfix
          for i in range(len(nums) - 2, -1, -1):
              post[i] = post[i + 1] * nums[i + 1]
          
          res = [0] * len(nums)
          for i in range(len(nums)):
              res[i] = pre[i] * post[i]
          return res
          # Time complexity: O(N)
          # Space complexity: O(N)
  
          ## !! Follow-up: O(1) extra space complexity
          res = [1] * len(nums)
          for i in range(1, len(nums)):
              res[i] = res[i - 1] * nums[i - 1]
  
          product = 1
          for i in range(len(nums) - 1, -1, -1):
              res[i] *= product
              product = product * nums[i]
          return res
          # Time complexity: O(N)
          # Space complexity: O(1) --> 输出数组不算进空间复杂度中，因此我们只需要常数的空间存放变量
  ```


## 2. Difference Array 差分数组

**差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减**

比如说，我给你输入一个数组 `nums`，然后又要求给区间 `nums[2..6]` 全部加 1，再给 `nums[3..9]` 全部减 3，再给 `nums[0..4]` 全部加 2，再给…问你，最后 `nums` 数组的值是什么？

常规的思路很容易，你让我给区间 `nums[i..j]` 加上 `val`，那我就一个 for 循环给它们都加上--> 这种思路的时间复杂度是 O(N)，由于这个场景下对 `nums` 的修改非常频繁，所以效率会很低下。

这里就需要差分数组的技巧，类似前缀和技巧构造的 `prefix` 数组，我们先对 `nums` 数组构造一个 `diff` 差分数组，**`diff[i]` 就是 `nums[i]` 和 `nums[i-1]` 之差**：

```python
diff = [] * len(nums)
# 构造差分数组
diff[0] = nums[0]
for i in range(1, len(nums)):
  diff[i] = nums[i] - nums[i - 1]
```

<img src="https://labuladong.github.io/algo/images/%e5%b7%ae%e5%88%86%e6%95%b0%e7%bb%84/2.jpeg" alt="img" style="zoom:50%;" />

通过这个 `diff` 差分数组是可以反推出原始数组 `nums` 的，代码逻辑如下：

```python
res = [] * len(diff)
# 根据差分数组构造结果数组
res[0] = diff[0]
for i in range(1, len(diff)):
  res[i] = res[i - 1] + diff[i]
```

**这样构造差分数组 `diff`，就可以快速进行区间增减的操作**，如果你想对区间 `nums[i..j]` 的元素全部加 3，那么只需要让 `diff[i] += 3`，然后再让 `diff[j+1] -= 3` 即可：

<img src="https://labuladong.github.io/algo/images/%e5%b7%ae%e5%88%86%e6%95%b0%e7%bb%84/3.jpeg" alt="img" style="zoom:50%;" />

只要花费 O(1) 的时间修改 `diff` 数组，就相当于给 `nums` 的整个区间做了修改。多次修改 `diff`，然后通过 `diff` 数组反推，即可得到 `nums` 修改后的结果。

```python
## Summary

# 输入一个初始数组，区间操作将在这个数组上进行
def difference(nums):
  diff = [] * len(nums)
  # 构造差分数组
  diff[0] = nums[0]
  for i in range(1, len(nums)):
    diff[i] = nums[i] - nums[i - 1]

# 给闭区间 [i, j] 增加 val（可以是负数）
def increment(i, j, val):
  diff[i] += val
  if j + 1 < len(diff): # 当 j+1 >= diff.length 时，说明是对nums[i]及以后的整个数组都进行修改，那么就不需要再给diff数组减 val了
    diff[j] -= val

# 返回结果数组
def result():
  res = [] * len(diff)
  # 根据差分数组构造结果数组
  res[0] = diff[0]
  for i in range(1, len(diff)):
    res[i] = res[i - 1] + diff[i]
  return res
```

### 1. 370 Range Addition

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

- **Solutions**

  - <u>Method 1: Naive Approach</u>

    首先想到的是直接进行计算

    ```python
    def getModifiedArray(self, length: int, updates: List[List[int]]) -> List[int]:
      arr = [0] * length
      for update in updates:
        l, r, val = update[0], update[1], update[2]
        arr[l:r+1] = [arr[i] + val for i in range(l, r + 1)]
      return arr
    ```

    - Time complexity: $O(N \times K)$, where k is the number of updated queries and n is the length of the array --> each update operation takes up $O(N)$ time

      Space complexity: O(1)

  - <u>Method 2: Difference Array</u>

    使用差分数组模板解决

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

    - Time complexity: $O(N + K)$, where k is the number of updated queries and n is the length of the array 

      Space complexity: O(1)

## 3. Two-Dimensional Array

- **Common Techniques**

  - <u>顺/逆时针旋转矩阵</u>

    对二维数组进行旋转是常见的笔试题。旋转二维矩阵的难点在于将「行」变成「列」，将「列」变成「行」，而只有<font color=blue>**按照对角线的对称操作**</font>是可以轻松完成这一点的，对称操作之后就很容易发现规律了。
  
    二维矩阵旋转总结：
  
    1. 矩阵顺时针旋转90度：以左上到右下对角线为轴进行对称 --> swap (`matrix[i][j], matrix[j][i]`) + reverse each row
  
    2. 矩阵逆时针旋转90度：以左下到右上对角线为轴进行对称 --> swap (`matrix[i][j], matrix[n-j-1][n-i-1]`) + reverse each row
  
       <img src="https://labuladong.github.io/algo/images/%e8%8a%b1%e5%bc%8f%e9%81%8d%e5%8e%86/5.jpeg" alt="img" style="zoom: 50%;" />

### 1. 48 [Rotate Image](https://leetcode.com/problems/rotate-image/description/)

|  Category  |   Difficulty    |                   Tags                    |
| :--------: | :-------------: | :---------------------------------------: |
| algorithms | Medium (66.95%) | [`array`](https://leetcode.com/tag/array) |

 You are given an `n x n` 2D `matrix` representing an image, rotate the image by **90** degrees (clockwise).

You have to rotate the image [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm), which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation. 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/08/28/mat1.jpg" alt="img" style="zoom: 67%;" />

```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [[7,4,1],[8,5,2],[9,6,3]]
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/08/28/mat2.jpg" alt="img" style="zoom:67%;" />

```
Input: matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
Output: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

- **Constraints:**

  - `n == matrix.length == matrix[i].length`

  - `1 <= n <= 20`

  - `-1000 <= matrix[i][j] <= 1000`

- **Thoughts**

  这道题的常规思路是去寻找原始坐标和旋转后坐标的映射规律，但是通过对角线的对称操作有新的发现。

  **我们可以先将 `n x n` 矩阵 `matrix` 按照左上到右下的对角线进行镜像对称**：

  [<img src="https://labuladong.github.io/algo/images/%e8%8a%b1%e5%bc%8f%e9%81%8d%e5%8e%86/2.jpeg" alt="img" style="zoom:50%;" />](https://labuladong.github.io/algo/images/花式遍历/2.jpeg)

  **然后再对矩阵的每一行进行反转**：

  [<img src="https://labuladong.github.io/algo/images/%e8%8a%b1%e5%bc%8f%e9%81%8d%e5%8e%86/3.jpeg" alt="img" style="zoom:50%;" />](https://labuladong.github.io/algo/images/花式遍历/3.jpeg)

  **发现结果就是 `matrix` 顺时针旋转 90 度的结果**：

  [<img src="https://labuladong.github.io/algo/images/%e8%8a%b1%e5%bc%8f%e9%81%8d%e5%8e%86/4.jpeg" alt="img" style="zoom:50%;" />](https://labuladong.github.io/algo/images/花式遍历/4.jpeg)

- **Solution**

   Idea: rotate the image --> rows and columns are exchanged

  - S1: transpose the matrix --> make a symmetric version of the matrix along the top-left to bottom-right diagonal

    --> swap (`matrix[i][j], matrix[j][i]`)

  - S2: reverse each row --> get the same result

  <font color=blue>**Transpose + Reflect**</font>

  ```python
  def rotate(self, matrix: List[List[int]]) -> None:
          """
          Do not return anything, modify matrix in-place instead.
          """
          n = len(matrix)
          # Transpose 
          for i in range(n):
              for j in range(i + 1, n):
                  temp = matrix[i][j]
                  matrix[i][j] = matrix[j][i]
                  matrix[j][i] = temp
          
          # Reflect
          for r in range(n):
              matrix[r] = reversed(matrix[r])   
  ```

  - Time complexity: $O(N + N) = O(N)$, where N is the number of cells --> moving the value of each cell once

    Space complexity: O(1)

  - <font color=red>注意点：</font>

    - i 的取值范围为[0,  n - 1]，j 的取值范围为[i + 1, n - 1]，否则对称过的值会再进行一次对称操作，最后得到和原矩阵相同的结果

    - 二维矩阵对称总结：

      1. 矩阵顺时针旋转90度：以左上到右下对角线为轴进行对称 --> swap (`matrix[i][j], matrix[j][i]`)

      2. 矩阵逆时针旋转90度：以左下到右上对角线为轴进行对称 --> swap (`matrix[i][j], matrix[n-j-1][n-i-1]`)

         <img src="https://labuladong.github.io/algo/images/%e8%8a%b1%e5%bc%8f%e9%81%8d%e5%8e%86/5.jpeg" alt="img" style="zoom:67%;" />

      

  