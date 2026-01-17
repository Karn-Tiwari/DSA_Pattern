# Complete Monotonic Stack Guide - From Basics to Mastery

## Table of Contents

### Part A: Foundation
1. [Understanding Stack Data Structure](#part-a1-understanding-stack-data-structure)
2. [Stack Implementation in C++](#part-a2-stack-implementation-in-c)
3. [Stack Use Cases & Applications](#part-a3-stack-use-cases--applications)

### Part B: Monotonic Stack
4. [What is a Monotonic Stack?](#part-b1-what-is-a-monotonic-stack)
5. [Why Monotonic Stack Works](#part-b2-why-monotonic-stack-works)
6. [Four Types of Monotonic Stacks](#part-b3-four-types-of-monotonic-stacks)

### Part C: Templates & Patterns
7. [Next Greater Element Pattern](#part-c1-next-greater-element-pattern)
8. [Previous Greater Element Pattern](#part-c2-previous-greater-element-pattern)
9. [Next Smaller Element Pattern](#part-c3-next-smaller-element-pattern)
10. [Previous Smaller Element Pattern](#part-c4-previous-smaller-element-pattern)
11. [Combined Patterns](#part-c5-combined-patterns)

### Part D: Advanced Applications
12. [Histogram Problems](#part-d1-histogram-problems)
13. [Subarray Problems](#part-d2-subarray-problems)
14. [Circular Array Problems](#part-d3-circular-array-problems)
15. [Problem Recognition Framework](#part-d4-problem-recognition-framework)

---

# PART A: FOUNDATION

## Part A1: Understanding Stack Data Structure

### What is a Stack?

```
A Stack is a LINEAR data structure that follows LIFO principle:
LIFO = Last In, First Out

Think of it like a stack of plates:
- You can only add a plate on top (PUSH)
- You can only remove the top plate (POP)
- You can only see the top plate (TOP/PEEK)

Visual:
    │     │
    │  3  │  ← TOP (Last In, First Out)
    │  2  │
    │  1  │  ← BOTTOM (First In, Last Out)
    └─────┘
```

### Stack Operations

```
1. PUSH(x)    - Add element x to top      O(1)
2. POP()      - Remove top element        O(1)
3. TOP/PEEK() - View top element          O(1)
4. ISEMPTY()  - Check if empty            O(1)
5. SIZE()     - Get number of elements    O(1)
```

### Real-World Analogy

```
Stack of Books in a Drawer:
┌─────────────┐
│   Book 3    │ ← Top (Last added, first to remove)
├─────────────┤
│   Book 2    │
├─────────────┤
│   Book 1    │ ← Bottom (First added, last to remove)
└─────────────┘

Operations:
- Add book → Place on top (PUSH)
- Remove book → Take from top (POP)
- See which book → Look at top (PEEK)
```

---

## Part A2: Stack Implementation in C++

### Method 1: Using STL Stack

```cpp
#include <stack>
#include <iostream>
using namespace std;

int main() {
    stack<int> st;

    // Push elements
    st.push(10);
    st.push(20);
    st.push(30);

    // Top element
    cout << "Top: " << st.top() << endl;  // 30

    // Pop element
    st.pop();  // Removes 30
    cout << "Top after pop: " << st.top() << endl;  // 20

    // Size and empty check
    cout << "Size: " << st.size() << endl;  // 2
    cout << "Empty: " << st.empty() << endl;  // 0 (false)

    return 0;
}
```

### Method 2: Array-Based Implementation

```cpp
class Stack {
private:
    int* arr;
    int top;
    int capacity;

public:
    Stack(int size) {
        arr = new int[size];
        capacity = size;
        top = -1;
    }

    ~Stack() {
        delete[] arr;
    }

    void push(int x) {
        if (top >= capacity - 1) {
            cout << "Stack Overflow!" << endl;
            return;
        }
        arr[++top] = x;
    }

    void pop() {
        if (top < 0) {
            cout << "Stack Underflow!" << endl;
            return;
        }
        top--;
    }

    int peek() {
        if (top < 0) {
            cout << "Stack is empty!" << endl;
            return -1;
        }
        return arr[top];
    }

    bool isEmpty() {
        return top < 0;
    }

    int size() {
        return top + 1;
    }
};
```

### Method 3: Linked List Implementation

```cpp
class Node {
public:
    int data;
    Node* next;

    Node(int val) : data(val), next(nullptr) {}
};

class Stack {
private:
    Node* topNode;
    int count;

public:
    Stack() : topNode(nullptr), count(0) {}

    void push(int x) {
        Node* newNode = new Node(x);
        newNode->next = topNode;
        topNode = newNode;
        count++;
    }

    void pop() {
        if (isEmpty()) {
            cout << "Stack is empty!" << endl;
            return;
        }
        Node* temp = topNode;
        topNode = topNode->next;
        delete temp;
        count--;
    }

    int top() {
        if (isEmpty()) return -1;
        return topNode->data;
    }

    bool isEmpty() {
        return topNode == nullptr;
    }

    int size() {
        return count;
    }
};
```

---

## Part A3: Stack Use Cases & Applications

### 1. Function Call Stack (Recursion)

```cpp
// When you call a function, it's pushed onto call stack

void functionC() {
    cout << "In C" << endl;
}  // C pops off

void functionB() {
    cout << "In B" << endl;
    functionC();
}  // B pops off

void functionA() {
    cout << "In A" << endl;
    functionB();
}  // A pops off

int main() {
    functionA();
    return 0;
}

Call Stack Visual:
Step 1: main() called
    │ main │

Step 2: A called
    │  A   │
    │ main │

Step 3: B called
    │  B   │
    │  A   │
    │ main │

Step 4: C called
    │  C   │ ← Currently executing
    │  B   │
    │  A   │
    │ main │

Step 5: C returns, pops off
    │  B   │
    │  A   │
    │ main │

... and so on
```

### 2. Expression Evaluation

```cpp
// Infix: 2 + 3 * 4
// Postfix: 2 3 4 * +

int evaluatePostfix(string expr) {
    stack<int> st;

    for (char c : expr) {
        if (isdigit(c)) {
            st.push(c - '0');
        } else {
            int val2 = st.top(); st.pop();
            int val1 = st.top(); st.pop();

            switch(c) {
                case '+': st.push(val1 + val2); break;
                case '-': st.push(val1 - val2); break;
                case '*': st.push(val1 * val2); break;
                case '/': st.push(val1 / val2); break;
            }
        }
    }

    return st.top();
}
```

### 3. Parentheses Matching

```cpp
bool isValidParentheses(string s) {
    stack<char> st;
    unordered_map<char, char> pairs = {
        {')', '('}, {']', '['}, {'}', '{'}
    };

    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty() || st.top() != pairs[c]) {
                return false;
            }
            st.pop();
        }
    }

    return st.empty();
}
```

### 4. Undo/Redo Operations

```cpp
class TextEditor {
private:
    stack<string> undoStack;
    stack<string> redoStack;
    string currentText;

public:
    void write(string text) {
        undoStack.push(currentText);
        currentText = text;
        // Clear redo stack on new write
        while (!redoStack.empty()) redoStack.pop();
    }

    void undo() {
        if (undoStack.empty()) return;
        redoStack.push(currentText);
        currentText = undoStack.top();
        undoStack.pop();
    }

    void redo() {
        if (redoStack.empty()) return;
        undoStack.push(currentText);
        currentText = redoStack.top();
        redoStack.pop();
    }
};
```

### 5. Browser Back/Forward

```cpp
class Browser {
private:
    stack<string> backStack;
    stack<string> forwardStack;
    string currentPage;

public:
    void visit(string url) {
        backStack.push(currentPage);
        currentPage = url;
        // Clear forward history
        while (!forwardStack.empty()) forwardStack.pop();
    }

    void back() {
        if (backStack.empty()) return;
        forwardStack.push(currentPage);
        currentPage = backStack.top();
        backStack.pop();
    }

    void forward() {
        if (forwardStack.empty()) return;
        backStack.push(currentPage);
        currentPage = forwardStack.top();
        forwardStack.pop();
    }
};
```

---

# PART B: MONOTONIC STACK

## Part B1: What is a Monotonic Stack?

### Definition

```
A Monotonic Stack is a stack where elements are
ALWAYS in INCREASING or DECREASING order.

It's not a different data structure - it's a TECHNIQUE
that maintains monotonicity while using a regular stack.
```

### Visual Comparison

```
Regular Stack:           Monotonic Increasing Stack:
  │  5  │                   │  9  │
  │  3  │                   │  7  │
  │  8  │                   │  5  │
  │  2  │                   │  3  │
  └────┘                    └────┘
  (No order)                (Bottom to Top: 3<5<7<9)

Monotonic Decreasing Stack:
  │  2  │
  │  5  │
  │  7  │
  │  9  │
  └────┘
  (Bottom to Top: 9>7>5>2)
```

### Key Insight

```
When pushing a new element:
  If it violates monotonicity → POP elements until valid
  Then PUSH the new element

This maintains the monotonic property!
```

---

## Part B2: Why Monotonic Stack Works

### The Magic Behind It

```
Problem: Find the NEXT GREATER element for each element

Array: [2, 1, 2, 4, 3]

Brute Force (O(n²)):
for i in 0..n:
    for j in i+1..n:
        if arr[j] > arr[i]:
            answer[i] = arr[j]
            break

Monotonic Stack (O(n)):
- Use decreasing stack
- When we find element > stack top
  → stack top has found its next greater!
```

### Why This Works - The Proof

```
Consider decreasing stack: [9, 7, 4]

When we encounter 6:
1. Pop 4 (because 6 > 4)
   → 6 is the NEXT GREATER of 4 ✓

2. Don't pop 7 (because 6 < 7)
   → 7 is still waiting for its next greater

3. Push 6
   → Stack becomes [9, 7, 6]

Key Insight:
- Elements we pop: found their answer (current element)
- Elements we don't pop: still searching
- We process each element exactly once!
```

### Time Complexity Analysis

```
Each element is:
1. Pushed ONCE: O(1)
2. Popped AT MOST ONCE: O(1)
3. Compared a few times: O(1)

Total operations per element: O(1)
Total for n elements: O(n)

This is why monotonic stack is O(n)!
```

---

## Part B3: Four Types of Monotonic Stacks

### Type 1: Strictly Increasing

```
Property: stack[i] < stack[i+1] (bottom to top)
Operator: while (!st.empty() && arr[i] <= st.top())

Example: [3, 5, 7, 9]
  │  9  │
  │  7  │
  │  5  │
  │  3  │
  └────┘

Use: Find NEXT SMALLER or PREVIOUS SMALLER
```

### Type 2: Non-Decreasing (Monotonic Increasing)

```
Property: stack[i] <= stack[i+1] (bottom to top)
Operator: while (!st.empty() && arr[i] < st.top())

Example: [3, 5, 5, 7, 9]
  │  9  │
  │  7  │
  │  5  │ Equal values allowed!
  │  5  │
  │  3  │
  └────┘

Use: Find NEXT SMALLER (with equal)
```

### Type 3: Strictly Decreasing

```
Property: stack[i] > stack[i+1] (bottom to top)
Operator: while (!st.empty() && arr[i] >= st.top())

Example: [9, 7, 5, 3]
  │  3  │
  │  5  │
  │  7  │
  │  9  │
  └────┘

Use: Find NEXT GREATER or PREVIOUS GREATER
```

### Type 4: Non-Increasing (Monotonic Decreasing)

```
Property: stack[i] >= stack[i+1] (bottom to top)
Operator: while (!st.empty() && arr[i] > st.top())

Example: [9, 7, 7, 5, 3]
  │  3  │
  │  5  │
  │  7  │ Equal values allowed!
  │  7  │
  │  9  │
  └────┘

Use: Find NEXT GREATER (with equal)
```

### Quick Reference Table

| Stack Type | Operator | Use Case |
|------------|----------|----------|
| Strictly Increasing | `arr[i] <=` | Next/Previous Smaller (strict) |
| Non-Decreasing | `arr[i] <` | Next/Previous Smaller (or equal) |
| Strictly Decreasing | `arr[i] >=` | Next/Previous Greater (strict) |
| Non-Increasing | `arr[i] >` | Next/Previous Greater (or equal) |

---

# PART C: TEMPLATES & PATTERNS

## Part C1: Next Greater Element Pattern

### Concept

```
For each element, find the first element to its RIGHT
that is GREATER than it.

Array: [2, 1, 2, 4, 3]
Answer:[4, 2, 4, -1, -1]

2 → 4 (first greater to right)
1 → 2
2 → 4
4 → -1 (no greater element)
3 → -1
```

### Why Decreasing Stack?

```
We maintain DECREASING stack (bottom to top)

[9, 7, 4] ← Current stack

When we see 6:
- 6 > 4, so POP 4 → 6 is next greater of 4
- 6 < 7, so STOP → 7 still waiting
- PUSH 6 → [9, 7, 6]

The stack stores elements still searching for their answer!
```

### Template

```cpp
vector<int> nextGreaterElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);  // Default: no answer
    stack<int> st;  // Store indices

    // Traverse LEFT to RIGHT
    for (int i = 0; i < n; i++) {
        // While current element is greater than stack top
        while (!st.empty() && arr[i] > arr[st.top()]) {
            int idx = st.top();
            st.pop();
            result[idx] = arr[i];  // Found next greater!
        }

        st.push(i);  // Push current index
    }

    return result;
}
```

### Detailed Dry Run

```cpp
arr = [2, 1, 2, 4, 3]
result = [-1, -1, -1, -1, -1]
stack = []

i=0, arr[0]=2:
  stack empty, push 0
  stack = [0]

i=1, arr[1]=1:
  1 > arr[0]=2? NO
  push 1
  stack = [0, 1]

i=2, arr[2]=2:
  2 > arr[1]=1? YES
    result[1] = 2
    pop 1
  stack = [0]
  2 > arr[0]=2? NO
  push 2
  stack = [0, 2]

i=3, arr[3]=4:
  4 > arr[2]=2? YES
    result[2] = 4
    pop 2
  stack = [0]
  4 > arr[0]=2? YES
    result[0] = 4
    pop 0
  stack = []
  push 3
  stack = [3]

i=4, arr[4]=3:
  3 > arr[3]=4? NO
  push 4
  stack = [3, 4]

Final result = [4, 2, 4, -1, -1]
```

### Visual Step-by-Step

```
Array: [2, 1, 2, 4, 3]
Index:  0  1  2  3  4

Step 1: i=0, value=2
Stack: [0]
Result: [-1, -1, -1, -1, -1]

Step 2: i=1, value=1
Stack: [0, 1]  (1 < 2, just push)
Result: [-1, -1, -1, -1, -1]

Step 3: i=2, value=2
  2 > 1 → POP index 1, result[1] = 2
Stack: [0, 2]
Result: [-1, 2, -1, -1, -1]

Step 4: i=3, value=4
  4 > 2 → POP index 2, result[2] = 4
  4 > 2 → POP index 0, result[0] = 4
Stack: [3]
Result: [4, 2, 4, -1, -1]

Step 5: i=4, value=3
  3 < 4 → just push
Stack: [3, 4]
Result: [4, 2, 4, -1, -1]

Indices 3 and 4 remain in stack → no next greater
```

---

## Part C2: Previous Greater Element Pattern

### Concept

```
For each element, find the first element to its LEFT
that is GREATER than it.

Array: [4, 5, 2, 10, 8]
Answer:[-1, -1, 5, -1, 10]

4 → -1 (no previous greater)
5 → -1
2 → 5 (first greater to left)
10 → -1
8 → 10
```

### Key Difference from Next Greater

```
NEXT Greater: When we POP → current is answer
PREVIOUS Greater: When we PUSH → top of stack is answer

Why? Because stack contains all previous larger elements!
```

### Template

```cpp
vector<int> previousGreaterElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;  // Decreasing stack

    // Traverse LEFT to RIGHT
    for (int i = 0; i < n; i++) {
        // Pop smaller or equal elements
        while (!st.empty() && arr[st.top()] <= arr[i]) {
            st.pop();
        }

        // Top of stack is previous greater
        if (!st.empty()) {
            result[i] = arr[st.top()];
        }

        st.push(i);
    }

    return result;
}
```

### Dry Run

```cpp
arr = [4, 5, 2, 10, 8]

i=0, arr[0]=4:
  stack empty
  result[0] = -1 (no previous greater)
  push 0
  stack = [0]

i=1, arr[1]=5:
  5 > arr[0]=4 → pop 0
  stack empty
  result[1] = -1
  push 1
  stack = [1]

i=2, arr[2]=2:
  2 < arr[1]=5 → don't pop
  result[2] = arr[1] = 5 ✓
  push 2
  stack = [1, 2]

i=3, arr[3]=10:
  10 > arr[2]=2 → pop 2
  10 > arr[1]=5 → pop 1
  stack empty
  result[3] = -1
  push 3
  stack = [3]

i=4, arr[4]=8:
  8 < arr[3]=10 → don't pop
  result[4] = arr[3] = 10 ✓
  push 4
  stack = [3, 4]

Final: [-1, -1, 5, -1, 10]
```

---

## Part C3: Next Smaller Element Pattern

### Concept

```
For each element, find the first element to its RIGHT
that is SMALLER than it.

Array: [4, 5, 2, 10, 8]
Answer:[2, 2, -1, 8, -1]

4 → 2
5 → 2
2 → -1 (no smaller to right)
10 → 8
8 → -1
```

### Why Increasing Stack?

```
We maintain INCREASING stack (bottom to top)

[2, 4, 7] ← Current stack

When we see 3:
- 3 < 7, so POP 7 → 3 is next smaller of 7
- 3 < 4, so POP 4 → 3 is next smaller of 4
- 3 > 2, so STOP
- PUSH 3 → [2, 3]
```

### Template

```cpp
vector<int> nextSmallerElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;  // Increasing stack

    for (int i = 0; i < n; i++) {
        // While current is smaller than top
        while (!st.empty() && arr[i] < arr[st.top()]) {
            int idx = st.top();
            st.pop();
            result[idx] = arr[i];
        }

        st.push(i);
    }

    return result;
}
```

---

## Part C4: Previous Smaller Element Pattern

### Concept

```
For each element, find the first element to its LEFT
that is SMALLER than it.

Array: [4, 5, 2, 10, 8]
Answer:[-1, 4, -1, 2, 2]

4 → -1
5 → 4
2 → -1
10 → 2
8 → 2
```

### Template

```cpp
vector<int> previousSmallerElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;  // Increasing stack

    for (int i = 0; i < n; i++) {
        // Pop elements >= current
        while (!st.empty() && arr[st.top()] >= arr[i]) {
            st.pop();
        }

        // Top is previous smaller
        if (!st.empty()) {
            result[i] = arr[st.top()];
        }

        st.push(i);
    }

    return result;
}
```

---

## Part C5: Combined Patterns

### Find Both Next and Previous in One Pass

```cpp
struct Result {
    vector<int> nextGreater;
    vector<int> prevGreater;
};

Result findBothGreater(vector<int>& arr) {
    int n = arr.size();
    Result res;
    res.nextGreater.resize(n, -1);
    res.prevGreater.resize(n, -1);

    stack<int> st;

    for (int i = 0; i < n; i++) {
        // Find next greater for popped elements
        while (!st.empty() && arr[i] > arr[st.top()]) {
            int idx = st.top();
            st.pop();
            res.nextGreater[idx] = arr[i];
        }

        // Find previous greater for current
        if (!st.empty()) {
            res.prevGreater[i] = arr[st.top()];
        }

        st.push(i);
    }

    return res;
}
```

---

## Summary Table: Which Stack for Which Problem?

| Problem | Stack Type | Operator | Direction |
|---------|-----------|----------|-----------|
| Next Greater | Decreasing | `arr[i] >` | Left → Right |
| Next Smaller | Increasing | `arr[i] <` | Left → Right |
| Previous Greater | Decreasing | `arr[top] <=` | Left → Right |
| Previous Smaller | Increasing | `arr[top] >=` | Left → Right |

---

# PART D: ADVANCED APPLICATIONS

## Part D1: Histogram Problems

### Problem: Largest Rectangle in Histogram

**Problem Statement:**
```
Given heights of histogram bars, find largest rectangle area.

heights = [2, 1, 5, 6, 2, 3]

Visual:
      6
    5 █
    █ █
    █ █   3
    █ █ 2 █
  2 █ █ █ █
  █ █ █ █ █
  ─────────

Largest rectangle: width=2, height=5, area=10
```

**Key Insight:**
```
For each bar as height:
  Find: How far left and right can we extend?

  Left boundary: Previous SMALLER element
  Right boundary: Next SMALLER element

  Width = right - left - 1
  Area = height * width
```

**Why This Works:**
```
For bar at index i with height h:
- We can extend left until we hit smaller bar
- We can extend right until we hit smaller bar
- This gives maximum width with height h

Example: height[2] = 5
  Left: index 1 has height 1 (smaller) → left boundary
  Right: index 4 has height 2 (smaller) → right boundary
  Width: 4 - 1 - 1 = 2
  Area: 5 * 2 = 10
```

**C++ Solution:**

```cpp
int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    vector<int> left(n), right(n);
    stack<int> st;

    // Find previous smaller (left boundary)
    for (int i = 0; i < n; i++) {
        while (!st.empty() && heights[st.top()] >= heights[i]) {
            st.pop();
        }
        left[i] = st.empty() ? -1 : st.top();
        st.push(i);
    }

    // Clear stack
    while (!st.empty()) st.pop();

    // Find next smaller (right boundary)
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && heights[st.top()] >= heights[i]) {
            st.pop();
        }
        right[i] = st.empty() ? n : st.top();
        st.push(i);
    }

    // Calculate max area
    int maxArea = 0;
    for (int i = 0; i < n; i++) {
        int width = right[i] - left[i] - 1;
        int area = heights[i] * width;
        maxArea = max(maxArea, area);
    }

    return maxArea;
}
```

**Optimized Single Pass Solution:**

```cpp
int largestRectangleArea(vector<int>& heights) {
    stack<int> st;
    int maxArea = 0;
    int n = heights.size();

    for (int i = 0; i <= n; i++) {
        int h = (i == n) ? 0 : heights[i];

        while (!st.empty() && h < heights[st.top()]) {
            int height = heights[st.top()];
            st.pop();

            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, height * width);
        }

        st.push(i);
    }

    return maxArea;
}
```

**Detailed Dry Run:**

```
heights = [2, 1, 5, 6, 2, 3]

Finding left boundaries (previous smaller):

i=0, h=2:
  stack empty → left[0] = -1
  push 0 → stack = [0]

i=1, h=1:
  1 < heights[0]=2 → pop 0
  stack empty → left[1] = -1
  push 1 → stack = [1]

i=2, h=5:
  5 > heights[1]=1 → don't pop
  left[2] = 1
  push 2 → stack = [1, 2]

i=3, h=6:
  6 > heights[2]=5 → don't pop
  left[3] = 2
  push 3 → stack = [1, 2, 3]

i=4, h=2:
  2 < heights[3]=6 → pop 3
  2 < heights[2]=5 → pop 2
  2 > heights[1]=1 → stop
  left[4] = 1
  push 4 → stack = [1, 4]

i=5, h=3:
  3 > heights[4]=2 → don't pop
  left[5] = 4
  push 5 → stack = [1, 4, 5]

left = [-1, -1, 1, 2, 1, 4]

Finding right boundaries (next smaller):
(Similar process from right to left)
right = [1, 6, 4, 4, 6, 6]

Calculating areas:
i=0: width = 1 - (-1) - 1 = 1, area = 2 * 1 = 2
i=1: width = 6 - (-1) - 1 = 6, area = 1 * 6 = 6
i=2: width = 4 - 1 - 1 = 2, area = 5 * 2 = 10 ✓ MAX
i=3: width = 4 - 2 - 1 = 1, area = 6 * 1 = 6
i=4: width = 6 - 1 - 1 = 4, area = 2 * 4 = 8
i=5: width = 6 - 4 - 1 = 1, area = 3 * 1 = 3

Maximum area = 10
```

---

## Part D2: Subarray Problems

### Problem: Sum of Subarray Minimums

**Problem Statement:**
```
Find sum of minimum elements in all subarrays.

arr = [3, 1, 2, 4]

All subarrays and their minimums:
[3] → 3
[3,1] → 1
[3,1,2] → 1
[3,1,2,4] → 1
[1] → 1
[1,2] → 1
[1,2,4] → 1
[2] → 2
[2,4] → 2
[4] → 4

Sum = 3+1+1+1+1+1+1+2+2+4 = 17
```

**Key Insight:**
```
For each element arr[i]:
  How many subarrays have arr[i] as minimum?

  Count = (number of elements to left) * (number to right)
  where left/right elements are >= arr[i]

  Contribution = arr[i] * count
```

**Why This Works:**
```
Element at index i with value v:

Left boundary: Previous SMALLER element (index = l)
Right boundary: Next SMALLER element (index = r)

Subarrays where i is minimum:
- Must include index i
- Can start anywhere from (l+1) to i: (i - l) choices
- Can end anywhere from i to (r-1): (r - i) choices

Total subarrays = (i - l) * (r - i)
```

**C++ Solution:**

```cpp
int sumSubarrayMins(vector<int>& arr) {
    const int MOD = 1e9 + 7;
    int n = arr.size();

    vector<int> left(n), right(n);
    stack<int> st;

    // Previous SMALLER or EQUAL (to handle duplicates)
    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] > arr[i]) {
            st.pop();
        }
        left[i] = st.empty() ? -1 : st.top();
        st.push(i);
    }

    while (!st.empty()) st.pop();

    // Next SMALLER (strict)
    for (int i = n - 1; i >= 0; i--) {
        while (!st.empty() && arr[st.top()] >= arr[i]) {
            st.pop();
        }
        right[i] = st.empty() ? n : st.top();
        st.push(i);
    }

    long long result = 0;
    for (int i = 0; i < n; i++) {
        long long leftCount = i - left[i];
        long long rightCount = right[i] - i;
        long long contribution = (arr[i] * leftCount % MOD) * rightCount % MOD;
        result = (result + contribution) % MOD;
    }

    return result;
}
```

**Dry Run:**

```
arr = [3, 1, 2, 4]

Finding previous smaller or equal:
i=0 (3): left[0] = -1
i=1 (1): left[1] = -1
i=2 (2): left[2] = 1 (arr[1]=1 <= 2)
i=3 (4): left[3] = 2 (arr[2]=2 <= 4)

left = [-1, -1, 1, 2]

Finding next smaller (strict):
i=3 (4): right[3] = 4 (none)
i=2 (2): right[2] = 4
i=1 (1): right[1] = 4
i=0 (3): right[0] = 1 (arr[1]=1 < 3)

right = [1, 4, 4, 4]

Calculating contributions:
i=0 (value=3):
  leftCount = 0 - (-1) = 1
  rightCount = 1 - 0 = 1
  contribution = 3 * 1 * 1 = 3

i=1 (value=1):
  leftCount = 1 - (-1) = 2
  rightCount = 4 - 1 = 3
  contribution = 1 * 2 * 3 = 6

i=2 (value=2):
  leftCount = 2 - 1 = 1
  rightCount = 4 - 2 = 2
  contribution = 2 * 1 * 2 = 4

i=3 (value=4):
  leftCount = 3 - 2 = 1
  rightCount = 4 - 3 = 1
  contribution = 4 * 1 * 1 = 4

Total = 3 + 6 + 4 + 4 = 17 ✓
```

---

### Problem: Daily Temperatures

**Problem Statement:**
```
Find how many days until a warmer temperature.

temperatures = [73, 74, 75, 71, 69, 72, 76, 73]
answer =       [1,  1,  4,  2,  1,  1,  0,  0]

Day 0 (73): Next warmer is day 1 (74) → wait 1 day
Day 1 (74): Next warmer is day 2 (75) → wait 1 day
Day 2 (75): Next warmer is day 6 (76) → wait 4 days
Day 3 (71): Next warmer is day 5 (72) → wait 2 days
...
```

**Key Insight:**
```
This is a classic "Next Greater Element" problem!

Use decreasing monotonic stack
When we find warmer temp → calculate days difference
```

**C++ Solution:**

```cpp
vector<int> dailyTemperatures(vector<int>& temperatures) {
    int n = temperatures.size();
    vector<int> answer(n, 0);
    stack<int> st;  // Store indices

    for (int i = 0; i < n; i++) {
        // While current temp is warmer than stack top
        while (!st.empty() && temperatures[i] > temperatures[st.top()]) {
            int prevDay = st.top();
            st.pop();
            answer[prevDay] = i - prevDay;  // Days to wait
        }

        st.push(i);
    }

    return answer;
}
```

**Dry Run:**

```
temps = [73, 74, 75, 71, 69, 72, 76, 73]

i=0, temp=73:
  stack empty
  push 0 → stack = [0]

i=1, temp=74:
  74 > temps[0]=73 → pop 0
  answer[0] = 1 - 0 = 1 ✓
  push 1 → stack = [1]

i=2, temp=75:
  75 > temps[1]=74 → pop 1
  answer[1] = 2 - 1 = 1 ✓
  push 2 → stack = [2]

i=3, temp=71:
  71 < 75 → don't pop
  push 3 → stack = [2, 3]

i=4, temp=69:
  69 < 71 → don't pop
  push 4 → stack = [2, 3, 4]

i=5, temp=72:
  72 > temps[4]=69 → pop 4
  answer[4] = 5 - 4 = 1 ✓
  72 > temps[3]=71 → pop 3
  answer[3] = 5 - 3 = 2 ✓
  72 < temps[2]=75 → stop
  push 5 → stack = [2, 5]

i=6, temp=76:
  76 > temps[5]=72 → pop 5
  answer[5] = 6 - 5 = 1 ✓
  76 > temps[2]=75 → pop 2
  answer[2] = 6 - 2 = 4 ✓
  push 6 → stack = [6]

i=7, temp=73:
  73 < 76 → don't pop
  push 7 → stack = [6, 7]

Indices 6,7 still in stack → no warmer day
answer[6] = 0, answer[7] = 0

Final: [1, 1, 4, 2, 1, 1, 0, 0] ✓
```

---

## Part D3: Circular Array Problems

### Problem: Next Greater Element II

**Problem Statement:**
```
Find next greater element in CIRCULAR array.

nums = [1, 2, 1]

Circular means:
[1, 2, 1] → [1, 2, 1, 1, 2, 1, ...]

Answers:
1 → 2 (next element)
2 → -1 (no greater, wraps around: 1, 1, 2 all smaller)
1 → 2 (wraps to index 1)

Result: [2, -1, 2]
```

**Key Insight:**
```
Simulate circular by processing array TWICE
Use modulo operator to wrap indices

Process indices: 0, 1, 2, 0, 1, 2
Actual indices:  0, 1, 2, 0, 1, 2 (using i % n)
```

**C++ Solution:**

```cpp
vector<int> nextGreaterElements(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st;

    // Process array TWICE to simulate circular
    for (int i = 0; i < 2 * n; i++) {
        int idx = i % n;  // Wrap around

        while (!st.empty() && nums[idx] > nums[st.top()]) {
            result[st.top()] = nums[idx];
            st.pop();
        }

        // Only push in first pass
        if (i < n) {
            st.push(idx);
        }
    }

    return result;
}
```

**Dry Run:**

```
nums = [1, 2, 1]
n = 3

First pass (i = 0, 1, 2):
  Same as regular next greater
  result = [-1, -1, -1]
  stack = [1, 2] (indices with no answer yet)

Second pass (i = 3, 4, 5):

i=3, idx=0, value=1:
  1 < nums[2]=1 → no pop
  Don't push (i >= n)
  stack = [1, 2]

i=4, idx=1, value=2:
  2 > nums[2]=1 → pop 2
  result[2] = 2 ✓
  2 == nums[1]=2 → no more pops
  Don't push
  stack = [1]

i=5, idx=2, value=1:
  1 < nums[1]=2 → no pop
  Don't push
  stack = [1]

Index 1 still in stack → no greater
result[1] = -1 ✓

Final: [2, -1, 2]
```

---

## Part D4: Problem Recognition Framework

### Decision Tree

```
MONOTONIC STACK PROBLEM?

Q1: Does the problem ask about:
├─ "Next/Previous greater/smaller"? → YES
├─ "Nearest larger/smaller"? → YES
├─ "First element that satisfies..."? → YES
├─ "Range queries with min/max"? → YES
├─ "Subarray with certain property"? → MAYBE
└─ Other? → Probably NOT

Q2: What are we finding?
├─ Next Greater → Decreasing Stack, Left to Right
├─ Next Smaller → Increasing Stack, Left to Right
├─ Previous Greater → Decreasing Stack, Check top before push
├─ Previous Smaller → Increasing Stack, Check top before push
└─ Both → Single pass with combined logic

Q3: Special cases?
├─ Circular array? → Process 2*n elements with modulo
├─ Count subarrays? → Find left and right boundaries
├─ Histogram/Rectangle? → Next/Previous smaller
└─ Stock span? → Previous greater or equal

Q4: What to store in stack?
├─ Need index? → Store indices
├─ Need value only? → Store values
└─ Need both? → Store pair/struct
```

### Pattern Recognition Cheat Sheet

| Keywords in Problem | Pattern | Stack Type |
|---------------------|---------|------------|
| "next greater" | Next Greater Element | Decreasing |
| "next warmer temperature" | Next Greater Element | Decreasing |
| "stock span" | Previous Greater/Equal | Decreasing |
| "next smaller" | Next Smaller Element | Increasing |
| "largest rectangle" | Next/Prev Smaller | Increasing |
| "sum of subarray minimums" | Prev/Next Smaller | Increasing |
| "remove k digits" | Monotonic Increasing | Increasing |
| "circular" + "next greater" | Circular Next Greater | Decreasing |

### Quick Reference: Template Selection

```cpp
// NEXT GREATER (Decreasing Stack)
for (int i = 0; i < n; i++) {
    while (!st.empty() && arr[i] > arr[st.top()]) {
        result[st.top()] = arr[i];
        st.pop();
    }
    st.push(i);
}

// PREVIOUS GREATER (Decreasing Stack)
for (int i = 0; i < n; i++) {
    while (!st.empty() && arr[st.top()] <= arr[i]) {
        st.pop();
    }
    result[i] = st.empty() ? -1 : arr[st.top()];
    st.push(i);
}

// NEXT SMALLER (Increasing Stack)
for (int i = 0; i < n; i++) {
    while (!st.empty() && arr[i] < arr[st.top()]) {
        result[st.top()] = arr[i];
        st.pop();
    }
    st.push(i);
}

// PREVIOUS SMALLER (Increasing Stack)
for (int i = 0; i < n; i++) {
    while (!st.empty() && arr[st.top()] >= arr[i]) {
        st.pop();
    }
    result[i] = st.empty() ? -1 : arr[st.top()];
    st.push(i);
}

// CIRCULAR (2 * n iterations)
for (int i = 0; i < 2 * n; i++) {
    int idx = i % n;
    // ... monotonic stack logic ...
    if (i < n) st.push(idx);
}
```

---

## Complete Problem List (By Category)

### Category 1: Basic Next/Previous Greater/Smaller
1. **Next Greater Element I** ([LC 496](https://leetcode.com/problems/next-greater-element-i/)) - Easy
2. **Next Greater Element II** ([LC 503](https://leetcode.com/problems/next-greater-element-ii/)) - Medium (Circular)
3. **Daily Temperatures** ([LC 739](https://leetcode.com/problems/daily-temperatures/)) - Medium
4. **Online Stock Span** ([LC 901](https://leetcode.com/problems/online-stock-span/)) - Medium

### Category 2: Histogram & Rectangle
5. **Largest Rectangle in Histogram** ([LC 84](https://leetcode.com/problems/largest-rectangle-in-histogram/)) - Hard
6. **Maximal Rectangle** ([LC 85](https://leetcode.com/problems/maximal-rectangle/)) - Hard
7. **Max Rectangle in Binary Matrix** - Hard

### Category 3: Subarray Problems
8. **Sum of Subarray Minimums** ([LC 907](https://leetcode.com/problems/sum-of-subarray-minimums/)) - Medium
9. **Sum of Subarray Ranges** ([LC 2104](https://leetcode.com/problems/sum-of-subarray-ranges/)) - Medium
10. **Maximum Subarray Min-Product** ([LC 1856](https://leetcode.com/problems/maximum-subarray-min-product/)) - Medium

### Category 4: String Problems
11. **Remove K Digits** ([LC 402](https://leetcode.com/problems/remove-k-digits/)) - Medium
12. **Remove Duplicate Letters** ([LC 316](https://leetcode.com/problems/remove-duplicate-letters/)) - Medium
13. **Smallest Subsequence of Distinct Characters** ([LC 1081](https://leetcode.com/problems/smallest-subsequence-of-distinct-characters/)) - Medium

### Category 5: Advanced
14. **Trapping Rain Water** ([LC 42](https://leetcode.com/problems/trapping-rain-water/)) - Hard
15. **132 Pattern** ([LC 456](https://leetcode.com/problems/132-pattern/)) - Medium
16. **Maximum Width Ramp** ([LC 962](https://leetcode.com/problems/maximum-width-ramp/)) - Medium
17. **Car Fleet** ([LC 853](https://leetcode.com/problems/car-fleet/)) - Medium

---

## Key Takeaways

### 1. The Core Principle
```
Monotonic Stack eliminates redundant comparisons
by maintaining a sorted order in the stack.

Time: O(n) - each element pushed and popped once
Space: O(n) - stack size
```

### 2. Four Fundamental Patterns
```
Next Greater   → Decreasing Stack + Pop when greater
Next Smaller   → Increasing Stack + Pop when smaller
Previous Greater → Decreasing Stack + Check top before push
Previous Smaller → Increasing Stack + Check top before push
```

### 3. When Elements are Popped
```
The CURRENT element is the answer for POPPED elements

Example: Decreasing stack [9, 7, 4], current = 6
  Pop 4 → 6 is next greater of 4
  Pop 7? NO → 7 is still waiting
```

### 4. When Elements are Pushed
```
The TOP of stack is the answer for CURRENT element

Example: Decreasing stack [9, 7], current = 5
  Don't pop 7 (5 < 7)
  7 is previous greater of 5
  Push 5 → [9, 7, 5]
```

### 5. Handling Duplicates
```
For "or equal" problems:
  Next Greater or Equal → while (arr[i] >= st.top())
  Next Greater (strict) → while (arr[i] > st.top())

Choose based on problem requirements!
```
Master monotonic stack, and an entire class of "impossible" O(n²) problems become elegant O(n) solutions!*

---

## Problem Recognition Framework

### Decision Tree for Stack Problems

```
START: Is it a Stack Problem?

Q1: Does it involve LIFO (Last In, First Out) operations?
├─ YES → Continue
├─ NO → Maybe not stack

Q2: What type of stack problem?
├─ Basic operations (push/pop) → Simple Stack
├─ Monotonic property → Monotonic Stack
├─ Nested structures → Stack for parentheses
├─ Expression evaluation → Stack for operators

Q3: What are we tracking?
├─ Next greater/smaller → Monotonic Stack
├─ Previous greater/smaller → Monotonic Stack
├─ Valid parentheses → Stack matching
├─ Function calls/recursion → Call Stack
├─ Undo/redo operations → Stack for history

Q4: Key indicator words?
├─ "Next greater element" → Monotonic Stack
├─ "Previous smaller element" → Monotonic Stack  
├─ "Valid parentheses" → Stack matching
├─ "Remove k digits" → Monotonic Stack
├─ "Largest rectangle" → Monotonic Stack
├─ "Trapping rain water" → Monotonic Stack
├─ "Daily temperatures" → Monotonic Stack

Q5: Time complexity requirements?
├─ O(n) required → Stack solution
├─ O(n²) acceptable → Brute force
```

## Key Takeaways & Mental Models

### 1. **Monotonic Stack = Smart Element Tracking**
```
Mental Model: A smart assistant that remembers only useful elements

Instead of checking all pairs O(n²), stack maintains candidates
When new element arrives, remove useless elements, keep promising ones
Each element processed once - O(n) total
```

### 2. **Stack for Validity = Matching Pairs**
```
Mental Model: Opening and closing doors

Push opening brackets, pop when closing brackets match
Stack empty at end = valid structure
Perfect for: parentheses, XML tags, nested structures
```

### 3. **Stack for Computation = State Preservation**
```
Mental Model: Calculator memory

Store intermediate results, operators, operands
Process in correct order (postfix, infix evaluation)
Perfect for: expression evaluation, undo operations
```

### 4. **Time Complexity Pattern**
```
Monotonic Stack: O(n) - each element pushed/popped once
Stack Matching: O(n) - linear scan with stack operations
Stack Computation: O(n) - single pass processing

Space: O(n) worst case, O(1) to O(n) depending on problem
```

### 5. **Stack Direction Matters**
```
Increasing Stack: Find previous/next smaller elements
Decreasing Stack: Find previous/next greater elements

Choose direction based on what you're searching for!
```

---

## Common Pitfalls & How to Avoid

### Pitfall 1: Wrong Monotonic Direction
```cpp
// WRONG: Using increasing stack for next greater
while (!st.empty() && arr[i] >= st.top())  // Wrong condition

// RIGHT: For next greater, use decreasing stack
while (!st.empty() && arr[i] > st.top())   // Correct for strictly decreasing
```

### Pitfall 2: Forgetting to Handle Empty Stack
```cpp
// WRONG: Accessing top without checking
int nextGreater = st.top();  // Crash if empty!

// RIGHT: Check before accessing
int nextGreater = st.empty() ? -1 : st.top();
```

### Pitfall 3: Wrong Index vs Value Storage
```cpp
// WRONG: Storing values when you need indices
stack<int> st;  // Stores values, but you need indices for result

// RIGHT: Store indices for result array access
stack<int> st;  // Push i, not arr[i]
result[st.top()] = arr[i];
```

### Pitfall 4: Off-by-One in Width Calculations
```cpp
// WRONG: Width calculation in histogram
int width = i - st.top() - 1;  // Missing consideration

// RIGHT: Consider the boundaries
int width = i - st.top() - 1;  // From popped to current
```

### Pitfall 5: Not Clearing Stack for Multiple Passes
```cpp
// WRONG: Stack not cleared between passes
for(int pass = 0; pass < 2; pass++) {
    // Stack retains elements from first pass!
}

// RIGHT: Clear or recreate stack
stack<int> st;  // Fresh stack for each pass
```

### Pitfall 6: Wrong Base Case Handling
```cpp
// WRONG: Not handling elements with no answer
// Stack empty means no previous greater
if(st.empty()) result[i] = -1;  // Correct

// But forgetting this leads to undefined behavior
```

---

## Final Thoughts

**The Power of Stack:**
- Transforms O(n²) problems into O(n) solutions
- Natural fit for LIFO operations and monotonicity
- Essential for expression evaluation and validity checking

**How to Master Stack:**
1. **Understand LIFO principle** - Last in, first out
2. **Master monotonic stacks** - The most powerful pattern
3. **Practice validity problems** - Parentheses, brackets
4. **Learn computation patterns** - Expression evaluation
5. **Focus on state management** - What to store (indices vs values)

**Remember:** Stack isn't just a data structure - it's a **problem-solving mindset**. When you see "next greater" or "valid parentheses", think stack first!

**Key Insight:** Every stack problem can be solved by asking:
- What should I store in the stack?
- When should I push/pop elements?
- What does the stack represent (candidates, state, history)?

Master this framework, and you'll solve stack problems with elegance and efficiency! 🚀

---

## General Time and Space Complexity of Stack Patterns

### Time Complexity Analysis

| Stack Pattern | Time Complexity | Explanation |
|---------------|----------------|-------------|
| **Monotonic Stack** | O(n) | Each element pushed/popped at most once |
| **Stack Matching** | O(n) | Single pass with constant stack operations |
| **Stack Computation** | O(n) | Linear processing with stack state |
| **Multiple Passes** | O(n) | Amortized O(n) across all passes |

**Why O(n) for Monotonic Stack?**
- Each element enters stack exactly once
- Each element exits stack at most once
- Total operations = 2n = O(n)

### Space Complexity Analysis

| Stack Usage | Space Complexity | Examples |
|-------------|------------------|----------|
| **Monotonic Stack** | O(n) | Worst case: all elements in stack |
| **Stack Matching** | O(n) | Maximum nesting depth |
| **Stack Computation** | O(n) | Expression evaluation state |
| **Index Storage** | O(n) | Storing indices instead of values |

**Optimization Tips:**
- Use stack of indices to save space when possible
- Clear stack between multiple passes
- Consider iterative vs recursive stack usage

---

## Practice Leetcode Questions

### Easy Level (Stack Basics)
1. **Valid Parentheses** - https://leetcode.com/problems/valid-parentheses/
2. **Implement Stack using Queues** - https://leetcode.com/problems/implement-stack-using-queues/
3. **Baseball Game** - https://leetcode.com/problems/baseball-game/
4. **Next Greater Element I** - https://leetcode.com/problems/next-greater-element-i/

### Medium Level (Monotonic Stack)
1. **Next Greater Element II** - https://leetcode.com/problems/next-greater-element-ii/
2. **Daily Temperatures** - https://leetcode.com/problems/daily-temperatures/
3. **Online Stock Span** - https://leetcode.com/problems/online-stock-span/
4. **Largest Rectangle in Histogram** - https://leetcode.com/problems/largest-rectangle-in-histogram/
5. **Trapping Rain Water** - https://leetcode.com/problems/trapping-rain-water/
6. **Remove K Digits** - https://leetcode.com/problems/remove-k-digits/
7. **Sum of Subarray Minimums** - https://leetcode.com/problems/sum-of-subarray-minimums/
8. **132 Pattern** - https://leetcode.com/problems/132-pattern/

### Hard Level (Advanced Stack)
1. **Maximal Rectangle** - https://leetcode.com/problems/maximal-rectangle/
2. **Largest Rectangle in Histogram** - https://leetcode.com/problems/largest-rectangle-in-histogram/
3. **Remove Duplicate Letters** - https://leetcode.com/problems/remove-duplicate-letters/
4. **Shortest Subarray with Sum at Least K** - https://leetcode.com/problems/shortest-subarray-with-sum-at-least-k/
5. **Constrained Subsequence Sum** - https://leetcode.com/problems/constrained-subsequence-sum/

### Advanced Practice (Multiple Concepts)
1. **Car Fleet** - https://leetcode.com/problems/car-fleet/
2. **Maximum Width Ramp** - https://leetcode.com/problems/maximum-width-ramp/
3. **Sum of Subarray Ranges** - https://leetcode.com/problems/sum-of-subarray-ranges/
4. **Maximum Subarray Min-Product** - https://leetcode.com/problems/maximum-subarray-min-product/

**Practice Strategy:**
- Start with basic stack operations
- Master monotonic stack patterns
- Practice validity and matching problems
- Focus on index vs value storage decisions
- Time yourself to build recognition speed

Master stack patterns, and you'll handle LIFO problems with confidence! 🎯