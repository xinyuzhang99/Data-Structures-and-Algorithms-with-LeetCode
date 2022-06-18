# Backtracking

--> 回溯算法和DFS 算法非常类似，本质上就是一种暴力穷举算法，复杂度一般都很高。回溯算法和 DFS 算法的细微差别是：**回溯算法是在遍历「树枝」，DFS 算法是在遍历「节点」**

- 解决一个回溯问题，实际上就是一个决策树的遍历过程，站在回溯树的一个节点上，你只需要思考 3 个问题：

  1、路径 / 状态：也就是已经做出的选择。 --> 记录在path中

  2、选择列表 / Path：也就是你当前可以做的选择。 --> 是一个栈

  3、结束条件：也就是到达决策树底层，无法再做选择的条件。

  **写 `backtrack` 函数时，需要维护走过的「路径」和当前可以做的「选择列表」，当触发「结束条件」时，将「路径」记入结果集**

- 经典做法：再设置一个布尔数组used，表示当前的数字是否之前已经出现过（是否在Path里面）; 初始都为`False`，当选定一个数字时，将相应下标设置为`True` --> <font color=blue>**以空间换时间**</font> （没有显式记录「选择列表」，而是通过 `used` 数组排除已经存在 `track` 中的元素，从而推导出当前的选择列表）

- **Coding Template**

  ```python
  result = []
  def backtrack(路径, 选择列表):
      if 满足结束条件:
          result.add(路径)
          return
      
      # 类似多叉数的遍历问题
      for 选择 in 选择列表:
          做选择  		# 将该选择从选择列表移除 --> 路径.add(选择) --> !!前序位置
          backtrack(路径, 选择列表)    
          # 得到一个结果后要返回，撤销选择，在上一个节点再次做选择得到另一个结果 --> 状态重置（回溯）！
          撤销选择   # 路径.remove(选择) --> 将该选择再加入选择列表 --> !!后序位置
  ```

  **核心就是 for 循环里面的递归，在递归调用之前「做选择」，在递归调用之后「撤销选择」**

  <img src="https://labuladong.github.io/algo/images/backtracking/5.jpg" alt="img" style="zoom:50%;" />

## 1. 46 [Permutations](https://leetcode.com/problems/permutations/description/)

|  Category  |   Difficulty    |                          Tags                           |
| :--------: | :-------------: | :-----------------------------------------------------: |
| algorithms | Medium (72.61%) | [`backtracking`](https://leetcode.com/tag/backtracking) |

Given an array `nums` of distinct integers, return *all the possible permutations*. You can return the answer in **any order**.

**Example 1:**

```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**Example 2:**

```
Input: nums = [0,1]
Output: [[0,1],[1,0]]
```

**Example 3:**

```
Input: nums = [1]
Output: [[1]]
```

- **Constraints:**

  - `1 <= nums.length <= 6`

  - `-10 <= nums[i] <= 10`

  - All the integers of `nums` are **unique**.

- **Thoughts**

  - 搜索的方法：按顺序枚举每一位可能出现的情况，已经选择的数字在 当前 要选择的数字中不能出现。按照这种策略搜索就能够做到 不重不漏。这样的思路，可以用一个树形结构表示。

  <img src="https://pic.leetcode-cn.com/0bf18f9b86a2542d1f6aa8db6cc45475fce5aa329a07ca02a9357c2ead81eec1-image.png" alt="image.png" style="zoom: 33%;" />

  - **说明：**

    - **每一个结点表示了求解全排列问题的不同的阶段**，这些阶段通过变量的「不同的值」体现，这些变量的不同的值，称之为「状态」；
    - 使用深度优先遍历有「回头」的过程，在「回头」以后， **状态变量需要设置成为和先前一样** ，因此在回到上一层结点的过程中，需要撤销上一次的选择，这个操作称之为「状态重置」；
    - 深度优先遍历，借助系统栈空间，保存所需要的状态变量，在编码中只需要注意遍历到相应的结点的时候，状态变量的值是正确的，具体的做法是：往下走一层的时候，path 变量在尾部追加，而往回走的时候，需要撤销上一次的选择，也是在尾部操作，因此 path 变量是一个栈；
    - 深度优先遍历通过「回溯」操作，实现了全局使用一份状态变量的效果。

    使用编程的方法得到全排列，就是在这样的一个树形结构中完成 **遍历**，从树的根结点到叶子结点形成的路径就是其中一个全排列。

  - **设计状态变量**

    - 首先这棵树除了根结点和叶子结点以外，每一个结点做的事情其实是一样的，即：在已经选择了一些数的前提下，在剩下的还没有选择的数中，依次选择一个数，这显然是一个 **递归** 结构；
    - 递归的终止条件是： **一个排列中的数字已经选够了** ，因此我们需要一个变量来表示当前程序递归到第几层，我们把这个变量叫做 depth，或者命名为 index ，表示当前要确定的是某个全排列中下标为 index 的那个数是多少；
    - 布尔数组 used，初始化的时候都为 false 表示这些数还没有被选择，当我们选定一个数的时候，就将这个数组的相应位置设置为 true ，这样在考虑下一个位置的时候，就能够以 O(1)的时间复杂度判断这个数是否被选择过，这是一种「**以空间换时间」**的思想。

    这些变量称为「状态变量」，它们表示了在求解一个问题的时候所处的阶段。需要根据问题的场景设计合适的状态变量。

- **Solution**

  ```python
  def permute(self, nums: List[int]) -> List[List[int]]:
          path = []                   # 记录「路径」
          used = [False] * len(nums)  # 「路径」中的元素会被标记为 true，避免重复使用
          res = []
  
          # 路径：记录在 path 中
          # 选择列表：nums 中不存在于 path 的那些元素（used[i] 为 false）
          # 结束条件：nums 中的元素全都在 path 中出现
          def backtrack(nums, used, path):
              # 触发结束条件
              if len(path) == len(nums):
                  res.append(path[:])  # path指向对象在不断地增加和删除元素，最后会变成空 --> 拷贝一个对象加入res
                  return
              
              for i in range(len(nums)):
                  if used[i]:          # 排除不合法的选择    
                      continue         # nums[i] 已经在 track 中，跳过
                  # 做选择
                  path.append(nums[i])
                  used[i] = True
                  # 进入下一层决策树
                  backtrack(nums, used, path)
                  # 取消选择
                  path.pop()
                  used[i] = False
          
          backtrack(nums, used, path)
          return res
  ```

  - Time complexity: $O(N \times N!)$ 

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220617015130121.png" alt="image-20220617015130121" style="zoom: 67%;" />

  - Space complexity: $O(N \times N!)$​  

    - 递归树深度 $O(NlogN)$；
    - 全排列个数 N!，每个全排列占空间 N。取较大者。

  - 易错点：

    - `res.append(path[:])`: 参数传递是值传递，对象类型变量在传参的过程中，==复制的是变量的地址==。这些地址被添加到 res 变量，但实际上指向的是同一块内存地址，因此我们会看到 6 个空的列表对象。解决的方法很简单，在 `res.add(path)` 这里做一次拷贝即可。

## 2. 51 [N-Queens](https://leetcode.com/problems/n-queens/description/)

|  Category  |  Difficulty   |                          Tags                           |
| :--------: | :-----------: | :-----------------------------------------------------: |
| algorithms | Hard (57.87%) | [`backtracking`](https://leetcode.com/tag/backtracking) |

The **n-queens** puzzle is the problem of placing `n` queens on an `n x n` chessboard such that no two queens attack each other.

Given an integer `n`, return *all distinct solutions to the **n-queens puzzle***. You may return the answer in **any order**.

Each solution contains a distinct board configuration of the n-queens' placement, where `'Q'` and `'.'` both indicate a queen and an empty space, respectively.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/11/13/queens.jpg" alt="img" style="zoom:50%;" />

```
Input: n = 4
Output: [[".Q..","...Q","Q...","..Q."],["..Q.","Q...","...Q",".Q.."]]
Explanation: There exist two distinct solutions to the 4-queens puzzle as shown above
```

**Example 2:**

```
Input: n = 1
Output: [["Q"]]
```

- **Constraints:**
  - `1 <= n <= 9`

- **Thoughts**

  - 皇后的走法是：可以横直斜走，格数不限。因此要求皇后彼此之间不能相互攻击，等价于要求任何两个皇后都不能在同一行、同一列以及同一条斜线上。 --> 为了获得valid board, 需要满足三个条件 --> 同Set.md的`36. Valid Sudoku`类似，建立三个集合set

  - 关键点：如何表示不在同一条斜线上

    - <img src="https://assets.leetcode-cn.com/solution-static/51/1.png" alt="fig1" style="zoom: 33%;" />

      --> 同一条斜线上的每个位置满足**行下标与列下标之差相等**

    - <img src="https://assets.leetcode-cn.com/solution-static/51/2.png" alt="fig2" style="zoom:33%;" />

      --> 同一条斜线上的每个位置满足**行下标与列下标之和相等**

  - 由于皇后可以一行一行放置，所以回溯函数的变量为列（选择列表）

  - 结束条件：当皇后放到最后一行，返回board

  - 做选择：放置皇后并在相应的集合里插入值；撤销选择：将皇后清空并在集合里撤销值

- **Solution**

  ```python 
  def solveNQueens(self, n: int) -> List[List[str]]:
    col = set()
    negDiag = set()   # (r - c) is a constant
    posDiag = set()   # (r + c) is a constant
    board = [['.'] * n for _ in range(n)]   # Initialize the board
    
    res = []
    def backtrack(r):
      if r == n:
        copy = [''.join(row) for row in board]
        res.append(copy)
        return
      
      for c in range(n):
        if c in col or (r - c) in negDiag or (r + c) in posDiag:
          continue
        
        board[r][c] = 'Q'
        col.add(c)
        negDiag.add(r - c)
        posDiag.add(r + c)
        
        backtrack(r + 1)     # proceed to the next row
        
        board[r][c] = '.'
        col.remove(c)
        negDiag.remove(r - c)
        posDiag.remove(r + c)
        
    backtrack(0)
    return res
  ```

  - Time complexity: $O(N!)$ --> 由于每个皇后必须位于不同列，因此已经放置的皇后所在的列不能放置别的皇后。第一个皇后有 N 列可以选择，第二个皇后最多有 N-1 列可以选择，第三个皇后最多有 N-2 列可以选择（如果考虑到不能在同一条斜线上，可能的选择数量更少），因此所有可能的情况不会超过 N! 种，遍历这些情况的时间复杂度是 O(N!)。
  - Space complexity: $O(N)$ --> N is the number of queens 空间复杂度主要取决于递归调用层数、记录每行放置的皇后的列下标的数组以及三个集合，递归调用层数不会超过 N，数组的长度为 N，每个集合的元素个数都不会超过 N。

## 3. 22 [Generate Parentheses](https://leetcode.com/problems/generate-parentheses/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (70.31%) | [`string`](https://leetcode.com/tag/string); [`backtracking`](https://leetcode.com/tag/backtracking) |

Given `n` pairs of parentheses, write a function to *generate all combinations of well-formed parentheses*.

**Example 1:**

```
Input: n = 3
Output: ["((()))","(()())","(())()","()(())","()()()"]
```

**Example 2:**

```
Input: n = 1
Output: ["()"] 
```

- **Constraints:**
  - `1 <= n <= 8`

- **Thoughts**

  - 括号问题可以简单分成两类，一类是`Stack.md`写过的 [括号的合法性判断](https://labuladong.github.io/algo/4/31/129/) ，一类是==合法括号的生成==。对于括号合法性的判断，主要是借助「栈」这种数据结构，而对于括号的生成，一般都要利用回溯递归的思想。

  - 有关括号问题，你只要记住以下性质，思路就很容易想出来：

    **1、一个「合法」括号组合的左括号数量一定等于右括号数量，这个很好理解**。

    **2、对于一个「合法」的括号字符串组合 `p`，必然对于任何 `0 <= i < len(p)` 都有：子串 `p[0..i]` 中左括号的数量都大于或等于右括号的数量**。

    --> add a left bracket when `left < n`

    --> add a right bracket when `right < left`

    --> add a valid path when `left == right == n`

- **Solution**

  ```python
   def generateParenthesis(self, n: int) -> List[str]:\
    if n == 0:
      return []
    
    res = []
    path = []
    def backtrack(left, right):   			# 当前的左括号数量为 left 个，右括号数量为 right 个
      if left == right == n:						# 得到一个合法的括号组合
        res.append(''.join(path[:]))
        return
      
      # Add a left bracket
      if left < n:
        path.append('(')
        backtrack(left + 1, right)
        path.pop()
      
      # Add a right bracket
      if right < left:
        path.append(')')
        backtrack(left, right + 1)
        path.pop()
    
    backtrack(0, 0)
    return res
  ```

  - Time complexity: <font color=red>空缺！！</font>
  - Space complexity: O(N)