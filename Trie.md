# Trie 前缀树 / 字典树

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220708023056958.png" alt="image-20220708023056958" style="zoom:50%;" />

(k is the length of each string)

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220708023633291.png" alt="image-20220708023633291" style="zoom:50%;" />

- **Trie 树本质上就是一棵从二叉树衍生出来的多叉树**。

  - 多叉树：

    ```python
    class TreeNode:
      def __init__(self):
        self.val = val
        self.children = TreeNode()  # children 数组中存储指向孩子节点的指针
    ```

    <img src="https://labuladong.github.io/algo/images/trie/2.jpeg" alt="img" style="zoom:50%;" />

- Trie can be efficient for checking prefixes --> reuse many nodes; not necessarily create a separate node for every single word

## 1. 208 [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (58.50%) | [`design`](https://leetcode.com/tag/design); [`trie`](https://leetcode.com/tag/trie) |

A [**trie**](https://en.wikipedia.org/wiki/Trie) (pronounced as "try") or **prefix tree** is a tree data structure used to efficiently store and retrieve keys in a dataset of strings. There are various applications of this data structure, such as autocomplete and spellchecker.

Implement the Trie class:

- `Trie()` Initializes the trie object.
- `void insert(String word)` Inserts the string `word` into the trie.
- `boolean search(String word)` Returns `true` if the string `word` is in the trie (i.e., was inserted before), and `false` otherwise.
- `boolean startsWith(String prefix)` Returns `true` if there is a previously inserted string `word` that has the prefix `prefix`, and `false` otherwise.

**Example 1:**

```java
Input
["Trie", "insert", "search", "search", "startsWith", "insert", "search"]
[[], ["apple"], ["apple"], ["app"], ["app"], ["app"], ["app"]]
Output
[null, null, true, false, true, null, true]

Explanation
Trie trie = new Trie();
trie.insert("apple");
trie.search("apple");   // return True
trie.search("app");     // return False
trie.startsWith("app"); // return True
trie.insert("app");
trie.search("app");     // return True
```

- **Constraints:**

  - `1 <= word.length, prefix.length <= 2000`

  - `word` and `prefix` consist only of lowercase English letters.

  - At most `3 * 104` calls **in total** will be made to `insert`, `search`, and `startsWith`.

- **Thoughts**

  - Trie，又称前缀树或字典树，是一棵有根树，其每个节点包含以下字段：

    - 指向子节点的指针数组 $\textit{children}$。使用哈希表实现，key为每个字符，value为指向下一个的TrieNode

    - 布尔字段 $\textit{endOfWord}$，表示该节点是否为字符串的结尾。

    ![image-20220707154503050](/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220707154503050.png)

  	- 注意：要先创建出`TrieNode` class

- **Solution**

  ```python
  class TrieNode:
      def __init__(self):
          self.children = {}      # a hashmap
          self.endOfWord = False  # Boolean value
          
  class Trie:
  
      def __init__(self):
          # build an empty node
          self.root = TrieNode()
          
      def insert(self, word: str) -> None:
          # start from root --> insert character by character and mark the end to True
          cur = self.root
          for ch in word:
              if ch not in cur.children:
                  cur.children[ch] = TrieNode()
              cur = cur.children[ch]
          cur.endOfWord = True     
  
      def search(self, word: str) -> bool:
          # start from root --> check if the string exists and end flag is True
          cur = self.root
          for ch in word:
              if ch not in cur.children:
                  return False
              cur = cur.children[ch]
          return cur.endOfWord 
  
      def startsWith(self, prefix: str) -> bool:
          # start from root --> check character by character; no need to  check flag
          cur = self.root
          for ch in prefix:
              if ch not in cur.children:
                  return False
              cur = cur.children[ch]
          return True
  ```

  - Time complexity: Initialization: O(1); Other: O(N)

    Space complexity: O(N) --> children needs extra memory

  - <font color=red>**注意点：**</font>
    - `cur.children[ch] = TrieNode()`不是`cur.children['ch'] = TrieNode()`!
    - 当移动到下一步时，是`cur = cur.children[ch]`不是`cur = cur.children`，要移动到对应的下一个节点处

## 2. 211 [Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (43.93%) | [`backtracking`](https://leetcode.com/tag/backtracking); [`design`](https://leetcode.com/tag/design); [`trie`](https://leetcode.com/tag/trie) |

Design a data structure that supports adding new words and finding if a string matches any previously added string.

Implement the `WordDictionary` class:

- `WordDictionary()` Initializes the object.
- `void addWord(word)` Adds `word` to the data structure, it can be matched later.
- `bool search(word)` Returns `true` if there is any string in the data structure that matches `word` or `false` otherwise. `word` may contain dots `'.'` where dots can be matched with any letter.

**Example:**

```java
Input
["WordDictionary","addWord","addWord","addWord","search","search","search","search"]
[[],["bad"],["dad"],["mad"],["pad"],["bad"],[".ad"],["b.."]]
Output
[null,null,null,null,false,true,true,true]

Explanation
WordDictionary wordDictionary = new WordDictionary();
wordDictionary.addWord("bad");
wordDictionary.addWord("dad");
wordDictionary.addWord("mad");
wordDictionary.search("pad"); // return False
wordDictionary.search("bad"); // return True
wordDictionary.search(".ad"); // return True
wordDictionary.search("b.."); // return True
```

- **Constraints:**

  - `1 <= word.length <= 25`

  - `word` in `addWord` consists of lowercase English letters.

  - `word` in `search` consist of `'.'` or lowercase English letters.

  - There will be at most `3` dots in `word` for `search` queries.

  - At most `104` calls will be made to `addWord` and `search`.

- **Thoughts**

  - 这道题的考点就在于这个`search`函数，使用的数据结构为前缀树 --> efficient for searching `b..`
  - 首先 addWord 操作不会带 `.` 符号，因此我们采用原有的 Trie 插入方式即可；而在 search 操作中会有 `.` 符号，我们需要枚举某个 `.` 所代指的字母是什么，这需要结合 DFS 来做。--> when meet a `.` --> dfs to traverse for all paths

-  **Solution**

  <font color=red>看官方题解！！ https://leetcode.cn/problems/design-add-and-search-words-data-structure/solution/tian-jia-yu-sou-suo-dan-ci-shu-ju-jie-go-n4ud/</font>

  ```python
  class TrieNode:
      def __init__(self):
          self.children = {}
          self.endOfWord = False
  
  class WordDictionary:
  
      def __init__(self):
          self.root = TrieNode()       
  
      def addWord(self, word: str) -> None:
          cur = self.root
          for ch in word:
              if ch not in cur.children:
                  cur.children[ch] = TrieNode()
              cur = cur.children[ch]
          cur.endOfWord = True        
  
      def search(self, word: str) -> bool:
          
          def dfs(index, root):
              cur = root
              for i in range(index, len(word)):   # 注意range开始是从index开始，搜索后面的字符
                  ch = word[i]
                  if ch != '.':
                      if ch not in cur.children:
                          return False
                      cur = cur.children[ch]
                  else:
                      for child in cur.children.values():
                          if dfs(i + 1, child):
                              return True
                      return False
              return cur.endOfWord
          return dfs(0, self.root)
  ```

  - Time complexity: Initialization: O(1); addWord: O(N); search: <font color=red>**空缺！！**</font>

    Space complexity: <font color=red>**空缺！！**</font>

## 3. 212 [Word Search II](https://leetcode.com/problems/word-search-ii/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (37.83%) | [`backtracking`](https://leetcode.com/tag/backtracking); [`trie`](https://leetcode.com/tag/trie) |

Given an `m x n` `board` of characters and a list of strings `words`, return *all words on the board*.

Each word must be constructed from letters of sequentially adjacent cells, where **adjacent cells** are horizontally or vertically neighboring. The same letter cell may not be used more than once in a word. 

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2020/11/07/search1.jpg" alt="img" style="zoom:50%;" />

```
Input: board = [["o","a","a","n"],["e","t","a","e"],["i","h","k","r"],["i","f","l","v"]], words = ["oath","pea","eat","rain"]
Output: ["eat","oath"]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/11/07/search2.jpg)

```
Input: board = [["a","b"],["c","d"]], words = ["abcb"]
Output: []
```

- **Constraints:**

  - `m == board.length`

  - `n == board[i].length`

  - `1 <= m, n <= 12`

  - `board[i][j]` is a lowercase English letter.

  - `1 <= words.length <= 3 * 104`

  - `1 <= words[i].length <= 10`

  - `words[i]` consists of lowercase English letters.

  - All the strings of `words` are unique.

- **Thoughts**

  - 这道题目是 79. [Word Search](https://leetcode.com/problems/word-search/description/) 的变形题，主要思想近似，都是用回溯算法选择路径。

  - 题目与79不同的地方在于：该题目需要对`words`里的每一个word都要单独进行对`board`上所有字母的回溯操作，时间复杂度巨大 --> idea: check all the words ==simultaneously== --> use Trie/Prefix tree

    <img src="/Users/xinyuzhang/Downloads/IMG_C0A11815A6FD-1.jpeg" alt="IMG_C0A11815A6FD-1" style="zoom:50%;" />

  -  <u>Procedures:</u>

    - S1: add all words into the prefix tree
    - S2: backtrack through each cell
      	1. Base case --> conditions for returning False
      	2. Add path in visited 
      	3. Check if the current path composes a valid word --> if a valid word exists, prune
      	4. Backtrack in four directions
      	5. Remove the path
    - S3: for each cell in the board, conduct backtracking function and return the list result

    在具体实现中，我们需要注意如下情况：

    - 因为同一个单词可能在多个不同的路径中出现，所以我们需要使用哈希集合对结果集去重。

    - 在回溯的过程中，我们不需要每一步都判断完整的当前路径是否是 words 中任意一个单词的前缀；而是可以记录下路径中每个单元格所对应的前缀树结点，每次只需要判断新增单元格的字母是否是上一个单元格对应前缀树结点的子结点即可。

  - <font color=red>**关键点！！**</font>

    考虑以下情况：假设给定一个所有单元格都是 a 的二维字符网格和单词列表 ["a", "aa", "aaa", "aaaa"] 。当我们使用上述来找出所有同时在二维网格和单词列表中出现的单词时，我们需要遍历每一个单元格的所有路径，会找到大量重复的单词。

    为了缓解这种情况，我们可以==将匹配到的单词从前缀树中移除，来避免重复寻找相同的单词==。因为这种方法可以保证每个单词只能被匹配一次；所以我们也不需要再对结果集去重了。


- **Solution**

  ```python
  class TrieNode:   # Three functions: initialization; add words; prune words
    def __init__(self):
      self.children = {}
      self.endOfWord = False
    
    def addWords(self, word):
      cur = self
      for ch in word:
        if ch not in cur.children:
          cur.chidren[ch] = TrieNode()
        cur = cur.children[ch]
      cur.endOfWord = True
    
      # Inverse of `addWord` function: prune/remove a word from the prefix tree
      # remove all characters of the input word from the prefix tree that are not used to store other words
      def pruneWord(self, word):
        cur = self
        stack = []
        
        # build a stack (similar to visited) to keep track of the visited nodes in the valid word
        for ch in word:
          stack.append((cur, ch))             # append each node and the according character
          cur = cur.children[ch]
  
          # delete the non-used characters
          for node, ch in reversed(stack):    # reversed: access the given sequence in the reverse order --> start from the last node
            target = node.children[ch]      	# the target node to delete
            if len(target.children) == 0:     # deleted node has no children --> not store other words
              del node.children[ch]						# delete the 'key-value' pair in node.children
            else:
              return
  
  class Solution:
      def findWords(self, board: List[List[str]], words: List[str]) -> List[str]:
        row, col = len(board), len(board[0])
        res, visited = [], set()
        
        root = TrieNode()
        for word in words:
          root.addWords(word)
        
        def backtrack(r, c, node, word):
          if (r < 0 or c < 0 or
              r >= row or c >= col or
              board[r][c] not in node.children or
              (r, c) in visited):
            return
          
          visited.add((r, c))
          ch = board[r][c]
          node = node.children[ch]				# move to the next node in Trie
          word += ch											# '+': concatenate two strings
          if node.endOfWord:
            res.append(word)
            node.endOfWord = False
            root.pruneWords(word)					# delete the word from the prefix tree
          
          visited.remove((r, c))
        
        for r in range(row):
          for c in range(col):
            backtrack(r, c, root, '')
        return res
  ```

  - Time complexity: $O(M \times N \times 4^{len(word)})$ --> `len(word)` is the length of the longest word in words

    Space complexity: $O(len(words) \times len(word))$ --> store the prefix tree --> intotal $len(words) \times len(word)$ nodes

  