# Backtracking

--> 回溯算法和DFS 算法非常类似，本质上就是一种暴力穷举算法，复杂度一般都很高。回溯算法和 DFS 算法的细微差别是：**回溯算法是在遍历「树枝」，DFS 算法是在遍历「节点」**

--> 回溯是递归的副产品，只要有递归就会有回溯

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

  <img src="https://img-blog.csdnimg.cn/20210130173631174.png" alt="回溯算法理论基础" style="zoom:50%;" />

  ==**for循环可以理解是横向遍历，backtracking（递归）就是纵向遍历**==

- 回溯法，一般可以解决如下几种问题：

  - 组合问题：N个数里面按一定规则找出k个数的集合
  - 切割问题：一个字符串按一定规则有几种切割方式
  - 子集问题：一个N个数的集合里有多少符合条件的子集
  - 排列问题：N个数按一定规则全排列，有几种排列方式
  - 棋盘问题：N皇后，解数独等等

- **排列/组合/子集问题**

  --> **本质就是穷举所有解，而这些解呈现树形结构，所以合理使用回溯算法框架**

  - **组合问题和子集问题是等价的** (Eg. 77, 78)

  <img src="https://labuladong.github.io/algo/images/%e6%8e%92%e5%88%97%e7%bb%84%e5%90%88/1.jpeg" alt="img" style="zoom:50%;" />

  <img src="https://labuladong.github.io/algo/images/%e6%8e%92%e5%88%97%e7%bb%84%e5%90%88/2.jpeg" alt="img" style="zoom:50%;" />

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

    Space complexity: O(N)

## 4. 79 [Word Search](https://leetcode.com/problems/word-search/description/)

|  Category  |   Difficulty    | Tags |
| :--------: | :-------------: | :--: |
| algorithms | Medium (39.75%) | 9896 |

Given an `m x n` grid of characters `board` and a string `word`, return `true` *if* `word` *exists in the grid*.

The word can be constructed from letters of sequentially adjacent cells, where adjacent cells are horizontally or vertically neighboring. The same letter cell may not be used more than once.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/04/word2.jpg)

```
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
Output: true
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/11/04/word-1.jpg)

```
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
Output: true
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2020/10/15/word3.jpg)

```
Input: board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
Output: false 
```

**Constraints:**

- `m == board.length`
- `n = board[i].length`
- `1 <= m, n <= 6`
- `1 <= word.length <= 15`
- `board` and `word` consists of only lowercase and uppercase English letters.

**Follow up:** Could you use search pruning to make your solution faster with a larger `board`?

- **Thoughts**

  - 从题目来看，这道题没有办法用efficient algorithm解决，只能使用brute-force，运用backtracking算法同时进行剪枝操作 (pruning) --> 有的选点是错的，选它就构建不出目标路径，不能继续选。要撤销这个选择，去尝试别的选择。

  - <img src="https://pic.leetcode-cn.com/1599959074-dSSwlm-image.png" alt="image.png" style="zoom:50%;" />

    以"SEE"为例，首先要选起点：遍历矩阵，找到起点S。

    - 起点可能不止一个，基于其中一个S，看看能否找出剩下的"EE"路径。

    - 下一个字符E有四个可选点：当前点的<font color=blue>**上、下、左、右**</font>。逐个尝试每一种选择。基于当前选择，为下一个字符选点，又有四种选择。

    - 每到一个点做的事情是一样的。DFS 往下选点，构建路径。

    - 当发现某个选择不对，不用继续选下去了，结束当前递归，考察别的选择。

  - 递归关注**当前考察的点**，处理它，其他丢给递归子调用去做。

    - 判断当前选择的点，本身是不是一个错的点。

    - 剩下的字符能否找到路径，交给递归子调用去做。

    如果当前点是错的，不用往下递归了，返回false。否则继续递归四个方向，为剩下的字符选点。

    那么，哪些情况说明这是一个错的点：

    - 当前的点，越出矩阵边界。`r/c < 0 or r/c >= row/col` 
    - 当前的点，之前访问过，不满足「同一个单元格内的字母不允许被重复使用」。
    - 当前的点，不是目标点，比如你想找 E，却来到了 D。

- **Solution**

  ```python
  def exist(self, board: List[List[str]], word: str) -> bool:
    row, col = len(board), len(board[0])
    path = set()			# used path --> no repetitive letter cell
    
    def backtrack(r, c, i):						# i is the index of the word: 表示字符串word从第i个字符开始的后缀子串
      if i == len(word):							# have found the last word
        return True
      if (r < 0 or c < 0 or
          r >= row or c >= col or
          board[r][c] != word[i] or
          (r, c) in path):
        return False
      
      path.add((r, c))
      # 基于当前选择的点[row,col]，能否找到剩余字符的路径。
      res = (backtrack(r + 1, c, i + 1) or
             backtrack(r - 1, c, i + 1) or
             backtrack(r, c + 1, i + 1) or
             backtrack(r, c - 1, i + 1))
      path.remove((r, c))
      return res
    
    # backtrack every cell in the board
    for r in range(row):				
      for c in range(col):
        if backtrack(r, c, 0):
          return True
    return False
  ```

  - Time complexity: $O(M \times N \times 4^{len(word)})$ --> $M\times N$是两个for循环的时间复杂度，在每一个循环里进行了backtrack操作；backtrack是递归的时间复杂度（递归算法的时间复杂度：==子问题个数 ^ 解决一个子问题的复杂度==），一个backtrack操作里会进行4次backtrack操作，解决一次backtrack操作需要进行`len(word)`的搜索，故为$O(M \times N \times 4^{len(word)})$ 

    Space complexity: $O(M\times N)$ --> extra memory for path set which stores each cell of the board (in the worst case)


## 5. 78 [Subsets](https://leetcode.com/problems/subsets/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (71.73%) | [`array`](https://leetcode.com/tag/array); [`backtracking`](https://leetcode.com/tag/backtracking); [`bit-manipulation`](https://leetcode.com/tag/bit-manipulation) |

Given an integer array `nums` of **unique** elements, return *all possible subsets (the power set)*.

The solution set **must not** contain duplicate subsets. Return the solution in **any order**.

**Example 1:**

```
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

**Example 2:**

```
Input: nums = [0]
Output: [[],[0]]
```

- **Constraints:**

  - `1 <= nums.length <= 10`

  - `-10 <= nums[i] <= 10`

  - All the numbers of `nums` are **unique**.

- **Thoughts**

  - 由于子集是无序的，**取过的元素不会重复取，写回溯算法的时候，for就要从==startIndex==开始，而不是从0开始！**

    <img src="https://camo.githubusercontent.com/3f67c171ede9807a17fdeaf91ac87e7813516ad272a3c4ac55ee292514ff2acf/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f3230323031313233323034313334382e706e67" alt="78.子集" style="zoom:50%;" />

    --> **遍历这个树的时候，把所有节点都记录下来，就是要求的子集集合**

  - <u>回溯三部曲：</u>

    - 递归函数参数：全局变量数组path为子集收集元素，二维数组result存放子集组合。（也可以放到递归函数参数里）；递归函数参数需要startIndex。

      ```python
      res = []
      path = []
      def backtrack(nums, start):
      ```

    - 递归终止条件：剩余集合为空的时候，就是叶子节点。--> startIndex已经大于数组的长度了，就终止了，因为没有元素可取了

      ```python
      if start >= len(nums):
        return
      ```

      **其实可以不需要加终止条件，因为startIndex >= nums.size()，本层for循环本来也结束了**。

    - 单层搜索逻辑：<font color=blue>**求取子集问题，不需要任何剪枝！因为子集就是要遍历整棵树**。</font>

      ```python
      for i in range(start, len(nums)):
        path.append(nums[i])		# 子集收集元素
        backtrack(nums, i + 1)	# 注意从i+1开始，元素不重复取
        path.pop()							# 回溯
      ```

- **Solution**

  ```python
  def subsets(self, nums: List[int]) -> List[List[int]]:
    res = []
    path = []
    def backtrack(nums, start):
      res.append(path[:])
      for i in range(start, len(nums)):
        path.append(nums[i])
        backtrack(nums, i + 1)
        path.pop()
    backtrack(nums, 0)
    return res
  ```

  - Time complexity: $O(n \times 2^n)$: for each element, the element can be its own value or empty --> each element has two subsets --> total number of subsets: $2^n$; the length of subset may be up to $n$ --> total time complexity: $O(n \times 2^n)$

    Space complexity: O(n) --> for `path` and stack memory for recursion

## 6. 77 [Combinations](https://leetcode.com/problems/combinations/description/)

|  Category  |   Difficulty    |                          Tags                           |
| :--------: | :-------------: | :-----------------------------------------------------: |
| algorithms | Medium (64.24%) | [`backtracking`](https://leetcode.com/tag/backtracking) |

Given two integers `n` and `k`, return *all possible combinations of* `k` *numbers out of the range* `[1, n]`.

You may return the answer in **any order**.

**Example 1:**

```
Input: n = 4, k = 2
Output:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

**Example 2:**

```
Input: n = 1, k = 1
Output: [[1]]
```

- **Constraints:**

  - `1 <= n <= 20`

  - `1 <= k <= n`

- **Thoughts**

  - **组合和子集是一样的：大小为 `k` 的组合就是大小为 `k` 的子集** --> 题目是标准的组合问题，但我给你翻译一下就变成子集问题了：**给你输入一个数组 `nums = [1,2..,n]` 和一个正整数 `k`，请你生成所有大小为 `k` 的子集**。--> 只需修改78题的base case即可

- **Solution**

  ```python
  def combine(self, n: int, k: int) -> List[List[int]]:
    res = []
    path = []
    
    def backtrack(start, n, k):
      if len(path) == k:
        res.append(path[:])
      
      for i in range(start, n + 1):
        path.append(i)
        backtrack(i + 1, n, k)
        path.pop()
    backtrack(0, n, k)
    return res
  ```

  - Time complexity: 由于我们需要在 n 个元素选 k 个，所以the total number of combinations is $n \choose k$, and each combination has the length of k --> total time complexity: $O(k \times {n \choose k})$

    Space complexity: $O(N + k) = O(N)$ --> O(N) is for the stack memory; O(k) is for the `path` array

## 7. 90 [Subsets II](https://leetcode.com/problems/subsets-ii/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (53.52%) | [`array`](https://leetcode.com/tag/array); [`backtracking`](https://leetcode.com/tag/backtracking) |

Given an integer array `nums` that may contain duplicates, return *all possible subsets (the power set)*.

The solution set **must not** contain duplicate subsets. Return the solution in **any order**. 

**Example 1:**

```
Input: nums = [1,2,2]
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]
```

**Example 2:**

```
Input: nums = [0]
Output: [[],[0]] 
```

- **Constraints:**

  - `1 <= nums.length <= 10`

  - `-10 <= nums[i] <= 10`

- **Thoughts**

  - 这道题与之前的区别在于输入的`nums`存在重复元素，按照之前的思路画出子集的树形结构，显然，两条值相同的相邻树枝会产生重复：

    <img src="https://labuladong.github.io/algo/images/%e6%8e%92%e5%88%97%e7%bb%84%e5%90%88/8.jpeg" alt="img" style="zoom:50%;" />

    所以我们需要进行剪枝，如果一个节点有多条值相同的树枝相邻，则只遍历第一条，剩下的都剪掉，不要去遍历：

    <img src="https://labuladong.github.io/algo/images/%e6%8e%92%e5%88%97%e7%bb%84%e5%90%88/9.jpeg" alt="img" style="zoom:50%;" />

    --> **体现在代码上，需要<font color=blue>先进行排序，让相同的元素靠在一起，如果发现 `nums[i] == nums[i-1]`，则跳过</font>** (类似3Sum; 4Sum题目去重步骤)

- **Solution**

  ```python
  def subsetsWithDup(self, nums: List[int]) -> List[List[int]]:
    res = []
    path = []
    nums.sort()				# 先排序，让相同的元素靠在一起，方便去重剪枝
    
    def backtrack(start, nums):
      res.append(path[:])
      for i in range(start, len(nums)):
        # 剪枝逻辑，值相同的相邻树枝，只遍历第一条
        if (i > start and nums[i] == nums[i - 1]):
          continue
        path.append(nums[i])
        backtrack(i + 1, nums)
        path.pop()
    backtrack(0, nums)
    return res
  ```

  - Time complexity: $O(NlogN + N \times 2^N) = O(N \times 2^N)$

    Space complexity: $O(N)$

## 8. 40 [Combination Sum II](https://leetcode.com/problems/combination-sum-ii/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (52.46%) | [`array`](https://leetcode.com/tag/array); [`backtracking`](https://leetcode.com/tag/backtracking) |

Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sum to `target`.

Each number in `candidates` may only be used **once** in the combination.

**Note:** The solution set must not contain duplicate combinations.

**Example 1:**

```
Input: candidates = [10,1,2,7,6,1,5], target = 8
Output: 
[
[1,1,6],
[1,2,5],
[1,7],
[2,6]
]
```

**Example 2:**

```
Input: candidates = [2,5,2,1,2], target = 5
Output: 
[
[1,2,2],
[5]
]
```

- **Constraints:**

  - `1 <= candidates.length <= 100`

  - `1 <= candidates[i] <= 50`

  - `1 <= target <= 30`

- **Thoughts**

  - 这是一个组合问题，其实换个问法就变成子集问题了：请你计算 `candidates` 中所有和为 `target` 的子集 --> 对比子集问题的解法，只要额外用一个 `pathSum` 变量记录回溯路径上的元素和，然后将 base case 改一改即可解决这道题

  - 关键点：**集合（数组candidates）有重复元素，但还不能有重复的组合**（同90 [Subsets II](https://leetcode.com/problems/subsets-ii/description/)的去重步骤）

    **所谓去重，其实就是使用过的元素不能重复选取** --> 组合问题可以抽象为树形结构，那么“使用过”在这个树形结构上是有两个维度的，一个维度是同一树枝上使用过，一个维度是同一树层上使用过

    回看一下题目，元素在同一个组合内是可以重复的，怎么重复都没事，但两个组合不能相同。**所以我们要去重的是同一树层上的“使用过”，同一树枝上的都是一个组合里的元素，不用去重**。

    <font color=red>**树层去重的话，需要对数组排序！树枝去重的话，使用`used`数组或者`startIndex`去重！**</font>

    ![40.组合总和II](https://camo.githubusercontent.com/3e488c39460e69b34dc80f13189fb69b4c2dfeca214a4570f6eacc19e374b1e6/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333230323733363338342e706e67)

    ![40.组合总和II1](https://camo.githubusercontent.com/01a9f11f54f6742f343d6bd2ad528891610a70242361d3169e3684c90e72fe92/68747470733a2f2f696d672d626c6f672e6373646e696d672e636e2f32303230313132333230323831373937332e706e67)

- **Solution**

  ```python
  def combinationSum2(self, candidates: List[int], target: int) -> List[List[int]]:
          res = []
          path = []
          candidates.sort()  # 先排序，让相同的元素靠在一起
  
          def backtrack(start, candidates, target, pathSum):
              if pathSum == target:
                  res.append(path[:])
              
              # base case，超过目标和，直接结束 --> 否则会导致time limit exceeded
              if pathSum > target:
                  return
              
              for i in range(start, len(candidates)):
                  if (i > start and candidates[i] == candidates[i - 1]):
                      continue
                  path.append(candidates[i])
                  pathSum += candidates[i]
                  backtrack(i + 1, candidates, target, pathSum)
                  path.pop()
                  pathSum -= candidates[i]
          backtrack(0, candidates, target, 0)
          return res
  ```

  - Time complexity: $O(NlogN + N \times 2^N) = O(N \times 2^N)$ 

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220713183406152.png" alt="image-20220713183406152" style="zoom:50%;" />

    Space complexity: $O(N)$