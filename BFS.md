# BFS 广度优先搜索

--> BFS 出现的常见场景：**问题的本质就是让你在一幅「图」中找到从起点 `start` 到终点 `target` 的最近距离**

这个广义的描述可以有各种变体，比如走迷宫，有的格子是围墙不能走，从起点到终点的最短距离是多少？如果这个迷宫带「传送门」可以瞬间传送呢？

再比如说两个单词，要求你通过某些替换，把其中一个变成另一个，每次只能替换一个字符，最少要替换几次？

再比如说连连看游戏，两个方块消除的条件不仅仅是图案相同，还得保证两个方块之间的最短连线不能多于两个拐点。你玩连连看，点击两个坐标，游戏是如何判断它俩的最短连线有几个拐点的？

- Procedures

  - Pick any node, visit the adjacent unvisited vertex, mark it as visited, display it, and insert it in a queue.
  - If there are no remaining adjacent vertices left, remove the first vertex from the queue.
  - Repeat step 1 and step 2 until the queue is empty or the desired node is found.

- **1、为什么 BFS 可以找到最短距离，DFS 不行吗**？

  首先，你看 BFS 的逻辑，`depth` 每增加一次，队列中的所有节点都向前迈一步，这保证了第一次到达终点的时候，走的步数是最少的。

  DFS 不能找最短路径吗？其实也是可以的，但是<u>时间复杂度相对高很多</u>。DFS 实际上是靠<u>递归的堆栈</u>记录走过的路径，你要找到最短路径，肯定得把二叉树中所有树杈都探索完才能对比出最短的路径有多长对不对？而 BFS 借助队列做到一次一步「齐头并进」，是可以在不遍历完整棵树的条件下找到最短距离的。

  形象点说，DFS 是线，BFS 是面；DFS 是单打独斗，BFS 是集体行动。这个应该比较容易理解吧。

  **2、既然 BFS 那么好，为啥 DFS 还要存在**？

  BFS 可以找到最短距离，但是空间复杂度高，而 DFS 的空间复杂度较低。

  还是拿刚才我们处理二叉树问题的例子，假设给你的这个二叉树是满二叉树，节点数为 `N`，对于 DFS 算法来说，空间复杂度无非就是递归堆栈，最坏情况下顶多就是树的高度，也就是 `O(logN)`。

  但是你想想 BFS 算法，队列中每次都会储存着二叉树一层的节点，这样的话最坏情况下空间复杂度应该是树的最底层节点的数量，也就是 `N/2`，用 Big O 表示的话也就是 `O(N)`。

  由此观之，BFS 还是有代价的，一般来说在找最短路径的时候使用 BFS，其他时候还是 DFS 使用得多一些（主要是递归代码好写）。

```python
from collections import deque
def BFS(start, target):
  visited = set()        # set to keep track of visited nodes --> visited 的主要作用是防止走回头路，大部分时候都是必须的，但是像一般的二叉树结构，没有子节点到父节点的指针，不会走回头路就不需要 visited
  q = deque([start])		 # S1: initialize a queue and add the start in queue
  visisted.add(start)		
  step = 0							 # 记录扩散的步数
  										
  while q:								# S2: traverse through each level
    n = len(q)
    # 将当前队列中的所有节点向四周扩散 
    for i in range(n):    # S3: traverse through each node in a level
      cur = q.popleft()   # S4: extract the first node in the queue
      # ！！判断是否到终点   # S5: check if the target is reached
      if cur == target:
        return step
      # 将cur的相邻节点加入队列  # S6: append adjacent nodes into the queue (for tree: cur.left and cur.right)
      for neighbor in (adjacent):    # 泛指 cur 相邻的节点，比如说二维数组中，cur 上下左右四面的位置就是相邻节点
      # eg. for neighbor in graph[cur]:
        if neighbor not in visited:
          q.append(neighbor)
          visisted.add(neighbor)
     step += 1
```

## 1. 111 [Minimum Depth of Binary Tree](https://leetcode.com/problems/minimum-depth-of-binary-tree/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (42.64%) | [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) |

Given a binary tree, find its ==minimum depth==.

The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

**Note:** A leaf is a node with no children.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/10/12/ex_depth.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [3,9,20,null,null,15,7]
Output: 2
```

**Example 2:**

```
Input: root = [2,null,3,null,4,null,5,null,6]
Output: 5
```

-  **Constraints:**

  - The number of nodes in the tree is in the range `[0, 105]`.

  - `-1000 <= Node.val <= 1000`

- **Thoughts**

  - 题目求的minimum length正好是BFS问题的变种（求最近距离）
  - 明确start和target: start是root节点，target是叶子节点 (with no children --> 判断是否到终点条件：`if not (cur.left or cur.right)`)

- **Solution**

  ```python
  from collections import deque
  def minDepth(self, root: Optional[TreeNode]) -> int:
    if not root:
      return 0
    q = deque([root])   										# S1: initialize a queue and add the start in queue
    depth = 1
    
    while q:																# S2: traverse through each level
      n = len(q)
      for _ in range(n):										# S3: traverse through each node in a level
        cur = q.popleft()										# S4: extract the first node in the queue
        if not (cur.left or cur.right):			# S5: check if the target is reached
          return depth
        if cur.left:												# S6: append adjacent nodes into the queue 
          q.append(cur.left)								# (for tree: cur.left and cur.right)
        if cur.right:
          q.append(cur.right)
      depth += 1
    return depth
  ```

  - Time complexity: O(N) --> traverse through each node in the tree, and each node is accessed once

    Space complexity: O(N) --> create a queue and N is the element in the queue (at most N elements)

## 2. 102 [Binary Tree Level Order Traversal](https://leetcode.com/problems/binary-tree-level-order-traversal/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (61.11%) | [`tree`](https://leetcode.com/tag/tree); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) |

Given the `root` of a binary tree, return *the level order traversal of its nodes' values*. (i.e., from left to right, level by level). 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/02/19/tree1.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [3,9,20,null,null,15,7]
Output: [[3],[9,20],[15,7]]
```

**Example 2:**

```
Input: root = [1]
Output: [[1]]
```

**Example 3:**

```
Input: root = []
Output: []
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[0, 2000]`.

  - `-1000 <= Node.val <= 1000`

- **Thoughts**

  - 从题目得知，这道题应该使用BFS (Breadth-first Search)算法，而BFS算法是用queue这个数据结构实现的

  - 图解过程：

    <img src="/Users/xinyuzhang/Downloads/IMG_4C35AB2AF17B-1.jpeg" alt="IMG_4C35AB2AF17B-1" style="zoom:50%;" />

- **Solution**

  - <u>Method 1: BFS</u>

    ```python
    from collections import deque
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
      res = []
      q = deque([root])
      
      while q:											# traverse through each level
        level = []
        for _ in range(len(q)):			# traverse through each node
          node = q.popleft()
          level.append(node.val)
          q.append(node.left) if node.left else None
          q.append(node.right) if node.right else None
        if level:
          res.append(level)
      return res
    ```

    - Time complexity: O(N) --> traverse through each node in the tree, and each node is accessed once

      Space complexity: O(N) --> create a queue and N is the element in the queue (at most N elements)

  - <u>Method 2: DFS</u>

    本题使用 DFS 同样能做。由于题目要求每一层的节点都是从左到右遍历，因此递归时也要<u>**先递归左子树、再递归右子树**</u>。

    DFS 做本题的主要问题是： DFS 不是按照层次遍历的。为了让递归的过程中同一层的节点放到同一个列表中，<font color=blue>**在递归时要记录每个节点的深度 level。递归到新节点要把该节点放入 level 对应列表的末尾**。</font>

    当遍历到一个新的深度 level，而最终结果 res 中还没有创建 level 对应的列表时 (`len(res) == level`)，应该在 res 中新建一个列表用来保存该 level 的所有节点。

    ```python
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
      res = []
      def dfs(root, level):
        if not root:
          return
        if len(res) == level:				# 判断当前层是否已经存在list，没有存在就新建一个
          res.append([])
        dfs(root.left, level + 1)
        dfs(root.right, level + 1)
      dfs(root, 0)
      return res
    ```

    - Time complexity: O(N) --> traverse through each node in the tree, and each node is accessed once#

      Space complexity: O(N) --> recursion needs stack memory

## 3. 199 [Binary Tree Right Side View](https://leetcode.com/problems/binary-tree-right-side-view/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (59.73%) | [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search); [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) |

Given the `root` of a binary tree, imagine yourself standing on the **right side** of it, return *the values of the nodes you can see ordered from top to bottom*.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/02/14/tree.jpg" alt="img" style="zoom: 67%;" />

```
Input: root = [1,2,3,null,5,null,4]
Output: [1,3,4]
```

**Example 2:**

```
Input: root = [1,null,3]
Output: [1,3]
```

**Example 3:**

```
Input: root = []
Output: []
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[0, 100]`.

  - `-100 <= Node.val <= 100`

- **Solution**

  - <u>Method 1: BFS</u>

    根据题意，遍历一棵树我们需要的是每一层的最右侧点 --> 使用广度优先搜索，将每一层最后一个节点加入进结果

    ```python
    from collections import
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
      res = []
      q = deque([root])
      
      while q:
        rightSide = q[-1]
        for _ in range(len(q)):
          node = q.popleft()
          if node:
            q.append(node.left) if node.left else None
            q.append(node.right) if node.right else None
        if rightSide:
          res.append(rightSide.val)
      return res
    ```

    - Time complexity: O(N) --> traverse through each node in the tree, and each node is accessed once

      Space complexity: O(N) --> create a queue and N is the element in the queue (at most N elements)

  - <u>Method 2: DFS</u>

    我们按照 「根结点 -> 右子树 -> 左子树」 的顺序访问，就可以保证每层都是最先访问最右边的节点的。我们可以存储在***每个深度***访问的第一个结点，一旦我们知道了树的层数，就可以得到最终的结果数组。

    <img src="https://assets.leetcode-cn.com/solution-static/199/fig1.png" alt="fig1" style="zoom: 25%;" />

    ```python
    def rightSideView(self, root: Optional[TreeNode]) -> List[int]:
      res = []
      def dfs(root, depth):
        if not root:
          return
        if len(res) == depth:
          res.append(root.val)
        # above: 当前节点所在深度还没有出现在res里，说明在该深度下当前节点是第一个被访问的节点，因此将当前节点加入res中。
        dfs(root.right, depth + 1)
        dfs(root.left, depth + 1)
      dfs(root, 0)
      return res
    ```

    - Time complexity: O(N) --> traverse through each node in the tree, and each node is accessed once

      Space complexity: O(N) --> recursion needs stack memory

## 4. 994 [Rotting Oranges](https://leetcode.com/problems/rotting-oranges/description/)

|  Category  |   Difficulty    |                        Tags                         |
| :--------: | :-------------: | :-------------------------------------------------: |
| algorithms | Medium (51.78%) | [`hash-table`](https://leetcode.com/tag/hash-table) |

You are given an `m x n` `grid` where each cell can have one of three values:

- `0` representing an empty cell,
- `1` representing a fresh orange, or
- `2` representing a rotten orange.

Every minute, any fresh orange that is **4-directionally adjacent** to a rotten orange becomes rotten.

Return *the minimum number of minutes that must elapse until no cell has a fresh orange*. If *this is impossible, return* `-1`.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/02/16/oranges.png)

```
Input: grid = [[2,1,1],[1,1,0],[0,1,1]]
Output: 4
```

**Example 2:**

```
Input: grid = [[2,1,1],[0,1,1],[1,0,1]]
Output: -1
Explanation: The orange in the bottom left corner (row 2, column 0) is never rotten, because rotting only happens 4-directionally.
```

**Example 3:**

```
Input: grid = [[0,2]]
Output: 0
Explanation: Since there are already no fresh oranges at minute 0, the answer is just 0.
```

- **Constraints:**

  - `m == grid.length`

  - `n == grid[i].length`

  - `1 <= m, n <= 10`

  - `grid[i][j]` is `0`, `1`, or `2`.

- **Thoughts**

  - 由题目我们可以知道每分钟每个腐烂的橘子都会使上下左右相邻的新鲜橘子腐烂，这其实是一个**模拟广度优先搜索**的过程。所谓广度优先搜索，就是从起点出发，每次都尝试访问同一层的节点，如果同一层都访问完了，再访问下一层，最后广度优先搜索找到的路径就是从起点开始的最短合法路径。

    回到题目中，假设图中只有一个腐烂的橘子，它每分钟向外拓展，腐烂上下左右相邻的新鲜橘子，那么下一分钟，就是这些被腐烂的橘子再向外拓展腐烂相邻的新鲜橘子，这与广度优先搜索的过程均一一对应，**上下左右相邻的新鲜橘子就是该腐烂橘子尝试访问的同一层的节点，路径长度就是新鲜橘子被腐烂的时间。**我们记录下每个新鲜橘子被腐烂的时间，最后如果单元格中没有新鲜橘子，腐烂所有新鲜橘子所必须经过的最小分钟数就是新鲜橘子被腐烂的时间的最大值。

    - Cannot use DFS: two rotten oranges will rot fresh oranges simultaneously, however, DFS can only rot in order
    - Use BFS: can run multiple sources at each time（多源广度优先搜索）观察到对于所有的腐烂橘子，其实它们**在广度优先搜索上是等价于同一层的节点的**

  - 问题等价：everytime all rotten oranges are the nodes in the same level, and the adjacent oranges are in the next level

  - <u>Procedures:</u>

    - S1: Initialize the queue and variables
    - S2: Prework: add initial rotten oranges to the queue; count the number of fresh oranges
    - S3: at each time unit, pop all oranges in the queue and add next-rotten oranges ([r, c])
    - S4: once the queue is empty, can stop
    - S5: check if fresh orange == 0

- **Solution**

  ```python
  from collections import deque
  def orangesRotting(self, grid: List[List[int]]) -> int:
    # S1
    q = deque()
    time, fresh = 0, 0
    row, col = len(grid), len(grid[0])
    
    # S2
    for r in range(row):
      for c in range(col):
        if grid[r][c] == 1:
          fresh += 1
        if grid[r][c] == 2:
          q.append((r, c))
    
    dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]
    while q and fresh: 
      for _ in range(len(q)):
        r, c = q.popleft()
        # find adjacent fresh oranges to make them rotten
        for d in dir:
          rNew, cNew = r + d[0], c + d[1]
          # base case
          if rNew < 0 or cNew < 0 or rNew >= row or cNew >= col:
            continue
          if grid[rNew][cNew] != 1:
              continue
          q.append((rNew, cNew))
          grid[rNew][cNew] = 2
          fresh -= 1
      time += 1     		# 遍历完一层节点 = 此时已将所有可变的新鲜橙子变成腐烂橙子 --> time += 1			
    return time if fresh == 0 else -1
  ```

  - Time complexity: O(nm) --> 进行一次广度优先搜索的时间

    Space complexity: O(nm) --> for queue

  - <font color=red>**注意点：** 图问题里设计到同一时刻或者多源的情况，用BFS解决！--> [Multi-source BFS]</font>

## 5. 286 Walls and Gates

Description

You are given a m x n 2D grid initialized with these three possible values.

```
-1` - A wall or an obstacle.
`0` - A gate.
`INF` - Infinity means an empty room. We use the value `2^31 - 1 = 2147483647` to represent INF as you may assume that the distance to a gate is less than `2147483647`.
Fill each empty room with the distance to its nearest gate. If it is impossible to reach a `Gate`, that room should remain filled with `INF
```

Example

**Example1**

```
Input:
[[2147483647,-1,0,2147483647],[2147483647,2147483647,2147483647,-1],[2147483647,-1,2147483647,-1],[0,-1,2147483647,2147483647]]
Output:
[[3,-1,0,1],[2,2,1,-1],[1,-1,2,-1],[0,-1,3,4]]

Explanation:
the 2D grid is:
INF  -1  0  INF
INF INF INF  -1
INF  -1 INF  -1
  0  -1 INF INF
the answer is:
  3  -1   0   1
  2   2   1  -1
  1  -1   2  -1
  0  -1   3   4
```

**Example2**

```
Input:
[[0,-1],[2147483647,2147483647]]
Output:
[[0,-1],[1,2]]
```

- **Thoughts**

  - 题目是BFS问题的应用：走迷宫，有的格子是围墙不能走，从起点到终点的最短距离是多少？--> 与其使用DFS遍历每一个空房间找到每一个的最短距离，不如从所有gate出发，遍历周围的空房间 --> <font color=red> [Multi-source BFS]</font>

  - S1: Check for base case --> if the array is empty

    S2: Traverse through all cells and add all gates to the queue

    S3: BFS --> at each time, pop the gate cell and make valid adjacent cells distance add 1

    S4: After q is empty, return the changed rooms

- **Solution**

  ```python
  def walls_and_gates(self, rooms: List[List[int]]):
          # write your code here
          if not rooms:
              return rooms
          row, col = len(rooms), len(rooms[0])
          q = deque()
          visited = set()
  
          for r in range(row):
              for c in range(col):
                  if rooms[r][c] == 0:
                      q.append((r, c))
                      visited.add((r, c))
          
          dir = [[1, 0], [-1, 0], [0, 1], [0, -1]]
          dist = 1
          while q:
              for _ in range(len(q)):
                  r, c = q.popleft()
                  for d in dir:
                      rNew = r + d[0]
                      cNew = c + d[1]
                      if rNew < 0 or cNew < 0 or rNew >= row or cNew >= col:
                          continue
                      if rooms[rNew][cNew] == -1 or (rNew, cNew) in visited:
                          continue
                      q.append((rNew, cNew))
                      rooms[rNew][cNew] = dist
                      visited.add((rNew, cNew))
              dist += 1
          return rooms
  ```

  - Time complexity: O(nm) --> 进行一次广度优先搜索的时间

    Space complexity: O(nm) --> for queue

  - <font color=red>**注意点：**</font>为了防止无限循环，添加一个`visited`的集合记录已经修改过的cell


## 6. 1293 [Shortest Path in a Grid with Obstacles Elimination](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (43.53%) | [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) |

You are given an `m x n` integer matrix `grid` where each cell is either `0` (empty) or `1` (obstacle). You can move up, down, left, or right from and to an empty cell in **one step**.

Return *the minimum number of **steps** to walk from the upper left corner* `(0, 0)` *to the lower right corner* `(m - 1, n - 1)` *given that you can eliminate **at most*** `k` *obstacles*. If it is not possible to find such walk return `-1`.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/09/30/short1-grid.jpg)

```
Input: grid = [[0,0,0],[1,1,0],[0,0,0],[0,1,1],[0,0,0]], k = 1
Output: 6
Explanation: 
The shortest path without eliminating any obstacle is 10.
The shortest path with one obstacle elimination at position (3,2) is 6. Such path is (0,0) -> (0,1) -> (0,2) -> (1,2) -> (2,2) -> (3,2) -> (4,2).
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/09/30/short2-grid.jpg)

```
Input: grid = [[0,1,1],[1,1,1],[1,0,0]], k = 1
Output: -1
Explanation: We need to eliminate at least two obstacles to find such a walk.
```

- **Constraints:**

  - `m == grid.length`

  - `n == grid[i].length`

  - `1 <= m, n <= 40`

  - `1 <= k <= m * n`

  - `grid[i][j]` is either `0` **or** `1`.

  - `grid[0][0] == grid[m - 1][n - 1] == 0`

- **Thoughts**

  - 当看到求最短路径时，如果是无向图首先想到使用BFS方法，如果是有向图首先想到使用Dijkstra方法 --> simplifying the problem: shortest path --> BFS for unweighted graphs; optimization: keep track of the removable obstacles

  - 这道题为上一道题 5. 286 Walls and Gates 的变种题，区别在于：对于此题，点坐标(x, y)本身不足以与当前状态一一对应：显然即使在同一位置上，可以越过障碍的剩余机会数目也会决定之后是否能走完、可以走的最短路径等信息，所以需要(x, y, restK)三元组来与当前状态一一对应。

  - 一个剪枝小技巧：

    假设网格中都是0，没有障碍物，每次只能走四个方向，那么最短路径一定是m+n-2。 如果k>=m+n-3，那么最短路径一定是m+n-2。不需要BFS，浪费性能。 如果k<m+n-3，才需要BFS。

    <img src="/Users/xinyuzhang/Downloads/IMG_A4391059976B-1.jpeg" alt="IMG_A4391059976B-1" style="zoom: 25%;" />

- **Solution**

  ```python
  from collections import deque
  def shortestPath(self, grid: List[List[int]], k: int) -> int:
    row, col = len(grid), len(grid[0])
    visited = set()   # keep track of the visited cells to avoid repetition
    q = deque()				# (r, c, kk): row, col, remaining k
    dir = [[-1, 0], [1, 0], [0, -1], [0, 1]]   # left, right, down, up
    
    # prune
    if k >= row + col - 2:
      return row + col - 2
    
    # initialize q, visited and result steps
    visited.add((0, 0, k))
    q.add((0, 0, k))
    steps = 0
    
    while q:
      for _ in range(len(q)):
        r, c, k_rest = q.popleft()
        if r == row - 1 and c == col - 1:				# reach the bottom-right cell
          return steps
        
        # traverse throught the four neighbor cells
        for d in dir:
          r_new, c_new = r + d[0], c + d[1]
          k_new = k_rest - grid[r_new][c_new]
          if r_new < 0 or r_new >= row or c_new < 0 or c_new >= col:   # out of bound
            continue
          state = (r_new, c_new, k_new)
          if k_new >= 0 and state not in visited:
            visited.add(state)
            q.append(state)
      steps += 1
    return -1
  ```

  - Time complexity: $O(m \times n \times k)$ --> for every cell (m*n), the worst case we have to put that cell into the queue/bfs k times

    Space complexity: $O(m \times n \times k)$ --> for every cell (m*n), in the worst case we have to put that cell into the queue/bfs k times which means we need to worst case <u>store all of those steps/paths in the visited set</u>.

## 7. 127 [Word Ladder](https://leetcode.com/problems/word-ladder/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (35.59%) | [`breadth-first-search`](https://leetcode.com/tag/breadth-first-search) |

A **transformation sequence** from word `beginWord` to word `endWord` using a dictionary `wordList` is a sequence of words `beginWord -> s1 -> s2 -> ... -> sk` such that:

- Every adjacent pair of words differs by a single letter.
- Every `si` for `1 <= i <= k` is in `wordList`. Note that `beginWord` does not need to be in `wordList`.
- `sk == endWord`

Given two words, `beginWord` and `endWord`, and a dictionary `wordList`, return *the **number of words** in the ==**shortest transformation sequence**== from* `beginWord` *to* `endWord`*, or* `0` *if no such sequence exists.* 

**Example 1:**

```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log","cog"]
Output: 5
Explanation: One shortest transformation sequence is "hit" -> "hot" -> "dot" -> "dog" -> cog", which is 5 words long.
```

**Example 2:**

```
Input: beginWord = "hit", endWord = "cog", wordList = ["hot","dot","dog","lot","log"]
Output: 0
Explanation: The endWord "cog" is not in wordList, therefore there is no valid transformation sequence.
```

- **Constraints:**

  - `1 <= beginWord.length <= 10`

  - `endWord.length == beginWord.length`

  - `1 <= wordList.length <= 5000`

  - `wordList[i].length == beginWord.length`

  - `beginWord`, `endWord`, and `wordList[i]` consist of lowercase English letters.

  - `beginWord != endWord`

  - All the words in `wordList` are **unique**.

- **Thoughts**

  - 从题目中的"the shortest transformation sequence"可以看出，这道题实际是求从`beginWord`变化至`endWord`的最短路径 --> 使用BFS算法 --> 但是本题并没有直截了当的给出图的模型，因此我们需要把它抽象成图的模型。

    我们可以把每个单词都抽象为一个点，如果两个单词可以只改变一个字母进行转换，那么说明他们之间有一条双向边。因此我们只需要把满足转换条件的点相连，就形成了一张图。基于该图，我们以 beginWord 为图的起点，以 endWord 为终点进行广度优先搜索，寻找 beginWord 到 endWord 的最短路径。

    <img src="https://assets.leetcode-cn.com/solution-static/127/1.png" alt="fig1" style="zoom: 33%;" />

  - 将题目抽象成图的模型后，可以看出，对于`beginWord`，满足成为下一个条件的单词为下一层，直到最后
  - <font color=blue>**关键点：如何将下一层的选择表示出来，即如何选出只有一个字母不相同的单词**</font> --> 建立哈希表：key为单词的pattern, value为符合pattern的单词
  - <u>Procedures:</u>
    - S1: check for base cases
    - S2: build an adjacency list --> {pattern: [list of words]} eg. {'*ot': [hot, dot, lot]}
    - S3: BFS: for each character, check its pattern and the list of words --> find neighbors
    - S4: if neighbor is a possible choice, add it to the queue

- **Solution**

  ```python
  from collections import defaultdict, deque
  def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
    # S1: check for base cases
    if endWord not in wordList:
      return 0
    
    # S2: build an adjacency list --> {pattern: [list of words]} eg. {'*ot': [hot, dot, lot]}
    neighbors = defaultdict(list)
    for word in wordList:
      for i in range(len(word)):		# the character to remove and replace with '*' --> O(n * m)
        pattern = word[:i] + '*' + word[i+1:]    # during slicing, to create a new substring: O(m)
        neighbors[pattern].append(word)
    # Time complexity: O(n * m * m)
    
    # S3: BFS: for each character, check its pattern and the list of words --> find neighbors
    q = deque([beginWord])
    visited = set([beginWord])
    res = 1													# res starts with 1 (beginWord)
    while q:	
      for i in range(len(q)):				# for each layer 
        word = q.popleft()
        if word == endWord:
          return res
        for j in range(len(word)):	# O(m)
          pattern = word[:i] + '*' + word[i+1:]
          # S4: if neighbor is a possible choice, add it to the queue
          for neighbor in neighbors[pattern]:	
            if neighbor not in visited:
              visited.add(neighbor)
              q.append(neighbor)		# add the next word into the queue
       res += 1
     return 0												# if not find a valid path after the loop exists, return 0
  ```

  - Time complexity: $O(n \times m^2)$ --> Suppose there are n words in total and the average length is m --> $O(n \times m)$ is the total number of patterns; during slicing, to create a new substring: O(m)

    Space complexity: $O(n \times m^2)$ --> Suppose there are n words in total and the average length is m --> $O(n \times m)$ is the total number of patterns;  for each pattern, the average length is m