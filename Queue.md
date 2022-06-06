# Queue

## 1. 622 [Design Circular Queue](https://leetcode.com/problems/design-circular-queue/description/)

|  Category  |   Difficulty    | Likes | Dislikes |                  Tag                  |
| :--------: | :-------------: | :---: | :------: | :-----------------------------------: |
| algorithms | Medium (48.73%) | 1693  |   171    | ==Queue==; Array; Linked List; Design |

Design your implementation of the circular queue. The circular queue is a linear data structure in which the operations are performed based on FIFO (First In First Out) principle and the last position is connected back to the first position to make a circle. It is also called **"Ring Buffer"**.

One of the benefits of the circular queue is that we can make use of the spaces in front of the queue. In a normal queue, once the queue becomes full, we cannot insert the next element even if there is a space in front of the queue. But using the circular queue, we can use the space to store new values.

Implementation the `MyCircularQueue` class:

- `MyCircularQueue(k)` Initializes the object with the size of the queue to be `k`.
- `int Front()` Gets the front item from the queue. If the queue is empty, return `-1`.
- `int Rear()` Gets the last item from the queue. If the queue is empty, return `-1`.
- `boolean enQueue(int value)` Inserts an element into the circular queue. Return `true` if the operation is successful.
- `boolean deQueue()` Deletes an element from the circular queue. Return `true` if the operation is successful.
- `boolean isEmpty()` Checks whether the circular queue is empty or not.
- `boolean isFull()` Checks whether the circular queue is full or not.

You must solve the problem without using the built-in queue data structure in your programming language. 

**Example 1:**

```Python
Input
["MyCircularQueue", "enQueue", "enQueue", "enQueue", "enQueue", "Rear", "isFull", "deQueue", "enQueue", "Rear"]
[[3], [1], [2], [3], [4], [], [], [], [4], []]
Output
[null, true, true, true, false, 3, true, true, true, 4]

Explanation
MyCircularQueue myCircularQueue = new MyCircularQueue(3);
myCircularQueue.enQueue(1); // return True
myCircularQueue.enQueue(2); // return True
myCircularQueue.enQueue(3); // return True
myCircularQueue.enQueue(4); // return False
myCircularQueue.Rear();     // return 3
myCircularQueue.isFull();   // return True
myCircularQueue.deQueue();  // return True
myCircularQueue.enQueue(4); // return True
myCircularQueue.Rear();     // return 4
```

- **Thoughts**

  - <u>Data Structure:</u> **Ring** whose head and tail are connected --> no such structure --> can simulate by using **1-D array** --> **By operating the *indices* to construct a virtual ring (start + end)** ==很多复杂数据结构都可以通过数组实现==

  - <u>Variable:</u> queue, head index, capacity, count (length of the queue)

    ==tail index = (head + count - 1) % capacity== <font color = red>对于环形循环，索引值计算记得加上(% capacity)!!</font>

- **Solution**

  ```python
  class MyCircularQueue:
  
      def __init__(self, k: int):
          self.queue = k * [0]
          self.head = 0
          self.capacity = k
          self.count = 0
          
      def enQueue(self, value: int) -> bool:
          if self.isFull():
              return False
          self.queue[(self.head + self.count) % self.capacity] = value
          self.count += 1
          return True
  
      def deQueue(self) -> bool:
          if self.isEmpty():
              return False
          self.head = (self.head + 1) % self.capacity
          self.count -= 1
          return True        
                 
      def Front(self) -> int:
          if self.isEmpty():
              return -1
          return self.queue[self.head]
  
      def Rear(self) -> int:
          if self.isEmpty():
              return -1
          return self.queue[(self.head + self.count - 1) % self.capacity]
          
      def isEmpty(self) -> bool:
          return self.count == 0
          
      def isFull(self) -> bool:
          return self.count == self.capacity
  ```

  Time Complexity : O(1)

  Space Complexity: O(N) --> create a list with size k

## 2. 641 [Design Circular Deque](https://leetcode.com/problems/design-circular-deque/description/)

|  Category  |   Difficulty    | Likes | Dislikes |                  Tag                  |
| :--------: | :-------------: | :---: | :------: | :-----------------------------------: |
| algorithms | Medium (57.26%) |  703  |    57    | ==Queue==; Array; Linked List; Design |

Design your implementation of the circular double-ended queue (deque).

Implement the `MyCircularDeque` class:

- `MyCircularDeque(int k)` Initializes the deque with a maximum size of `k`.
- `boolean insertFront()` Adds an item at the front of Deque. Returns `true` if the operation is successful, or `false` otherwise.
- `boolean insertLast()` Adds an item at the rear of Deque. Returns `true` if the operation is successful, or `false` otherwise.
- `boolean deleteFront()` Deletes an item from the front of Deque. Returns `true` if the operation is successful, or `false` otherwise.
- `boolean deleteLast()` Deletes an item from the rear of Deque. Returns `true` if the operation is successful, or `false` otherwise.
- `int getFront()` Returns the front item from the Deque. Returns `-1` if the deque is empty.
- `int getRear()` Returns the last item from Deque. Returns `-1` if the deque is empty.
- `boolean isEmpty()` Returns `true` if the deque is empty, or `false` otherwise.
- `boolean isFull()` Returns `true` if the deque is full, or `false` otherwise.

**Example 1:**

```Python
Input
["MyCircularDeque", "insertLast", "insertLast", "insertFront", "insertFront", "getRear", "isFull", "deleteLast", "insertFront", "getFront"]
[[3], [1], [2], [3], [4], [], [], [], [4], []]
Output
[null, true, true, true, false, 2, true, true, true, 4]

Explanation
MyCircularDeque myCircularDeque = new MyCircularDeque(3);
myCircularDeque.insertLast(1);  // return True
myCircularDeque.insertLast(2);  // return True
myCircularDeque.insertFront(3); // return True
myCircularDeque.insertFront(4); // return False, the queue is full.
myCircularDeque.getRear();      // return 2
myCircularDeque.isFull();       // return True
myCircularDeque.deleteLast();   // return True
myCircularDeque.insertFront(4); // return True
myCircularDeque.getFront();     // return 4
```

- **Thoughts**
  - Similar to 622 --> still set 4 variables: <u>queue, head, count, capacity</u>

- **Solution**

  ```python
  class MyCircularDeque: 
  
      # Initialize queue, head, count and capacity
      def __init__(self, k: int):
          self.queue = k * [0]
          self.head = 0
          self.count = 0
          self.capacity = k
          
      # Check if the queue is full
      # queue[head - 1] = value
      # count + 1
      # head - 1
      def insertFront(self, value: int) -> bool:
          if self.isFull():
              return False
          self.head = (self.head - 1) % self.capacity
          self.queue[self.head] = value
          self.count += 1
          return True
  
      def insertLast(self, value: int) -> bool:
          if self.isFull():
              return False
          self.queue[(self.head + self.count) % self.capacity] = value
          self.count += 1   
          return True
  
      # Check if the queue is empty
      # head += 1
      # count -= 1
      def deleteFront(self) -> bool:
          if self.isEmpty():
              return False
          self.head = (self.head + 1) % self.capacity
          self.count -= 1
          return True
  
      def deleteLast(self) -> bool:
          if self.isEmpty():
              return False
          self.count -= 1
          return True
          
      def getFront(self) -> int:
          return -1 if self.isEmpty() else self.queue[self.head]
  
      def getRear(self) -> int:
          return -1 if self.isEmpty() else self.queue[(self.head + self.count - 1) % self.capacity]
  
      def isEmpty(self) -> bool:
          return self.count == 0
          
      def isFull(self) -> bool:
          return self.count == self.capacity
  ```

  - ==Mistake!==:
    - 表达tail索引的时候记得要-1！`tail index = (self.head + self.count - 1) % self.capacity`
    - For `deleteFront`, head索引应该+1，指向后面一个元素