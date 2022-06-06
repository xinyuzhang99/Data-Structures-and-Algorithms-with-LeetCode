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