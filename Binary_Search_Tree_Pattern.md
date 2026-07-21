# Ultimate Binary Search Tree (BST) Pattern Guide - From Basics to Mastery

## Table of Contents

### Part A: Foundation
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [BST Properties & Operations](#2-bst-properties--operations)
3. [BST vs Binary Tree](#3-bst-vs-binary-tree)

### Part B: Core Patterns
4. [Pattern 1: Inorder Traversal (Sorted Order)](#4-pattern-1-inorder-traversal-sorted-order)
5. [Pattern 2: Search, Insert & Delete](#5-pattern-2-search-insert--delete)
6. [Pattern 3: Validate BST](#6-pattern-3-validate-bst)
7. [Pattern 4: Kth Smallest / Kth Largest](#7-pattern-4-kth-smallest--kth-largest)

### Part C: Advanced Patterns
8. [Pattern 5: LCA in BST](#8-pattern-5-lca-in-bst)
9. [Pattern 6: Range Queries & Trim BST](#9-pattern-6-range-queries--trim-bst)
10. [Pattern 7: Convert Sorted Array to BST](#10-pattern-7-convert-sorted-array-to-bst)
11. [Pattern 8: BST Iterator & Inorder Successor](#11-pattern-8-bst-iterator--inorder-successor)

### Part D: Problem Solving
12. [Problem Recognition Framework](#12-problem-recognition-framework)
13. [Practice Problems](#13-practice-problems)
14. [Common Pitfalls](#14-common-pitfalls)
15. [Template Summary](#15-template-summary)

---

## 1. Core Concept & Philosophy

### What is a BST Really About?

**Wrong Understanding:**
"A BST is a binary tree where left child < parent < right child"

**Correct Understanding:**
"A BST is a binary tree where **every node in left subtree < node < every node in right subtree**, enabling **O(h) search, insert, delete** — O(log n) when balanced, O(n) when skewed"

### The BST Property (Global, Not Local)

```
WRONG CHECK (local only):
  node.left.val < node.val < node.right.val

CORRECT CHECK (global):
  ALL nodes in left subtree < node.val
  ALL nodes in right subtree > node.val

Counterexample for local-only check:
       10
      /  \
     5   15
        /  \
       6   20

Local check passes at 10, but 6 < 10 violates BST (6 is in right subtree of 10)
```

### BST Visual

```
         8
        / \
       3   10
      / \    \
     1   6    14
        / \   /
       4   7 13

Inorder: 1, 3, 4, 6, 7, 8, 10, 13, 14  ← SORTED!
```

### When to Use BST Patterns?

**Key Indicators:**
1. Need **sorted order** or **Kth element**
2. **Dynamic insert/delete** with ordering
3. **Range queries** (values between low and high)
4. **Search** in O(log n) average
5. Problem mentions "binary search tree" explicitly

---

## 2. BST Properties & Operations

### Core Properties

```
1. Inorder traversal gives SORTED sequence
2. Left subtree < Root < Right subtree (all nodes, not just children)
3. No duplicate values (typically) or handle duplicates consistently
4. Search, Insert, Delete: O(h) where h = height
5. Min = leftmost node, Max = rightmost node
```

### Time Complexities

| Operation | Average (Balanced) | Worst (Skewed) |
|-----------|-------------------|----------------|
| Search    | O(log n)          | O(n)           |
| Insert    | O(log n)          | O(n)           |
| Delete    | O(log n)          | O(n)           |
| Min/Max   | O(log n)          | O(n)           |
| Inorder   | O(n)              | O(n)           |

---

## 3. BST vs Binary Tree

```
BINARY TREE:                    BST:
- No ordering constraint        - Strict ordering property
- All traversal patterns        - Inorder = sorted
- General recursion             - Exploit ordering for O(log n) search
- LCA needs full search         - LCA uses comparison (O(h))
- Validate: structure only      - Validate: ordering + structure
```

---

## 4. Pattern 1: Inorder Traversal (Sorted Order)

### Concept

```
Inorder (Left → Root → Right) on BST = ascending sorted order
Reverse inorder = descending order
```

### Template

```cpp
void inorder(TreeNode* root, vector<int>& result) {
    if (!root) return;
    inorder(root->left, result);
    result.push_back(root->val);
    inorder(root->right, result);
}
```

### Iterative Inorder (Stack)

```cpp
vector<int> inorderIterative(TreeNode* root) {
    vector<int> result;
    stack<TreeNode*> st;
    TreeNode* curr = root;

    while (curr || !st.empty()) {
        while (curr) {
            st.push(curr);
            curr = curr->left;
        }
        curr = st.top();
        st.pop();
        result.push_back(curr->val);
        curr = curr->right;
    }
    return result;
}
```

### Application: Kth Smallest via Inorder

```cpp
int kthSmallest(TreeNode* root, int k) {
    stack<TreeNode*> st;
    TreeNode* curr = root;

    while (curr || !st.empty()) {
        while (curr) {
            st.push(curr);
            curr = curr->left;
        }
        curr = st.top();
        st.pop();
        if (--k == 0) return curr->val;
        curr = curr->right;
    }
    return -1;
}
```

---

## 5. Pattern 2: Search, Insert & Delete

### Search

```cpp
TreeNode* searchBST(TreeNode* root, int val) {
    if (!root || root->val == val) return root;

    if (val < root->val) return searchBST(root->left, val);
    else return searchBST(root->right, val);
}

// Iterative
TreeNode* searchIterative(TreeNode* root, int val) {
    while (root && root->val != val) {
        root = val < root->val ? root->left : root->right;
    }
    return root;
}
```

### Insert

```cpp
TreeNode* insertIntoBST(TreeNode* root, int val) {
    if (!root) return new TreeNode(val);

    if (val < root->val) root->left = insertIntoBST(root->left, val);
    else root->right = insertIntoBST(root->right, val);

    return root;
}
```

### Delete (Three Cases)

```cpp
TreeNode* deleteNode(TreeNode* root, int key) {
    if (!root) return nullptr;

    if (key < root->val) {
        root->left = deleteNode(root->left, key);
    } else if (key > root->val) {
        root->right = deleteNode(root->right, key);
    } else {
        // Case 1: Leaf node
        if (!root->left && !root->right) {
            delete root;
            return nullptr;
        }
        // Case 2: One child
        if (!root->left) {
            TreeNode* temp = root->right;
            delete root;
            return temp;
        }
        if (!root->right) {
            TreeNode* temp = root->left;
            delete root;
            return temp;
        }
        // Case 3: Two children — replace with inorder successor (min of right)
        TreeNode* successor = root->right;
        while (successor->left) successor = successor->left;
        root->val = successor->val;
        root->right = deleteNode(root->right, successor->val);
    }
    return root;
}
```

**Delete Cases:**

```
Case 1 — Leaf:           Case 2 — One child:      Case 3 — Two children:
    5                        5                        5
   /                        /                        / \
  3                        3                        3   7
Delete 3 →               Delete 5 →               Delete 5 →
  5                          3                        3
                            /                        / \
                           3                        3   7
                                                    (replace 5 with 6, min of right)
```

---

## 6. Pattern 3: Validate BST

### Approach 1: Range Checking (Best)

```cpp
bool isValidBST(TreeNode* root) {
    function<bool(TreeNode*, long, long)> validate = [&](TreeNode* node, long minVal, long maxVal) {
        if (!node) return true;
        if (node->val <= minVal || node->val >= maxVal) return false;
        return validate(node->left, minVal, node->val)
            && validate(node->right, node->val, maxVal);
    };
    return validate(root, LONG_MIN, LONG_MAX);
}
```

**Why long?** Node values can be INT_MIN or INT_MAX — need bounds outside int range.

### Approach 2: Inorder (Check Sorted)

```cpp
bool isValidBST(TreeNode* root) {
    long prev = LONG_MIN;

    function<bool(TreeNode*)> inorder = [&](TreeNode* node) {
        if (!node) return true;
        if (!inorder(node->left)) return false;
        if (node->val <= prev) return false;
        prev = node->val;
        return inorder(node->right);
    };
    return inorder(root);
}
```

**Key:** Each inorder value must be **strictly greater** than previous.

---

## 7. Pattern 4: Kth Smallest / Kth Largest

### Kth Smallest — Inorder

Already shown in Pattern 1 — stop at Kth element.

### Kth Largest — Reverse Inorder

```cpp
int kthLargest(TreeNode* root, int k) {
    int result = 0;

    function<void(TreeNode*)> reverseInorder = [&](TreeNode* node) {
        if (!node) return;
        reverseInorder(node->right);
        if (--k == 0) { result = node->val; return; }
        reverseInorder(node->left);
    };
    reverseInorder(root);
    return result;
}
```

### Kth Smallest with Augmented BST (Follow-up)

```cpp
struct AugmentedNode {
    int val, count;  // count = size of subtree
    AugmentedNode *left, *right;
};

// During insert, update count
// During search, if left.count >= k: go left
// elif left.count + 1 == k: return root
// else: k -= left.count + 1, go right
```

---

## 8. Pattern 5: LCA in BST

### Concept

```
Use BST property: if both p and q < root → go left
                  if both p and q > root → go right
                  else → root is LCA
```

```cpp
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    while (root) {
        if (p->val < root->val && q->val < root->val) {
            root = root->left;
        } else if (p->val > root->val && q->val > root->val) {
            root = root->right;
        } else {
            return root;
        }
    }
    return nullptr;
}
```

**Why simpler than general LCA?** BST ordering tells us which subtree to search — no need to search both sides.

**Dry Run:**

```
BST:       6
          / \
         2   8
        / \ / \
       0  4 7 9

LCA(2, 8) = 6  (split at root)
LCA(2, 4) = 2  (2 is ancestor of 4)
LCA(7, 9) = 8  (both in right subtree of 6, split at 8)
```

---

## 9. Pattern 6: Range Queries & Trim BST

### Range Sum of BST

```cpp
int rangeSumBST(TreeNode* root, int low, int high) {
    if (!root) return 0;

    if (root->val < low) return rangeSumBST(root->right, low, high);
    if (root->val > high) return rangeSumBST(root->left, low, high);

    return root->val
         + rangeSumBST(root->left, low, high)
         + rangeSumBST(root->right, low, high);
}
```

**Optimization:** Skip entire subtrees when out of range.

### Trim BST

```cpp
TreeNode* trimBST(TreeNode* root, int low, int high) {
    if (!root) return nullptr;

    if (root->val < low) return trimBST(root->right, low, high);
    if (root->val > high) return trimBST(root->left, low, high);

    root->left = trimBST(root->left, low, high);
    root->right = trimBST(root->right, low, high);
    return root;
}
```

---

## 10. Pattern 7: Convert Sorted Array to BST

### Concept

```
Pick middle element as root (ensures balance).
Recursively build left from left half, right from right half.
```

```cpp
TreeNode* sortedArrayToBST(vector<int>& nums) {
    function<TreeNode*(int, int)> build = [&](int lo, int hi) -> TreeNode* {
        if (lo > hi) return nullptr;
        int mid = lo + (hi - lo) / 2;
        TreeNode* node = new TreeNode(nums[mid]);
        node->left = build(lo, mid - 1);
        node->right = build(mid + 1, hi);
        return node;
    };
    return build(0, nums.size() - 1);
}
```

**Result:** Height-balanced BST with O(n) time.

### Convert Sorted List to BST

```cpp
// Use inorder simulation with global list pointer
ListNode* curr;
TreeNode* sortedListToBST(ListNode* head) {
    curr = head;
    function<TreeNode*(int, int)> build = [&](int lo, int hi) -> TreeNode* {
        if (lo > hi) return nullptr;
        int mid = lo + (hi - lo) / 2;
        TreeNode* left = build(lo, mid - 1);
        TreeNode* node = new TreeNode(curr->val);
        curr = curr->val;
        node->left = left;
        node->right = build(mid + 1, hi);
        return node;
    };
    int n = getLength(head);
    return build(0, n - 1);
}
```

---

## 11. Pattern 8: BST Iterator & Inorder Successor

### BST Iterator (O(1) amortized next)

```cpp
class BSTIterator {
    stack<TreeNode*> st;

    void pushLeft(TreeNode* node) {
        while (node) {
            st.push(node);
            node = node->left;
        }
    }

public:
    BSTIterator(TreeNode* root) {
        pushLeft(root);
    }

    int next() {
        TreeNode* node = st.top();
        st.pop();
        pushLeft(node->right);
        return node->val;
    }

    bool hasNext() {
        return !st.empty();
    }
};
```

### Inorder Successor

```cpp
TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
    TreeNode* successor = nullptr;

    while (root) {
        if (p->val < root->val) {
            successor = root;  // Potential successor
            root = root->left;
        } else {
            root = root->right;
        }
    }
    return successor;
}
```

**Key Insight:** When going left, current node could be the successor.

---

## 12. Problem Recognition Framework

```
START: BST Problem
│
├─ Need sorted order or Kth element?
│   └─ INORDER (or reverse inorder for Kth largest)
│
├─ Search/insert/delete a value?
│   └─ Compare and go left/right — O(h)
│
├─ Check if tree is valid BST?
│   └─ Range validation (min, max bounds)
│
├─ Find LCA of two nodes?
│   └─ BST LCA — compare values, no full tree search
│
├─ Sum/count in range [low, high]?
│   └─ Prune subtrees outside range
│
├─ Build balanced BST from sorted data?
│   └─ Pick middle as root, recurse on halves
│
└─ Sequential access to sorted values?
    └─ BST Iterator with stack
```

### Pattern Selection Table

| Problem Type | Pattern | Time | Space |
|-------------|---------|------|-------|
| Kth smallest | Inorder, stop at k | O(h + k) | O(h) |
| Validate BST | Range check | O(n) | O(h) |
| LCA in BST | Compare values | O(h) | O(1) |
| Range sum | Pruned recursion | O(n) | O(h) |
| Sorted array → BST | Middle as root | O(n) | O(log n) |
| BST Iterator | Stack + left push | O(1) amortized | O(h) |
| Delete node | 3 cases | O(h) | O(h) |

---

## 13. Practice Problems

### Easy
1. Search in a Binary Search Tree - https://leetcode.com/problems/search-in-a-binary-search-tree/
2. Minimum Absolute Difference in BST - https://leetcode.com/problems/minimum-absolute-difference-in-bst/
3. Range Sum of BST - https://leetcode.com/problems/range-sum-of-bst/
4. Increasing Order Search Tree - https://leetcode.com/problems/increasing-order-search-tree/

### Medium
5. Validate Binary Search Tree - https://leetcode.com/problems/validate-binary-search-tree/
6. Kth Smallest Element in a BST - https://leetcode.com/problems/kth-smallest-element-in-a-bst/
7. Lowest Common Ancestor of a Binary Search Tree - https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/
8. Insert into a Binary Search Tree - https://leetcode.com/problems/insert-into-a-binary-search-tree/
9. Delete Node in a BST - https://leetcode.com/problems/delete-node-in-a-bst/
10. Convert Sorted Array to Binary Search Tree - https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/
11. Construct Binary Search Tree from Preorder Traversal - https://leetcode.com/problems/construct-binary-search-tree-from-preorder-traversal/
12. Trim a Binary Search Tree - https://leetcode.com/problems/trim-a-binary-search-tree/
13. Binary Search Tree Iterator - https://leetcode.com/problems/binary-search-tree-iterator/
14. Balance a Binary Search Tree - https://leetcode.com/problems/balance-a-binary-search-tree/
15. All Elements in Two Binary Search Trees - https://leetcode.com/problems/all-elements-in-two-binary-search-trees/

### Hard
16. Count of Smaller Numbers After Self - https://leetcode.com/problems/count-of-smaller-numbers-after-self/
17. Maximum Sum BST in Binary Tree - https://leetcode.com/problems/maximum-sum-bst-in-binary-tree/
18. Serialize and Deserialize BST - https://leetcode.com/problems/serialize-and-deserialize-bst/

---

## 14. Common Pitfalls

### Pitfall 1: Local vs Global BST Check

```cpp
// WRONG: Only checks immediate children
node->left->val < node->val && node->right->val > node->val

// RIGHT: Pass valid range down the tree
validate(node, minVal, maxVal)
```

### Pitfall 2: Integer Bounds in Validation

```cpp
// WRONG: INT_MIN and INT_MAX as bounds fail for nodes with those values
validate(root, INT_MIN, INT_MAX)

// RIGHT: Use long
validate(root, LONG_MIN, LONG_MAX)
```

### Pitfall 3: Equal Values in BST

```cpp
// Typically BST uses strict inequality
// left < root < right (not <= and >=)
if (node->val <= prev) return false;  // Strict!
```

### Pitfall 4: Delete Node with Two Children

```cpp
// WRONG: Just delete and leave dangling
delete root;

// RIGHT: Replace with inorder successor (min of right subtree)
// OR inorder predecessor (max of left subtree)
root->val = successor->val;
root->right = deleteNode(root->right, successor->val);
```

### Pitfall 5: Skewed BST Performance

```
Sorted input → skewed BST → O(n) operations
Solution: AVL/Red-Black tree or rebuild balanced BST
For interviews: mention balance; use "middle as root" for construction
```

---

## 15. Template Summary

### Search
```cpp
while (root && root->val != val) {
    root = val < root->val ? root->left : root->right;
}
```

### Validate BST (Range)
```cpp
bool validate(TreeNode* node, long minV, long maxV) {
    if (!node) return true;
    if (node->val <= minV || node->val >= maxV) return false;
    return validate(node->left, minV, node->val)
        && validate(node->right, node->val, maxV);
}
```

### Kth Smallest (Inorder)
```cpp
while (curr || !st.empty()) {
    while (curr) { st.push(curr); curr = curr->left; }
    curr = st.top(); st.pop();
    if (--k == 0) return curr->val;
    curr = curr->right;
}
```

### LCA in BST
```cpp
while (root) {
    if (p->val < root->val && q->val < root->val) root = root->left;
    else if (p->val > root->val && q->val > root->val) root = root->right;
    else return root;
}
```

### Sorted Array to BST
```cpp
TreeNode* build(int lo, int hi) {
    if (lo > hi) return nullptr;
    int mid = lo + (hi - lo) / 2;
    TreeNode* node = new TreeNode(nums[mid]);
    node->left = build(lo, mid - 1);
    node->right = build(mid + 1, hi);
    return node;
}
```

### BST Iterator
```cpp
int next() {
    TreeNode* node = st.top(); st.pop();
    pushLeft(node->right);
    return node->val;
}
```

---

## Final Thoughts

**The Power of BST Patterns:**
- Inorder = instant sorted order
- Search/insert/delete in O(h) using comparisons
- LCA becomes trivial with BST property
- Range queries prune entire subtrees
- Middle-element construction guarantees balance

**How to Master:**
1. Always validate BST with range bounds, not local checks
2. Inorder is your best friend for Kth element problems
3. Delete has three cases — memorize them
4. BST LCA is O(h) iterative — much simpler than general LCA

---

*BST adds ordering to trees — exploit that property and problems become logarithmic!*
