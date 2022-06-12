# Sliding Window 滑动窗口

**不断地调节子序列的起始位置和终止位置，从而得出我们要想的结果** --> 目的：减少while循环

<img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220524204634854.png" alt="image-20220524204634854" style="zoom:50%;" />

如果是要求连续10000个数相加的最大值，暴力解法每次要进行10000个数相加，而滑动窗口每次只有两步（减去出窗口的数，加上进入窗口的数）

--> 常用：==数组中定长问题（求k个数的和最大值/最小值）==

- **Template**

  ```python
  def slidingWindow(s: str, t: str):
    # edge/corner case: (Example)
    # if t == '':
      # return ''  
    
    # need = dict()   # needs和window 相当于计数器，分别记录t中字符出现次数(需要凑齐的字符)和「窗口」中的相应字符的出现次数
    # window = dict()
    countT, window = {}, {}
    
    # S1: insert all characters and their counts in `countT` dictionary
    for c in t:
      countT[c] = 1 + countT.get(c, 0)
    
    left, right = 0, 0
    valid = 0        # valid：表示窗口中满足 need 条件的字符个数 --> 如果valid和len(need)的大小相同，则说明窗口已满足条件，已经完全覆盖了串 T
    res, resLen = [-1, -1], float('infinity')
    
    while (right < len(s)):
      c = s[right]   # c是即将移入窗口的字符
      # 增大窗口
      right += 1
      # 进行窗口内数据的一系列更新（加入字符时，应该更新哪些数据）## Question 1
      ...
  
      # debug输出的位置
      print("Window: [%d, %d]", left, right)
  
      ## have already get a possible solution
      # 判断左侧窗口是否要搜索
      while (condition: window needs shrink):				## Question 2     
        # 更新最终结果（如果结果是在缩小窗口时更新）
        ...
        d = s[left]   # d是即将移出窗口的字符
        # 缩小窗口
        left += 1
        # 进行窗口内数据的一系列更新										  ## Question 3
        ...
        
        																						## Question 4: 我们要的结果应该在扩大窗口时还是缩小窗口时进行更新
  ```

## 1. 209 [Minimum Size Subarray Sum](https://leetcode.com/problems/minimum-size-subarray-sum/description/)

|  Category  |   Difficulty    | Likes | Dislikes |
| :--------: | :-------------: | :---: | :------: |
| algorithms | Medium (43.40%) | 6589  |   191    |

<details open=""><summary><strong>Tags</strong></summary><p style="margin-top: 0px; margin-bottom: 0.7em;"><a href="https://leetcode.com/tag/array" title="https://leetcode.com/tag/array" style="color: var(--vscode-textLink-foreground); text-decoration: none;"><code style="color: var(--vscode-textLink-foreground); font-family: var(--vscode-editor-font-family, &quot;SF Mono&quot;, Monaco, Menlo, Consolas, &quot;Ubuntu Mono&quot;, &quot;Liberation Mono&quot;, &quot;DejaVu Sans Mono&quot;, &quot;Courier New&quot;, monospace); font-size: 1em; line-height: 1.357em; white-space: pre-wrap;">array</code></a><span>&nbsp;</span>|<span>&nbsp;</span><a href="https://leetcode.com/tag/two-pointers" title="https://leetcode.com/tag/two-pointers" style="color: var(--vscode-textLink-foreground); text-decoration: none;"><code style="color: var(--vscode-textLink-foreground); font-family: var(--vscode-editor-font-family, &quot;SF Mono&quot;, Monaco, Menlo, Consolas, &quot;Ubuntu Mono&quot;, &quot;Liberation Mono&quot;, &quot;DejaVu Sans Mono&quot;, &quot;Courier New&quot;, monospace); font-size: 1em; line-height: 1.357em; white-space: pre-wrap;">two-pointers</code></a><span>&nbsp;</span>|<span>&nbsp;</span><a href="https://leetcode.com/tag/binary-search" title="https://leetcode.com/tag/binary-search" style="color: var(--vscode-textLink-foreground); text-decoration: none;"><code style="color: var(--vscode-textLink-foreground); font-family: var(--vscode-editor-font-family, &quot;SF Mono&quot;, Monaco, Menlo, Consolas, &quot;Ubuntu Mono&quot;, &quot;Liberation Mono&quot;, &quot;DejaVu Sans Mono&quot;, &quot;Courier New&quot;, monospace); font-size: 1em; line-height: 1.357em; white-space: pre-wrap;">binary-search</code></a></p></details>

Given an array of positive integers `nums` and a positive integer `target`, return the minimal length of a **contiguous subarray** `[numsl, numsl+1, ..., numsr-1, numsr]` of which the sum is greater than or equal to `target`. If there is no such subarray, return `0` instead.

**Example 1:**

```
Input: target = 7, nums = [2,3,1,2,4,3]
Output: 2
Explanation: The subarray [4,3] has the minimal length under the problem constraint.
```

**Example 2:**

```
Input: target = 4, nums = [1,4,4]
Output: 1
```

**Example 3:**

```
Input: target = 11, nums = [1,1,1,1,1,1,1,1]
Output: 0
```

- **Constraints:**

  - `1 <= target <= 109`

  - `1 <= nums.length <= 105`

  - `1 <= nums[i] <= 105`

- **Solution**

  - <u>Method 1:</u> Sliding Window

    - 可以尝试从**贪吃蛇** (Snake) 的角度去考虑这个问题。一直吃前面的方块，直到长度符合最小要求，这时我们不可能去掉头部方块，因为没吃它之前长度不够，然后为了减肥，我们只能考虑去减掉后面的方块。 减完一阵子之后，蛇蛇还想知道怎么才能更瘦，那肯定是靠前面有没有更大的方块进来，以便我们排出更多小方块。所以需要继续**边吃边吐**的过程。

    - S1: 定义两个指针 start 和 end 分别表示子数组（滑动窗口窗口）的开始位置和结束位置，维护变量 sum 存储子数组中的元素和（即从nums[start] 到 nums[end] 的元素和`total += nums[end]`。初始状态下，start 和 end 都指向下标 0，sum 的值为 0。

    - S2: 每一轮迭代，将 nums[end] 加到 sum，如果 sum≥s，则更新子数组的最小长度（此时子数组的长度是 end−start+1），然后将 nums[start] 从 sum 中减去并将 start 右移，直到 sum<s，在此过程中同样更新子数组的最小长度。在每一轮迭代的最后，将 end 右移。

      ==滑动窗口的缩小和伸长== --> 也可理解为双指针中的快慢指针算法

      窗口就是 满足其和 ≥ s 的长度最小的连续子数组。

      窗口的**起始位置**如何移动：如果当前窗口的值大于s了，窗口就要向前移动了（也就是该缩小了）。

      窗口的**结束位置**如何移动：窗口的结束位置就是遍历数组的指针，窗口的起始位置设置为数组的起始位置就可以了

    
```python
    class Solution:
        def minSubArrayLen(self, target: int, nums: List[int]) -> int:
            if (nums is None or len(nums) == 0):
                return 0
            
            window = len(nums) + 1  # the length
            total = 0               # the sum
            i = 0                   # slow pointer
            j = 0                   # fast pointer
    
            while (j < len(nums)):
                total += nums[j]
                j += 1                    # increase the length of the window
                while (total >= target):  # reduce the length of the window
                    window = min(window, j - i)
                    total -= nums[i]      # 去掉最前面的元素
                    i += 1								# 后移i指针看是否还能满足条件
            
            if (window == len(nums) + 1): # 所有值加起来都小于target，没能进入while循环
                return 0
    
            return window
```

Time Complexity: <font color=red>**O(N)**</font> --> n is the length of the array (the two pointers move n times at most) 主要是看每一个元素被操作的次数，每个元素在滑动窗后进来操作一次，出去操作一次，每个元素都是被操作两次，所以时间复杂度是 2 × n 也就是O(n)
    
Space Complexity: O(1)

## 2. 1456 [Maximum Number of Vowels in a Substring of Given Length](https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/description/)

|  Category  |   Difficulty    |
| :--------: | :-------------: |
| algorithms | Medium (57.41%) |

Given a string `s` and an integer `k`, return *the maximum number of vowel letters in any substring of* `s` *with length* `k`.

**Vowel letters** in English are `'a'`, `'e'`, `'i'`, `'o'`, and `'u'`.

 **Example 1:**

```
Input: s = "abciiidef", k = 3
Output: 3
Explanation: The substring "iii" contains 3 vowel letters.
```

**Example 2:**

```
Input: s = "aeiou", k = 2
Output: 2
Explanation: Any substring of length 2 contains 2 vowels.
```

**Example 3:**

```
Input: s = "leetcode", k = 3
Output: 2
Explanation: "lee", "eet" and "ode" contain 2 vowels.
```

- **Constraints:**

  - `1 <= s.length <= 105`

  - `s` consists of lowercase English letters.

  - `1 <= k <= s.length`

- **Thoughts**

  - 由于length已定，所以该问题是数组定长问题，可考虑使用滑动窗口方法
  - S1: build a hashset to store all vowels
  - S2: check if each letter in the first window is a vowel
  - S3: move the window to the next element --> if the out element is a vowel, `count -= 1`, if the in element is a vowel, `count += 1`

- **Solution**

  ```python
  def maxVowels(self, s: str, k: int) -> int:   
          
          # S1: build a hashset to store all vowels
          hashset = set(['a', 'e', 'i', 'o', 'u'])
          ## Not use!!!!
          # hashset = set()
          # hashset.add('a')
          # hashset.add('e')
          # hashset.add('i')
          # hashset.add('o')
          # hashset.add('u')
          
  				
          # S2: check if each letter in the first window is a vowel
          res = 0    # the result
          count = 0  # number of vowels in the current window
          for i in range(0, k):
              if (s[i] in hashset):
                  count += 1
          res = max(res, count)
  	
    			# S3: move the window to examine the next element
          for i in range(k, len(s)):
              if (s[i - k] in hashset):
                  count -= 1
              if (s[i] in hashset):
                  count += 1
              res = max(res, count)
          return res
  ```

  Time Complexity: O(N) --> N is the length of the string（先遍历了0~k，再遍历了k~len(s)）

  Space Complexity: O(1)

## 3. 76 [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/description/)

|  Category  |  Difficulty   |                            Likes                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (39.05%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`two-pointers`](https://leetcode.com/tag/two-pointers); [`string`](https://leetcode.com/tag/string); [`sliding-window`](https://leetcode.com/tag/sliding-window) |

Given two strings `s` and `t` of lengths `m` and `n` respectively, return *the **minimum window substring** of* `s` *such that every character in* `t` *(**including duplicates**) is included in the window. If there is no such substring**, return the empty string* `""`*.*

The testcases will be generated such that the answer is **unique**.

A **substring** is a contiguous sequence of characters within the string.

**Example 1:**

```
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
Explanation: The minimum window substring "BANC" includes 'A', 'B', and 'C' from string t.
```

**Example 2:**

```
Input: s = "a", t = "a"
Output: "a"
Explanation: The entire string s is the minimum window.
```

**Example 3:**

```
Input: s = "a", t = "aa"
Output: ""
Explanation: Both 'a's from t must be included in the window.
Since the largest window of s only has one 'a', return empty string.
```

**Constraints:**

- `m == s.length`
- `n == t.length`
- `1 <= m, n <= 105`
- `s` and `t` consist of uppercase and lowercase English letters.

**Follow up:** Could you find an algorithm that runs in `O(m + n)` time?

- **Thoughts**

  套用滑动窗口的模板，主要考虑模板中提出的四个问题做出更改

  - S1: check the edge/corner case
  - S2: create two hash tables to store 1. characters and counts in T(target) 2. those in current window
  - S3: move *right* pointer to increase the window length and record `valid` value
  - S5: move *left* pointer to shorten the window length
  - S6: output the final result res

- **Solution**

  ```python
  class Solution:
      def minWindow(self, s: str, t: str) -> str:
  
          if t == '':
              return ''
          
          countT, window = {}, {}
          for c in t:
              countT[c] = 1 + countT.get(c, 0)
          # countT = collections.Counter(t)
  
          left, right = 0, 0
          valid = 0
          res, resLen = [-1, -1], float('infinity')
          for right in range(len(s)):
              in_char = s[right]
              right += 1
              if in_char in countT:
                  window[in_char] = 1 + window.get(in_char, 0)
                  if window[in_char] == countT[in_char]:
                      valid += 1
  
              while valid == len(countT):
                  # update the result 
                  if (right - left + 1) < resLen:
                      res = [left, right]
                      resLen = right - left + 1
  								
                  # pop the leftmost element
                  out_char = s[left]
                  left += 1
                  if out_char in countT:
                      if window[out_char] == countT[out_char]:
                          valid -= 1
                      window[out_char] -= 1
          
          l, r = res
          return s[l:r] if resLen != float('infinity') else ''  
          # Time complexity: O(M + N)
          # Space complexity: O(N)
  ```

## 4. 567 [Permutation in String](https://leetcode.com/problems/permutation-in-string/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (44.54%) | [`two-pointers`](https://leetcode.com/tag/two-pointers); [`sliding-window`](https://leetcode.com/tag/sliding-window) |

Given two strings `s1` and `s2`, return `true` *if* `s2` *contains a permutation of* `s1`*, or* `false` *otherwise*.

In other words, return `true` if one of `s1`'s permutations is the substring of `s2`. 

**Example 1:**

```
Input: s1 = "ab", s2 = "eidbaooo"
Output: true
Explanation: s2 contains one permutation of s1 ("ba").
```

**Example 2:**

```
Input: s1 = "ab", s2 = "eidboaoo"
Output: false
```

- **Constraints:**

  - `1 <= s1.length, s2.length <= 104`

  - `s1` and `s2` consist of lowercase English letters.

- **Thought**

  - 是明显的滑动窗口算法，**相当给你一个 `S` 和一个 `T`，请问你 `S` 中是否存在一个子串，包含 `T` 中所有字符且不包含其他字符**？
  - 窗口缩小条件：当`left`和`right`指针的距离大于`s1`长度时，开始更新结果并移动左指针

- **Solution**

  ```python
  class Solution:
      def checkInclusion(self, s1: str, s2: str) -> bool:
          # S1: check if there are edge/corner cases
          # S2: create two hash tables to record count of s1 (need) and window
          # S3: extend the window until find a valid char
          # S4: shorten the window
  
          countS1, window = {}, {}
          for c in s1:
              countS1[c] = 1 + countS1.get(c, 0)
          
          left, right = 0, 0
          valid = 0
          for right in range(len(s2)):
              in_char = s2[right]
              right += 1
              if in_char in countS1:
                  window[in_char] = 1 + window.get(in_char, 0)
                  if window[in_char] == countS1[in_char]:
                      valid += 1
              
              while (right - left + 1) > len(s1):
                  if valid == len(countS1):
                      return True
                  out_char = s2[left]
                  left += 1             
                  
                  if out_char in countS1:
                      if window[out_char] == countS1[out_char]:
                          valid -= 1
                      window[out_char] -= 1
          return False
  ```

  Time complexity: O(M + N) --> M is the number of elements in s1 and N is the number of elements in s2

  Space complexity: O(M + N) --> use two hash tables which may at most store all elements in the two strings

## 5. 438 [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/description/)

|  Category  |   Difficulty    |                        Tags                         |
| :--------: | :-------------: | :-------------------------------------------------: |
| algorithms | Medium (48.36%) | [`hash-table`](https://leetcode.com/tag/hash-table) |

Given two strings `s` and `p`, return *an array of all the start indices of* `p`*'s anagrams in* `s`. You may return the answer in **any order**.

An **Anagram** is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

**Example 1:**

```
Input: s = "cbaebabacd", p = "abc"
Output: [0,6]
Explanation:
The substring with start index = 0 is "cba", which is an anagram of "abc".
The substring with start index = 6 is "bac", which is an anagram of "abc".
```

**Example 2:**

```
Input: s = "abab", p = "ab"
Output: [0,1,2]
Explanation:
The substring with start index = 0 is "ab", which is an anagram of "ab".
The substring with start index = 1 is "ba", which is an anagram of "ab".
The substring with start index = 2 is "ab", which is an anagram of "ab".
```

- **Constraints:**

  - `1 <= s.length, p.length <= 3 * 10^4`

  - `s` and `p` consist of lowercase English letters.

- **Thoughts**

  - 该题目和76, 567题很像，只需要修改`update the result`部分
  - 由于constraints里提及`1 <= s.length, p.length` --> 不用考虑corner case

- **Solution**

  ```python
  class Solution:
      def findAnagrams(self, s: str, p: str) -> List[int]:
          countP, window = {}, {}
          for c in p:
              countP[c] = 1 + countP.get(c, 0)
          
          res = []
          left, right = 0, 0
          valid = 0
          for right in range(len(s)):
              in_char = s[right]
              right += 1
  
              if in_char in countP:
                  window[in_char] = 1 + window.get(in_char, 0)
                  if window[in_char] == countP[in_char]:
                      valid += 1
              
              while (right - left + 1) > len(p):
                  if valid == len(countP):
                      res.append(left)   # update the result
  
                  out_char = s[left]
                  left += 1
                                 
                  if out_char in countP:
                      if window[out_char] == countP[out_char]:
                          valid -= 1
                      window[out_char] -= 1
          return res
  ```

  Time complexity: O(M + N) --> M is the number of elements in s1 and N is the number of elements in s2

  Space complexity: O(M + N) --> use two hash tables which may at most store all elements in the two strings

## 6. 3 [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (33.12%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`two-pointers`](https://leetcode.com/tag/two-pointers); [`string`](https://leetcode.com/tag/string); [`sliding-window`](https://leetcode.com/tag/sliding-window) |

Given a string `s`, find the length of the **longest substring** without repeating characters.

**Example 1:**

```
Input: s = "abcabcbb"
Output: 3
Explanation: The answer is "abc", with the length of 3.
```

**Example 2:**

```
Input: s = "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

**Example 3:**

```
Input: s = "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3.
Notice that the answer must be a substring, "pwke" is a subsequence and not a substring. 
```

- **Constraints:**

  - `0 <= s.length <= 5 * 104`

  - `s` consists of English letters, digits, symbols and spaces.

- **Thoughts**

  - 这道题连 `need` 和 `valid` 都不需要，而且更新窗口内数据也只需要简单的更新计数器 `window` 即可。当 `window[c]` 值大于 1 时，说明窗口中存在重复字符，不符合条件，就该移动 `left` 缩小窗口了嘛。
  - <font color=red>**唯一需要注意的是，在哪里更新结果 `res` 呢？**</font>我们要的是最长无重复子串，哪一个阶段可以保证窗口中的字符串是没有重复的呢？这里和之前不一样，要==在收缩窗口完成后更新 `res`==，因为窗口收缩的 while 条件是存在重复元素，换句话说收缩完成后一定保证窗口中没有重复

- **Solution**

  - 套用模板 --> 最稳定的解法

  ```python
  class Solution:
      def lengthOfLongestSubstring(self, s: str) -> int:
          # S1: check the edge/corner case (len(s) == 0)
          # S2: create a hash table (window) to store characters and their counts
          # S3: move right pointer to extend the window
          # S4: when there is a repeated character --> stop extending
          # S5: move left pointer to shorten the window
          # **S6: update the result after shortening the window
  
          if (s is None or len(s) == 0):
              return 0
          
          window = {}
          left, right = 0, 0
          res = 0
          for right in range(len(s)):
              in_char = s[right]
              right += 1
              window[in_char] = 1 + window.get(in_char, 0)
  
              while window[in_char] > 1:
                  out_char = s[left]
                  left += 1
                  window[out_char] -= 1
                  
              res = max(res, right - left)
          return res
  ```

  Time complexity: O(N)

  Space complexity: O(N)

  - 简化版本

    ```python
    class Solution:
        def lengthOfLongestSubstring(self, s: str) -> int:
          charSet = set()
          l = 0
          res = []
    
          for r in range(len(s)):
            while s[r] in charSet:
              charSet.remove(s[l])
              l += 1
              res = max(res, r - l + 1)
              return res
    ```

    