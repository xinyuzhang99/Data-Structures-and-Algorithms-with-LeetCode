# 1-D Dynamic Programming

**动态规划问题的一般形式就是求<font color=red>最值</font>**; **求解动态规划的核心问题是<font color=red>穷举</font>**。

穷举所有可行解其实并不是一件容易的事，需要你熟练掌握递归思维，只有列出**正确的「状态转移方程」**，才能正确地穷举。而且，你需要判断算法问题是否**具备「最优子结构」(Optimal Substructure)**，是否能够通过子问题的最值得到原问题的最值。另外，动态规划问题**存在「重叠子问题」(Overlapping Subproblem)**，如果暴力穷举的话效率会很低，所以需要你使用*<u>「备忘录」或者「DP table」</u>*来优化穷举过程，避免不必要的计算。

==[重叠子问题、最优子结构、状态转移方程]==就是动态规划三要素！

- 「状态转移方程」这个名词，实际上就是描述问题结构的数学形式（直接代表着暴力解法）:

  <img src="https://labuladong.github.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/fib.png" alt="img" style="zoom: 50%;" />

  状态转移方程思维框架：**明确 base case -> 明确「状态」-> 明确「选择」 -> 定义 `dp` 数组/函数的含义**。

```python
# 自顶向下递归的动态规划: Top-Down with Memoization
def dp(状态1, 状态2, ...):
    for 选择 in 所有可能的选择:
        # 此时的状态已经因为做了选择而改变
        result = 求最值(result, dp(状态1, 状态2, ...))
    return result

# 自底向上迭代的动态规划: Bottom-Up with Tabulations
# 初始化 base case
dp[0][0][...] = base case
# 进行状态转移
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 求最值(选择1，选择2...)
```

- **带备忘录自顶向下的递归解法 (Top-Down with Memoization)**

  ```python
  def fib(self, n: int) -> int:
          def helper(n, memo):
              if (n == 0 or n == 1):
                  return n
              if n not in memo:
                  memo[n] = helper(n - 1, memo) + helper(n - 2, memo)
              return memo[n]
          
          memo = {}
          return helper(n, memo)
  ```

  即然耗时的原因是重复计算，那么可以造一个「备忘录」，每次算出某个子问题的答案后别急着返回，先记到「备忘录」里再返回；每次遇到一个子问题先去「备忘录」里查一查，如果发现之前已经解决过这个问题了，直接把答案拿出来用，不要再耗时去计算了。一般使用一个**数组**充当这个「备忘录」，当然也可以使用哈希表（字典），思想都是一样的。

  <img src="https://labuladong.github.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/2.jpg" alt="img" style="zoom:50%;" />

  实际上，带「备忘录」的递归算法，把一棵存在巨量冗余的递归树通过「剪枝」，改造成了一幅不存在冗余的递归图，极大减少了子问题（即递归图中节点）的个数。

  <img src="https://labuladong.github.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/3.jpg" alt="img" style="zoom:50%;" />

  - **递归算法的时间复杂度就是用子问题个数乘以解决一个子问题需要的时间**。

    子问题个数，即图中节点的总数，由于本算法不存在冗余计算，子问题就是 `f(1)`, `f(2)`, `f(3)` … `f(20)`，数量和输入规模 n = 20 成正比，所以子问题个数为 O(n)。

    解决一个子问题的时间，同上，没有什么循环，时间为 O(1)。

    --> <font color=blue>**算法时间复杂度：O(N)**</font>

- **`dp` 数组的迭代（递推）解法 (Bottom-Up with Tabulations)**

  --> 把上一步「备忘录」独立出来成为一张表，通常叫做 DP table，在这张表上完成「自底向上」的推算

  ```python
  def fib(N):
    if N == 0 or N == 1:
      return N
    # base case
    dp = [0, 1]
    for i in range(2, N + 1):
      dp.append(dp[i - 1] + dp[i - 2])    # the total length is N + 1
    return dp[N]
  ```

  <img src="https://labuladong.github.io/algo/images/%e5%8a%a8%e6%80%81%e8%a7%84%e5%88%92%e8%af%a6%e8%a7%a3%e8%bf%9b%e9%98%b6/4.jpg" alt="img" style="zoom:50%;" />

  - <u>细节优化</u>

    根据斐波那契数列的状态转移方程，当前状态只和之前的两个状态有关，其实并不需要那么长的一个 DP table 来存储所有的状态，只要想办法存储之前的两个状态就行了。所以，可以进一步优化，把空间复杂度降为 O(1)。这也就是我们最常见的计算斐波那契数的算法：

    ```python
    def fib(N):
      if N == 0 or N == 1:
        return N
      dp0, dp1 = 0, 1
      for i in range(2, N + 1):
        dp = dp0 + dp1
        dp0 = dp1
        dp1 = dp
      return dp1
    ```

    这一般是动态规划问题的最后一步优化，如果我们发现<font color=blue>**每次状态转移只需要 DP table 中的一部分**</font>，那么可以尝试缩小 DP table 的大小，只记录必要的数据，从而降低空间复杂度。--> <font color=blue>**一般来说是把一个二维的 DP table 压缩成一维，即把空间复杂度从 $O(n^2)$ 压缩到 $O(n)$**</font>