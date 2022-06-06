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