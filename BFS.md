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

  Time complexity: O(N) --> traverse through each node in the tree, and each node is accessed once

  Space complexity: O(N) --> create a queue and N is the element in the queue (at most N elements)