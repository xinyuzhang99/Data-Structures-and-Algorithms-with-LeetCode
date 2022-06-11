# Set 集合

- 无序 + 不重复

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220520172225314.png" alt="image-20220520172225314" style="zoom:50%;" />

- 主要作用：
  - 检查某一个元素是否存在
  - 检查重复元素 --> **数据集元素是否和插入数据的集合元素相等**

- **Hash set**：在LeetCode中主要使用

  <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220520172505666.png" alt="image-20220520172505666" style="zoom:50%;" />

  遇见哈希冲突时经常使用链表方法

- <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220520172754309.png" alt="image-20220520172754309" style="zoom:50%;" />

- **Common Operations**

  ```python
  # Create set
  s = set()
  
  # Add Element
  # Time Complexity: O(1)
  s.add(10)
  
  # Search Element
  # Time Complexity: O(1)
  2 in s
  
  # Delete Element
  # Time Complexity: O(1)
  s.remove(2)
  
  # Size
  # Time Complexity: O(1)
  len(s)
  ```

## 1. 217 [Contains Duplicate](https://leetcode.com/problems/contains-duplicate/description/)

|  Category  |  Difficulty   |          Tag           |
| :--------: | :-----------: | :--------------------: |
| algorithms | Easy (60.84%) | Array; Hash-table; Set |

Given an integer array `nums`, return `true` if any value appears **at least twice** in the array, and return `false` if every element is distinct.

**Example 1:**

```python
Input: nums = [1,2,3,1]
Output: true
```

**Example 2:**

```python
Input: nums = [1,2,3,4]
Output: false
```

**Example 3:**

```python
Input: nums = [1,1,1,3,3,4,3,2,4,2]
Output: true
```

- **Solution**

  - <u>Method 1: Hash table</u>

    ```python
    def containsDuplicate(self, nums: List[int]) -> bool:
            # Method 1: Hash table
            hashtable = dict()
            for i in nums:
                if i in hashtable:
                    return True
                else:
                    hashtable[i] = 1
            return False  
    ```

    Time Complexity: O(N)

    Space Complexity: O(N)

  - <u>Method 2: Set</u>

    ```python
    def containsDuplicate(self, nums: List[int]) -> bool:  
            ## Method 2: Set
            hashset = set()
            for num in nums:
                hashset.add(num)
            return True if len(hashset) != len(nums) else False
    ```

    Time Complexity: O(N)

    Space Complexity: O(N)

## 2. 705 [Design HashSet](https://leetcode.com/problems/design-hashset/description/)

|  Category  |  Difficulty   |                            Tag                            |
| :--------: | :-----------: | :-------------------------------------------------------: |
| algorithms | Easy (66.45%) | Array; Hash Table; Linked List; ==Design==; Hash Function |

Design a HashSet without using any built-in hash table libraries.

Implement `MyHashSet` class:

- `void add(key)` Inserts the value `key` into the HashSet.
- `bool contains(key)` Returns whether the value `key` exists in the HashSet or not.
- `void remove(key)` Removes the value `key` in the HashSet. If `key` does not exist in the HashSet, do nothing.

**Example 1:**

```java
Input
["MyHashSet", "add", "add", "contains", "contains", "add", "contains", "remove", "contains"]
[[], [1], [2], [1], [3], [2], [2], [2], [2]]
Output
[null, null, null, true, false, null, true, null, false]

Explanation
MyHashSet myHashSet = new MyHashSet();
myHashSet.add(1);      // set = [1]
myHashSet.add(2);      // set = [1, 2]
myHashSet.contains(1); // return True
myHashSet.contains(3); // return False, (not found)
myHashSet.add(2);      // set = [1, 2]
myHashSet.contains(2); // return True
myHashSet.remove(2);   // set = [1]
myHashSet.contains(2); // return False, (already removed)
```

**Constraints:**

- `0 <= key <= 10^6`
- At most `104` calls will be made to `add`, `remove`, and `contains`.

- **Thoughts**

  根据constraints，已知这个哈希集合一共有$0-10^6$个元素，可以构建长度为1000001的布尔数组（初始值均为False)

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220520182721439.png" alt="image-20220520182721439" style="zoom:50%;" />

- **Solution**

  ```python
  class MyHashSet:
  
      def __init__(self):
          self.hashset = [False] * 1000001
  
      def add(self, key: int) -> None:
          self.hashset[key] = True
          
      def remove(self, key: int) -> None:
          self.hashset[key] = False
  
      def contains(self, key: int) -> bool:
          return self.hashset[key]
  ```

  Time Complexity: O(1)

  Space Complexity: O(1) ==Space Complexity 不考虑构造函数！==

  - Disadvantage:
    - Need to construct a very large array --> take up too much memory
    - The question needs to set the limit 

## 3. 36 [Valid Sudoku](https://leetcode.com/problems/valid-sudoku/description/)

|  Category  |   Difficulty    |                        Tags                         |
| :--------: | :-------------: | :-------------------------------------------------: |
| algorithms | Medium (55.28%) | [`hash-table`](https://leetcode.com/tag/hash-table) |

Determine if a `9 x 9` Sudoku board is valid. Only the filled cells need to be validated **according to the following rules**:

1. Each row must contain the digits `1-9` without repetition.
2. Each column must contain the digits `1-9` without repetition.
3. Each of the nine `3 x 3` sub-boxes of the grid must contain the digits `1-9` without repetition.

**Note:**

- A Sudoku board (partially filled) could be valid but is not necessarily solvable.
- Only the filled cells need to be validated according to the mentioned rules. 

**Example 1:**

![img](https://upload.wikimedia.org/wikipedia/commons/thumb/f/ff/Sudoku-by-L2G-20050714.svg/250px-Sudoku-by-L2G-20050714.svg.png)

```
Input: board = 
[["5","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: true
```

**Example 2:**

```
Input: board = 
[["8","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: false
Explanation: Same as Example 1, except with the 5 in the top left corner being modified to 8. Since there are two 8's in the top left 3x3 sub-box, it is invalid.
```

- **Constraints:**

  - `board.length == 9`

  - `board[i].length == 9`

  - `board[i][j]` is a digit `1-9` or `'.'`.

- **Thoughts**

  有效的数独满足以下三个条件：同一个数字在每一行只能出现一次；同一个数字在每一列只能出现一次；同一个数字在每一个小九宫格只能出现一次。--> <font color=red>**Without repetition! --> 考虑使用set --> create three sets according to row, column, square**</font> 

- **Solution**

  ```python
  from collections import defaultdict
  def isValidSudoku(self, board: List[List[str]]) -> bool:
    row = defaultdict(set)
    col = defaultdict(set)
    square = defaultdict(set)
    
    for r in range(9):
      for c in range(9):
        if board[r][c] == '.':
          continue
        if (board[r][c] in row[r] or
            board[r][c] in col[c] or
            board[r][c] in square[(r//3, c//3)]):
          return False
        row[r].add(board[r][c])
        col[c].add(board[r][c])
        square[(r//3, c//3)].add(board[r][c])
  ```

  - Time complexity: $O(r*c) = O(81) = O(1)$

    Space complexity: $O(81) = O(1)$

## 4. 128 [Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (48.75%) | [`array`](https://leetcode.com/tag/array); [`union-find`](https://leetcode.com/tag/union-find) |

Given an unsorted array of integers `nums`, return *the length of the longest consecutive elements sequence.*

You must write an algorithm that runs in `O(n)` time.

**Example 1:**

```
Input: nums = [100,4,200,1,3,2]
Output: 4
Explanation: The longest consecutive elements sequence is [1, 2, 3, 4]. Therefore its length is 4.
```

**Example 2:**

```
Input: nums = [0,3,7,2,5,8,4,6,0,1]
Output: 9
```

- **Constraints:**

  - `0 <= nums.length <= 105`

  - `-109 <= nums[i] <= 109`

- **Thoughts**

  - 首先想到的算法为先将数组排序，然而排序所需的时间复杂度为$O(nlogn)$，不满足题目要求的$O(N)$ 
  - 由于同一个数对结果的影响一样，可以使用`set`来进行去重
  - --> 考虑枚举数组中的每个数 x，考虑以其为起点，不断尝试匹配 x+1, x+2, x+1,x+2,⋯ 是否存在; 如果这个数的前一个值 (x-1) 已经存在在set里，则这个数不是起点，跳过

- **Solution**

  - S1: create a set and record nums
  - S2: iterate through each element to see if it's a start of a consecutive sequence
  - S3: if find a start, continue to find the length

  ```python
  def longestConsecutive(self, nums: List[int]) -> int:
    numSet = set(nums)
    longest = 0
    for n in numSet:
      if n - 1 not in numSet:
        length = 1
        while (n + length in numSet):
          length += 1
        longest = max(longest, length)
    return longest
  ```

  - Time complexity: $O(N)$

    Space complexity: $O(N)$

  