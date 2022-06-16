# Stack 栈

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