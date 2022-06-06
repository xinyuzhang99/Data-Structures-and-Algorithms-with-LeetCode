# Recursion 递归

- 递归常和<u>回溯(backtracking)，深度优先搜索 (DFS), 分治法 (Divide and conquer)</u>结合形成复杂算法

- 四个要素：

  - 接受的参数
  - 终止条件 (base condition )
  - 递归拆解（如何进入 递归下一层）
  - 返回值

  <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220525191021205.png" alt="image-20220525191021205" style="zoom:50%;" />

  

- - Time Complexity: for recursive calls, runtime is often the form of ==$O(branches^{depth})$​​​==

    --> **branch**:  the number of children in each node / the number of times that each recursive calls itself

    ​     **depth**: the parameter number of function

  - Space Complexity: O(N) if it call itself --> N stacks in memory (空间复杂度主要取决于递归调用的栈空间，==最多为 *n* 层==。)

## 1. 509 [Fibonacci Number](https://leetcode.com/problems/fibonacci-number/description/)

|  Category  |  Difficulty   |                  Tags                   |
| :--------: | :-----------: | :-------------------------------------: |
| algorithms | Easy (68.20%) | [`tree`](https://leetcode.com/tag/tree) |

The **Fibonacci numbers**, commonly denoted `F(n)` form a sequence, called the **Fibonacci sequence**, such that each number is the sum of the two preceding ones, starting from `0` and `1`. That is,

```
F(0) = 0, F(1) = 1
F(n) = F(n - 1) + F(n - 2), for n > 1.
```

Given `n`, calculate `F(n)`.

**Example 1:**

```
Input: n = 2
Output: 1
Explanation: F(2) = F(1) + F(0) = 1 + 0 = 1.
```

**Example 2:**

```
Input: n = 3
Output: 2
Explanation: F(3) = F(2) + F(1) = 1 + 1 = 2.
```

**Example 3:**

```
Input: n = 4
Output: 3
Explanation: F(4) = F(3) + F(2) = 2 + 1 = 3.
```

- **Constraints:**

  - `0 <= n <= 30`

- **Solution**

  - <u>Method 1: Recursion</u>

    ```python
    class Solution:
        def fib(self, n: int) -> int:
            if (n == 1 or n == 0):
                return n
            return self.fib(n-1) + self.fib(n-2)
    ```

    Time Complexity: $O(2^N)$ ($O(branches^{depth})$, here each node has two children and the depth is N)

    Space Complexity: $O(N)$

  - <u>Method 2: Dynamic Programming</u>

    **<font color=red>空缺！！</font>**