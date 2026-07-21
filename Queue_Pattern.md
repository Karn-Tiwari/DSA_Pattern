# Ultimate Queue Pattern Guide - From Basics to Mastery

## Table of Contents

### Part A: Foundation
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [Queue Implementation in C++](#2-queue-implementation-in-c)
3. [Basic Operations & Use Cases](#3-basic-operations--use-cases)

### Part B: Core Patterns
4. [Pattern 1: BFS Level-Order Traversal](#4-pattern-1-bfs-level-order-traversal)
5. [Pattern 2: Sliding Window with Deque (Monotonic Queue)](#5-pattern-2-sliding-window-with-deque-monotonic-queue)
6. [Pattern 3: Circular Queue](#6-pattern-3-circular-queue)
7. [Pattern 4: Queue with Stack (Design Problems)](#7-pattern-4-queue-with-stack-design-problems)

### Part C: Advanced Patterns
8. [Pattern 5: Multi-Source BFS](#8-pattern-5-multi-source-bfs)
9. [Pattern 6: 0-1 BFS (Deque for Weighted Graph)](#9-pattern-6-0-1-bfs-deque-for-weighted-graph)
10. [Pattern 7: Task Scheduling with Queue](#10-pattern-7-task-scheduling-with-queue)

### Part D: Problem Solving
11. [Problem Recognition Framework](#11-problem-recognition-framework)
12. [Practice Problems](#12-practice-problems)
13. [Common Pitfalls](#13-common-pitfalls)
14. [Template Summary](#14-template-summary)

---

## 1. Core Concept & Philosophy

### What is a Queue Really About?

**Wrong Understanding:**
"A queue is just a line where you add to back and remove from front"

**Correct Understanding:**
"A queue is a **FIFO (First In, First Out) data structure** that processes elements in **order of arrival**, making it ideal for **BFS, scheduling, and streaming window problems**"

### Queue vs Stack vs Deque

```
STACK (LIFO)          QUEUE (FIFO)           DEQUE (Both Ends)
    │                     front → back           front ⇄ back
    │ push                    push→  ←pop         push/pop both ends
    │ pop                     pop←   →push
    └─ top                  front    back
Process: Last in first    First in first      Flexible access
Use: DFS, parentheses     BFS, scheduling     Sliding window max
```

### When to Use Queue?

**Key Indicators:**
1. Process elements in **order of discovery** (BFS)
2. **Level-by-level** traversal (trees, graphs)
3. **Sliding window maximum/minimum** (monotonic deque)
4. **Scheduling** or **simulation** problems
5. **Shortest path** in unweighted graphs
6. **Multi-source spreading** (rotting oranges, walls and gates)

---

## 2. Queue Implementation in C++

### Method 1: STL Queue

```cpp
#include <queue>
using namespace std;

queue<int> q;
q.push(10);       // Enqueue — O(1)
q.push(20);
q.front();        // Peek front — O(1)
q.pop();          // Dequeue — O(1)
q.empty();        // Check empty — O(1)
q.size();         // Get size — O(1)
```

### Method 2: STL Deque (Double-Ended Queue)

```cpp
#include <deque>
using namespace std;

deque<int> dq;
dq.push_back(10);    // Add to rear
dq.push_front(5);    // Add to front
dq.pop_back();       // Remove from rear
dq.pop_front();      // Remove from front — O(1)
dq.front();          // Peek front
dq.back();           // Peek back
```

### Method 3: Array-Based Circular Queue

```cpp
class CircularQueue {
private:
    vector<int> data;
    int front, rear, count, capacity;

public:
    CircularQueue(int k) : data(k), front(0), rear(-1), count(0), capacity(k) {}

    bool enQueue(int value) {
        if (isFull()) return false;
        rear = (rear + 1) % capacity;
        data[rear] = value;
        count++;
        return true;
    }

    bool deQueue() {
        if (isEmpty()) return false;
        front = (front + 1) % capacity;
        count--;
        return true;
    }

    int Front() { return isEmpty() ? -1 : data[front]; }
    int Rear()  { return isEmpty() ? -1 : data[rear]; }
    bool isEmpty() { return count == 0; }
    bool isFull()  { return count == capacity; }
};
```

### Method 4: Linked List Queue

```cpp
class LinkedQueue {
private:
    struct Node {
        int val;
        Node* next;
        Node(int v) : val(v), next(nullptr) {}
    };
    Node* head;
    Node* tail;

public:
    LinkedQueue() : head(nullptr), tail(nullptr) {}

    void push(int val) {
        Node* node = new Node(val);
        if (!tail) head = tail = node;
        else { tail->next = node; tail = node; }
    }

    void pop() {
        if (!head) return;
        Node* temp = head;
        head = head->next;
        if (!head) tail = nullptr;
        delete temp;
    }

    int front() { return head ? head->val : -1; }
    bool empty() { return head == nullptr; }
};
```

---

## 3. Basic Operations & Use Cases

### Real-World Analogies

```
PRINTER QUEUE:          BFS EXPLORATION:         TASK SCHEDULER:
Job1 → Job2 → Job3     Level 0: [root]          Task A (waiting)
First submitted        Level 1: [L, R]          Task B (waiting)
prints first           Level 2: [LL, LR, RL, RR] Round-robin execution
```

### Time Complexities

| Operation | Array Queue | Linked Queue | Deque |
|-----------|-------------|--------------|-------|
| Enqueue   | O(1)*       | O(1)         | O(1)  |
| Dequeue   | O(1)*       | O(1)         | O(1)  |
| Peek      | O(1)        | O(1)         | O(1)  |
| Search    | O(n)        | O(n)         | O(n)  |

*Amortized O(1) with circular buffer

---

## 4. Pattern 1: BFS Level-Order Traversal

### Concept

```
BFS explores nodes level by level using a queue:

        1              Queue: [1]
       / \              Process 1 → enqueue 2, 3
      2   3             Queue: [2, 3]
     / \                 Process 2 → enqueue 4, 5
    4   5                Queue: [3, 4, 5]
                         ...
```

### Universal BFS Template

```cpp
void bfs(int start, vector<vector<int>>& graph) {
    queue<int> q;
    vector<bool> visited(graph.size(), false);

    q.push(start);
    visited[start] = true;

    while (!q.empty()) {
        int node = q.front();
        q.pop();

        // Process node
        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push(neighbor);
            }
        }
    }
}
```

### Level-Order Traversal (Tree)

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;

    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        int levelSize = q.size();  // Nodes at current level
        vector<int> level;

        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            level.push_back(node->val);

            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(level);
    }
    return result;
}
```

### Problem: Binary Tree Level Order Traversal

**Dry Run:**

```
Tree:    3
        / \
       9  20
         /  \
        15   7

Queue: [3]           → Level 0: [3]
Queue: [9, 20]       → Level 1: [9, 20]
Queue: [20, 15, 7]   → Level 2: [15, 7]
Result: [[3], [9, 20], [15, 7]]
```

### Shortest Path in Unweighted Graph

```cpp
int shortestPath(vector<vector<int>>& graph, int src, int dest) {
    queue<pair<int, int>> q;  // {node, distance}
    vector<bool> visited(graph.size(), false);

    q.push({src, 0});
    visited[src] = true;

    while (!q.empty()) {
        auto [node, dist] = q.front();
        q.pop();

        if (node == dest) return dist;

        for (int neighbor : graph[node]) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.push({neighbor, dist + 1});
            }
        }
    }
    return -1;
}
```

---

## 5. Pattern 2: Sliding Window with Deque (Monotonic Queue)

### Concept

```
For "maximum in sliding window of size k":
Maintain a DECREASING deque of indices.
Front always holds index of maximum in current window.

Array: [1, 3, -1, -3, 5, 3, 6, 7], k=3

Window [1,3,-1]:  deque=[1,3] (indices), max=3
Window [3,-1,-3]: deque=[3,2], max=3
Window [-1,-3,5]:  deque=[4], max=5
...
```

### Template: Sliding Window Maximum

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq;  // Stores indices, decreasing values
    vector<int> result;

    for (int i = 0; i < nums.size(); i++) {
        // Remove indices outside window
        while (!dq.empty() && dq.front() <= i - k) {
            dq.pop_front();
        }

        // Maintain decreasing order
        while (!dq.empty() && nums[dq.back()] <= nums[i]) {
            dq.pop_back();
        }

        dq.push_back(i);

        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    return result;
}
```

**Why It Works:**
- Each element pushed once, popped at most once → O(n)
- Front of deque always has maximum for current window

### Sliding Window Minimum

```cpp
// Same template, change <= to >= for increasing deque
while (!dq.empty() && nums[dq.back()] >= nums[i]) {
    dq.pop_back();
}
```

---

## 6. Pattern 3: Circular Queue

### Concept

```
Fixed-size array with wrap-around:

capacity = 5
[_, _, _, _, _]
 ↑front    ↑rear

After enQueue(1,2,3,4,5) and deQueue() twice:
[_, _, 3, 4, 5]
       ↑front  ↑rear

enQueue(6): wraps to front
[6, _, 3, 4, 5]
 ↑rear ↑front
```

### Design Circular Queue (LeetCode 622)

```cpp
class MyCircularQueue {
    vector<int> data;
    int front, rear, size, capacity;

public:
    MyCircularQueue(int k) : data(k), front(0), rear(-1), size(0), capacity(k) {}

    bool enQueue(int value) {
        if (isFull()) return false;
        rear = (rear + 1) % capacity;
        data[rear] = value;
        size++;
        return true;
    }

    bool deQueue() {
        if (isEmpty()) return false;
        front = (front + 1) % capacity;
        size--;
        return true;
    }

    int Front() { return isEmpty() ? -1 : data[front]; }
    int Rear()  { return isEmpty() ? -1 : data[rear]; }
    bool isEmpty() { return size == 0; }
    bool isFull()  { return size == capacity; }
};
```

---

## 7. Pattern 4: Queue with Stack (Design Problems)

### Implement Queue using Stacks

**Idea:** Use two stacks — `inStack` for enqueue, `outStack` for dequeue.

```cpp
class MyQueue {
    stack<int> inStack, outStack;

    void push(int x) {
        inStack.push(x);
    }

    int pop() {
        peek();
        int val = outStack.top();
        outStack.pop();
        return val;
    }

    int peek() {
        if (outStack.empty()) {
            while (!inStack.empty()) {
                outStack.push(inStack.top());
                inStack.pop();
            }
        }
        return outStack.top();
    }

    bool empty() {
        return inStack.empty() && outStack.empty();
    }
};
```

**Amortized O(1):** Each element moved between stacks at most once.

### Implement Stack using Queues

```cpp
class MyStack {
    queue<int> q;

public:
    void push(int x) {
        q.push(x);
        // Rotate: bring new element to front
        for (int i = 0; i < q.size() - 1; i++) {
            q.push(q.front());
            q.pop();
        }
    }

    int pop() {
        int val = q.front();
        q.pop();
        return val;
    }

    int top() { return q.front(); }
    bool empty() { return q.empty(); }
};
```

---

## 8. Pattern 5: Multi-Source BFS

### Concept

```
Instead of BFS from one source, start from ALL sources simultaneously.
Used for: rotting oranges, walls and gates, nearest 0 in matrix.
```

### Problem: Rotting Oranges

```cpp
int orangesRotting(vector<vector<int>>& grid) {
    int rows = grid.size(), cols = grid[0].size();
    queue<pair<int, int>> q;
    int fresh = 0;

    // Enqueue all rotten oranges
    for (int r = 0; r < rows; r++) {
        for (int c = 0; c < cols; c++) {
            if (grid[r][c] == 2) q.push({r, c});
            else if (grid[r][c] == 1) fresh++;
        }
    }

    if (fresh == 0) return 0;

    int minutes = 0;
    int dirs[] = {0, 1, 0, -1, 0};

    while (!q.empty() && fresh > 0) {
        int size = q.size();
        minutes++;

        for (int i = 0; i < size; i++) {
            auto [r, c] = q.front();
            q.pop();

            for (int d = 0; d < 4; d++) {
                int nr = r + dirs[d], nc = c + dirs[d + 1];
                if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && grid[nr][nc] == 1) {
                    grid[nr][nc] = 2;
                    fresh--;
                    q.push({nr, nc});
                }
            }
        }
    }
    return fresh == 0 ? minutes : -1;
}
```

**Key Insight:** Process level by level (each level = 1 minute).

---

## 9. Pattern 6: 0-1 BFS (Deque for Weighted Graph)

### Concept

```
For graphs with edge weights 0 or 1:
- Weight 0 edges → push FRONT of deque (same priority)
- Weight 1 edges → push BACK of deque (lower priority)

Finds shortest path in O(V + E) — faster than Dijkstra for 0-1 weights.
```

```cpp
int zeroOneBFS(vector<vector<pair<int, int>>>& graph, int src, int dest) {
    int n = graph.size();
    vector<int> dist(n, INT_MAX);
    deque<int> dq;

    dist[src] = 0;
    dq.push_front(src);

    while (!dq.empty()) {
        int node = dq.front();
        dq.pop_front();

        for (auto [neighbor, weight] : graph[node]) {
            if (dist[node] + weight < dist[neighbor]) {
                dist[neighbor] = dist[node] + weight;
                if (weight == 0) dq.push_front(neighbor);
                else dq.push_back(neighbor);
            }
        }
    }
    return dist[dest];
}
```

---

## 10. Pattern 7: Task Scheduling with Queue

### Problem: Task Scheduler

**Key Insight:** Schedule most frequent tasks first with cooldown gaps.

```cpp
int leastInterval(vector<char>& tasks, int n) {
    vector<int> freq(26, 0);
    for (char t : tasks) freq[t - 'A']++;

    priority_queue<int> maxHeap;
    for (int f : freq) if (f > 0) maxHeap.push(f);

    queue<pair<int, int>> cooldown;  // {count, available_time}
    int time = 0;

    while (!maxHeap.empty() || !cooldown.empty()) {
        time++;

        if (!maxHeap.empty()) {
            int count = maxHeap.top();
            maxHeap.pop();
            count--;

            if (count > 0) {
                cooldown.push({count, time + n});
            }
        }

        if (!cooldown.empty() && cooldown.front().second == time) {
            maxHeap.push(cooldown.front().first);
            cooldown.pop();
        }
    }
    return time;
}
```

---

## 11. Problem Recognition Framework

```
START: Should I use a Queue?
│
├─ Shortest path in UNWEIGHTED graph?
│   └─ YES → BFS with queue
│
├─ Level-by-level processing (tree/graph/matrix)?
│   └─ YES → BFS, track level size
│
├─ Sliding window max/min?
│   └─ YES → Monotonic DEQUE
│
├─ Multiple sources spreading simultaneously?
│   └─ YES → Multi-source BFS
│
├─ Edge weights only 0 and 1?
│   └─ YES → 0-1 BFS with deque
│
├─ Design FIFO structure or simulate scheduling?
│   └─ YES → Queue / circular queue
│
└─ Need access from BOTH ends?
    └─ YES → Deque
```

### Pattern Selection Table

| Problem Type | Data Structure | Time | Space |
|-------------|----------------|------|-------|
| BFS traversal | queue | O(V+E) | O(V) |
| Level order | queue + level size | O(n) | O(n) |
| Sliding window max | monotonic deque | O(n) | O(k) |
| Multi-source BFS | queue (all sources) | O(mn) | O(mn) |
| 0-1 BFS | deque | O(V+E) | O(V) |
| Task scheduling | queue + max heap | O(n) | O(26) |
| Circular queue | array + mod | O(1) ops | O(k) |

---

## 12. Practice Problems

### Easy
1. Implement Queue using Stacks - https://leetcode.com/problems/implement-queue-using-stacks/
2. Implement Stack using Queues - https://leetcode.com/problems/implement-stack-using-queues/
3. Binary Tree Level Order Traversal - https://leetcode.com/problems/binary-tree-level-order-traversal/
4. Number of Recent Calls - https://leetcode.com/problems/number-of-recent-calls/
5. Design Circular Queue - https://leetcode.com/problems/design-circular-queue/

### Medium
6. Sliding Window Maximum - https://leetcode.com/problems/sliding-window-maximum/
7. Rotting Oranges - https://leetcode.com/problems/rotting-oranges/
8. Walls and Gates - https://leetcode.com/problems/walls-and-gates/
9. Task Scheduler - https://leetcode.com/problems/task-scheduler/
10. Perfect Squares - https://leetcode.com/problems/perfect-squares/
11. Open the Lock - https://leetcode.com/problems/open-the-lock/
12. Jump Game III - https://leetcode.com/problems/jump-game-iii/
13. Shortest Path in Binary Matrix - https://leetcode.com/problems/shortest-path-in-binary-matrix/
14. As Far from Land as Possible - https://leetcode.com/problems/as-far-from-land-as-possible/
15. Design Circular Deque - https://leetcode.com/problems/design-circular-deque/

### Hard
16. Shortest Path in a Grid with Obstacles Elimination - https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/
17. Bus Routes - https://leetcode.com/problems/bus-routes/
18. Minimum Cost to Make at Least One Valid Path in a Grid - https://leetcode.com/problems/minimum-cost-to-make-at-least-one-valid-path-in-a-grid/

---

## 13. Common Pitfalls

### Pitfall 1: Not Tracking Level Size in BFS

```cpp
// WRONG: Level boundaries lost
while (!q.empty()) {
    TreeNode* node = q.front(); q.pop();
    // All nodes mixed together
}

// RIGHT: Capture level size before processing
int levelSize = q.size();
for (int i = 0; i < levelSize; i++) { ... }
```

### Pitfall 2: Forgetting to Mark Visited on Enqueue

```cpp
// WRONG: Mark visited on dequeue → duplicates in queue
q.push(neighbor);
// ... later ...
visited[neighbor] = true;

// RIGHT: Mark visited when adding to queue
if (!visited[neighbor]) {
    visited[neighbor] = true;
    q.push(neighbor);
}
```

### Pitfall 3: Wrong Monotonic Deque Direction

```cpp
// For MAXIMUM: decreasing deque, pop back when <= current
while (!dq.empty() && nums[dq.back()] <= nums[i]) dq.pop_back();

// For MINIMUM: increasing deque, pop back when >= current
while (!dq.empty() && nums[dq.back()] >= nums[i]) dq.pop_back();
```

### Pitfall 4: Circular Queue Index Overflow

```cpp
// WRONG
rear = rear + 1;

// RIGHT: Wrap with modulo
rear = (rear + 1) % capacity;
```

---

## 14. Template Summary

### BFS Template
```cpp
queue<int> q;
vector<bool> visited(n, false);
q.push(start);
visited[start] = true;

while (!q.empty()) {
    int node = q.front(); q.pop();
    for (int neighbor : graph[node]) {
        if (!visited[neighbor]) {
            visited[neighbor] = true;
            q.push(neighbor);
        }
    }
}
```

### Level-Order BFS
```cpp
while (!q.empty()) {
    int levelSize = q.size();
    for (int i = 0; i < levelSize; i++) {
        auto node = q.front(); q.pop();
        // process node, enqueue children
    }
}
```

### Monotonic Deque (Window Max)
```cpp
for (int i = 0; i < n; i++) {
    while (!dq.empty() && dq.front() <= i - k) dq.pop_front();
    while (!dq.empty() && nums[dq.back()] <= nums[i]) dq.pop_back();
    dq.push_back(i);
    if (i >= k - 1) result.push_back(nums[dq.front()]);
}
```

### Multi-Source BFS
```cpp
// Enqueue ALL sources first
for (each source) q.push(source);

while (!q.empty()) {
    int size = q.size();
    for (int i = 0; i < size; i++) {
        // process and spread to neighbors
    }
}
```

---

## Final Thoughts

**The Power of Queue Patterns:**
- BFS guarantees shortest path in unweighted graphs
- Level-order processing unlocks tree and grid problems
- Monotonic deque transforms sliding window from O(nk) to O(n)
- Multi-source BFS elegantly handles simultaneous spreading

**How to Master:**
1. Default to BFS for "shortest" or "minimum steps" problems
2. Use deque when window max/min is needed
3. Always mark visited on enqueue, not dequeue
4. Track level size for level-by-level problems

---

*Queues process in order — master them and BFS becomes second nature!*
