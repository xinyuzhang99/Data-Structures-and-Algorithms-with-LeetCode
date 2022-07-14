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
    board = [['.'] * n for _ in range(n)]   # Initialize the board; !!注意是['.']而不是'.'
    
    res = []
    def backtrack(r):
      if r == n:
        copy = [''.join(row) for row in board]  # the result shows each row --> concatenate each row into a string; row = len(board) 
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

## 9. 47 [Permutations II](https://leetcode.com/problems/permutations-ii/description/)

|  Category  |   Difficulty    |     Tags     |
| :--------: | :-------------: | :----------: |
| algorithms | Medium (55.49%) | backtracking |

Given a collection of numbers, `nums`, that might contain duplicates, return *all possible unique permutations **in any order**.*

**Example 1:**

```
Input: nums = [1,1,2]
Output:
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```

**Example 2:**

```
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

- **Constraints:**

  - `1 <= nums.length <= 8`

  - `-10 <= nums[i] <= 10`

- **Thoughts**

  - 这道题和前面的题目很接近，因为序列中包含了重复的数字，要求我们返回不重复的全排列 --> remove repetitive on each tree level（给树层去重）

  - <font color=red>**难点！！：排列问题的剪枝逻辑，和子集/组合问题的剪枝逻辑略有不同：新增了 `!used[i - 1]` 的逻辑判断**</font>

    为了方便研究，依然把相同的元素用上标 `'` 以示区别。

    假设输入为 `nums = [1,2,2']`，标准的全排列算法会得出如下答案：

    ```
    [
        [1,2,2'],[1,2',2],
        [2,1,2'],[2,2',1],
        [2',1,2],[2',2,1]
    ]
    ```

    显然，这个结果存在重复，比如 `[1,2,2']` 和 `[1,2',2]` 应该只被算作同一个排列，但被算作了两个不同的排列。

    所以现在的关键在于，如何设计剪枝逻辑，把这种重复去除掉？

    **答案是，<font color=red>保证相同元素在排列中的相对位置保持不变</font>**。

    比如说 `nums = [1,2,2']` 这个例子，我保持排列中 `2` 一直在 `2'` 前面。

    这样的话，你从上面 6 个排列中只能挑出 3 个排列符合这个条件：

    ```
    [ [1,2,2'],[2,1,2'],[2,2',1] ]
    ```

    这也就是正确答案。进一步，如果 `nums = [1,2,2',2'']`，我只要保证重复元素 `2` 的相对位置固定，比如说 `2 -> 2' -> 2''`，也可以得到无重复的全排列结果。

    **标准全排列算法之所以出现重复，是因为把相同元素形成的排列序列视为不同的序列，但实际上它们应该是相同的；而如果==固定相同元素形成的序列顺序，当然就避免了重复==**。

    那么反映到代码上，你注意看这个剪枝逻辑：

    ```java
    // 新添加的剪枝逻辑，固定相同的元素在排列中的相对位置
    if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
        // 如果前面的相邻相等元素没有用过，则跳过
        continue;
    }
    // 选择 nums[i]
    ```

    **当出现重复元素时，比如输入 `nums = [1,2,2',2'']`，`2'` 只有在 `2` 已经被使用的情况下才会被选择，同理，`2''` 只有在 `2'` 已经被使用的情况下才会被选择，这就保证了相同元素在排列中的相对位置保证固定**。

    如果你把上述剪枝逻辑中的 `!used[i - 1]` 改成 `used[i - 1]`，其实也可以通过所有测试用例，但效率会有所下降，这是为什么呢？--> 之所以这样修改不会产生错误，是因为这种写法相当于维护了 `2'' -> 2' -> 2` 的相对顺序，最终也可以实现去重的效果。但为什么这样写效率会下降呢？因为这个写法剪掉的树枝不够多。

    <img src="https://labuladong.github.io/algo/images/%e6%8e%92%e5%88%97%e7%bb%84%e5%90%88/13.jpeg" alt="img" style="zoom:50%;" />

    <img src="https://labuladong.github.io/algo/images/%e6%8e%92%e5%88%97%e7%bb%84%e5%90%88/14.jpeg" alt="img" style="zoom:50%;" />

    --> `!used[i - 1]` 这种剪枝逻辑剪得干净利落，而 `used[i - 1]` 这种剪枝逻辑虽然也能得到无重结果，但它剪掉的树枝较少，存在的无效计算较多，所以效率会差一些

  - ```
    i > 0 && nums[i] == nums[i - 1] && !vis[i - 1]
    ```

    - 这句话可以翻译为 遇到相同的节点 要填后面的节点就一定先填过前面的节点 什么意思？ 假设三个1 分别为 1a 1b 1c 要想填1b 就一定之前填过1a 要想填过1c 就一定填过1b 那么三个1都被填充的顺序就一定为1a 1b 1c

    - 同理可以把 !vis[i - 1]换成vis[i-1] 也能通过 此时意思就是要想填i 就必须没填过i-1 顺序就是1c 1b 1a

    - 此外!vis[i - 1]会比vis[i-1] 快很多： 大家可以画个树状图，原因在于!vis[i - 1] 在遇到相同节点时，会一次性取出，而vis[i-1] 则是试错了前面所有的才找到对的

- **Solution**

  ```python
  def permuteUnique(self, nums: List[int]) -> List[List[int]]:
          nums.sort()
          res = []
          path = []
          used = [False] * len(nums)
  
          def backtrack(nums):
              if len(path) == len(nums):
                  res.append(path[:])
                  return
              
              for i in range(len(nums)):
                  if used[i]:
                      continue
  
                  # 新添加的剪枝逻辑，固定相同的元素在排列中的相对位置
                  # !used[i - 1]: 如果前面的相邻相等元素没有用过，则跳过
                  # *遇到相同的节点 要填后面的节点就一定先填过前面的节点* --> 假设三个1 分别为 1a 1b 1c 要想填1b 就一定之前填过1a 要想填过1c 就一定填过1b 那么三个1都被填充的顺序就一定为1a 1b 1c
                  if (i > 0 and nums[i] == nums[i - 1] and not used[i - 1]):
                      continue
  
                  path.append(nums[i])
                  used[i] = True
                  backtrack(nums)
                  path.pop()
                  used[i] = False
  
          backtrack(nums)
          return res
  ```

  - Time complexity: the total number of permutations is $N!$, and each permutation has length N --> total time complexity: $O(N \times N!)$

    Space complexity: O(N)

## 10. 39 [Combination Sum](https://leetcode.com/problems/combination-sum/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (65.80%) | [`array`](https://leetcode.com/tag/array); [`backtracking`](https://leetcode.com/tag/backtracking) |

Given an array of **distinct** integers `candidates` and a target integer `target`, return *a list of all **unique combinations** of* `candidates` *where the chosen numbers sum to* `target`*.* You may return the combinations in **any order**.

The **same** number may be chosen from `candidates` an **unlimited number of times**. Two combinations are unique if the frequency of at least one of the chosen numbers is different.

It is **guaranteed** that the number of unique combinations that sum up to `target` is less than `150` combinations for the given input.

**Example 1:**

```
Input: candidates = [2,3,6,7], target = 7
Output: [[2,2,3],[7]]
Explanation:
2 and 3 are candidates, and 2 + 2 + 3 = 7. Note that 2 can be used multiple times.
7 is a candidate, and 7 = 7.
These are the only two combinations.
```

**Example 2:**

```
Input: candidates = [2,3,5], target = 8
Output: [[2,2,2,2],[2,3,3],[3,5]]
```

**Example 3:**

```
Input: candidates = [2], target = 1
Output: []
```

- **Constraints:**

  - `1 <= candidates.length <= 30`

  - `1 <= candidates[i] <= 200`

  - All elements of `candidates` are **distinct**.

  - `1 <= target <= 500`

- **Thoughts**

  - 这道题和40. [Combination Sum II](https://leetcode.com/problems/combination-sum-ii/description/)的唯一区别在于数组内的元素可以无限取，不需要进行去重的步骤 --> 则去掉树层去重的步骤以及修改回溯算法的`startIndex`就可以了

  - **对于组合问题，什么时候需要startIndex呢？**

    如果是一个集合来求组合的话，就需要startIndex，例如：[77.组合](https://programmercarl.com/0077.组合.html)，[216.组合总和III](https://programmercarl.com/0216.组合总和III.html)。

    如果是多个集合取组合，各个集合之间相互不影响，那么就不用startIndex，例如：[17.电话号码的字母组合](https://programmercarl.com/0017.电话号码的字母组合.html)

- **Solution**

  ```python
  def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
          res = []
          path = []
  
          def backtrack(start,  candidates, target, pathSum):
              if pathSum == target:
                  res.append(path[:])
              
              if pathSum > target:
                  return
              
              for i in range(start, len(candidates)):
                  path.append(candidates[i])
                  pathSum += candidates[i]
                  backtrack(i, candidates, target, pathSum)		# 不用i+1了，表示可以重复读取当前的数
                  path.pop()
                  pathSum -= candidates[i]
          backtrack(0, candidates, target, 0)
          return res
  ```

  - Time complexity: $O(N \times 2^N) = O(N \times 2^N)$ 

    Space complexity: $O(N)$

## 11. 131 [Palindrome Partitioning](https://leetcode.com/problems/palindrome-partitioning/description/)

|  Category  |   Difficulty    |     Tags     |
| :--------: | :-------------: | :----------: |
| algorithms | Medium (59.82%) | backtracking |

Given a string `s`, partition `s` such that ==every substring== of the partition is a **palindrome**. Return all possible palindrome partitioning of `s`.

A **palindrome** string is a string that reads the same backward as forward.

**Example 1:**

```
Input: s = "aab"
Output: [["a","a","b"],["aa","b"]]
```

**Example 2:**

```
Input: s = "a"
Output: [["a"]]
```

- **Constraints:**

  - `1 <= s.length <= 16`

  - `s` contains only lowercase English letters.

- **Thoughts**

  - 本题这涉及到两个关键问题：

    1. 切割问题，有不同的切割方式
    2. 判断回文

  - **其实切割问题类似组合问题**。

    例如对于字符串abcdef：

    - 组合问题：选取一个a之后，在bcdef中再去选取第二个，选取b之后在cdef中在选组第三个.....。
    - 切割问题：切割一个a之后，在bcdef中再去切割第二段，切割b之后在cdef中在切割第三段.....。

    <img src="https://camo.githubusercontent.com/907a2a94961ed3e0c1b67469e6f51163ce7a044af822069f0983ceccf4c168a2/68747470733a2f2f636f64652d7468696e6b696e672e63646e2e626365626f732e636f6d2f706963732f3133312e2545352538382538362545352538392542322545352539422539452545362539362538372545342542382542322e6a7067" alt="131.分割回文串" style="zoom:50%;" />

    递归用来纵向遍历，for循环用来横向遍历，切割线（就是图中的红线）切割到字符串的结尾位置，说明找到了一个切割方法。

  - 回溯三部曲：

    - <u>递归函数参数</u>: 

      - 全局变量数组path存放切割后回文的子串，二维数组result存放结果集。 （这两个参数可以放到函数参数里）

      - startIndex，因为切割过的地方，不能重复切割，和组合问题也是保持一致的。

    - <u>递归函数终止条件: </u>从树形结构的图中可以看出：切割线切到了字符串最后面，说明找到了一种切割方法，此时就是本层递归的终止条件。

      **那么在代码里什么是切割线呢？**

      在处理组合问题的时候，递归参数需要传入startIndex，表示下一轮递归遍历的起始位置，这个<font color=blue>**startIndex就是切割线**</font>。

    - <u>单层搜索的逻辑:</u>

      在`for i in range(start, len(s))`循环中，我们 定义了起始位置startIndex，那么 <font color=blue>**[startIndex, i] 就是要截取的子串**</font>。

      首先判断这个子串是不是回文，如果是回文，就加入在` path`中，path用来记录切割过的回文子串，如果不是则直接跳过。

- **Solution**

  ```python
  def partition(self, s: str) -> List[List[str]]:
    res = []
    path = []
    
    def backtrack(start):
      if start == len(s):				# 当切割线迭代至字符串末尾，说明找到一种切割方法
        res.append(path[:])
        return
      
      for i in range(start, len(s)):
        temp = s[start:i+1]			# start是上一层已经确定了的分割线，i是这一层试图寻找的新分割线
  
        if temp == temp[::-1]:	# 判断被截取的这一段子串是否为回文串
          path.append(temp)
          backtrack(i + 1)			# 切割过的地方不能重复切割所以递归函数需要传入i + 1 --> 从下一处继续切割
          path.pop()
        else:
          continue
    backtrack(0)
    return res 
  ```

  - Time complexity: $O(N \times 2^N) = O(N \times 2^N)$ 

    Space complexity: $O(N)$

## 12. 17 [Letter Combinations of a Phone Number](https://leetcode.com/problems/letter-combinations-of-a-phone-number/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (54.37%) | [`string`](https://leetcode.com/tag/string); [`backtracking`](https://leetcode.com/tag/backtracking) |

Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent. Return the answer in **any order**.

A mapping of digits to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.

<img src="https://assets.leetcode.com/uploads/2022/03/15/1200px-telephone-keypad2svg.png" alt="img" style="zoom:33%;" />



**Example 1:**

```
Input: digits = "23"
Output: ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

**Example 2:**

```
Input: digits = ""
Output: []
```

**Example 3:**

```
Input: digits = "2"
Output: ["a","b","c"] 
```

- **Constraints:**

  - `0 <= digits.length <= 4`

  - `digits[i]` is a digit in the range `['2', '9']`.

- **Thoughts**

  - 本题是一个标准的回溯问题，但是首先需要将数字和字母map --> 首先使用哈希表存储每个数字对应的所有可能的字母，然后进行回溯操作。
  - 注意：输出的是字符串，所以在储存结果时要将`list`存储为字符串形式

- **Solution**

  ```python
  def letterCombinations(self, digits: str) -> List[str]:
          if not digits:
              return 
          
          res = []
          path = []
          # build a hashmap to map each number with according characters
          digitsToChar = { '2': 'abc',
                           '3': 'def',
                           '4': 'ghi',
                           '5': 'jkl',
                           '6': 'mno',
                           '7': 'pqrs',
                           '8': 'tuv',
                           '9': 'wxyz'
                          }
          
          def backtrack(i):
              if i == len(digits):
                  res.append(''.join(path))
                  return
              
              for c in digitsToChar[digits[i]]:
                  path.append(c)
                  backtrack(i + 1)
                  path.pop()
          backtrack(0)
          return res
  ```

  - Time complexity: $O((m + n) \times 3^m \times 4^n)$

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220714184928937.png" alt="image-20220714184928937" style="zoom:50%;" />

    Space complexity: $O(m+n)$

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220714185032162.png" alt="image-20220714185032162" style="zoom:50%;" />