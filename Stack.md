# Stack 栈

- **单调栈模板**

  单调栈：单调栈用途不太广泛，只处理一类典型的问题，比如==「下一个更大元素」，「上一个更小元素」==等

  --> 输入一个数组 `nums`，请你返回一个等长的结果数组，结果数组中对应索引存储着下一个更大元素，如果没有更大的元素，就存 -1。

  - S1: 将数组倒着往栈里放
  - S2: 对于每一个数`nums[i]`，先判断栈是否为空 --> 若为空，则证明没有下一个更大的数；若栈不为空，则判断`nums[i]`与栈顶值的大小 --> 若该数大于栈顶值，则证明该数更大，将小于该数的值都除去，获得下一个数或者-1
  - S3: 经过`while`循环除去中间小的元素，获得比`nums[i]`大的元素 
  - S4: 将`nums[i]`加入到栈中，继续向前找寻是否该值为前面值的下一个更大元素

  ```python
  def nextGreaterElement(nums):
    n = len(nums)
    res = [-1] * n
    stack = []								# 储存比当前值更大的数
    
    # 倒着往栈里放 --> 正着出栈（逆序遍历）
    for i in range(n - 1, -1, -1):
      # 把两个大的元素中间的元素排除 --> 先让栈顶中比nums[i]小的元素出栈
      while stack and nums[i] >= stack[-1]:
        stack.pop()
        
      # 在结果加上nums[i] 身后的更大元素
      res[i] = stack[-1] if stack else -1  
      stack.append(nums[i])
    return res
  ```

​		题目：5(496)

## 1. 232 [Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/description/)

|  Category  |  Difficulty   |     Tags      |
| :--------: | :-----------: | :-----------: |
| algorithms | Easy (58.36%) | Stack; design |

Implement a first in first out (FIFO) queue using only two stacks. The implemented queue should support all the functions of a normal queue (`push`, `peek`, `pop`, and `empty`).

Implement the `MyQueue` class:

- `void push(int x)` Pushes element x to the back of the queue.
- `int pop()` Removes the element from the front of the queue and returns it.
- `int peek()` Returns the element at the front of the queue.
- `boolean empty()` Returns `true` if the queue is empty, `false` otherwise.

**Notes:**

- You must use **only** standard operations of a stack, which means only `push to top`, `peek/pop from top`, `size`, and `is empty` operations are valid.
- Depending on your language, the stack may not be supported natively. You may simulate a stack using a list or deque (double-ended queue) as long as you use only a stack's standard operations.

**Example 1:**

```java
Input
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
Output
[null, null, null, 1, 1, false]

Explanation
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```

- **Thoughts**

  - 本题要求使用两个栈来实践队列。队列和栈的构造如下。

    <img src="https://labuladong.github.io/algo/images/%e6%a0%88%e9%98%9f%e5%88%97/1.jpg" alt="img" style="zoom: 50%;" />

    要使两个栈的结构成为队列，如下放置栈：

    <img src="https://labuladong.github.io/algo/images/%e6%a0%88%e9%98%9f%e5%88%97/2.jpg" alt="img" style="zoom:33%;" />

<img src="/Users/xinyuzhang/Downloads/IMG_3C53DD54131A-1.jpeg" alt="IMG_3C53DD54131A-1" style="zoom:33%;" />

- **Solution**

  ```python
  class MyQueue:
  
      def __init__(self):
          self.s1 = []   # original元素顺序
          self.s2 = []   # 进行反转并存储最终元素顺序
  
      def push(self, x: int) -> None:
          self.s1.append(x)
  
      def pop(self) -> int:
          self.peek()    # First make sure all values are sent to s2
          return self.s2.pop()
  
      def peek(self) -> int:
          if len(self.s2) == 0:
              while len(self.s1) != 0:
                  self.s2.append(self.s1.pop())
          return self.s2[-1]
          
      def empty(self) -> bool:
          return len(self.s1) == 0 and len(self.s2) == 0
  ```

  值得一提的是，这几个操作的时间复杂度是多少呢？有点意思的是 `peek` 操作，调用它时可能触发 `while` 循环，这样的话时间复杂度是 O(N)，但是大部分情况下 `while` 循环不会被触发，时间复杂度是 O(1)。由于 `pop` 操作调用了 `peek`，它的时间复杂度和 `peek` 相同。

  像这种情况，可以说它们的==**最坏时间复杂度**(Worst-case time complexity)是 O(N)==，因为包含 `while` 循环，**可能**需要从 `s1` 往 `s2` 搬移元素。

  但是它们的==**均摊时间复杂度** (Amortized time complexity) 是 O(1)==，这个要这么理解：对于一个元素，最多只可能被搬运一次，也就是说 `peek` 操作平均到每个元素的时间复杂度是 O(1)。

  - 易错点：

    - 在`pop`函数中，要先确保s1的元素已经都转移到s2栈中再进行pop操作，故需先进行一次`self.peek`

    - <font color=red>在stack里，取出栈顶元素(peek)使用的是`self.stack[-1]`!!</font>

## 2. 20 [Valid Parentheses](https://leetcode.com/problems/valid-parentheses/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (40.87%) | [`string`](https://leetcode.com/tag/string); [`stack`](https://leetcode.com/tag/stack) |

Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order. 

**Example 1:**

```
Input: s = "()"
Output: true
```

**Example 2:**

```
Input: s = "()[]{}"
Output: true
```

**Example 3:**

```
Input: s = "(]"
Output: false 
```

- **Constraints:**

  - `1 <= s.length <= 104`

  - `s` consists of parentheses only `'()[]{}'`.

- **Thoughts**

  栈是一种先进后出的数据结构，处理**括号问题**的时候尤其有用。

  遇到左括号就入栈，遇到右括号就去栈中寻找最近的左括号，看是否匹配。

- **Solution**

  ```python
  def isValid(self, s: str) -> bool:
          if not s:																						# empty string
              return True
          
          stack = []
          for ss in s:
              if (ss == '(' or ss == '[' or ss == '{'):
                  stack.append(ss)
              else:
                  if not stack:
                      return False
                  left = stack.pop()
                  if (ss == ')' and left != '(' or
                      ss == ']' and left != '[' or
                      ss == '}' and left != '{'):
                      return False
          
          # After traversal, check if there are brackets in the stack
          return True if len(stack) == 0 else False						
  ```

  - Time complexity: $O(N)$

    Space complexity: O(N) --> create a stack to store left brackets

## 3. 155 [Min Stack](https://leetcode.com/problems/min-stack/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Easy (50.43%) | [`stack`](https://leetcode.com/tag/stack); [`design`](https://leetcode.com/tag/design) |

Design a stack that supports push, pop, top, and retrieving the minimum element ==in constant time==.

Implement the `MinStack` class:

- `MinStack()` initializes the stack object.
- `void push(int val)` pushes the element `val` onto the stack.
- `void pop()` removes the element on the top of the stack.
- `int top()` gets the top element of the stack.
- `int getMin()` retrieves the minimum element in the stack.

**Example 1:**

```java
Input
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

Output
[null,null,null,null,-3,null,0,-2]

Explanation
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2
```

- **Constraints:**

  - `-231 <= val <= 231 - 1`

  - Methods `pop`, `top` and `getMin` operations will always be called on **non-empty** stacks.

  - At most `3 * 104` calls will be made to `push`, `pop`, `top`, and `getMin`.

- **Thoughts**

  由于题目要求所有操作都要在O(1)的时间完成，而除了`getMin()`的其他操作都是O(1)，所以本题重点在于使`getMin()`为O(1)的时间复杂度 --> 使用一个辅助栈储存每一个元素对应的栈内最小值

- **Solution**

  - <u>Method 1: Use an additional stack</u>

    ```python
    class MinStack:
    
        def __init__(self):
            self.stack = []
            self.minStack = []
    
        def push(self, val: int) -> None:
            self.stack.append(val) 
            minimum = min(val, self.minStack[-1] if self.minStack else val)
            self.minStack.append(minimum)      
    
        def pop(self) -> None:
            if not self.stack:
                return
            self.stack.pop()  
            self.minStack.pop()   
    
        def top(self) -> int:
            if not self.stack:
                return
            return self.stack[-1]
    
        # get the minimum element in O(1) time
        # --> have another stack to store min for each value
        def getMin(self) -> int:
            if not self.stack:
                return
            return self.minStack[-1]
    ```

    - Time complexity: $O(1)$

      Space complexity: $O(N)$ --> create two stacks

  - <u>Method 2: O(1) time and space complexity</u>

    --> 可以用一个栈，这个栈同时保存的是每个数字进栈的时候的值与插入该值后的栈内最小值。即每次新元素入栈的时候保存一个元组：`（当前值 x，栈内最小值）`。

    ```python
    class MinStack:
    
        def __init__(self):
            self.stack = []
            
    
        def push(self, val: int) -> None:
            if not self.stack:
                self.stack.append((val, val))
            else:
                self.stack.append((val, min(val, self.stack[-1][1])))
    
        def pop(self) -> None:
            self.stack.pop()
    
        def top(self) -> int:
            return self.stack[-1][0]     
    
        def getMin(self) -> int:
            return self.stack[-1][1]
    ```

    - Time complexity: $O(1)$

      Space complexity: $O(1)$

  ## 4. 150 [Evaluate Reverse Polish Notation](https://leetcode.com/problems/evaluate-reverse-polish-notation/description/)

  |  Category  |   Difficulty    | Tags  |
  | :--------: | :-------------: | :---: |
  | algorithms | Medium (42.84%) | stack |

  Evaluate the value of an arithmetic expression in [Reverse Polish Notation](http://en.wikipedia.org/wiki/Reverse_Polish_notation).

  Valid operators are `+`, `-`, `*`, and `/`. Each operand may be an integer or another expression.

  **Note** that division between two integers should truncate toward zero.

  It is guaranteed that the given RPN expression is always valid. That means the expression would always evaluate to a result, and there will not be any division by zero operation. 

  **Example 1:**

  ```
  Input: tokens = ["2","1","+","3","*"]
  Output: 9
  Explanation: ((2 + 1) * 3) = 9
  ```

  **Example 2:**

  ```
  Input: tokens = ["4","13","5","/","+"]
  Output: 6
  Explanation: (4 + (13 / 5)) = 6
  ```

  **Example 3:**

  ```
  Input: tokens = ["10","6","9","3","+","-11","*","/","*","17","+","5","+"]
  Output: 22
  Explanation: ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
  = ((10 * (6 / (12 * -11))) + 17) + 5
  = ((10 * (6 / -132)) + 17) + 5
  = ((10 * 0) + 17) + 5
  = (0 + 17) + 5
  = 17 + 5
  = 22 
  ```

  **Constraints:**

  - `1 <= tokens.length <= 104`
  - `tokens[i]` is either an operator: `"+"`, `"-"`, `"*"`, or `"/"`, or an integer in the range `[-200, 200]`.

- **Thoughts**

  逆波兰表达式发明出来就是为了方便计算机运用「栈」进行表达式运算的，其运算规则如下：

  按顺序遍历逆波兰表达式中的字符，如果是数字，则放入栈；如果是运算符，则将栈顶的两个元素拿出来进行运算，再将结果放入栈。对于减法和除法，运算顺序别搞反了，栈顶第二个数是被除（减）数。

  所以这题很简单，直接按照运算规则借助栈计算表达式结果即可。

- **Solution**

  ```python
  def evalRPN(self, tokens: List[str]) -> int:
          stack = []
  
          for i in range(len(tokens)):
              if tokens[i] == '+':
                  a = int(stack.pop())
                  b = int(stack.pop())
                  stack.append(a + b)
              elif tokens[i] == '-':
                  a = int(stack.pop())
                  b = int(stack.pop())
                  stack.append(b - a)
              elif tokens[i] == '*':
                  a = int(stack.pop())
                  b = int(stack.pop())
                  stack.append(a * b)
              elif tokens[i] == '/':
                  a = int(stack.pop())
                  b = int(stack.pop())
                  stack.append(int(b / a))   # python负数整除
              else:
                  stack.append(int(tokens[i]))
          
          return stack.pop() if len(stack) == 1 else None
  ```

  - Time complexity: O(N)

    Space complexity: O(N)

  - 易错点：
    - 在Python中进行负数整除: `int(a / b)`向上取整 --> 如果用`a // b`会向下取整

## 5. 496 [Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/description/)

|  Category  |  Difficulty   |                   Tags                    |
| :--------: | :-----------: | :---------------------------------------: |
| algorithms | Easy (70.12%) | [`stack`](https://leetcode.com/tag/stack) |

The **next greater element** of some element `x` in an array is the **first greater** element that is **to the right** of `x` in the same array.

You are given two **distinct 0-indexed** integer arrays `nums1` and `nums2`, where `nums1` is a subset of `nums2`.

For each `0 <= i < nums1.length`, find the index `j` such that `nums1[i] == nums2[j]` and determine the **next greater element** of `nums2[j]` in `nums2`. If there is no next greater element, then the answer for this query is `-1`.

Return *an array* `ans` *of length* `nums1.length` *such that* `ans[i]` *is the **next greater element** as described above.*

**Example 1:**

```
Input: nums1 = [4,1,2], nums2 = [1,3,4,2]
Output: [-1,3,-1]
Explanation: The next greater element for each value of nums1 is as follows:
- 4 is underlined in nums2 = [1,3,4,2]. There is no next greater element, so the answer is -1.
- 1 is underlined in nums2 = [1,3,4,2]. The next greater element is 3.
- 2 is underlined in nums2 = [1,3,4,2]. There is no next greater element, so the answer is -1.
```

**Example 2:**

```
Input: nums1 = [2,4], nums2 = [1,2,3,4]
Output: [3,-1]
Explanation: The next greater element for each value of nums1 is as follows:
- 2 is underlined in nums2 = [1,2,3,4]. The next greater element is 3.
- 4 is underlined in nums2 = [1,2,3,4]. There is no next greater element, so the answer is -1. 
```

- **Constraints:**

  - `1 <= nums1.length <= nums2.length <= 1000`

  - `0 <= nums1[i], nums2[i] <= 104`

  - All integers in `nums1` and `nums2` are **unique**.

  - All the integers of `nums1` also appear in `nums2`.

**Follow up:** Could you find an `O(nums1.length + nums2.length)` solution?

- **Thoughts**

  - 当题目出现「找到最近一个比其大的元素」的字眼时，自然会想到==「单调栈」==。

    具体的，由于目标是找到某个数其在nums2的右边中第一个比其大的数，因此可以对 nums2进行逆序遍历。

    我们在遍历nums2时，实时维护一个单调栈，当我们遍历到元素 nums2[i]时，可以先将栈顶中比 nums2[i]小的元素出栈，最终结果有两种可能：

    - 栈为空，说明nums2[i]之前（右边）没有比其大的数；

    - 栈不为空， 此时栈顶元素为nums2[i]在nums2中（右边）最近的比其大的数。

  - 总结：该题目为单调栈模板的应用，先使用单调栈得到`nums2`里每一个值的下一个最大值，存入哈希表，然后利用`nums1`元素的index直接得到结果

- **Solution**

  ```python
  def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
    stack = []
    hashtable = {}
    res = []
  
    for i in range(len(nums2) - 1, -1, -1):
      while stack and nums2[i] > stack[-1]:
        stack.pop()
      hashtable[nums2[i]] = stack[-1] if stack else -1
      stack.append(nums2[i])
  
    for n in nums1:
        res.append(hashtable[n])
    return res
  ```

  - Time complexity: O(M + N) --> `M = len(nums2); N = len(nums1)`

    Space complexity: O(N) --> create a hashtable with `len(nums2)`

## 6. 739 [Daily Temperatures](https://leetcode.com/problems/daily-temperatures/description/)

|  Category  |   Difficulty    |                             Tags                             |
| :--------: | :-------------: | :----------------------------------------------------------: |
| algorithms | Medium (67.27%) | [`hash-table`](https://leetcode.com/tag/hash-table); [`stack`](https://leetcode.com/tag/stack) |

Given an array of integers `temperatures` represents the daily temperatures, return *an array* `answer` *such that* `answer[i]` *is the number of days you have to wait after the* `ith` *day to get a warmer temperature*. If there is no future day for which this is possible, keep `answer[i] == 0` instead.

**Example 1:**

```
Input: temperatures = [73,74,75,71,69,72,76,73]
Output: [1,1,4,2,1,1,0,0]
```

**Example 2:**

```
Input: temperatures = [30,40,50,60]
Output: [1,1,1,0]
```

**Example 3:**

```
Input: temperatures = [30,60,90]
Output: [1,1,0] 
```

- **Constraints:**

  - `1 <= temperatures.length <= 105`

  - `30 <= temperatures[i] <= 100`

- **Thoughts**

  该题仍然是单调栈，本质上也是找下一个更大元素，只不过现在不是问你下一个更大元素的值是多少，而是问你当前元素距离下一个更大元素的索引距离而已 --> stack储存的不是值而是索引

- **Solution**

  ```python
  def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
          n = len(temperatures)
          stack = []				# 放元素索引，而不是元素
  
          res = [0] * n
          for i in range(n - 1, -1, -1):
              while stack and temperatures[i] >= temperatures[stack[-1]]:
                  stack.pop()
              res[i] = stack[-1] - i if stack else -
              stack.append(i)
          return res
  ```

  - Time complexity: O(N)

    Space complexity: O(N) 

## 7. 503 [Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/description/)

|  Category  |   Difficulty    |                   Tags                    |
| :--------: | :-------------: | :---------------------------------------: |
| algorithms | Medium (62.20%) | [`stack`](https://leetcode.com/tag/stack) |

Given a circular integer array `nums` (i.e., the next element of `nums[nums.length - 1]` is `nums[0]`), return *the **next greater number** for every element in* `nums`.

The **next greater number** of a number `x` is the first greater number to its traversing-order next in the array, which means you could search circularly to find its next greater number. If it doesn't exist, return `-1` for this number.

**Example 1:**

```
Input: nums = [1,2,1]
Output: [2,-1,2]
Explanation: The first 1's next greater number is 2; 
The number 2 can't find next greater number. 
The second 1's next greater number needs to search circularly, which is also 2.
```

**Example 2:**

```
Input: nums = [1,2,3,4,3]
Output: [2,3,4,-1,4]
```

- **Constraints:**

  - `1 <= nums.length <= 104`

  - `-109 <= nums[i] <= 109`

- **Thoughts**

  - 该题目是一个典型的单调栈题目，只是需要处理的是循环数组。对于循环数组，一般是通过`%`求余数来模拟环形特效 --> `arr[index % len(nums)]`
  - 这个问题肯定还是要用单调栈的解题模板，但难点在于，比如输入是 `[2,1,2,4,3]`，对于最后一个元素 3，如何找到元素 4 作为下一个更大元素。**对于这种需求，常用套路就是将数组长度翻倍**

- **Solution**

  ```python
  def nextGreaterElements(self, nums: List[int]) -> List[int]:
          n = len(nums)
          res = [-1] * n
          stack = []
          
          for i in range(2 * n - 1, -1, -1):
              while stack and nums[i % n] >= stack[-1]:
                  stack.pop()
              res[i % n] = stack[-1] if stack else -1
              stack.append(nums[i % n])
          return res
  ```

  - Time complexity: O(N)

    Space complexity: O(N) 

## 8. 853 [Car Fleet](https://leetcode.com/problems/car-fleet/description/)

|  Category  |   Difficulty    |                          Tags                           |
| :--------: | :-------------: | :-----------------------------------------------------: |
| algorithms | Medium (48.42%) | [`two-pointers`](https://leetcode.com/tag/two-pointers) |

There are `n` cars going to the same destination along a one-lane road. The destination is `target` miles away.

You are given two integer array `position` and `speed`, both of length `n`, where `position[i]` is the position of the `ith` car and `speed[i]` is the speed of the `ith` car (in miles per hour).

A car can never pass another car ahead of it, but it can catch up to it and drive bumper to bumper **at the same speed**. The faster car will **slow down** to match the slower car's speed. The distance between these two cars is ignored (i.e., they are assumed to have the same position).

A **car fleet** is some non-empty set of cars driving at the same position and same speed. Note that a single car is also a car fleet.

If a car catches up to a car fleet right at the destination point, it will still be considered as one car fleet.

Return *the **number of car fleets** that will arrive at the destination*. 

**Example 1:**

```
Input: target = 12, position = [10,8,0,5,3], speed = [2,4,1,1,3]
Output: 3
Explanation:
The cars starting at 10 (speed 2) and 8 (speed 4) become a fleet, meeting each other at 12.
The car starting at 0 does not catch up to any other car, so it is a fleet by itself.
The cars starting at 5 (speed 1) and 3 (speed 3) become a fleet, meeting each other at 6. The fleet moves at speed 1 until it reaches target.
Note that no other cars meet these fleets before the destination, so the answer is 3.
```

**Example 2:**

```
Input: target = 10, position = [3], speed = [3]
Output: 1
Explanation: There is only one car, hence there is only one fleet.
```

**Example 3:**

```
Input: target = 100, position = [0,2,4], speed = [4,2,1]
Output: 1
Explanation:
The cars starting at 0 (speed 4) and 2 (speed 2) become a fleet, meeting each other at 4. The fleet moves at speed 2.
Then, the fleet (speed 2) and the car starting at 4 (speed 1) become one fleet, meeting each other at 6. The fleet moves at speed 1 until it reaches target.
```

- **Constraints:**

  - `n == position.length == speed.length`

  - `1 <= n <= 105`

  - `0 < target <= 106`

  - `0 <= position[i] < target`

  - All the values of `position` are **unique**.

  - `0 < speed[i] <= 106`

- **Thoughts**

  - 是否能够形成车队，取决于下述规律：

    **如果车 `x` 排在 车 `y` 后面，且 `x` 到达终点所需时间比 `y` 少，则 `x` 必然会被 `y` 卡住，形成车队**。

    所以本题的思路是先根据每辆车的起始位置 `position` 排序，然后计算出时间数组 `time`。

    假设计算出的 `time` 数组为 `[12, 3, 7, 1, 2]`，那么观察数组的单调性变化，最后肯定会形成三个车队，他们到达终点的时间分别是 12, 7, 2。

  - S1: sort the (position, speed) pair in ascending order based on position

    S2: create a time array to store time needed for each car

    S3: 将车辆按照起始位置降序排序后，我们顺序扫描这些车辆。

    - 如果相邻的两辆车，前者比后者行驶到终点需要的时间短，那么后者永远追不上前者，即从后者开始的若干辆车辆会组成一个新的车队；如果前者不比后者行驶到终点需要的时间短，那么后者可以在终点前追上前者，并和前者形成车队。此时我们将后者到达终点的时间置为前者到达终点的时间。

- **Solution**

  ```python
  def carFleet(self, target: int, position: List[int], speed: List[int]) -> int:
    pair = sorted(zip(position, speed))
    
    time = []
    for p, s in pair:
      time.append((target - p) / s)
  
    res = 0
    while len(time) > 1:
      lead = time.pop()
      if lead < time[-1]:  # lead不会被追上，车队数量+1
        res += 1
      else:
        time[-1] = lead    # lead会被追上，为了确认该车队有多少车辆，将lead设置为time[-1]，根据lead最终到达时间进行判断
    
    return res + 1				 # remain one car --> a fleet
  ```

  - Time complexity: O(NlogN) --> 排序的时间复杂度

    Space complexity: O(N) --> create a time stack 

## 9. 84 [Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/description/)

|  Category  |  Difficulty   |                             Tags                             |
| :--------: | :-----------: | :----------------------------------------------------------: |
| algorithms | Hard (40.93%) | [`array`](https://leetcode.com/tag/array); [`stack`](https://leetcode.com/tag/stack) |

Given an array of integers `heights` representing the histogram's bar height where the width of each bar is `1`, return *the area of the largest rectangle in the histogram*.

**Example 1:**

<img src="https://assets.leetcode.com/uploads/2021/01/04/histogram.jpg" alt="img" style="zoom:50%;" />

```
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: The above is a histogram where width of each bar is 1.
The largest rectangle is shown in the red area, which has an area = 10 units.
```

**Example 2:**

<img src="https://assets.leetcode.com/uploads/2021/01/04/histogram-1.jpg" alt="img" style="zoom:50%;" />

```
Input: heights = [2,4]
Output: 4 
```

- **Constraints:**

  - `1 <= heights.length <= 105`

  - `0 <= heights[i] <= 104`

- **Thoughts**

  - 该题目求的是最大面积，所以需要记录的信息包含宽度。宽度是由下标确定的，记录了下标其实对应的高度就可以直接从输入数组中得出，因此，应该记录的是下标。

  - 我们就拿示例的数组 [2, 1, 5, 6, 2, 3] 为例：

    1、一开始看到的柱形高度为 2 ，这个时候以这个 2 为高度的最大面积的矩形还不能确定，我们需要继续向右遍历。

    <img src="https://pic.leetcode-cn.com/414a06bad966eba25ef6c1281e5780381205d1c76c70b7a2561969bfe859eb01-image.png" alt="image.png" style="zoom:33%;" />

    2、然后看到到高度为 1 的柱形，这个时候以这个柱形为高度的矩形的最大面积还是不知道的。但是**它之前的以 2 为高度的最大面积的矩形是可以确定的**，这是因为这个 1 比 2 小 ，因为这个 1 卡在了这里 2 不能再向右边扩展了。

    <img src="https://pic.leetcode-cn.com/dcbed1d0cba33c059f3833a0da2e78e5e0a96370a415930acbdb126b44b398c8-image.png" alt="image.png" style="zoom:50%;" />

    我们计算一下以 2 为高度的最大矩形的面积是 2。其实这个时候，求解这个问题的思路其实已经慢慢打开了。如果已经确定了一个柱形的高度，我们可以无视它，将它去除。

    <img src="https://pic.leetcode-cn.com/da72cf520eb05a42725a8982bb27e507f9213b257b6f32bd6ef4a49e1d416a1f-image.png" alt="image.png" style="zoom:50%;" />

    3、遍历到高度为 5 的柱形，同样的以当前看到柱形为高度的矩形的最大面积也是不知道的，因为我们还要看右边高度的情况。那么它的左右有没有可以确定的柱形呢？没有，这是因为 5 比 1 大，我们看后面马上就出现了 6，不管是 1 这个柱形还是 5 这个柱形，都还可以向右边扩展

    <img src="https://pic.leetcode-cn.com/1bc87193557ab8c2a0bc2319aef66a12c4514aa45e9aa823eefa0e536289f0ed-image.png" alt="image.png" style="zoom:50%;" />

    4、接下来，遍历到高度为 6 的柱形，同样的，以柱形 1、5、6 为高度的最大矩形面积还是不能确定下来；

    <img src="https://pic.leetcode-cn.com/c34663bee2af70da626134a0426e7ac8e8e305a039abb43862a807fc1a7183a8-image.png" alt="image.png" style="zoom:50%;" />

    5、再接下来，遍历到高度为 2 的柱形。

    <img src="https://pic.leetcode-cn.com/180d06d75a6480c649d0d4ad6c31e755a4e9c03294d23b7fb28bbac3fb0c006a-image.png" alt="image.png" style="zoom:50%;" />

    发现了一件很神奇的事情，**高度为 6 的柱形对应的最大矩形的面积的宽度可以确定下来**，它就是夹在高度为 5 的柱形和高度为 2 的柱形之间的距离，它的高度是 6，宽度是 1。

    <img src="https://pic.leetcode-cn.com/5a60100c86cacd620424c807b6c062dd8f9990538da27420de81822557f8c782-image.png" alt="image.png" style="zoom:50%;" />

    将可以确定的柱形设置为虚线。

    <img src="https://pic.leetcode-cn.com/5cc53fec12ab6a3c790c30c47ac183e5fd0426a50a7cd46d813cac6718f658cc-image.png" alt="image.png" style="zoom:50%;" />

    接下来柱形 5 对应的最大面积的矩形的宽度也可以确定下来，它是夹在高度为 1 和高度为 2 的两个柱形之间的距离；

    <img src="https://pic.leetcode-cn.com/c4ae9e65efe6247d63b1ac2834015bf1be6270e0d323e6f02d7e1bb4ab6644ed-image.png" alt="image.png" style="zoom:50%;" />

    确定好以后，我们将它标成虚线。

    <img src="https://pic.leetcode-cn.com/973788fa2d6407c9db41c100e9fa9b9ef00e4891343089773b716316c6f3e7f2-image.png" alt="image.png" style="zoom:50%;" />

    我们发现了，只要是遇到了当前柱形的高度比它上一个柱形的高度严格小的时候，一定可以确定它之前的某些柱形的最大宽度，并且确定的柱形宽度的顺序是从右边向左边。
    这个现象告诉我们，在遍历的时候需要记录的信息就是遍历到的柱形的下标，它一左一右的两个柱形的下标的差就是这个面积最大的矩形对应的最大宽度。

  - S1: create a stack to store the index and height of each element

    S2: for each elment, while heights[i] > stack[-1] --> calculate the area and remove it --> set the index back to the original index

    S3: for the remaining rectangles in stack, area = (len - index) * height

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220620190715410.png" alt="image-20220620190715410" style="zoom: 67%;" />

    <img src="/Users/xinyuzhang/Library/Application Support/typora-user-images/image-20220620190726465.png" alt="image-20220620190726465" style="zoom:50%;" />

  - **Solution**

    ```python
    def largestRectangleArea(self, heights: List[int]) -> int:
      maxArea = 0
      
      stack = []    # store a pair: (index, height)
      for i, h in enumerate(heights):
        start = i
        while stack and h < stack[-1][1]:
          index, height = stack.pop()
          area = height * (i - index)
          maxArea = max(area, maxArea)
          start = index
        stack.append((start, h))
      
      for i, h in stack:
        maxArea = max(maxArea, h * (len(heights) - i))
      return maxArea
    ```

    - Time complexity: O(N) --> 输入数组里的每一个元素入栈一次，出栈一次

      Space complexity: O(N) --> create a stack 