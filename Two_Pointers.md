# Two Pointers 双指针

--> 用两个指针解决一道题

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220523151009670.png" alt="image-20220523151009670" style="zoom:50%;" />

对撞双指针 --> 在使用之前一定要先将数组sort! 

- **链表+双指针技巧在<u>Linked List 链表</u>笔记里📒**
- **快慢指针的一大类题目为滑动窗口算法，详见<u>Sliding Window 滑动窗口</u>笔记里📒**
- 该笔记包含**NSum**题型: 6, 

## **1. 26 Remove Duplicates from Sorted Array**

Given an integer array `nums` sorted in **non-decreasing order**, remove the duplicates [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm) such that each unique element appears only **once**. The **relative order** of the elements should be kept the **same**.

Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the **first part** of the array `nums`. More formally, if there are `k` elements after removing the duplicates, then the first `k` elements of `nums` should hold the final result. It does not matter what you leave beyond the first `k` elements.

Return `k` *after placing the final result in the first* `k` *slots of* `nums`.

Do **not** allocate extra space for another array. You must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

一个指针 `i` 进行数组遍历 (fast)，另外一个指针` j` 指向有效数组的最后一个位置 (slow)。

只有当 `i` 所指向的值和 `j` 不一致（不重复），才将 `i` 的值添加到 `j` 的下一位置。这样，就保证了 `nums[0..slow]` 都是无重复的元素，当 `fast` 指针遍历完整个数组 `nums` 后，`nums[0..slow]` 就是整个数组去重之后的结果。

<img src="https://labuladong.github.io/algo/images/%e6%95%b0%e7%bb%84%e5%8e%bb%e9%87%8d/1.gif" alt="img" style="zoom:33%;" />

```python
class Solution:
    def removeDuplicates(self, nums: List[int]) -> int:
        n = len(nums)
        j = 0
        for i in range(n):
            if nums[i] != nums[j]:
                j += 1
                nums[j] = nums[i]
        return j + 1  # the length of the array is index + 1
```

- 时间复杂度：O(n)
- 空间复杂度：O(1)

## 通用解法

--> **保留k位**

- 由于是保留 k 个相同数字，对于前 k 个数字，我们可以直接保留
- 对于后面的任意数字，能够保留的前提是：<u>与当前写入的位置前面的第 k 个元素进行比较</u>，不相同则保留

```python
class Solution:
     def removeDuplicates(self, nums: List[int]) -> int:
        n = len(nums)
        slow = 0

        for fast in range(n):
            if slow < k or nums[fast] != nums[slow - k]: ## !! Important!
                nums[slow] = nums[fast]
                slow += 1
        return slow
# Time Complexity: O(n)
# Space Complexity: O(1) --> no additional memory needed
```

<img src="/Users/xinyuzhang/Desktop/CS/Leetcode/LeetCode Notes/Two Pointers.gif" alt="Two Pointers" style="zoom:67%;" />

## 2. 881 [Boats to Save People](https://leetcode.com/problems/boats-to-save-people/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (52.48%) | [`depth-first-search`](https://leetcode.com/tag/depth-first-search) |

You are given an array `people` where `people[i]` is the weight of the `ith` person, and an **infinite number of boats** where each boat can carry a maximum weight of `limit`. Each boat carries at most two people at the same time, provided the sum of the weight of those people is at most `limit`.

Return *the minimum number of boats to carry every given person*. 

**Example 1:**

```
Input: people = [1,2], limit = 3
Output: 1
Explanation: 1 boat (1, 2)
```

**Example 2:**

```
Input: people = [3,2,2,1], limit = 3
Output: 3
Explanation: 3 boats (1, 2), (2) and (3)
```

**Example 3:**

```
Input: people = [3,5,3,4], limit = 5
Output: 4
Explanation: 4 boats (3), (3), (4), (5)
```

**Constraints:**

- `1 <= people.length <= 5 * 104`
- `1 <= people[i] <= limit <= 3 * 104`

- **Thoughts**

  这道题可使用**<font color=blue>对撞双指针</font>**解。

  - S1: 先将原数组按从小到大(ascending order)进行排序
  - S2: 设立两个指针，头指针`i`和尾指针`j`。如果`people[i] + people[j] <= limit`，则`i`和`j`坐一艘船走 (`i += 1; j -= 1`)，否则只有`j`走 (`j -= 1`)。
  - S3: 直到`i`和`j`相遇，则添加一艘船并停止步骤

- **Solution**

  ```python
  class Solution:
      def numRescueBoats(self, people: List[int], limit: int) -> int:
          res = 0
          i = 0
          j = len(people) - 1
          people.sort()
  
          if len(people) == 0 or people == None:
              return 0
          
          # Simplified Version
          while(i <= j):
              if people[i] + people[j] <= limit:
                  i += 1
              res += 1
              j -= 1
          return res 
        
        ## Not-simplified Version
        # while (i <= j):
          #     if i == j:
          #         res += 1          
          #         break
          #     if people[i] + people[j] > limit:
          #         res += 1
          #         j -= 1
          #     elif people[i] + people[j] <= limit:
          #         res += 1
          #         i += 1       # the only different process
          #         j -= 1
  ```

  <font color=red>当有多个条件时注意代码是否能简化！如果在每个条件中都有一样会执行的步骤，把该步骤提出循环单独放在外面！（见上simplified / non-simplified version）</font>

## 3. 27 [Remove Element](https://leetcode.com/problems/remove-element/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (51.26%) | [`array`](https://leetcode.com/tag/array); [`two-pointers`](https://leetcode.com/tag/two-pointers) |

Given an integer array `nums` and an integer `val`, remove all occurrences of `val` in `nums` [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm). The relative order of the elements may be changed.

Since it is impossible to change the length of the array in some languages, you must instead have the result be placed in the **first part** of the array `nums`. More formally, if there are `k` elements after removing the duplicates, then the first `k` elements of `nums` should hold the final result. It does not matter what you leave beyond the first `k` elements.

Return `k` *after placing the final result in the first* `k` *slots of* `nums`.

Do **not** allocate extra space for another array. You must do this by **modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** with O(1) extra memory.

**Custom Judge:**

The judge will test your solution with the following code:

```java
int[] nums = [...]; // Input array
int val = ...; // Value to remove
int[] expectedNums = [...]; // The expected answer with correct length.
                            // It is sorted with no values equaling val.

int k = removeElement(nums, val); // Calls your implementation

assert k == expectedNums.length;
sort(nums, 0, k); // Sort the first k elements of nums
for (int i = 0; i < actualLength; i++) {
    assert nums[i] == expectedNums[i];
}
```

If all assertions pass, then your solution will be **accepted**.

**Example 1:**

```
Input: nums = [3,2,2,3], val = 3
Output: 2, nums = [2,2,_,_]
Explanation: Your function should return k = 2, with the first two elements of nums being 2.
It does not matter what you leave beyond the returned k (hence they are underscores).
```

**Example 2:**

```
Input: nums = [0,1,2,2,3,0,4,2], val = 2
Output: 5, nums = [0,1,4,0,3,_,_,_]
Explanation: Your function should return k = 5, with the first five elements of nums containing 0, 0, 1, 3, and 4.
Note that the five elements can be returned in any order.
It does not matter what you leave beyond the returned k (hence they are underscores).
```

- **Constraints:**

  - `0 <= nums.length <= 100`

  - `0 <= nums[i] <= 50`

  - `0 <= val <= 100`

- **Solution**

  该题使用**快慢指针**，如果 `fast` 遇到值为 `val` 的元素，则直接跳过，否则就赋值给 `slow` 指针，并让 `slow` 前进一步。

  <img src="/Users/xinyuzhang/Downloads/IMG_9694B5B5B2FF-1.jpeg" alt="IMG_9694B5B5B2FF-1" style="zoom:25%;" />

  ```python
  from typing import List
  class Solution:
      def removeElement(self, nums: List[int], val: int) -> int:
          n = len(nums)
          slow = 0
  
          if n == 0 or nums is None:
              return 0
          for fast in range(n):
              if nums[fast] != val:
                  nums[slow] = nums[fast]
                  slow += 1
          return slow
  ```

## 4. 283 [Move Zeroes](https://leetcode.com/problems/move-zeroes/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (60.69%) | [`array`](https://leetcode.com/tag/array); [`two-pointers`](https://leetcode.com/tag/two-pointers) |

Given an integer array `nums`, move all `0`'s to the end of it while maintaining the relative order of the non-zero elements.

**Note** that you must do this in-place without making a copy of the array.

**Example 1:**

```
Input: nums = [0,1,0,3,12]
Output: [1,3,12,0,0]
```

**Example 2:**

```
Input: nums = [0]
Output: [0] 
```

- **Constraints:**

  - `1 <= nums.length <= 104`

  - `-231 <= nums[i] <= 231 - 1`

- **Solution**

  思路和27一模一样，除了将val变成0，然后把末尾所有值都赋为0

  ```python
  from typing import List
  class Solution:
      def moveZeroes(self, nums: List[int]) -> None:
          """
          Do not return anything, modify nums in-place instead.
          """
          n = len(nums)
          fast = 0
          slow = 0
          for fast in range(n):
              if nums[fast] != 0:
                  nums[slow] = nums[fast]
                  slow += 1
          for i in range(slow, n):
              nums[i] = 0
  ```

## 5. 344 [Reverse String](https://leetcode.com/problems/reverse-string/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (74.93%) | [`two-pointers`](https://leetcode.com/tag/two-pointers); [`string`](https://leetcode.com/tag/string) |

Write a function that reverses a string. The input string is given as an array of characters `s`.

You must do this by modifying the input array [in-place](https://en.wikipedia.org/wiki/In-place_algorithm) with `O(1)` extra memory. 

**Example 1:**

```
Input: s = ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]
```

**Example 2:**

```
Input: s = ["H","a","n","n","a","h"]
Output: ["h","a","n","n","a","H"]
```

- **Constraints:**

  - `1 <= s.length <= 105`

  - `s[i]` is a [printable ascii character](https://en.wikipedia.org/wiki/ASCII#Printable_characters).

- **Solution**

  此题要求反转字符串，其实是第一个字符和最后一个反转，第二个字符和倒数第二个字符反转…… -->**对撞双指针**

  ```python
  def reverseString(self, s: List[str]) -> None:
          """
          Do not return anything, modify s in-place instead.
          """
          i = 0
          j = len(s) - 1
  
          while i < j:
              temp = s[i]
              s[i] = s[j]
              s[j] = temp
              i += 1
              j -= 1
  ```

  Time Complexity: O(N)

  Space Complexity: O(1)

## 6. 167 [Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (58.88%) | [`array`](https://leetcode.com/tag/array);  [`two-pointers`](https://leetcode.com/tag/two-pointers); [`binary-search`](https://leetcode.com/tag/binary-search) |

Given a **1-indexed** array of integers `numbers` that is already ***sorted in non-decreasing order\***, find two numbers such that they add up to a specific `target` number. Let these two numbers be `numbers[index1]` and `numbers[index2]` where `1 <= index1 < index2 <= numbers.length`.

Return *the indices of the two numbers,* `index1` *and* `index2`*, **added by one** as an integer array* `[index1, index2]` *of length 2.*

The tests are generated such that there is **exactly one solution**. You **may not** use the same element twice.

Your solution must use only constant extra space.

**Example 1:**

```
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore, index1 = 1, index2 = 2. We return [1, 2].
```

**Example 2:**

```
Input: numbers = [2,3,4], target = 6
Output: [1,3]
Explanation: The sum of 2 and 4 is 6. Therefore index1 = 1, index2 = 3. We return [1, 3].
```

**Example 3:**

```
Input: numbers = [-1,0], target = -1
Output: [1,2]
Explanation: The sum of -1 and 0 is -1. Therefore index1 = 1, index2 = 2. We return [1, 2].
```

- **Constraints:**

  - `2 <= numbers.length <= 3 * 104`

  - `-1000 <= numbers[i] <= 1000`

  - `numbers` is sorted in **non-decreasing order**.

  - `-1000 <= target <= 1000`

  - The tests are generated such that there is **exactly one solution**.

- **Solution**

  - <u>Method 1: Hashtable</u>

    用哈希表解决，则该题和LC 1-Two Sum是同一解法，将每个值存入哈希表，并每次搜索`target - nums[i]`是否存在于哈希表中

    ```python
    class Solution:
        def twoSum(self, numbers: List[int], target: int) -> List[int]:
            n = len(numbers)
            hashtable = dict()
            output = []
    
            for i in range(n):
                if target - numbers[i] in hashtable:
                    output = [hashtable[target - numbers[i]] + 1, i + 1]
                    return output
                else:
                    hashtable[numbers[i]] = i
    ```

    Time Complexity: O(N) --> traverse through the whole list

    Space Complexity: O(N) --> create a hash table

  - <u>Method 2: Two-pointers</u>

    由于该数组为有序 --> ==**二分搜索 + 对撞指针 **== --> 更改二分搜索模板即可

    ```python
    class Solution:
        def twoSum(self, numbers: List[int], target: int) -> List[int]:
            l = 0
            r = len(numbers) - 1
            while (l < r):
                if numbers[l] + numbers[r] == target:
                    return [l+1, r+1]
                elif numbers[l] + numbers[r] > target:
                    r -= 1
                elif numbers[l] + numbers[r] < target:
                    l += 1
    ```

    Time Complexity: O(N) --> the two pointers traverse through the list

    Space Complexity: O(1)

## 7. ※※ 5 [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (31.83%) | [`string`](https://leetcode.com/tag/string); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

Given a string `s`, return *the longest palindromic substring* in `s`. 

**Example 1:**

```
Input: s = "babad"
Output: "bab"
Explanation: "aba" is also a valid answer.
```

**Example 2:**

```
Input: s = "cbbd"
Output: "bb"
```

- **Constraints:**

  - `1 <= s.length <= 1000`

  - `s` consist of only digits and English letters.

- **Thoughts**

  - 回文串的特点在于正着读和反着读都一样，左右对称。若需要判断一个字符串是不是回文串，使用对撞双指针技巧:

    ```python
    def isPalindromic(self, s):
      left = 0
      right = len(s) - 1
      
      while (left < right):
        if s[left] != s[right]:
          return False
        left += 1
        right -= 1
      return True
    ```

  - 找回文串的难点在于，回文串的的长度可能是奇数也可能是偶数，解决该问题的核心是==**从中心向两端扩散的双指针技巧**==。

    如果回文串的长度为奇数，则它有一个中心字符；如果回文串的长度为偶数，则可以认为它有两个中心字符。

-  **Solution**

  - <u>Method 1: Two-Pointers</u>

    ```python
     def longestPalindrome(self, s: str) -> str:
            # two-pointers --> consider each number is the center
            # if the string satisfies palindromic conditions 
            res = s[0]
            resLen = 0  # as the length is considered
    
            for i in range(len(s)):
                # odd length
                l, r = i, i
                while (l >= 0 and r < len(s) and s[l] == s[r]): # find a palindromic substring
                    if (r - l + 1) > resLen:
                        res = s[l:r+1]
                        resLen = r - l + 1
                    l -= 1
                    r += 1
                
                # even length
                l, r = i, i+1
                while (l >= 0 and r < len(s) and s[l] == s[r]):
                    if (r - l + 1) > resLen:
                        res = s[l:r+1]
                        resLen = r - l + 1
                    l -= 1
                    r += 1
            return res
    ```

    Time complexity: $O(N^2)$ --> i traverse through the whole string + l and r traverse through the whole string

    Space complexity: O(1)

  - <u>Method 2: Dynamic Programming</u>

    <font color=red>**空缺！！**</font>

## 8. 15 [3Sum](https://leetcode.com/problems/3sum/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (31.23%) | [`array`](https://leetcode.com/tag/array); [`two-pointers`](https://leetcode.com/tag/two-pointers) |

Given an integer array nums, return all the triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.

Notice that the solution set must not contain duplicate triplets.

**Example 1:**

```
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
```

**Example 2:**

```
Input: nums = []
Output: []
```

**Example 3:**

```
Input: nums = [0]
Output: [] 
```

- **Constraints:**

  - `0 <= nums.length <= 3000`

  - `-105 <= nums[i] <= 105`

- **Thoughts**

  - 该题目为167的变形题，同样是套用模板排序+对撞双指针，要注意去除掉重复元素
  - 题目要求`a + b + c == 0` --> `a`用`i`表达，`b, c`用`l, r`对撞双指针表达

- **Solution**

  ```python
  def threeSum(self, nums: List[int]) -> List[List[int]]:
          res = []
          nums.sort()
  
          for i, value in enumerate(nums):
              # the first value
              if (i > 0 and value == nums[i - 1]):
                  continue
              
              l, r = i + 1, len(nums) - 1            
              while (l < r):
                  threeSum = value + nums[l] + nums[r]
                  if threeSum > 0:
                      r -= 1
                  elif threeSum < 0:
                      l += 1
                  elif threeSum == 0:
                      res.append([value, nums[l], nums[r]])
                      l += 1
                      while (l < r and nums[l] == nums[l - 1]):
                          l += 1
          return res
  ```

  Time complexity: $O(NlogN + N^2) = O(N^2)$ --> 排序的复杂度加循环的复杂度

  Space complexity: $O(N)$ --> 排序的复杂度

## 9. [4Sum](https://leetcode.com/problems/4sum/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (37.38%) | [`array`](https://leetcode.com/tag/array); [`hash-table`](https://leetcode.com/tag/hash-table); [`two-pointers`](https://leetcode.com/tag/two-pointers) |

Given an array `nums` of `n` integers, return *an array of all the **unique** quadruplets* `[nums[a], nums[b], nums[c], nums[d]]` such that:

- `0 <= a, b, c, d < n`
- `a`, `b`, `c`, and `d` are **distinct**.
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

You may return the answer in **any order**.

**Example 1:**

```
Input: nums = [1,0,-1,0,-2,2], target = 0
Output: [[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**Example 2:**

```
Input: nums = [2,2,2,2,2], target = 8
Output: [[2,2,2,2]]
```

- **Constraints:**

  - `1 <= nums.length <= 200`

  - `-109 <= nums[i] <= 109`

  - `-109 <= target <= 109`

- **Thoughts**

  该题目和3Sum一样，是2Sum的延伸，先遍历两个元素(first + second)再套用模板进行双指针遍历

- **Solution**

  - <u>Method 1: Iteration</u>

    ```python
    class Solution:
        def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
            res = []
            nums.sort()
    
            for i, first in enumerate(nums):
                if (i > 0 and first == nums[i - 1]):
                    continue
                for j in range(i+1, len(nums)):
                    second = nums[j]
                    if (j > i + 1 and second == nums[j - 1]):
                        continue
    
                    l, r = j + 1, len(nums) - 1
                    while (l < r):  
                        fourSum = first + second + nums[l] + nums[r]
                        if fourSum > target:
                            r -= 1
                        elif fourSum < target:
                            l += 1
                        elif fourSum == target:
                            res.append([first, second, nums[l], nums[r]])
                            l += 1
                            while (l < r and nums[l] == nums[l - 1]):
                                l += 1
            return res
    ```

  - <u>Method 2: Recursion</u>

    <font color=red>**空缺！！**</font>


## 10. 125 [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (41.92%) | [`two-pointers`](https://leetcode.com/tag/two-pointers); [`string`](https://leetcode.com/tag/string) |

A phrase is a **palindrome** if, after converting all uppercase letters into lowercase letters and removing all non-alphanumeric characters, it reads the same forward and backward. Alphanumeric characters include letters and numbers.

Given a string `s`, return `true` *if it is a **palindrome**, or* `false` *otherwise*.

**Example 1:**

```
Input: s = "A man, a plan, a canal: Panama"
Output: true
Explanation: "amanaplanacanalpanama" is a palindrome.
```

**Example 2:**

```
Input: s = "race a car"
Output: false
Explanation: "raceacar" is not a palindrome.
```

**Example 3:**

```
Input: s = " "
Output: true
Explanation: s is an empty string "" after removing non-alphanumeric characters.
Since an empty string reads the same forward and backward, it is a palindrome. 
```

- **Constraints:**

  - `1 <= s.length <= 2 * 105`

  - `s` consists only of printable ASCII characters.

- **Thought**

  - 这道题的难点在于如何构造lower-case Alphanumeric字符串

    - Method 1: 使用Python自带函数

      ```python
      newStr = ""
      for ch in s:
        if ch.isalnum():
          newStr += ch.lower()
      ```

    - Method 2: 使用ASCII码筛选出字母和数字

      ```python
      def alphaNum(self, ch):
        return (ord('A') <= ord(c) <= ord('Z') or
                ord('a') <= ord(c) <= ord('z') or
                ord('0') <= ord(c) <= ord('9'))
      ```

- **Solution**

  ```python
  def isPalindrome(self, s: str) -> bool: 
    # Method 1: API
    newStr = ""
    for ch in s:
      if ch.isalnum():
        newStr += ch.lower()
    return newStr == newStr[::-1]   # newStr[::-1]: reversed version of newStr
  	# Time complexity: O(N)
    # Space complexity: O(N) --> build a new string
  
  	# Method 2: Two-Pointers
    i = 0
    j = len(s) - 1
    while (i < j):
      while (i < j and not self.alphaNum(s[i])):   # while (i < j and not s[i].isalnum()):  
        i += 1
      while (i < j and not self.alphaNum(s[j])):	 # while (i < j and not s[j].isalnum()): 
        j -= 1
      if s[i] != s[j]:
        return False
      else:
        i += 1
        j -= 1
    return True
    # Time complexity: O(N)
    # Space complexity: O(1)
  ```

## 11. 42 [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (56.75%) | [`array`](https://leetcode.com/tag/array); [`two-pointers`](https://leetcode.com/tag/two-pointers); [`stack`](https://leetcode.com/tag/stack) |

Given `n` non-negative integers representing an elevation map where the width of each bar is `1`, compute how much water it can trap after raining.

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)

```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.
```

**Example 2:**

```
Input: height = [4,2,0,3,2,5]
Output: 9
```

- **Constraints:**

  - `n == height.length`

  - `1 <= n <= 2 * 104`

  - `0 <= height[i] <= 105`

- **Solution**

  - <u>Method 1: Brute-force</u>

    ---> 直接按问题描述进行。对于数组中的每个元素，我们找出下雨后水能达到的最高位置，等于两边最大高度的较小值减去当前高度的值 --> calculate at each point on x-axis, how much water can be trapped `min(l_max, r_max) - height[i]`

    ```python
    def trap(height: List[int]) -> int:
      if not height:
        return 0
      res = 0
      n = len(height)
      
      for i in range(n):
        l_max = 0
        r_max = 0
        # !! Important 在计算左墙和右墙时都要把自己本身i算入，以免出现res<0的情况
        # find the max wall in the left: max(height[0...i])
        for j in range(i + 1):
          l_max = max(l_max, height[j])
        # find the max wall in the right: max(height[i...end])
        for j in range(i, n):
          r_max = max(r_max, height[j])
        # 如果自己就是最高的话，l_max == r_max == height[i]
        res += min(l_max, r_max) - height[i]
      return res
    ```

    - Time complexity: $O(N^2)$

      Space complexity: $O(1)$

  - <u>Method 2: 备忘录优化 --> 提前将`l_max`和`r_max`存储为数组</u>

    --> **我们开两个数组 `r_max` 和 `l_max` 充当备忘录，`l_max[i]` 表示位置 `i` 左边最高的柱子高度，`r_max[i]` 表示位置 `i` 右边最高的柱子高度**

    ```python
    def trap(height: List[int]) -> int:
      if not height:
        return 0
      n = len(height)
      l_max = [0] * n
      r_max = [0] * n
      
      # 初始化 base case
      l_max[0] = height[0]
      r_max[n - 1] = height[n - 1]
      
      # 从左向右计算 l_max
      for i in range(1, n):
        l_max[i] = max(l_max[i - 1], height[i])
      # 从右向左计算 r_max
      for j in range(n - 2, -1, -1):
        r_max[j] = max(r_max[i + 1], height[j])
        
      res = 0
      for i in range(n):
        res += min(l_max[i], r_max[i]) - height[i]
      return res
    ```

    - Time complexity: $O(3N) = O(N)$

      Space complexity: $O(2N) = O(N)$

  - <u>==Method 3: Two Pointers==</u>

    --> 不使用备忘录提前计算了，而是用双指针**边走边算**，节省下空间复杂度

    ```python
    def trap(height: List[int]) -> int:
      if not height:
        return 0
      n = len(height)
      l, r = 0, n - 1
      l_max, r_max = height[l], height[r]
      
      res = 0
      while (l < r): 
        if l_max <= r_max:
          l += 1
          l_max = max(l_max, height[l])
          res += l_max - height[i]
        else:
          r -= 1
          r_max = max(r_max, height[r])
          res += r_max - height[i]
      return res
    ```
    
    - Time complexity: $O(N)$
    
      Space complexity: $O(1)$

## 12. 11 [Container With Most Water](https://leetcode.com/problems/container-with-most-water/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (53.98%) | [`array`](https://leetcode.com/tag/array); [`two-pointers`](https://leetcode.com/tag/two-pointers) |

You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `ith` line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return *the maximum amount of water a container can store*.

**Notice** that you may not slant the container. 

**Example 1:**

<img src="https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg" alt="img" style="zoom:50%;" />

```
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explanation: The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
```

**Example 2:**

```
Input: height = [1,1]
Output: 1
```

- **Constraints:**

  - `n == height.length`

  - `2 <= n <= 105`

  - `0 <= height[i] <= 104`

- **Thoughts**

  - 这题和42.接雨水问题很类似，可以完全套用思路，而且还更简单。两道题的区别在于：**接雨水问题给出的类似一幅直方图，每个横坐标都有宽度，而本题给出的每个横坐标是一条竖线，没有宽度**。

  - 因为本题中竖线没有宽度，所以 `left` 和 `right` 之间能够盛的水就是：

    ```python
    min(height[left], height[right]) * (right - left)
    ```

- **Solution**

  ```python
  def maxArea(self, height: List[int]) -> int:
    if not height:
      return 0
    
    n = len(height)
    l, r = 0, n - 1
    res = 0
    while l < r:
      area = min(height[l], height[r]) * (r - l)
      res = max(res, area)
      # 双指针技巧，移动较低的一边
      if height[l] <= height[r]:
        l += 1
      else:
        r -= 1
    return res
  ```
  
  - Time complexity: $O(N)$
  
    Space complexity: $O(1)$
  
  - <font color=red>**注意点：**</font>
  
    移动较低的一边：**因为矩形的高度是由 `min(height[left], height[right])` 即较低的一边决定的**：
  
    你如果移动较低的那一边，那条边可能会变高，使得矩形的高度变大，进而就「有可能」使得矩形的面积变大；相反，如果你去移动较高的那一边，矩形的高度是无论如何都不会变大的，所以不可能使矩形的面积变得更大。

## 13. 121 [Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (54.14%) | [`array`](https://leetcode.com/tag/array); [`dynamic-programming`](https://leetcode.com/tag/dynamic-programming) |

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return *the maximum profit you can achieve from this transaction*. If you cannot achieve any profit, return `0`.

**Example 1:**

```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

**Example 2:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```

- **Constraints:**

  - `1 <= prices.length <= 105`

  - `0 <= prices[i] <= 104`

- **Solution**

  - <u>Method 1: Two-Pointers</u>

    ```python
    def maxProfit(self, prices: List[int]) -> int:
      l, r = 0, 1      # l: to buy (low); r: to sell (high)
      res = 0
      while r < len(prices):
        if prices[r] > prices[l]:   # profitable
          profit = prices[r] - prices[l]
          res = max(res, profit)
        else:
          # if prices[r] < prices[l] --> not profitable --> set l to the low price --> l = r
          l = r
        r += 1
     return res
    ```

    - Time complexity: $O(N)$

      Space complexity: $O(1)$
  
  - <u>Method 2: Dynamic Programming</u>
  
    --> see 2D-Dynamic Programming.md

## 14. 31 [Next Permutation](https://leetcode.com/problems/next-permutation/description/)

|  Category  |   Difficulty    |                          Tags                           |
| :--------: | :-------------: | :-----------------------------------------------------: |
| algorithms | Medium (36.51%) | [`array`](https://leetcode.com/tag/array); Two-Pointers |

A **permutation** of an array of integers is an arrangement of its members into a sequence or linear order.

- For example, for `arr = [1,2,3]`, the following are all the permutations of `arr`: `[1,2,3], [1,3,2], [2, 1, 3], [2, 3, 1], [3,1,2], [3,2,1]`.

The **next permutation** of an array of integers is the next lexicographically greater permutation of its integer. More formally, if all the permutations of the array are sorted in one container according to their lexicographical order, then the **next permutation** of that array is the permutation that follows it in the sorted container. If such arrangement is not possible, the array must be rearranged as the lowest possible order (i.e., sorted in ascending order).

- For example, the next permutation of `arr = [1,2,3]` is `[1,3,2]`.
- Similarly, the next permutation of `arr = [2,3,1]` is `[3,1,2]`.
- While the next permutation of `arr = [3,2,1]` is `[1,2,3]` because `[3,2,1]` does not have a lexicographical larger rearrangement.

Given an array of integers `nums`, *find the next permutation of* `nums`.

The replacement must be **[in place](http://en.wikipedia.org/wiki/In-place_algorithm)** and use only constant extra memory. 

**Example 1:**

```
Input: nums = [1,2,3]
Output: [1,3,2]
```

**Example 2:**

```
Input: nums = [3,2,1]
Output: [1,2,3]
```

**Example 3:**

```
Input: nums = [1,1,5]
Output: [1,5,1] 
```

- **Constraints:**

  - `1 <= nums.length <= 100`

  - `0 <= nums[i] <= 100`

- **Thoughts**

  - “下一个排列”的定义是：给定数字序列的字典序中下一个更大的排列。如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。我们可以将该问题形式化地描述为：给定若干个数字，将其组合为一个整数。如何将这些数字重新排列，以得到下一个更大的整数。如 123 下一个更大的数为 132。如果没有更大的整数，则输出最小的整数。

  - Approach: 

    1. Generate every permutation until find the current and next permutation

       cons: not satisfy problem requirement; time complexity: O(N * N!)

    2. Case Analysis

       a strictly decreasing section is the last permutation

  - 如何得到这样的排列顺序？这是本文的重点。我们可以这样来分析：

    1. 我们希望下一个数比当前数大，这样才满足“下一个排列”的定义。因此只需要将后面的「大数」与前面的「小数」交换，就能得1到一个更大的数。比如 123456，将 5 和 6 交换就能得到一个更大的数 123465。
    2. 我们还希望下一个数增加的幅度尽可能的小，这样才满足“下一个排列与当前排列紧邻“的要求。为了满足这个要求，我们需要：
       1. **在尽可能靠右的低位进行交换，需要从后向前查找**
       2. 将一个 尽可能小的「大数」 与前面的「小数」交换。比如 123465，下一个排列应该把 5 和 4 交换而不是把 6 和 4 交
       3. 将「大数」换到前面后，需要将「大数」后面的所有数重置为升序，升序排列就是最小的排列。以 123465 为例：首先按照上一步，交换 5 和 4，得到 123564；然后需要将 5 之后的数重置为升序，得到 123546。显然 123546 比 123564 更小，123546 就是 123465 的下一个排列。

  - 资料：[Next lexicographical permutation algorithm](https://www.nayuki.io/page/next-lexicographical-permutation-algorithm)

    步骤如下：

    <img src="/Users/xinyuzhang/Downloads/IMG_722D001CF367-1.jpeg" alt="IMG_722D001CF367-1" style="zoom: 33%;" />

- **Solution**

  ```python
  def nextPermutation(self, nums: List[int]) -> None:
    def nextPermutation(self, nums: List[int]) -> None:
          """
          Do not return anything, modify nums in-place instead.
          """
          n = len(nums)
          # S1: find non-increasing suffix
          i = n - 1
          while i > 0 and nums[i - 1] >= nums[i]:
              i -= 1
          if i == 0:
              nums.reverse()
              return
          
          # S2: find successor to pivot; nums[i - 1] is the last ascending position
          j = n - 1
          while nums[j] <= nums[i - 1]:
              j -= 1
          nums[i - 1], nums[j] = nums[j], nums[i - 1]
  
          # S3: reverse sufix --> reverse 
          nums[i:] = nums[n-1:i-1:-1]  # reverse nums[i:n - 1]
  ```

  - Time complexity: $O(N)$

    Space complexity: $O(1)$
