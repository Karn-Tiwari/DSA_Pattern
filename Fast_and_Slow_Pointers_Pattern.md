# Fast and Slow Pointers Pattern

## Ultimate Guide to Fast & Slow Pointers Technique in DSA

> Master the Fast & Slow Pointers technique - essential for linked list problems and cycle detection.

## 📋 Table of Contents

- [🚀 Quick Start](#-quick-start)
- [🎯 Core Concept](#-core-concept)
- [📖 Template](#-template)
- [💡 Problem Examples](#-problem-examples)
- [🎯 Practice Problems](#-practice-problems)
- [⚠️ Common Pitfalls](#️-common-pitfalls)

## 🚀 Quick Start

Fast & Slow Pointers is used for:

- **Cycle Detection** in linked lists and graphs
- **Finding Middle** elements
- **Finding Duplicates** in arrays
- **Palindrome** linked list checks

**Time Complexity:** O(n) - Single pass through the structure

## 🎯 Core Concept

```
Fast pointer moves 2x speed of slow pointer
Used for: cycle detection, finding middle
[1] → [2] → [3] → [4] → [5]
 ^           ^
slow        fast
```

**Why It Works:**
- Fast moves 2 steps, slow moves 1 step
- If there's a cycle, fast will eventually catch slow
- Distance between them decreases by 1 each iteration
- When they meet, cycle is detected

## 📖 Template

```cpp
ListNode* fastSlowTemplate(ListNode* head) {
    if (!head) return nullptr;
    
    ListNode* slow = head;
    ListNode* fast = head;
    
    while (fast && fast->next) {
        slow = slow->next;        // Move 1 step
        fast = fast->next->next;  // Move 2 steps
        
        if (slow == fast) {
            // They met - cycle detected!
            return slow;
        }
    }
    
    return nullptr;  // No cycle
}
```

---

### Problem 1: Linked List Cycle Detection

**Problem:** Detect if linked list has a cycle.

**Why Fast-Slow Works:**
```
If there's a cycle:
- Fast will eventually catch slow (like runners on track)
- Distance between them decreases by 1 each iteration

Proof:
After k steps:
  slow at position k
  fast at position 2k
  
If cycle of length C:
  slow enters cycle at some point
  fast is behind by distance d
  Each iteration: d decreases by 1
  Eventually d = 0 → they meet!
```

**C++ Solution:**

```cpp
bool hasCycle(ListNode* head) {
    if (!head || !head->next) return false;
    
    ListNode* slow = head;
    ListNode* fast = head;
    
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        
        if (slow == fast) {
            return true;  // Cycle detected
        }
    }
    
    return false;  // No cycle
}
```

**Dry Run:**

```
Cycle: 1 → 2 → 3 → 4 → 5
                ↑       ↓
                └───────┘

Step 1: slow=1, fast=1
Step 2: slow=2, fast=3
Step 3: slow=3, fast=5
Step 4: slow=4, fast=4  ← MEET! Cycle detected

No Cycle: 1 → 2 → 3 → 4 → 5 → null

Step 1: slow=1, fast=1
Step 2: slow=2, fast=3
Step 3: slow=3, fast=5
Step 4: slow=4, fast=null ← fast reached end, no cycle
```

---

### Problem 2: Find Middle of Linked List

**Problem:** Return middle node of linked list.

**Why This Works:**
```
When fast reaches end:
  fast moved 2n steps
  slow moved n steps
  slow is at middle!

Even length: return second middle
Odd length: return exact middle
```

**C++ Solution:**

```cpp
ListNode* middleNode(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;
    
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }
    
    return slow;  // Middle node
}
```

**Dry Run:**

```
List: 1 → 2 → 3 → 4 → 5 → null

Initial: slow=1, fast=1

Step 1: slow → 2, fast → 3
        1 → 2 → 3 → 4 → 5
            ^       ^

Step 2: slow → 3, fast → 5
        1 → 2 → 3 → 4 → 5
                ^           ^

Step 3: fast → null (end)
        Return slow = 3 ✓
```

---

## 🎯 Practice Problems

### Easy
1. [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
2. [Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/)
3. [Palindrome Linked List](https://leetcode.com/problems/palindrome-linked-list/)

### Medium
1. [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
2. [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)
3. [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
4. [Reorder List](https://leetcode.com/problems/reorder-list/)
5. [Rotate List](https://leetcode.com/problems/rotate-list/)

### Hard
1. [Circular Array Loop](https://leetcode.com/problems/circular-array-loop/)
2. [Sort List](https://leetcode.com/problems/sort-list/)
3. [Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)
4. [Intersection of Two Arrays](https://leetcode.com/problems/intersection-of-two-arrays/)
5. [Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/)

## ⚠️ Common Pitfalls

### Pitfall 1: Null Pointer Checks
```cpp
// WRONG: May crash if fast->next is null
while (fast->next) {  // fast could be null!

// RIGHT: Check both
while (fast && fast->next) {
```

### Pitfall 2: Off-by-One in Cycle Detection
```cpp
// For finding cycle start, need additional logic
// Floyd's algorithm: find meeting point, then reset one pointer
```

### Pitfall 3: Wrong Speed Ratio
```cpp
// Standard is 2:1, but sometimes 3:1 or others
// Always clarify the problem requirements
```

### Pitfall 4: Not Handling Edge Cases
- Empty list
- Single node
- Two nodes (with/without cycle)
- Cycle at beginning

---

**Master fast & slow pointers, and you'll detect cycles and find middles effortlessly! 🏃‍♂️**