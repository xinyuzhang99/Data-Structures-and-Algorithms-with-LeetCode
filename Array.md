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
        for i in range(1, len(preSum)):
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