# Tree / Binary Tree 树 / 二叉树

- 树节点类：

  ```python
  class TreeNode:
    def __init__(self, val=0, left=None, right=None):
      self.val = val
      self.left = left
      self.right = right
  ```

- 二叉树解题的思维模式分两类：

  **1、是否可以通过==遍历一遍二叉树==得到答案**？如果可以，用一个 `traverse` 函数配合外部变量来实现，这叫**「遍历」**的思维模式。

  **2、是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案**？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值，这叫**「分解问题」**的思维模式。

  无论使用哪种思维模式，你都需要思考：**如果单独抽出一个二叉树节点，它需要做什么事情？需要在什么时候（前/中/后序位置）做**？其他的节点不用你操心，递归函数会帮你在所有节点上执行相同的操作。

- **二叉树遍历框架**

  应用部分，选择遍历方法的基本的原则：<font color=red>**更快的访问到你想访问的节点**</font>。先序会先访问根节点，后序会先访问叶子节点

  **前中后序是遍历二叉树过程中处理每一个节点的三个<font color=blue>特殊时间点</font>**，绝不仅仅是三个顺序不同的 List：

  前序位置的代码在刚刚进入一个二叉树节点的时候执行；

  后序位置的代码在将要离开一个二叉树节点的时候执行；

  中序位置的代码在一个二叉树节点左子树都遍历完，即将开始遍历右子树的时候执行。

  **前序位置的代码只能从函数参数中获取<u>父节点传递来的数据</u>，而后序位置的代码不仅可以获取参数数据，还可以获取到子树通过函数返回值传递回来的数据**

  <img src="https://labuladong.github.io/algo/images/%e4%ba%8c%e5%8f%89%e6%a0%91%e6%94%b6%e5%ae%98/2.jpeg" alt="img" style="zoom:25%;" />

  **二叉树的所有问题，就是让你在前中后序位置注入巧妙的代码逻辑，去达到自己的目的，你只需要单独思考每一个节点应该做什么，其他的不用你管，抛给二叉树遍历框架，递归会在所有节点上做相同的操作**。--> <font color=red>先确定位置，再在位置后注入代码实现相应功能</font>

  ==**一旦题目和子树有关，那大概率要给函数设置合理的定义和返回值，在后序位置写代码了**。==

  ```python
  def traverse(self, root):
    if root is None:
      return                
    # 前序位置
    traverse(root.left)     # 中序位置   
    traverse(root.right)    # 后序位置
  ```

- <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220706234652980.png" alt="image-20220706234652980" style="zoom:50%;" />

- **二叉树的构造问题一般都是使用「分解问题」的思路：构造整棵树 = 根节点 + 构造左子树 + 构造右子树**。

- BST: 11, 12

## 1. 104 [Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (71.92%) | [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

Given the `root` of a binary tree, return *its maximum depth*.

A binary tree's **maximum depth** is the number of nodes along the longest path from the root node down to the farthest leaf node.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/26/tmp-tree.jpg)

```
Input: root = [3,9,20,null,null,15,7]
Output: 3
```

**Example 2:**

```
Input: root = [1,null,2]
Output: 2
```

- **Solution**

  - <u>Method 1: Recursion --> Depth-first Search_</u>

    ```python
    # Definition for a binary tree node.
    # class TreeNode:
    #     def __init__(self, val=0, left=None, right=None):
    #         self.val = val
    #         self.left = left
    #         self.right = right
    class Solution:
        # 定义：输入根节点，返回这棵二叉树的最大深度
        def maxDepth(self, root: Optional[TreeNode]) -> int:
            if not root:
                return 0
            # 利用定义，计算左右子树的最大深度
            left_height = self.maxDepth(root.left)
            right_height = self.maxDepth(root.right)
            return max(left_height, right_height) + 1  # 整棵树的最大深度等于左右子树的最多节点数取最大值加根节点
    ```
    
    - Time complexity: O(N) --> 其中 n 为二叉树节点的个数。每个节点在递归中只被遍历一次。
    
      Space complexity: O(Height) --> recursion function needs stack memory which depends on the height --> O(logN)

## 2. 144 [Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (62.63%) | [`stack`](https://leetcode.com/tag/stack); [`tree`](https://leetcode.com/tag/tree) |

Given the `root` of a binary tree, return *the preorder traversal of its nodes' values*.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_1.jpg)

```
Input: root = [1,null,2,3]
Output: [1,2,3]
```

**Example 2:**

```
Input: root = []
Output: []
```

**Example 3:**

```
Input: root = [1]
Output: [1]
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[0, 100]`.

  - `-100 <= Node.val <= 100`

**Follow up:** Recursive solution is trivial, could you do it iteratively?

- **Solution**

  - <u>Method 1: Recursion</u>

    ```python
    class Solution:
        def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
            def preOrder(root: Optional[TreeNode]):       
                if not root:
                    return
                res.append(root.val)  # at pre-order location
                preOrder(root.left)
                preOrder(root.right)
            
            res = []
            preOrder(root)
            return res
    ```

    - 易错点：
      - <font color=red>主函数里要单独写一个递归函数！</font>If all of the logis is in one function, `res` will be set to `[]` everytime `preorderTraversal` is called --> 递归过程要用一个新的函数写，然后在主函数call

### 2.1 872 [Leaf-Similar Trees](https://leetcode.com/problems/leaf-similar-trees/description/)

|  Category  |  Difficulty   | Likes | Dislikes |
| :--------: | :-----------: | :---: | :------: |
| algorithms | Easy (64.98%) | 2174  |    60    |

Consider all the leaves of a binary tree, from left to right order, the values of those leaves form a **leaf value sequence***.*

<img src="https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/16/tree.png" alt="img" style="zoom:50%;" />

For example, in the given tree above, the leaf value sequence is `(6, 7, 4, 9, 8)`.

Two binary trees are considered *leaf-similar* if their leaf value sequence is the same.

Return `true` if and only if the two given trees with head nodes `root1` and `root2` are leaf-similar.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/09/03/leaf-similar-1.jpg" alt="img" style="zoom:50%;" />

```
Input: root1 = [3,5,1,6,2,9,8,null,null,7,4], root2 = [3,5,1,6,7,4,2,null,null,null,null,null,null,9,8]
Output: true
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/09/03/leaf-similar-2.jpg" alt="img" style="zoom:50%;" />

```
Input: root1 = [1,2,3], root2 = [1,3,2]
Output: false
```

- **Constraints:**

  - The number of nodes in each tree will be in the range `[1, 200]`.

  - Both of the given trees will have values in the range `[0, 200]`.

- **Thoughts**

  从这道题的题意可看出，关键是要求出binary tree的leaf node的值并组成一个list。根据二叉树的两类思维模式：

  **1、是否可以通过==遍历一遍二叉树==得到答案**？如果可以，用一个 `traverse` 函数配合外部变量来实现，这叫**「遍历」**的思维模式。

  **2、是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案**？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值，这叫**「分解问题」**的思维模式。

  可以判断得出，这道题可以使用递归解决 --> 以得到以root为根的数的leaf node sequence为函数

- **Solution**

  ```python
  class TreeNode:
    def __init__(self, val=0, left=None, right=None):
      self.val = val
      self.left = left
      self.right = right
  
  def leafSimilar(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> bool:
    def getLeafValue(root):  # get the leaf node value sequence of root
      if not root:
        return []
      if not root.left and not root.right:
        return [root.val]
      left = getLeafValue(root.left)      # the leaf node value sequence of left subtree
      right = getLeafValue(root.right)		# the leaf node value sequence of right subtree
      return left + right   							
    return getLeafValue(root1) == getLeafValue(root2)  
  ```

  - Time complexity: O(N1 + N2) --> 其中 n1, n2 为二叉树节点的个数 --> 遍历了两棵树的所有节点

    Space complexity: O(N1 + N2) --> recursion uses stack memory 空间复杂度主要取决于存储「叶值序列」的空间以及深度优先搜索的过程中需要使用的栈空间。

## 3. 226 [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (71.76%) | [Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/description/) |

Given the `root` of a binary tree, invert the tree, and return *its root*. 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/14/invert1-tree.jpg)

```
Input: root = [4,2,7,1,3,6,9]
Output: [4,7,2,9,6,3,1]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/03/14/invert2-tree.jpg)

```
Input: root = [2,1,3]
Output: [2,3,1]
```

**Example 3:**

```
Input: root = []
Output: []
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[0, 100]`.

  - `-100 <= Node.val <= 100`

- **Thoughts**

  默念二叉树解题总纲：

  - 这题能不能用「遍历」的思维模式解决： 可以，写一个 `traverse` 函数遍历每个节点，让每个节点的左右子节点颠倒过来就行了。

  - 单独抽出一个节点，需要让它做什么？让它把自己的左右子节点交换一下。

  - 需要在什么时候做？好像前中后序位置都可以。

- **Solution**

  - <u>Method 1: DFS</u>

    ```python
    def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
            # Method 1: traverse
            if root == None:
                return
            
            temp = root.left
            root.left = root.right
            root.right = temp
            self.invertTree(root.left)
            self.invertTree(root.right)
            return root
    ```

    Time complexity:  O(N) --> 其中 N 为二叉树节点的数目。我们会遍历二叉树中的每一个节点，对每个节点而言，我们在常数时间内交换其两棵子树。

    Space complexity: O(N) 

  - <u>Method 2: Recursion</u>

    ```python
    class Solution:
        def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
            if not root:
                return
    
            leftTree = self.invertTree(root.left)     # Time complexity: O(n/2)
            rightTree = self.invertTree(root.right)   # Time complexity: O(n/2)
    
            root.right = leftTree
            root.left = rightTree
            
            # above can also be written as: 
            # root.right, root.left = self.invertTree(root.left), self.invertTree(root.right)
            return root 
    ```

    Time complexity: O(N) --> 其中 N 为二叉树节点的数目。我们会遍历二叉树中的每一个节点，对每个节点而言，我们在常数时间内交换其两棵子树

    Space complexity: O(N) --> ==stack memory!== 使用的空间由递归栈的==深度==决定，它等于当前节点在二叉树中的高度。在平均情况下，二叉树的高度与节点个数为对数关系，即O(logN)。而在最坏情况下，树形成链状，空间复杂度为O(N)。

## 4. 116 [Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (57.44%) | [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

You are given a **perfect binary tree** where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:

```java
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`. 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

```
Input: root = [1,2,3,4,5,6,7]
Output: [1,#,2,3,#,4,5,6,7,#]
Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.
```

**Example 2:**

```
Input: root = []
Output: []
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[0, 212 - 1]`.

  - `-1000 <= Node.val <= 1000`

- **Follow-up:**

  - You may only use constant extra space.

  - The recursive approach is fine. You may assume implicit stack space does not count as extra space for this problem.

- **Thoughts**

  这道题的难点在于如何将不属于同一个父节点的节点连接起来 --> **遍历的其实是两个相邻节点之间的「空隙」**

- **Solution**

  - <u>Method 1: Level-order-traversal --> BFS</u>

    题目本身希望我们将二叉树的每一层节点都连接起来形成一个链表。因此直观的做法我们可以对二叉树进行<font color=red>**层次遍历**</font>，在层次遍历的过程中将我们将二叉树每一层的节点拿出来遍历并连接。

    层次遍历基于<font color=red>**广度优先搜索**</font>，它与广度优先搜索的不同之处在于，广度优先搜索每次只会取出一个节点来拓展，而层次遍历会每次将<font color=red>**队列中的所有元素**</font>都拿出来拓展，这样能保证每次从队列中拿出来遍历的元素都是属于同一层的，因此我们可以在遍历的过程中修改每个节点的 next 指针，同时拓展下一层的新队列。

    ```python
    from collections import deque
    class Solution:
        def connect(self, root: 'Optional[Node]') -> 'Optional[Node]':
            if not root:
                return
            
            Q = deque([root])
            
            # while 循环迭代每一层
            while Q:
                n = len(Q)   									# 当前队列大小
                for i in range(n):						# 遍历这一层所有节点
                    node = Q.popleft()				# 从队首取出元素
                    # 将队首元素取出并进行连接
                    if i < n - 1:
                        node.next = Q[0]
    
                    # 拓展下一层节点
                    if node.left:						
                        Q.append(node.left)
                    if node.right:
                        Q.append(node.right)
            return root 
    ```

    Time complexity: O(N) --> 每个节点会被访问一次且只会被访问一次，即从队列中弹出，并建立next 指针。

    Space complexity: O(N) --> 这是一棵完美二叉树 (perfect binary tree)，它的最后一个层级包含 N/2个节点。==广度优先遍历的复杂度取决于一个层级上的最大元素数量。==这种情况下空间复杂度为 O(N)

  - <u>Method 2: 三叉树</u>

    <img src="https://labuladong.github.io/algo/images/%e4%ba%8c%e5%8f%89%e6%a0%91%e7%b3%bb%e5%88%97/3.png" alt="img" style="zoom:25%;" />

    --> **一棵二叉树被抽象成了一棵三叉树，三叉树上的每个节点就是原先二叉树的两个相邻节点** --> 每个「三叉树节点」需要做的事就是把自己内部的两个二叉树节点穿起来：

    ```python
    class Solution:
        def connect(self, root: 'Optional[Node]') -> 'Optional[Node]':
            def traverse(node1: 'Optional[Node]', node2: 'Optional[Node]'):
                if (node1 == None or node2 == None):   # 三叉树每个root包含两个节点
                    return 
    
                node1.next = node2										# 将传入的两个节点穿起来
                traverse(node1.left, node1.right)			# 连接相同父节点的两个节点
                traverse(node2.left, node2.right)
                traverse(node1.right, node2.left)			# 连接跨越父节点的两个子节点
    
            if root == None:
                return
            traverse(root.left, root.right)
            return root 
    ```

  - <u>Method 3: Recursion / DFS</u>

    一共有两种类型的next指针：

    - 第一种情况两个子节点属于同一个父节点，因此直接通过父节点建立两个子节点的 next 指针即可。

      `node.left.next = node.right`

      <img src="https://assets.leetcode-cn.com/solution-static/116/4.png" alt="fig4" style="zoom: 25%;" />

      - 第二种情况是连接不同父节点之间子节点的情况。更具体地说，连接的是第一个父节点的右孩子和第二父节点的左孩子。由于已经在父节点这一层建立了next 指针，因此可以直接通过第一个父节点的next 指针找到第二个父节点，然后在它们的孩子之间建立连接。

        `node.right.next = node.next.left`

        <img src="https://assets.leetcode-cn.com/solution-static/116/5.png" alt="fig5" style="zoom:25%;" />

      完成当前层的连接后，进入下一层重复操作，直到所有的节点全部连接。进入下一层后需要更新最左节点，然后从新的最左节点开始遍历该层所有节点。因为是完美二叉树，因此最左节点一定是当前层最左节点的左孩子。如果当前最左节点的左孩子不存在，说明已经到达该树的最后一层，完成了所有节点的连接。
    
    ```python
    def connect(self, root: 'Optional[Node]') -> 'Optional[Node]':
            def traverse(root):
         				# 递归退出条件: 当遍历到最后一层时
                if not (root.left or root.right):
                    return
                
                root.left.next = root.right    # each root's left child must have a right child
                if root.next:									 # 如果root是左孩子的话才有可能
                    root.right.next = root.next.left
            
                traverse(root.left)
                traverse(root.right)s
    
            if not root:
                return
            traverse(root)
            return root 
    
    # Time complexity: O(N) --> each node is accessed once
    # Space complexity: O(N) --> stack memory
    ```

  - <font color=red>**<u>Method 4: Traversal with O(1) memory</u>**</font>

    ==方法3的变种==

    从根节点开始，由于第0层只有一个节点，所以不需要连接，直接为第1层节点建立 next 指针即可。该算法中需要注意的一点是，当我们为第 N层节点建立next指针时，处于第N-1层。当第N层节点的 next 指针全部建立完成后，移至第N层，建立第 N+1层节点的next 指针。

    遍历某一层的节点时，这层节点的next 指针已经建立。因此我们只需要知道这一层的最左节点，就可以按照链表方式遍历，不需要使用队列。

    上面思路的伪代码如下：

    ```pseudocode
    leftmost = root
    while (leftmost.left != null) {
        head = leftmost
        while (head.next != null) {
            1) Establish Connection 1
            2) Establish Connection 2 using next pointers
            head = head.next
        }
        leftmost = leftmost.left
    }
    ```

    ```python
    def connect(self, root: 'Optional[Node]') -> 'Optional[Node]':
            # Method 1: BFS + O(1) Space Complexity
            if not root:
                return 
        
            leftmost = root    # 从根节点开始
            while leftmost.left:            # 遍历每一层
                head = leftmost
                while head:                 # 遍历每一层的所有节点
                    head.left.next = head.right
                    if head.next:
                        head.right.next = head.next.left
                
                    head = head.next        # 指针向后移动
                leftmost = leftmost.left    # 指针指向下一层最左的节点
            return root
    ```

  ## 5. 114 [Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/description/)

  |  Category  |   Difficulty    |                             Tags                             |
  | :--------: | :-------------: | :----------------------------------------------------------: |
  | algorithms | Medium (58.05%) | [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

  Given the `root` of a binary tree, flatten the tree into a "linked list":

  - The "linked list" should use the same `TreeNode` class where the `right` child pointer points to the next node in the list and the `left` child pointer is always `null`.
  - The "linked list" should be in the same order as a [**pre-order** **traversal**](https://en.wikipedia.org/wiki/Tree_traversal#Pre-order,_NLR) of the binary tree.

  **Example 1:**

  <img src="https://assets.leetcode.com/uploads/2021/01/14/flaten.jpg" alt="img" style="zoom:50%;" />

  ```
  Input: root = [1,2,5,3,4,null,6]
  Output: [1,null,2,null,3,null,4,null,5,null,6]
  ```

  **Example 2:**

  ```
  Input: root = []
  Output: []
  ```

  **Example 3:**

  ```
  Input: root = [0]
  Output: [0]
  ```

  **Constraints:**

  - The number of nodes in the tree is in the range `[0, 2000]`.
  - `-100 <= Node.val <= 100`

  **Follow up:** Can you flatten the tree in-place (with `O(1)` extra space)?

- **Thoughts**

  - 由于题目要求不能返回值(`Do not return anything, modify root in-place instead.`)，所以该题不能用遍历的思想做（遍历的话一定要求要`return root`）--> 用==分解问题==的思想解决：**是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案**？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值

  - 在此题中，如果定义一个递归函数`flatten`，则这个函数的定义为：*输入节点 root，然后 root 为根的二叉树就会被拉平为一条链表* --> Procedures:

    - S1：利用`flatten(root.left)`和`flatten(root.right)`先将root的左右子树拉平并记录

    - S2：将拉平的左子树移到右子树上，再将已保存的右子树连接在后面

      <img src="https://labuladong.github.io/algo/images/%e4%ba%8c%e5%8f%89%e6%a0%91%e7%b3%bb%e5%88%97/2.jpeg" alt="img" style="zoom:33%;" />

- **Solution**

  ```python
  def flatten(self, root: Optional[TreeNode]) -> None:
          """
          Do not return anything, modify root in-place instead.
          """
          # S1: call function recursively to get flattened left and right tree
          # S2: change the left tree to the right
          # S3: connect the root with the flattened right tree
  
          if not root:
              return
          
          # 利用定义，把左右子树拉平
          flatten(root.left)  
          flatten(root.right)
          
          # /**** 后序遍历位置 ****/
      		# 1、左右子树已经被拉平成一条链表
          left = root.left
          right = root.right
          
          # 2、将左子树作为右子树
          root.left = None
          root.right = left
          
          # 3. 将原先的右子树接到当前右子树的末端
          head = root
          while head.right:
            head = head.right
          head.right = right
  ```

  Time complexity: O(N) --> flatten every node

  Space complexity: O(1) --> modify the tree in-place

  - 注意点：
    - 因为root是treenode不是node，所以遍历node是用`head.right`而不是`head.next`

## 6. 543 [Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/description/)

|  Category  |  Difficulty   |                  Tags                   |
| :--------: | :-----------: | :-------------------------------------: |
| algorithms | Easy (54.45%) | [`tree`](https://leetcode.com/tag/tree) |

Given the `root` of a binary tree, return *the length of the **diameter** of the tree*.

The **diameter** of a binary tree is the **length** of the longest path between any two nodes in a tree. This path may or may not pass through the `root`.

The **length** of a path between two nodes is represented by the number of edges between them.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/03/06/diamtree.jpg" alt="img" style="zoom: 50%;" />

```
Input: root = [1,2,3,4,5]
Output: 3
Explanation: 3 is the length of the path [4,2,1,3] or [5,2,1,3].
```

**Example 2:**

```
Input: root = [1,2]
Output: 1 
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[1, 104]`.

  - `-100 <= Node.val <= 100`

- **Thoughts**

  - 从题目可以获得几个定义：

    - Length = longest path = depth(left tree) + depth(right tree)
    - depth = 1 + max(depth(left child), depth(right child))

  - 这道题可用遍历做，但是由于我们要求最大直径，需要获取子树的位置 --> ==后序位置==

    <font color=red>**一旦你发现题目和子树有关，那大概率要给函数设置合理的定义和返回值，在后序位置写代码了**</font>

- **Solution**

  ```python
  def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
    # Method: DFS
    # question:  longest path = depth(left tree) + depth(right tree)
    # --> depth = 1 + max(depth(left child), depth(right child))
    
    self.maxDiameter = 0
    def depth(root):
      if not root:
        return 0
      left = depth(root.left)
      right = depth(root.right)
      
      # left + right 为最大直径，如果直径大于已有答案，则进行更新
      self.maxDiameter = max(self.maxDiameter, left + right)
      return max(left, right) + 1							# return depth (not longest path!!)
    depth(root)
    return self.maxDiameter
  ```

  Time complexity: O(N) --> traverse through all nodes in the tree

  Space complexity: O(logN) / O(Height) -->  其中Height 为二叉树的高度。由于递归函数在递归过程中需要为==每一层递归函数分配栈空间==，所以这里需要额外的空间且该空间取决于递归的深度，而递归的深度显然为二叉树的高度，并且每次递归调用的函数里又只用了常数个变量，所以所需空间复杂度为 O(Height) 。

## 7. 654 [Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/description/)

|  Category  |   Difficulty    |                  Tags                   |
| :--------: | :-------------: | :-------------------------------------: |
| algorithms | Medium (83.70%) | [`tree`](https://leetcode.com/tag/tree) |

You are given an integer array `nums` with no duplicates. A **maximum binary tree** can be built ==recursively== from `nums` using the following algorithm:

1. Create a root node whose value is the maximum value in `nums`.
2. Recursively build the left subtree on the **subarray prefix** to the **left** of the maximum value.
3. Recursively build the right subtree on the **subarray suffix** to the **right** of the maximum value.

Return *the **maximum binary tree** built from* `nums`. 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/12/24/tree1.jpg" alt="img" style="zoom:50%;" />

```
Input: nums = [3,2,1,6,0,5]
Output: [6,3,5,null,2,0,null,null,1]
Explanation: The recursive calls are as follow:
- The largest value in [3,2,1,6,0,5] is 6. Left prefix is [3,2,1] and right suffix is [0,5].
    - The largest value in [3,2,1] is 3. Left prefix is [] and right suffix is [2,1].
        - Empty array, so no child.
        - The largest value in [2,1] is 2. Left prefix is [] and right suffix is [1].
            - Empty array, so no child.
            - Only one element, so child is a node with value 1.
    - The largest value in [0,5] is 5. Left prefix is [0] and right suffix is [].
        - Only one element, so child is a node with value 0.
        - Empty array, so no child.
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/12/24/tree2.jpg" alt="img" style="zoom:50%;" />

```
Input: nums = [3,2,1]
Output: [3,null,2,null,1]
```

- **Constraints:**

  - `1 <= nums.length <= 1000`

  - `0 <= nums[i] <= 1000`

  - All integers in `nums` are **unique**.

- **Thoughts**

  - 由题目可得，此题目用递归的方法进行，先构造root，再构造左右子树，为前序遍历
  - 步骤：
    - S1: calculate the max value and its index 
    - S2: create a root based on information above
    - S3: call the function recursively to build left tree and right tree

- **Solution**

  ```python
  def constructMaximumBinaryTree(self, nums: List[int]) -> Optional[TreeNode]:
    if (nums == None or len(nums) == 0):
      return 
    
    # S1: find the max value and its index
    maxVal = float('-inf')
    for i in range(len(nums)):      
      if nums[i] > maxVal:
        maxVal = nums[i]
        index = i
    
    # S2: create the root node
    root = TreeNode(maxVal)
    
    # S3: call the function recursively to build left and right tree
    root.left = self.constructMaximumBinaryTree(nums[0:index])
    root.right = self.constructMaximumBinaryTree(nums[index+1:])
    return root
  ```

  Time complexity: $O(N^2)$ --> 最坏情况是数组单调有序，那么每次查找都需要O(n)，递归深度O(n) <font color=red>每一次递归函数都会进行一次遍历查找 --> $O(N^2)$</font>

  Space complexity: O(N) --> recursion

## 8. 105 [Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/)

|  Category  |   Difficulty    |                            Likes                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (57.93%) | [`array`](https://leetcode.com/tag/array); [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

Given two integer arrays `preorder` and `inorder` where `preorder` is the preorder traversal of a binary tree and `inorder` is the inorder traversal of the same tree, construct and return *the binary tree*.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/02/19/tree.jpg" alt="img" style="zoom:50%;" />

```
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
```

**Example 2:**

```
Input: preorder = [-1], inorder = [-1]
Output: [-1]
```

- **Constraints:**

  - `1 <= preorder.length <= 3000`

  - `inorder.length == preorder.length`

  - `-3000 <= preorder[i], inorder[i] <= 3000`

  - `preorder` and `inorder` consist of **unique** values.

  - Each value of `inorder` also appears in `preorder`.

  - `preorder` is **guaranteed** to be the preorder traversal of the tree.

  - `inorder` is **guaranteed** to be the inorder traversal of the tree.

- **Thoughts**

  该题目给了前序遍历和中序遍历的结果，要求创造出满足遍历结果的树。解决该题的关键在于使用好前序和中序的特性。

  - Preorder: root --> left --> right 第一个数一定是root的值
  - Inorder: left --> root --> right root前的一定是左子树，root后的一定是右子树

- **Solution**

  <img src="/Users/xinyuzhang/Downloads/IMG_505842D2948B-1.jpeg" alt="IMG_505842D2948B-1" style="zoom:25%;" />

  ```python
  def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
    if not preorder or not inorder:
      return None
    root = TreeNode(preorder[0])
    mid = inorder.index(preorder[0]) # linear time complexity: for 1 value, time complexity is O(1)
    root.left = self.buildTree(preorder[1:mid+1], inorder[:mid])
    root.right = self.buildTree(preorder[mid+1:], inorder[mid+1:])
    return root
  ```

  Time complexity: $O(N)$ --> N is the number of nodes in the tree

  Space complexity: O(N) --> recursion stack memory for each element in the two lists

## 9. 297 [Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (53.91%) | [`tree`](https://leetcode.com/tag/tree); [`design`](https://leetcode.com/tag/design) |

Serialization is the process of converting a data structure or object into a sequence of bits so that it can be stored in a file or memory buffer, or transmitted across a network connection link to be reconstructed later in the same or another computer environment.

Design an algorithm to serialize and deserialize a binary tree. There is no restriction on how your serialization/deserialization algorithm should work. You just need to ensure that a binary tree can be serialized to a string and this string can be deserialized to the original tree structure.

**Clarification:** The input/output format is the same as [how LeetCode serializes a binary tree](vscode-webview://0vrk0tlarcnaq4sq9bsaglc4mhs9fiho7ccg0maihuejt14g6cc1/faq/#binary-tree). You do not necessarily need to follow this format, so please be creative and come up with different approaches yourself.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/09/15/serdeser.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [1,2,3,null,null,4,5]
Output: [1,2,3,null,null,4,5]
```

**Example 2:**

```
Input: root = []
Output: []
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[0, 104]`.

  - `-1000 <= Node.val <= 1000

- **Thoughts**

  - 该题目的serialization其实就是对于==树的遍历==，可使用DFS进行前序遍历； deserialization和==树的构造题==类型相近，将list的数构造成树
  - 关键点在于serialization输出一条完整字符串；deserialization最开始要将该字符串split

- **Solution**

  ```python
  class Codec:
  
      def serialize(self, root):
          """Encodes a tree to a single string.
          
          :type root: TreeNode
          :rtype: str
          """
          # Method: DFS (pre-order traversal)
          res = []
          
          def dfs(root):
            if not root:
              res.append('N')    # use 'N' to indicate null node
              return None
            res.append(str(root.val))
            dfs(root.left)
            dfs(root.right)
          dfs(root)
          return ','.join(res)   # use comma to connect all characters into a new string
        
      def deserialize(self, data):
           """Decodes your encoded data to tree.
          
           :type data: str
           :rtype: TreeNode
           """
          vals = data.split(',') # split the whole string into array by delimiter ","
          self.index = 0         # make it global --> a member variable of the class
          
          def dfs():
            if vals[self.index] == 'N':
              self.index += 1
              return None
            root = TreeNode(int(vals[self.index]))
            self.index += 1
            root.left = dfs()
            root.right = dfs()
            return root
          return dfs()
  ```

  Time complexity: O(N) --> traverse through each node

  Space complexity: O(N) --> recursion operations require stack memory

## 10. 652 [Find Duplicate Subtrees](https://leetcode.com/problems/find-duplicate-subtrees/description/)

|  Category  |   Difficulty    |                  Tags                   |
| :--------: | :-------------: | :-------------------------------------: |
| algorithms | Medium (55.97%) | [`tree`](https://leetcode.com/tag/tree) |

Given the `root` of a binary tree, return all **duplicate subtrees**.

For each kind of duplicate subtrees, you only need to return the root node of any **one** of them.

Two trees are **duplicate** if they have the **same structure** with the **same node values**. 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/08/16/e1.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [1,2,3,4,null,2,4,null,null,4]
Output: [[2,4],[4]]
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/08/16/e2.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [2,1,1]
Output: [[1]]
```

**Example 3:**

<img src="https://assets.leetcode.com/uploads/2020/08/16/e33.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [2,2,2,3,null,3,null]
Output: [[2,3],[3]]
```

- **Constraints:**

  - The number of the nodes in the tree will be in the range `[1, 10^4]`

  - `-200 <= Node.val <= 200`

- **Thoughts**

  - 比如说，你站在图中这个节点 2 上：

    

    <img src="https://labuladong.github.io/algo/images/%E4%BA%8C%E5%8F%89%E6%A0%913/4.png" alt="img" style="zoom:25%;" />

    

    如果你想知道以自己为根的子树是不是重复的，是否应该被加入结果列表中，你需要知道什么信息？

    **你需要知道以下两点**：

    **1、以我为根的这棵二叉树（子树）长啥样**？--> [序列化和反序列化二叉树](https://labuladong.github.io/article/fname.html?fname=二叉树的序列化)，二叉树的前序/中序/后序遍历结果可以描述二叉树的结构。<u>[每棵不同子树的序列化结果都是唯一的]</u>

    **2、以其他节点为根的子树都长啥样**？每个节点都把以自己为根的子树的样子存到一个外部的数据结构里(hash map)即可。

- **Solution**

  ```python
  def findDuplicateSubtrees(self, root: Optional[TreeNode]) -> List[Optional[TreeNode]]:
          # S1: serialize the binary tree to find each subtree as a string
          # S2: use a hash table to store each node's string (key: string, value: count)
          res = []
          hashtable = dict()
  
          # extract each tree as a string
          def dfs(root):
              if not root:
                  return 
              
              # left = dfs(root.left)   # left subtree
              # right = dfs(root.right) # right subtree
              # subtree = str(left) + ',' + str(right) + ',' + str(root.val)
              subtree = '{}, {}, {}'.format(dfs(root.left), dfs(root.right), root.val)
  
              hashtable[subtree] = 1 + hashtable.get(subtree, 0)
              if hashtable[subtree] == 2:   # subtree is a duplicate
                  res.append(root)
              return subtree
          
          dfs(root)
          return res
  ```

  Time complexity: $O(N^2)$ -->  N 是二叉树上节点的数量。遍历所有节点，在每个节点处序列化 (连接成字符串) 需要时间 O(N) --> in each call, generating `subtree` and computing hashtable both cost O(N)

  Space complexity: $O(N^2)$  --> Recursion has O(N) time complexity, and in each recursive call, building a hashtable costs O(N)

## 11. 230 [Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/description/)

|  Category  |   Difficulty    |                            Likes                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (67.85%) | [`binary-search`](https://leetcode.com/tag/binary-search); [`tree`](https://leetcode.com/tag/tree) |

Given the `root` of a binary search tree, and an integer `k`, return *the* `kth` *smallest value (**1-indexed**) of all the values of the nodes in the tree*.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/01/28/kthtree1.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [3,1,4,null,2], k = 1
Output: 1
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2021/01/28/kthtree2.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [5,3,6,2,4,null,null,1], k = 3
Output: 3
```

- **Constraints:**

  - The number of nodes in the tree is `n`.

  - `1 <= k <= n <= 104`

  - `0 <= Node.val <= 104`

- **Follow up:** If the BST is modified often (i.e., we can do insert and delete operations) and you need to find the kth smallest frequently, how would you optimize? ==Add `size` information in the TreeNode initialization==

  想找到第 `k` 小的元素，或者说找到排名为 `k` 的元素，如果想达到对数级复杂度，关键也在于每个节点得知道他自己排第几。

  比如说你让我查找排名为 `k` 的元素，当前节点知道自己排名第 `m`，那么我可以比较 `m` 和 `k` 的大小：

  1、如果 `m == k`，显然就是找到了第 `k` 个元素，返回当前节点就行了。

  2、如果 `k < m`，那说明排名第 `k` 的元素在左子树，所以可以去左子树搜索第 `k` 个元素。

  3、如果 `k > m`，那说明排名第 `k` 的元素在右子树，所以可以去右子树搜索第 `k - m - 1` 个元素。

  这样就可以将时间复杂度降到 `O(logN)` 了。

  那么，如何让每一个节点知道自己的排名呢？

  这就是我们之前说的，需要在==二叉树节点中维护额外信息==。**每个节点需要记录，以自己为根的这棵二叉树有多少个节点**。(节点 `x` 的左子树的节点个数就是 `x` 的排名)

  ```python
  class TreeNode:
      def __init__(self, val=0, left=None, right=None):
          self.val = val
          self.left = left
          self.right = right
          self.size = 0  # 以该节点为根的树的节点总数
  ```

  有了 `size` 字段，外加 BST 节点左小右大的性质，对于每个节点 `node` 就可以通过 `node.left` 推导出 `node` 的排名，从而做到我们刚才说到的对数级算法。

  当然，`size` 字段需要在增删元素的时候需要被正确维护，力扣提供的 `TreeNode` 是没有 `size` 这个字段的，所以我们这道题就只能利用 BST 中序遍历的特性实现了，但是我们上面说到的优化思路是 BST 的常见操作，还是有必要理解的。

- **Thoughts**

  - ==二叉树的中序遍历结果是有序的（升序）!== --> 可以通过中序遍历找到第 k 个最小元素 --> dfs + record the order of each element
  - 二叉树的中序遍历可以通过迭代和递归两种方式实现

- **Solution**

  - <u>Method 1: Recursion</u> ❤ Better! 

    ```python
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
            self.res = 0
            self.order = 0      # current order of the node traversed
            def dfs(root, k):
                if not root:
                    return 
                dfs(root.left, k)
                self.order += 1
                if self.order == k:
                    self.res = root.val
                dfs(root.right, k)
            dfs(root, k)   
            return self.res 
    ```

    Time Complexity: O(N) --> traverse all nodes in the tree

    Space Complexity: O(N) --> recursion operations require stack memory

  - <u>Method 2: Iteration</u>

    ```python
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
            stack = []
            n = 0
            while root or stack:
                while root:
                    stack.append(root)   # append a root in the stack and continue to find the left subtree
                    root = root.left
                root = stack.pop()
                n += 1
                if n == k:
                    return root.val
                root = root.right 
    ```

    Time Complexity: O(N) --> O(H+k)，其中 H 是树的高度。在开始遍历之前，我们需要 O(H) 到达叶结点。当树是平衡树时，时间复杂度取得最小值 O(logN+k)；当树是线性树（树中每个结点都只有一个子结点或没有子结点）时，时间复杂度取得最大值 O(N+k)。

    Space Complexity: O(N) --> recursion operations require stack memory

  ## 12. 538 [Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/description/)

  |  Category  |   Difficulty    |                  Tags                   |
  | :--------: | :-------------: | :-------------------------------------: |
  | algorithms | Medium (66.39%) | [`tree`](https://leetcode.com/tag/tree) |

  Given the `root` of a Binary Search Tree (BST), convert it to a Greater Tree such that every key of the original BST is changed to the original key plus the sum of all keys greater than the original key in BST.

  As a reminder, a *binary search tree* is a tree that satisfies these constraints:

  - The left subtree of a node contains only nodes with keys **less than** the node's key.
  - The right subtree of a node contains only nodes with keys **greater than** the node's key.
  - Both the left and right subtrees must also be binary search trees.

  **Example 1:**

  <img src="https://assets.leetcode.com/uploads/2019/05/02/tree.png" alt="img" style="zoom:50%;" />

  ```
  Input: root = [4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
  Output: [30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
  ```

  **Example 2:**

  ```
  Input: root = [0,null,1]
  Output: [1,null,1]
  ```

  **Constraints:**

  - The number of nodes in the tree is in the range `[0, 104]`.
  - `-104 <= Node.val <= 104`
  - All the values in the tree are **unique**.
  - `root` is guaranteed to be a valid binary search tree.

- **Thoughts**

  BST 的每个节点左小右大，这似乎是一个有用的信息，既然累加和是计算大于等于当前值的所有元素之和，那么每个节点都去计算右子树的和 --> 利用中序遍历

- **Solution**

  ```python
  def convertBST(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
    self.sum = 0
    def dfs(root):
      if not root:
        return
      dfs(root.right)
      self.sum += root.val
      root.val = self.sum
      dfs(root.left)
    dfs(root)
    return root
  ```

  Time Complexity: O(N) --> traverse all nodes in the tree

  Space Complexity: O(N) --> recursion operations require stack memory

## 13. 98 [Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/description/)

|  Category  |   Difficulty    |                  Tree                   |
| :--------: | :-------------: | :-------------------------------------: |
| algorithms | Medium (30.63%) | [`tree`](https://leetcode.com/tag/tree) |

Given the `root` of a binary tree, *determine if it is a valid binary search tree (BST)*.

A **valid BST** is defined as follows:

- The left subtree of a node contains only nodes with keys **less than** the node's key.
- The right subtree of a node contains only nodes with keys **greater than** the node's key.
- Both the left and right subtrees must also be binary search trees. 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/12/01/tree1.jpg" alt="img" style="zoom:67%;" />

```
Input: root = [2,1,3]
Output: true
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/12/01/tree2.jpg" alt="img" style="zoom:67%;" />

```
Input: root = [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[1, 104]`.

  - `-231 <= Node.val <= 231 - 1`

- **Thoughts**

  - 初学者做这题很容易有误区：BST 不是左小右大么，那我只要检查 `root.val > root.left.val` 且 `root.val < root.right.val` 不就行了？

    这样是不对的，因为 BST 左小右大的特性是指 `root.val` 要比左子树的所有节点都更大，要比右子树的所有节点都小，你只检查左右两个子节点当然是不够的。

    正确解法是通过==使用辅助函数，增加函数参数列表，在参数中携带额外信息，将这种约束传递给子树的所有节点==，这也是二叉搜索树算法的一个小技巧吧。


  - 出现的坑在于，对于每一个节点 `root`，代码值检查了它的左右孩子节点是否符合左小右大的原则；但是根据 BST 的定义，`root` 的整个左子树都要小于 `root.val`，整个右子树都要大于 `root.val`。 --> 对于某一个节点`root`，除了检验自己的左右子节点，==还要把root的约束传递给左右子树==

- **Solution**

  ```python
  def isValidBST(self, root: Optional[TreeNode]) -> bool:
    # 限定以root为根的每个子树节点必须满足 max.val > root.val > min.val (key!)
    def valid(root, min, max):
      # base case
      if not root:
        return True
      if not (root.val < max and root.val > min):
        return False
      # max value for root.left is root.val; min value for root.right is root.val
      return valid(root.left, min, root.val) and valid(root.right, root.val, max)
    return valid(root, float('-inf'), float('inf'))
  ```

  Time complexity: O(N)

  Space complexity: O(N)

## 14. 110 [Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (46.94%) | [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as:

> a binary tree in which the left and right subtrees of *every* node differ in height by no more than 1.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/10/06/balance_1.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [3,9,20,null,null,15,7]
Output: true
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/10/06/balance_2.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [1,2,2,3,3,null,null,4,4]
Output: false
```

**Example 3:**

```
Input: root = []
Output: true
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[0, 5000]`.

  - `-104 <= Node.val <= 104`

- **Thoughts**

  - 这道题的重点是计算出每个节点左子树和右子树的高度，所以该题应在后序位置进行计算，与`6. 543 [Diameter of Binary Tree]`题类似

- **Solution**

  ```python
  def isBalanced(self, root: Optional[TreeNode]) -> bool:
    self.res = True
    
    def depth(root):
      if not root:
        return 0
      
      left = depth(root.left)
      right = depth(root.right)
      if abs(left - right) > 1:
        self.res = False
      return 1 + max(left, right)     # return height of the root node
    
    depth(root)
    return self.res
  ```

  - Time complexity: O(N) --> 其中 n 是二叉树中的节点个数。使用自底向上的递归，每个节点的计算高度和判断是否平衡都只需要处理一次，最坏情况下需要遍历二叉树中的所有节点，因此时间复杂度是 O(n)。

    Space complexity: O(N) --> 空间复杂度主要取决于递归调用的层数，递归调用的层数不会超过 n。

## 15. 100 [Same Tree](https://leetcode.com/problems/same-tree/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (55.62%) | [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

Given the roots of two binary trees `p` and `q`, write a function to check if they are the same or not.

Two binary trees are considered the same if they are structurally identical, and the nodes have the same value.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/12/20/ex1.jpg" alt="img" style="zoom: 67%;" />

```
Input: p = [1,2,3], q = [1,2,3]
Output: true
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2020/12/20/ex2.jpg" alt="img" style="zoom:67%;" />

```
Input: p = [1,2], q = [1,null,2]
Output: false
```

**Example 3:**

<img src="https://assets.leetcode.com/uploads/2020/12/20/ex3.jpg" alt="img" style="zoom:67%;" />

```
Input: p = [1,2,1], q = [1,1,2]
Output: false
```

- **Constraints:**

  - The number of nodes in both trees is in the range `[0, 100]`.

  - `-104 <= Node.val <= 104`

- **Thought**

  - 要确定两棵树是否相同，先检查root是否一样，然后分别检查左右子树是否相同 -->  recursively check each node 
  - 使用dfs递归方法:
    - Recursive function: `sameTree(root1.left, root2.left) and sameTree(root1.right, root2.right)`
    - Base case: 
      - 如果root1和root2都为空时 --> return True
      - 如果root1和root2有一个为空另一个不为空时 --> return False
      - 如果root1和root2的值不相同时 --> return False

- **Solution**

  ```python
  def isSameTree(self, p: Optional[TreeNode], q: Optional[TreeNode]) -> bool:
    if not p and not q:
      return True
    if not p or not q or p.val != q.val:
      return False
    return self.isSameTree(p.left, q.left) and self.isSameTree(p.right, q.right)
  ```

  - Time complexity: O(min(m,n))，其中 m 和 n 分别是两个二叉树的节点数。对两个二叉树==同时==进行深度优先搜索，只有当两个二叉树中的对应节点都不为空时才会访问到该节点，因此被访问到的节点数不会超过较小的二叉树的节点数。

    Space complexity: O(min(m,n)) --> 空间复杂度取决于递归调用的层数，递归调用的层数不会超过较小的二叉树的最大高度，最坏情况下，二叉树的高度等于节点数。

## 16. 572 [Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/description/)

|  Category  |  Difficulty   |                  Tags                   |
| :--------: | :-----------: | :-------------------------------------: |
| algorithms | Easy (45.42%) | [`tree`](https://leetcode.com/tag/tree) |

Given the roots of two binary trees `root` and `subRoot`, return `true` if there is a subtree of `root` with the same structure and node values of` subRoot` and `false` otherwise.

A subtree of a binary tree `tree` is a tree that consists of a node in `tree` and all of this node's descendants. The tree `tree` could also be considered as a subtree of itself. 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/04/28/subtree1-tree.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [3,4,5,1,2], subRoot = [4,1,2]
Output: true
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2021/04/28/subtree2-tree.jpg" alt="img" style="zoom:50%;" />

```
Input: root = [3,4,5,1,2,null,null,null,null,0], subRoot = [4,1,2]
Output: false
```

- **Constraints:**

  - The number of nodes in the `root` tree is in the range `[1, 2000]`.

  - The number of nodes in the `subRoot` tree is in the range `[1, 1000]`.

  - `-104 <= root.val <= 104`

  - `-104 <= subRoot.val <= 104`

- **Thoughts**

  - 要判断一个树 t 是不是树 s 的子树，那么可以判断 t 是否和树 s 的任意子树相等。那么就转化成 [100. Same Tree](https://leetcode-cn.com/problems/same-tree/) --> 在 s 的每个子节点上，判断该子节点是否和 t 相等
  - 判断两个树是否==相等==的三个条件是==与==的关系，即：

    1. 当前两个树的根节点值相等；
    2. s 的左子树和 t 的左子树相等；
    3. s 的右子树和 t 的右子树相等。
  - 判断 t 是否为 s 的子树的三个条件是==或==的关系，即：
    1. 当前两棵树相等；
    2. t 是 s 的左子树；
    3. t 是 s 的右子树。
  - 注意考虑`root`为空和`subRoot`为空的情况！

- **Solution**

  ```python
  def isSubtree(self, root: Optional[TreeNode], subRoot: Optional[TreeNode]) -> bool:
    if not subRoot: return True
  	if not root: return False		# now subRoot is not empty and root is empty
  	if self.isSametree(root, subRoot):
      return True
    
    return self.isSubtree(root.left, subRoot) or self.isSubtree(root.right, subRoot)
    
  def isSametree(self, p, q):
    if not p and not q:
      return True
    if not p or not q or p.val != q.val:
      return False
    return self.isSametree(p.left, q.left) and self.isSametree(p.right, q.right)
  ```

  - Time complexity: **O(|s| \* |t|)** --> all the node value in s are equal to `t->val` and when we go to the new function of `isSametree` all the nodes in `t` has the same value except the last one then the time complexity in this case is indeed near to s*t --> 对于每一个 s 上的点，都需要做一次dfs来和 t 匹配，匹配一次的时间代价是 O(|t|)，那么总的时间代价就是 $O(|s| \times |t|)$。故渐进时间复杂度为 $O(|s| \times |t|)$

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220701151551854.png" alt="image-20220701151551854" style="zoom:100%;" />

## 17. 236 [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/)

|  Category  |   Difficulty    |                  Tags                   |
| :--------: | :-------------: | :-------------------------------------: |
| algorithms | Medium (55.62%) | [`tree`](https://leetcode.com/tag/tree) |

Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).” 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3
Explanation: The LCA of nodes 5 and 1 is 3.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
Explanation: The LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself according to the LCA definition.
```

**Example 3:**

```
Input: root = [1,2], p = 1, q = 2
Output: 1
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[2, 105]`.

  - `-109 <= Node.val <= 109`

  - All `Node.val` are **unique**.

  - `p != q`

  - `p` and `q` will exist in the tree.

- **Thoughts**

  - <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220701161130905.png" alt="image-20220701161130905" style="zoom:80%;" />

    

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220701162023397.png" alt="image-20220701162023397" style="zoom:80%;" />

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220701162108213.png" alt="image-20220701162108213" style="zoom:80%;" />

- **Solution**

  ```python
  # Situation 1: not left and not right --> return None
  # Situation 2: left and right --> return root
  # Situation 3: left or right --> return left if left else right
  def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    # Base case
    if not root:
      return
    if (root.val == p.val) or (root.val == q.val):					 # because p and q exist and all values are unique
      return root
    
    # Recursive function
    left = self.lowestCommonAncestor(root.left, p, q)					# find the LCA node in left subtree
    right = self.lowestCommonAncestor(root.right, p, q)				# find the LCA node in right subtree
    if left and right:																			  # Situation 2
      return root
    return left if left else right														# Situation 3 --> p, q in one subtree
  ```

  - Time complexity: O(N) --> 其中 N 为二叉树节点数；最差情况下，需要递归遍历树的所有节点。

    Space complexity: O(N) --> 最差情况下，递归深度达到 N ，系统使用 O(N) 大小的额外空间

  - 公共祖先题目变体: https://mp.weixin.qq.com/s/njl6nuid0aalZdH5tuDpqQ

## 18. 235 [Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/description/)

|  Category  |   Difficulty    |                  Tags                   |
| :--------: | :-------------: | :-------------------------------------: |
| algorithms | Medium (55.62%) | [`tree`](https://leetcode.com/tag/tree) |

Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).”

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3
Explanation: The LCA of nodes 5 and 1 is 3.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
Explanation: The LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself according to the LCA definition.
```

**Example 3:**

```
Input: root = [1,2], p = 1, q = 2
Output: 1 
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[2, 105]`.

  - `-109 <= Node.val <= 109`

  - All `Node.val` are **unique**.

  - `p != q`

  - `p` and `q` will exist in the tree.

- **Thoughts**

  - Cases are decided based on p.val, q.val and root.val

  - 整体的遍历过程：

    - 我们从根节点开始遍历；

    - 如果当前节点的值大于 p 和 q 的值，说明 p 和 q 应该在当前节点的左子树，因此将当前节点移动到它的左子节点；

    - 如果当前节点的值小于 p 和 q 的值，说明 p 和 q 应该在当前节点的右子树，因此将当前节点移动到它的右子节点；

    - 如果当前节点的值不满足上述两条要求，那么说明当前节点就是「分岔点」。此时，p 和 q 要么在当前节点的不同的子树中，要么其中一个就是当前节点。

- **Solution**

  ```python
  def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    cur = root
  
    while cur:   # guaranteed there must be an LCA --> p and q will exist in the tree
      if p.val < cur.val and q.val < cur.val:     # find in the left tree
        cur = cur.left
      elif p.val > cur.val and q.val > cur.val:   # find in the right tree
        cur = cur.right
      # if one value is larger and the other one is smaller;or if root.val = one value
      else:   
        return cur
  ```

  - Time complexity: O(logn) --> 其中 N 为二叉搜索树节点数

    Space complexity: O(1) --> no extra memory used

  - <font color=red>**注意点：**</font>
    - 为什么不直接用root而是创建了一个cur指针：如果直接移动root指针 (reassign root to the next node on each step of the loop)
      - Root would no longer point to the original root of the tree. What if the code that called your function was relying on the root variable they passed to you? 
      - Reassigning parameters is an anti-pattern except when the function explicitly makes clear that that is the intention (除非是modifying in-place).
      - Since cur here is just a pointer/reference to the object and not a copy of the tree itself, it <font color=red>**costs basically nothing**</font> to make that variable and preserve the original root parameter

## 19. [Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/description/)

|  Category  |   Difficulty    | Tags |
| :--------: | :-------------: | :--: |
| algorithms | Medium (72.98%) | Tree |

Given a binary tree `root`, a node *X* in the tree is named **good** if in the path from root to *X* there are no nodes with a value *greater than* X.

Return the number of **good** nodes in the binary tree.

**Example 1:**

**![img](https://assets.leetcode.com/uploads/2020/04/02/test_sample_1.png)**

```
Input: root = [3,1,4,3,null,1,5]
Output: 4
Explanation: Nodes in blue are good.
Root Node (3) is always a good node.
Node 4 -> (3,4) is the maximum value in the path starting from the root.
Node 5 -> (3,4,5) is the maximum value in the path
Node 3 -> (3,1,3) is the maximum value in the path.
```

**Example 2:**

**![img](https://assets.leetcode.com/uploads/2020/04/02/test_sample_2.png)**

```
Input: root = [3,3,null,4,2]
Output: 3
Explanation: Node 2 -> (3, 3, 2) is not good, because "3" is higher than it.
```

**Example 3:**

```
Input: root = [1]
Output: 1
Explanation: Root is considered as good.
```

- **Constraints:**

  - The number of nodes in the binary tree is in the range `[1, 10^5]`.

  - Each node's value is between `[-10^4, 10^4]`.

- **Thoughts**

  二叉树的递归分为「遍历」和「分解问题」两种思维模式，这道题需要用到「遍历」的思维，利用函数参数给子树传递信息。

  函数参数 `pathMax` 记录从根节点到当前节点路径中的最大值，通过比较 `root.val` 和 `pathMax` 比较就可判断 `root` 节点是不是「好节点」。

  --> traverse through all nodes, if `root.val >= pathSum` --> the node is a good node

- **Solution**

  ```python
  def goodNodes(self, root: TreeNode) -> int:
    self.count = 0
    def dfs(root, pathSum):
      if not root:
        return
      if root.val >= pathSum:						 # good node: root.val >= pathMax
        self.count += 1
      pathSum = max(pathSum, root.val)
      dfs(root.left, pathSum)
      dfs(root.right, pathSum)
    dfs(root, root.val)
    return self.count
  ```

  - Time complexity: O(N) 

    Space complexity: O(N) --> stack memory required for recursion

## 20. 124 [Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (37.77%) | [`tree`](https://leetcode.com/tag/tree); [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

A **path** in a binary tree is a sequence of nodes where each pair of adjacent nodes in the sequence has an edge connecting them. A node can only appear in the sequence **at most once**. Note that the path does not need to pass through the root.

The **path sum** of a path is the sum of the node's values in the path.

Given the `root` of a binary tree, return *the maximum **path sum** of any **non-empty** path*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/13/exx1.jpg)

```
Input: root = [1,2,3]
Output: 6
Explanation: The optimal path is 2 -> 1 -> 3 with a path sum of 2 + 1 + 3 = 6.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/10/13/exx2.jpg)

```
Input: root = [-10,9,20,null,null,15,7]
Output: 42
Explanation: The optimal path is 15 -> 20 -> 7 with a path sum of 15 + 20 + 7 = 42. 
```

- **Constraints:**

  - The number of nodes in the tree is in the range `[1, 3 * 104]`.

  - `-1000 <= Node.val <= 1000`

- **Thoughts**

  - 这道题目和543. Diameter of Binary Tree相似，区别为所求要素不同

  - 具体而言，该函数的计算如下。

    - 空节点的最大贡献值等于 0。

    - 非空节点的最大贡献值等于节点值与其子节点中的最大贡献值之和（对于叶节点而言，最大贡献值等于节点值）。

- **Solution**

  ```python
  def maxPathSum(self, root: Optional[TreeNode]) -> int:
    # maxPathSum = root.val + pathSum(root.left) + pathSum(root.right)
    # pathSum = root.val + max(left, right)
    self.maxSum = float('-inf')
    def pathSum(root):
      if not root:
        return 0
      left = max(pathSum(root.left), 0)				# 只有在最大贡献值大于 0 时，才会选取对应子节点
      right = max(pathSum(root.right), 0)
      # 后序遍历位置，顺便更新最大路径和
      self.maxSum = max(self.maxSum, left + right + root.val)
      return root.val + max(left, right) # 实现函数定义，左右子树的最大单边路径和加上根节点的值
    pathSum(root)
    return self.pathSum
  ```

  - Time complexity: O(N) 

    Space complexity: O(N) --> stack memory required for recursion

  - <font color=red>注意点：</font>

    - 只有在最大贡献值大于 0 时，才会选取对应子节点！！如果左右子树和的最大值都小于0，则忽略掉该子树的贡献

      ```python
      left = max(pathSum(root.left), 0)				# 只有在最大贡献值大于 0 时，才会选取对应子节点
      right = max(pathSum(root.right), 0)
      ```

      