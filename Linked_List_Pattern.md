# Ultimate Linked List Template

## Table of Contents
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [Universal Node & Utility Templates](#2-universal-node--utility-templates)
3. [Why Linked List Patterns Work](#3-why-linked-list-patterns-work)
4. [Mental Model & Pattern Recognition](#4-mental-model--pattern-recognition)
5. [Basic Applications](#5-basic-applications)
6. [Advanced Applications](#6-advanced-applications)
7. [Problem-Solving Framework](#7-problem-solving-framework)
8. [Common Pitfalls & How to Avoid](#8-common-pitfalls--how-to-avoid)
9. [Practice Problems (Ordered by Difficulty)](#9-practice-problems-ordered-by-difficulty)

---

## 1. Core Concept & Philosophy

### What Linked List Problem-Solving Really Is

**Traditional Understanding (INCOMPLETE):**  
"Linked list is just inserting/deleting nodes"

**True Understanding (COMPLETE):**  
"Linked list problems are about controlling **node relationships** using a few pointer primitives: **traverse, relink, split, merge, reverse, and detect structure**."

### The Generalized Form

```
Given head, transform pointer topology to satisfy constraints.
```

This is the key shift: most linked list questions are not about values first — they’re about **edges between nodes**.

### The 6 Primitive Operations

1. **Traverse**: move pointer safely (`curr = curr->next`)
2. **Relink**: redirect one edge (`a->next = b`)
3. **Split**: cut list into segments
4. **Merge**: weave two sorted/unsorted streams
5. **Reverse**: invert directional edges in-place
6. **Detect structure**: cycle, middle, intersection via pointer speed/alignment

Master these primitives, and most linked list problems reduce to composition.

---

## 2. Universal Node & Utility Templates

### C++ Node Definition

```cpp
struct ListNode {
    int val;
    ListNode* next;
    ListNode(int x) : val(x), next(nullptr) {}
};
```

### Template A: Safe Traversal

```cpp
for (ListNode* curr = head; curr != nullptr; curr = curr->next) {
    // process curr->val
}
```

### Template B: Dummy Node (Most Useful Pattern)

Use a dummy node whenever head may change (delete first node, merge, partition, etc.).

```cpp
ListNode dummy(0);
dummy.next = head;
ListNode* prev = &dummy;
ListNode* curr = head;
```

### Template C: In-Place Reverse (Core Primitive)

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* curr = head;

    while (curr) {
        ListNode* nextNode = curr->next;
        curr->next = prev;
        prev = curr;
        curr = nextNode;
    }

    return prev;
}
```

### Template D: Fast & Slow Pointer

```cpp
ListNode* slow = head;
ListNode* fast = head;

while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
}
// slow is at middle (for even length, second middle)
```

### Template E: Merge Two Sorted Lists

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

---

## 3. Why Linked List Patterns Work

### Invariant Thinking

At each step, ask:
- Which segment is already correct?
- Which pointer is the boundary between solved and unsolved parts?
- Did I preserve access to remaining nodes?

### Golden Rule: Never Lose the Rest of the List

Before rewiring `curr->next`, store next:

```cpp
ListNode* nextNode = curr->next;
// now safe to modify curr->next
```

### Why Dummy Node Eliminates Edge Cases

Without dummy: deleting head needs special if-case.  
With dummy: all deletions are "delete node after prev" uniformly.

---

## 4. Mental Model & Pattern Recognition

### When to Use Which Pattern?

1. **Need middle / cycle / palindrome split?** → Fast & Slow
2. **Need local rewiring / reverse order?** → Reverse primitive
3. **Head may change?** → Dummy node
4. **Two sorted streams?** → Merge pattern
5. **Every k nodes / group transforms?** → Segment + reconnect
6. **Intersection?** → Length alignment or pointer switching

### Visual Pattern Cues

```
Pattern 1: Reverse
A -> B -> C -> null
becomes
null <- A <- B <- C

Pattern 2: Dummy-based deletion
dummy -> head -> ...
Operate on prev->next uniformly

Pattern 3: Fast/Slow
slow: 1 step, fast: 2 steps
- meet => cycle
- fast ends => no cycle
```

---

## 5. Basic Applications

### Example 1: Reverse Linked List (LC 206)

**Problem:** Reverse a singly linked list.

**Approach:** Iterative 3-pointer rewiring.

```cpp
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* curr = head;

    while (curr) {
        ListNode* nextNode = curr->next;
        curr->next = prev;
        prev = curr;
        curr = nextNode;
    }

    return prev;
}
```

**Dry Run:** `1 -> 2 -> 3 -> null`
- Step 1: reverse 1, prev=1, curr=2
- Step 2: reverse 2, prev=2->1, curr=3
- Step 3: reverse 3, prev=3->2->1, curr=null
- Return prev

**Complexity:** Time `O(n)`, Space `O(1)`

---

### Example 2: Remove Nth Node From End (LC 19)

**Problem:** Delete nth node from end in one pass.

**Approach:** Two pointers with gap `n`, plus dummy.

```cpp
ListNode* removeNthFromEnd(ListNode* head, int n) {
    ListNode dummy(0);
    dummy.next = head;

    ListNode* fast = &dummy;
    ListNode* slow = &dummy;

    for (int i = 0; i < n; i++) fast = fast->next;

    while (fast->next) {
        fast = fast->next;
        slow = slow->next;
    }

    ListNode* toDelete = slow->next;
    slow->next = slow->next->next;
    delete toDelete;

    return dummy.next;
}
```

**Why this works:** when fast reaches end, slow is just before target.

**Complexity:** Time `O(n)`, Space `O(1)`

---

### Example 3: Middle of the Linked List (LC 876)

```cpp
ListNode* middleNode(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    return slow;
}
```

**Complexity:** Time `O(n)`, Space `O(1)`

---

## 6. Advanced Applications

### Example 4: Linked List Cycle Detection (LC 141)

```cpp
bool hasCycle(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) return true;
    }

    return false;
}
```

### Example 5: Detect Cycle Start (LC 142)

After first meeting, move one pointer to head; step both 1-by-1. Meeting point is cycle start.

```cpp
ListNode* detectCycle(ListNode* head) {
    ListNode* slow = head;
    ListNode* fast = head;

    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
        if (slow == fast) {
            ListNode* p1 = head;
            ListNode* p2 = slow;
            while (p1 != p2) {
                p1 = p1->next;
                p2 = p2->next;
            }
            return p1;
        }
    }

    return nullptr;
}
```

---

### Example 6: Merge Two Sorted Lists (LC 21)

(Already shown in template section)

Key pattern: tail pointer always marks last finalized node.

---

### Example 7: Reverse Nodes in k-Group (LC 25)

**Core idea:**
1. Find kth node from current group start.
2. Reverse that closed segment.
3. Reconnect previous tail and next segment.

```cpp
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode dummy(0);
        dummy.next = head;
        ListNode* groupPrev = &dummy;

        while (true) {
            ListNode* kth = getKth(groupPrev, k);
            if (!kth) break;
            ListNode* groupNext = kth->next;

            // reverse [groupPrev->next ... kth]
            ListNode* prev = groupNext;
            ListNode* curr = groupPrev->next;
            while (curr != groupNext) {
                ListNode* tmp = curr->next;
                curr->next = prev;
                prev = curr;
                curr = tmp;
            }

            ListNode* oldGroupStart = groupPrev->next;
            groupPrev->next = kth;
            groupPrev = oldGroupStart;
        }

        return dummy.next;
    }

private:
    ListNode* getKth(ListNode* curr, int k) {
        while (curr && k > 0) {
            curr = curr->next;
            k--;
        }
        return curr;
    }
};
```

**Complexity:** Time `O(n)`, Space `O(1)`

---

### Example 8: Palindrome Linked List (LC 234)

**Pattern combo:** middle + reverse second half + compare + optional restore.

```cpp
bool isPalindrome(ListNode* head) {
    if (!head || !head->next) return true;

    ListNode* slow = head;
    ListNode* fast = head;
    while (fast && fast->next) {
        slow = slow->next;
        fast = fast->next->next;
    }

    // reverse second half
    ListNode* prev = nullptr;
    ListNode* curr = slow;
    while (curr) {
        ListNode* nextNode = curr->next;
        curr->next = prev;
        prev = curr;
        curr = nextNode;
    }

    ListNode* p1 = head;
    ListNode* p2 = prev;
    while (p2) {
        if (p1->val != p2->val) return false;
        p1 = p1->next;
        p2 = p2->next;
    }

    return true;
}
```

---

## 7. Problem-Solving Framework

### Step-by-Step Checklist

**Step 1: Identify Operation Type**
- Traverse?
- Delete/insert?
- Reverse?
- Merge/split?
- Detect structure (middle/cycle/intersection)?

**Step 2: Decide Pointer Toolkit**
- Dummy needed?
- Fast/slow needed?
- Prev/curr/next triad needed?

**Step 3: Define Invariant**
- What part is already correct after each loop?

**Step 4: Handle Head/Null Edge Cases First**
- Empty list
- Single node
- Operation at head

**Step 5: Perform Pointer Updates in Safe Order**
1. save next
2. rewire current
3. advance pointers

---

## 8. Common Pitfalls & How to Avoid

### Pitfall 1: Losing Remaining List
```cpp
// WRONG
curr->next = prev;
curr = curr->next; // now moves backward, rest lost

// RIGHT
ListNode* nextNode = curr->next;
curr->next = prev;
curr = nextNode;
```

### Pitfall 2: Forgetting Dummy for Head Changes
Use dummy for delete/insert near head to unify logic.

### Pitfall 3: Null Dereference
Always guard:
```cpp
while (fast && fast->next) { ... }
```

### Pitfall 4: Cycles from Incorrect Rewiring
After manipulations, ensure tails point to correct next segment or `nullptr`.

### Pitfall 5: Memory Leaks (C++)
If deleting nodes, actually `delete` removed node when ownership implies responsibility.

---

## 9. Practice Problems (Ordered by Difficulty)

### Easy
1. Reverse Linked List (LC 206)
2. Middle of the Linked List (LC 876)
3. Merge Two Sorted Lists (LC 21)
4. Linked List Cycle (LC 141)
5. Remove Linked List Elements (LC 203)
6. Palindrome Linked List (LC 234)

### Medium
1. Remove Nth Node From End of List (LC 19)
2. Add Two Numbers (LC 2)
3. Odd Even Linked List (LC 328)
4. Sort List (LC 148)
5. Partition List (LC 86)
6. Reorder List (LC 143)
7. Rotate List (LC 61)
8. Swap Nodes in Pairs (LC 24)

### Hard
1. Reverse Nodes in k-Group (LC 25)
2. Merge k Sorted Lists (LC 23)
3. Copy List with Random Pointer (LC 138)
4. LRU Cache (LC 146) (DLL + hashmap design)
5. All O`one Data Structure (LC 432) (advanced DLL design)

---

## Key Takeaways

1. Linked list mastery = pointer invariants + safe update order.
2. Dummy node removes most head-edge-case bugs.
3. Fast/slow unlocks middle, cycle, and split-based patterns.
4. Reverse is the most reusable primitive — practice it deeply.
5. Most “hard” linked list problems are compositions of 2–3 core patterns.

---

## Final Thoughts

If binary search is about **decision boundaries**, linked lists are about **pointer topology control**.

Whenever you face a linked list problem, ask:
- Which links must change?
- Where is my stable boundary pointer?
- Do I need dummy / fast-slow / reverse primitive?
- What invariant stays true after each iteration?

Master these, and linked list questions become systematic instead of scary. 🚀
