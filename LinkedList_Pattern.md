# Ultimate Linked List Pattern Guide - From Basics to Mastery

## Table of Contents

### Part A: Foundation
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [Linked List Node & Implementation](#2-linked-list-node--implementation)
3. [Basic Operations](#3-basic-operations)

### Part B: Core Patterns
4. [Pattern 1: Dummy Head Node](#4-pattern-1-dummy-head-node)
5. [Pattern 2: Fast & Slow Pointers](#5-pattern-2-fast--slow-pointers)
6. [Pattern 3: Reverse Linked List](#6-pattern-3-reverse-linked-list)
7. [Pattern 4: Merge Linked Lists](#7-pattern-4-merge-linked-lists)
8. [Pattern 5: Two List Pointers](#8-pattern-5-two-list-pointers)

### Part C: Advanced Patterns
9. [Pattern 6: Cycle Detection (Floyd's Algorithm)](#9-pattern-6-cycle-detection-floyds-algorithm)
10. [Pattern 7: Reorder & Rotate](#10-pattern-7-reorder--rotate)
11. [Pattern 8: Deep Copy & Random Pointers](#11-pattern-8-deep-copy--random-pointers)

### Part D: Problem Solving
12. [Problem Recognition Framework](#12-problem-recognition-framework)
13. [Practice Problems](#13-practice-problems)
14. [Common Pitfalls](#14-common-pitfalls)
15. [Template Summary](#15-template-summary)

---

## 1. Core Concept & Philosophy

### What is a Linked List Really About?

**Wrong Understanding:**
"A linked list is just an array with pointers"

**Correct Understanding:**
"A linked list is a **dynamic chain of nodes** where each node stores **data + reference to next node**, enabling **O(1) insertion/deletion at known positions** but **O(n) random access**"

### Linked List vs Array

```
ARRAY                          LINKED LIST
[10][20][30][40]               10 → 20 → 30 → 40 → NULL
 ^                              ^
index 0 = O(1) access           head = O(1) insert at front
index i = O(1) access           access i-th node = O(n)
insert at middle = O(n)         insert after known node = O(1)
contiguous memory               scattered memory
cache-friendly                  cache-unfriendly
```

### When to Use Linked Lists?

**Key Indicators:**
1. Frequent **insertions/deletions** at head or after a known node
2. **Unknown size** that grows/shrinks dynamically
3. No need for **random access** by index
4. Problems involving **cycles**, **middle finding**, or **reordering**
5. Implementing **LRU Cache**, **polynomial addition**, or **merge operations**

### Types of Linked Lists

```
SINGLY LINKED:     1 → 2 → 3 → NULL
DOUBLY LINKED:     NULL ← 1 ⇄ 2 ⇄ 3 → NULL
CIRCULAR:          1 → 2 → 3 → (back to 1)
```

---

## 2. Linked List Node & Implementation

### Node Definition

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode() : val(0), next(nullptr) {}
    ListNode(int x) : val(x), next(nullptr) {}
    ListNode(int x, ListNode* next) : val(x), next(next) {}
};
```

### Create List from Vector

```cpp
ListNode* createList(vector<int>& vals) {
    if (vals.empty()) return nullptr;

    ListNode* head = new ListNode(vals[0]);
    ListNode* curr = head;

    for (int i = 1; i < vals.size(); i++) {
        curr->next = new ListNode(vals[i]);
        curr = curr->next;
    }
    return head;
}
```

### Print & Delete List

```cpp
void printList(ListNode* head) {
    while (head) {
        cout << head->val;
        if (head->next) cout << " → ";
        head = head->next;
    }
    cout << " → NULL" << endl;
}

void deleteList(ListNode* head) {
    while (head) {
        ListNode* temp = head;
        head = head->next;
        delete temp;
    }
}
```

---

## 3. Basic Operations

### Traversal — O(n)

```cpp
ListNode* curr = head;
while (curr != nullptr) {
    // process curr->val
    curr = curr->next;
}
```

### Insert at Head — O(1)

```cpp
ListNode* newNode = new ListNode(val);
newNode->next = head;
head = newNode;
```

### Insert After Node — O(1)

```cpp
void insertAfter(ListNode* node, int val) {
    ListNode* newNode = new ListNode(val);
    newNode->next = node->next;
    node->next = newNode;
}
```

### Delete Node (Given Access to Previous Node) — O(1)

```cpp
void deleteNode(ListNode* prev) {
    ListNode* toDelete = prev->next;
    prev->next = toDelete->next;
    delete toDelete;
}
```

### Get Length — O(n)

```cpp
int getLength(ListNode* head) {
    int len = 0;
    while (head) {
        len++;
        head = head->next;
    }
    return len;
}
```

---

## 4. Pattern 1: Dummy Head Node

### Concept

```
When modifying the head might change (delete head, merge, insert before head),
use a DUMMY node pointing to the real head.

dummy → 1 → 2 → 3 → NULL
  ^
  always stable reference point
```

### Template

```cpp
ListNode* processList(ListNode* head) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode* prev = &dummy;

    ListNode* curr = head;
    while (curr) {
        if (shouldRemove(curr)) {
            prev->next = curr->next;
            delete curr;
            curr = prev->next;
        } else {
            prev = curr;
            curr = curr->next;
        }
    }

    return dummy.next;  // New head (may differ from original)
}
```

### Problem: Remove Linked List Elements

**Problem:** Remove all nodes with value `val`.

**Key Insights:**
- Without dummy node, deleting head requires special handling
- With dummy node, all deletions use same logic

**C++ Solution:**

```cpp
ListNode* removeElements(ListNode* head, int val) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode* prev = &dummy;

    while (prev->next) {
        if (prev->next->val == val) {
            ListNode* toDelete = prev->next;
            prev->next = toDelete->next;
            delete toDelete;
        } else {
            prev = prev->next;
        }
    }

    return dummy.next;
}
```

**Dry Run:**

```
head = 1 → 2 → 6 → 3 → 4 → 5 → 6, val = 6

dummy → 1 → 2 → 6 → 3 → 4 → 5 → 6
         prev at 1, skip
         prev at 2, delete 6
dummy → 1 → 2 → 3 → 4 → 5 → 6
                   prev at 5, delete 6
Result: 1 → 2 → 3 → 4 → 5
```

---

## 5. Pattern 2: Fast & Slow Pointers

### Concept

```
Two pointers move at different speeds:
- slow moves 1 step
- fast moves 2 steps

Uses:
- Find middle
- Detect cycle
- Find nth from end
- Palindrome check
```

### Find Middle Template

```cpp
ListNode* findMiddle(ListNode* head) {
    ListNode *slow = head, *fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    return slow;  // Middle (or second middle if even length)
}
```

### Problem: Middle of Linked List

**Dry Run:**

```
1 → 2 → 3 → 4 → 5

Step 1: slow=1, fast=1
Step 2: slow=2, fast=3
Step 3: slow=3, fast=5
fast->next is NULL → stop
Answer: 3 ✓
```

### Find Nth From End

```cpp
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode *fast = &dummy, *slow = &dummy;

    // Move fast n+1 steps ahead
    for (int i = 0; i <= n; i++) {
        fast = fast->next;
    }

    // Move both until fast reaches end
    while (fast) {
        slow = slow->next;
        fast = fast->next;
    }

    // slow is just before node to delete
    slow->next = slow->next->next;
    return dummy.next;
}
```

**Why n+1 steps?** So slow stops at the node BEFORE the one to delete.

---

## 6. Pattern 3: Reverse Linked List

### Concept

```
Iteratively flip pointers one by one:

Before: 1 → 2 → 3 → NULL
After:  NULL ← 1 ← 2 ← 3
                              ↑ head
```

### Iterative Template — O(n) time, O(1) space

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* curr = head;

    while (curr) {
        ListNode* nextTemp = curr->next;
        curr->next = prev;
        prev = curr;
        curr = nextTemp;
    }

    return prev;
}
```

### Recursive Template — O(n) time, O(n) space

```cpp
ListNode* reverseListRecursive(ListNode* head) {
    if (!head || !head->next) return head;

    ListNode* newHead = reverseListRecursive(head->next);
    head->next->next = head;
    head->next = nullptr;
    return newHead;
}
```

### Reverse Between Positions (m to n)

```cpp
ListNode* reverseBetween(ListNode* head, int left, int right) {
    ListNode dummy(0);
    dummy.next = head;
    ListNode* prev = &dummy;

    // Move prev to node before 'left'
    for (int i = 1; i < left; i++) {
        prev = prev->next;
    }

    // Reverse sublist in-place
    ListNode* curr = prev->next;
    for (int i = 0; i < right - left; i++) {
        ListNode* nextNode = curr->next;
        curr->next = nextNode->next;
        nextNode->next = prev->next;
        prev->next = nextNode;
    }

    return dummy.next;
}
```

---

## 7. Pattern 4: Merge Linked Lists

### Merge Two Sorted Lists

```cpp
ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
    ListNode dummy(0);
    ListNode* tail = &dummy;

    while (l1 && l2) {
        if (l1->val <= l2->val) {
            tail->next = l1;
            l1 = l1->next;
        } else {
            tail->next = l2;
            l2 = l2->next;
        }
        tail = tail->next;
    }

    tail->next = l1 ? l1 : l2;
    return dummy.next;
}
```

### Merge K Sorted Lists (Divide & Conquer)

```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    if (lists.empty()) return nullptr;

    while (lists.size() > 1) {
        vector<ListNode*> merged;
        for (int i = 0; i < lists.size(); i += 2) {
            ListNode* l1 = lists[i];
            ListNode* l2 = (i + 1 < lists.size()) ? lists[i + 1] : nullptr;
            merged.push_back(mergeTwoLists(l1, l2));
        }
        lists = merged;
    }
    return lists[0];
}
```

---

## 8. Pattern 5: Two List Pointers

### Intersection of Two Linked Lists

**Key Insight:** If lists intersect, from intersection onward they share the same tail length.

```cpp
ListNode* getIntersectionNode(ListNode* headA, ListNode* headB) {
    ListNode *a = headA, *b = headB;

    // When one reaches end, switch to other list
    // After 2 passes, both pointers travel same total distance
    while (a != b) {
        a = a ? a->next : headB;
        b = b ? b->next : headA;
    }

    return a;  // Intersection node or NULL
}
```

**Why It Works:**

```
List A: a1 → a2 → c1 → c2 → c3
List B:      b1 → b2 → b3 → c1 → c2 → c3

Pointer A travels: a1,a2,c1,c2,c3,NULL,b1,b2,b3,c1
Pointer B travels: b1,b2,b3,c1,c2,c3,NULL,a1,a2,c1
Both meet at c1 after lenA + lenB steps
```

---

## 9. Pattern 6: Cycle Detection (Floyd's Algorithm)

### Detect Cycle

```cpp
bool hasCycle(ListNode* head) {
    ListNode *slow = head, *fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;
    }
    return false;
}
```

### Find Cycle Start

```cpp
ListNode* detectCycle(ListNode* head) {
    ListNode *slow = head, *fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) break;
    }

    if (!fast || !fast->next) return nullptr;

    // Reset slow to head, move both 1 step
    slow = head;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }

    return slow;  // Cycle entry point
}
```

**Math Proof:**

```
Let:
- a = distance from head to cycle start
- b = distance from cycle start to meeting point
- c = cycle length

When they meet:
  slow traveled: a + b
  fast traveled: a + b + n*c (some multiple of cycle)

Since fast = 2 * slow:
  2(a + b) = a + b + n*c
  a + b = n*c
  a = n*c - b

So moving 'a' steps from head and 'b' steps from meeting point
both reach cycle start!
```

---

## 10. Pattern 7: Reorder & Rotate

### Reorder List (L0→Ln→L1→Ln-1→...)

**Steps:**
1. Find middle using fast/slow
2. Reverse second half
3. Merge two halves alternately

```cpp
void reorderList(ListNode* head) {
    if (!head || !head->next) return;

    // Step 1: Find middle
    ListNode *slow = head, *fast = head;
    while (fast->next && fast->next->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    // Step 2: Reverse second half
    ListNode* second = reverseList(slow->next);
    slow->next = nullptr;

    // Step 3: Merge alternately
    ListNode *first = head;
    while (second) {
        ListNode* temp1 = first->next;
        ListNode* temp2 = second->next;

        first->next = second;
        second->next = temp1;

        first = temp1;
        second = temp2;
    }
}
```

### Rotate List by k

```cpp
ListNode* rotateRight(ListNode* head, int k) {
    if (!head || !head->next || k == 0) return head;

    // Find length and tail
    int len = 1;
    ListNode* tail = head;
    while (tail->next) {
        tail = tail->next;
        len++;
    }

    k = k % len;
    if (k == 0) return head;

    // Find new tail (len - k - 1 steps from head)
    ListNode* newTail = head;
    for (int i = 0; i < len - k - 1; i++) {
        newTail = newTail->next;
    }

    ListNode* newHead = newTail->next;
    newTail->next = nullptr;
    tail->next = head;

    return newHead;
}
```

---

## 11. Pattern 8: Deep Copy & Random Pointers

### Copy List with Random Pointer

**Three-Step Approach:**

```
Step 1: Interleave copies
  1 → 1' → 2 → 2' → 3 → 3'

Step 2: Set random pointers for copies

Step 3: Separate the two lists
```

```cpp
Node* copyRandomList(Node* head) {
    if (!head) return nullptr;

    // Step 1: Create interleaved list
    Node* curr = head;
    while (curr) {
        Node* copy = new Node(curr->val);
        copy->next = curr->next;
        curr->next = copy;
        curr = copy->next;
    }

    // Step 2: Assign random pointers
    curr = head;
    while (curr) {
        if (curr->random) {
            curr->next->random = curr->random->next;
        }
        curr = curr->next->next;
    }

    // Step 3: Separate lists
    Node* dummy = new Node(0);
    Node* copyCurr = dummy;
    curr = head;

    while (curr) {
        Node* copy = curr->next;
        curr->next = copy->next;
        copyCurr->next = copy;
        copyCurr = copy;
        curr = curr->next;
    }

    return dummy->next;
}
```

### Palindrome Linked List

```cpp
bool isPalindrome(ListNode* head) {
    if (!head || !head->next) return true;

    // Find middle
    ListNode *slow = head, *fast = head;
    while (fast->next && fast->next->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    // Reverse second half
    ListNode* second = reverseList(slow->next);
    slow->next = nullptr;

    // Compare both halves
    ListNode *p1 = head, *p2 = second;
    while (p2) {
        if (p1->val != p2->val) return false;
        p1 = p1->next;
        p2 = p2->next;
    }
    return true;
}
```

---

## 12. Problem Recognition Framework

```
START: Linked List Problem
│
├─ Need to modify head or delete from head?
│   └─ YES → Use DUMMY HEAD NODE
│
├─ Need middle / nth from end / cycle?
│   └─ YES → Use FAST & SLOW POINTERS
│
├─ Need to reverse (full or partial)?
│   └─ YES → Use REVERSE TEMPLATE (iterative/recursive)
│
├─ Two sorted lists or K sorted lists?
│   └─ YES → Use MERGE PATTERN (two-pointer merge)
│
├─ Two lists, find intersection or difference?
│   └─ YES → Use TWO LIST POINTERS (switch heads trick)
│
├─ Reorder, rotate, or palindrome?
│   └─ YES → COMBINE: find middle + reverse + merge
│
└─ Random/deep copy?
    └─ YES → Interleave copy nodes pattern
```

### Pattern Selection Table

| Problem Type | Pattern | Time | Space |
|-------------|---------|------|-------|
| Remove elements | Dummy head | O(n) | O(1) |
| Find middle | Fast/slow | O(n) | O(1) |
| Nth from end | Fast/slow gap | O(n) | O(1) |
| Reverse list | Iterative flip | O(n) | O(1) |
| Merge sorted | Two pointers | O(n+m) | O(1) |
| Cycle detect | Floyd's | O(n) | O(1) |
| Intersection | Switch heads | O(n+m) | O(1) |
| Palindrome | Middle + reverse | O(n) | O(1) |
| Reorder list | Middle + reverse + merge | O(n) | O(1) |

---

## 13. Practice Problems

### Easy
1. Reverse Linked List - https://leetcode.com/problems/reverse-linked-list/
2. Merge Two Sorted Lists - https://leetcode.com/problems/merge-two-sorted-lists/
3. Linked List Cycle - https://leetcode.com/problems/linked-list-cycle/
4. Middle of the Linked List - https://leetcode.com/problems/middle-of-the-linked-list/
5. Remove Linked List Elements - https://leetcode.com/problems/remove-linked-list-elements/
6. Palindrome Linked List - https://leetcode.com/problems/palindrome-linked-list/
7. Intersection of Two Linked Lists - https://leetcode.com/problems/intersection-of-two-linked-lists/

### Medium
8. Add Two Numbers - https://leetcode.com/problems/add-two-numbers/
9. Remove Nth Node From End of List - https://leetcode.com/problems/remove-nth-node-from-end-of-list/
10. Swap Nodes in Pairs - https://leetcode.com/problems/swap-nodes-in-pairs/
11. Rotate List - https://leetcode.com/problems/rotate-list/
12. Reverse Linked List II - https://leetcode.com/problems/reverse-linked-list-ii/
13. Reorder List - https://leetcode.com/problems/reorder-list/
14. Copy List with Random Pointer - https://leetcode.com/problems/copy-list-with-random-pointer/
15. Linked List Cycle II - https://leetcode.com/problems/linked-list-cycle-ii/
16. Odd Even Linked List - https://leetcode.com/problems/odd-even-linked-list/
17. Partition List - https://leetcode.com/problems/partition-list/

### Hard
18. Merge K Sorted Lists - https://leetcode.com/problems/merge-k-sorted-lists/
19. Reverse Nodes in k-Group - https://leetcode.com/problems/reverse-nodes-in-k-group/
20. LRU Cache - https://leetcode.com/problems/lru-cache/

---

## 14. Common Pitfalls

### Pitfall 1: Losing Reference to Head

```cpp
// WRONG: head moves and original reference lost
while (head) {
    head = head->next;  // Can't return original head!
}

// RIGHT: Use separate curr pointer
ListNode* curr = head;
while (curr) {
    curr = curr->next;
}
```

### Pitfall 2: Null Pointer Dereference

```cpp
// WRONG: Access fast->next without checking fast
while (fast->next) { ... }

// RIGHT: Check fast AND fast->next
while (fast && fast->next) { ... }
```

### Pitfall 3: Not Using Dummy for Head Deletion

```cpp
// WRONG: Special case for deleting head
if (head->val == val) head = head->next;

// RIGHT: Dummy node handles all cases uniformly
ListNode dummy(0);
dummy.next = head;
```

### Pitfall 4: Memory Leaks

```cpp
// WRONG: Remove node without deleting
prev->next = curr->next;

// RIGHT: Free memory in C++
ListNode* toDelete = prev->next;
prev->next = toDelete->next;
delete toDelete;
```

### Pitfall 5: Off-by-One in Nth From End

```cpp
// WRONG: Move fast only n steps
for (int i = 0; i < n; i++) fast = fast->next;

// RIGHT: Move fast n+1 steps (to stop before target)
for (int i = 0; i <= n; i++) fast = fast->next;
```

---

## 15. Template Summary

### Dummy Head
```cpp
ListNode dummy(0);
dummy.next = head;
ListNode* prev = &dummy;
// ... process ...
return dummy.next;
```

### Fast & Slow
```cpp
ListNode *slow = head, *fast = head;
while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
}
```

### Reverse
```cpp
ListNode* prev = nullptr;
while (curr) {
    ListNode* next = curr->next;
    curr->next = prev;
    prev = curr;
    curr = next;
}
return prev;
```

### Merge Two Sorted
```cpp
ListNode dummy(0);
ListNode* tail = &dummy;
while (l1 && l2) {
    if (l1->val <= l2->val) { tail->next = l1; l1 = l1->next; }
    else { tail->next = l2; l2 = l2->next; }
    tail = tail->next;
}
tail->next = l1 ? l1 : l2;
return dummy.next;
```

---

## Final Thoughts

**The Power of Linked List Patterns:**
- Dummy head eliminates edge cases
- Fast/slow solves middle, cycle, and nth-from-end in one pass
- Reverse is the building block for reorder and palindrome problems
- Merge pattern extends to K-way merge with divide & conquer

**How to Master:**
1. Always draw the pointer movements on paper
2. Use dummy head by default when modifying structure
3. Identify if fast/slow or reverse sub-pattern applies
4. Practice until pointer manipulation feels natural

---

*Master these patterns and linked list problems become mechanical!*
