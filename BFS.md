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

    - Time complexity: O(N) --> traverse through each node in the tree, and each node is accessed once#

      Space complexity: O(N) --> recursion needs stack memory