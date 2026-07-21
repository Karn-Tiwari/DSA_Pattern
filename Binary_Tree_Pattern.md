# Ultimate Binary Tree Pattern Guide - From Basics to Mastery

## Table of Contents

### Part A: Foundation
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [Tree Node & Traversals](#2-tree-node--traversals)
3. [Recursive Thinking Framework](#3-recursive-thinking-framework)

### Part B: Core Patterns
4. [Pattern 1: DFS Traversals (Pre/In/Post Order)](#4-pattern-1-dfs-traversals-preinpost-order)
5. [Pattern 2: BFS Level-Order Traversal](#5-pattern-2-bfs-level-order-traversal)
6. [Pattern 3: Height, Depth & Diameter](#6-pattern-3-height-depth--diameter)
7. [Pattern 4: Path Sum Problems](#7-pattern-4-path-sum-problems)

### Part C: Advanced Patterns
8. [Pattern 5: Lowest Common Ancestor (LCA)](#8-pattern-5-lowest-common-ancestor-lca)
9. [Pattern 6: Tree Construction from Traversals](#9-pattern-6-tree-construction-from-traversals)
10. [Pattern 7: Serialize & Deserialize](#10-pattern-7-serialize--deserialize)
11. [Pattern 8: Morris Traversal (O(1) Space)](#11-pattern-8-morris-traversal-o1-space)

### Part D: Problem Solving
12. [Problem Recognition Framework](#12-problem-recognition-framework)
13. [Practice Problems](#13-practice-problems)
14. [Common Pitfalls](#14-common-pitfalls)
15. [Template Summary](#15-template-summary)

---

## 1. Core Concept & Philosophy

### What is a Binary Tree Really About?

**Wrong Understanding:**
"A binary tree is just a graph with two children per node"

**Correct Understanding:**
"A binary tree is a **hierarchical recursive structure** where each node has at most **two children**, and most tree problems reduce to: **solve for left subtree + solve for right subtree + combine at current node**"

### Tree Structure

```
         1          ← Root (no parent)
        / \
       2   3        ← Internal nodes
      / \   \
     4   5   6      ← Leaves (no children)

Terminology:
- Height: longest path root → leaf (edges)
- Depth: distance root → node
- Level: depth + 1
- Subtree: node + all descendants
```

### When to Use Which Traversal?

```
PREORDER  (Root → Left → Right):  Copy tree, prefix expressions, top-down info
INORDER   (Left → Root → Right):  BST sorted order, validate BST
POSTORDER (Left → Right → Root):  Delete tree, bottom-up computation
LEVEL-ORDER (BFS):                Level-by-level, shortest path in tree
```

---

## 2. Tree Node & Traversals

### Node Definition

```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode() : val(0), left(nullptr), right(nullptr) {}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    TreeNode(int x, TreeNode* l, TreeNode* r) : val(x), left(l), right(r) {}
};
```

### Recursive Traversals

```cpp
// Preorder: Root → Left → Right
void preorder(TreeNode* root, vector<int>& result) {
    if (!root) return;
    result.push_back(root->val);
    preorder(root->left, result);
    preorder(root->right, result);
}

// Inorder: Left → Root → Right
void inorder(TreeNode* root, vector<int>& result) {
    if (!root) return;
    inorder(root->left, result);
    result.push_back(root->val);
    inorder(root->right, result);
}

// Postorder: Left → Right → Root
void postorder(TreeNode* root, vector<int>& result) {
    if (!root) return;
    postorder(root->left, result);
    postorder(root->right, result);
    result.push_back(root->val);
}
```

### Iterative Traversals

```cpp
// Iterative Preorder
vector<int> preorderIterative(TreeNode* root) {
    vector<int> result;
    if (!root) return result;

    stack<TreeNode*> st;
    st.push(root);

    while (!st.empty()) {
        TreeNode* node = st.top();
        st.pop();
        result.push_back(node->val);
        if (node->right) st.push(node->right);
        if (node->left) st.push(node->left);
    }
    return result;
}

// Iterative Inorder
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

---

## 3. Recursive Thinking Framework

### The Universal Tree Recursion Template

```cpp
ReturnType solve(TreeNode* root) {
    // Base case
    if (!root) return baseValue;

    // Recurse on subtrees
    ReturnType left = solve(root->left);
    ReturnType right = solve(root->right);

    // Combine results at current node
    return combine(root->val, left, right);
}
```

### Top-Down vs Bottom-Up

```
TOP-DOWN (Preorder style):
  Pass information FROM parent TO children
  Example: Max Depth with current depth parameter

  void dfs(TreeNode* node, int depth) {
      if (!node) return;
      // use depth at current node
      dfs(node->left, depth + 1);
      dfs(node->right, depth + 1);
  }

BOTTOM-UP (Postorder style):
  Compute information FROM children TO parent
  Example: Height, diameter, balanced check

  int height(TreeNode* node) {
      if (!node) return 0;
      return 1 + max(height(node->left), height(node->right));
  }
```

---

## 4. Pattern 1: DFS Traversals (Pre/In/Post Order)

### Maximum Depth of Binary Tree

```cpp
int maxDepth(TreeNode* root) {
    if (!root) return 0;
    return 1 + max(maxDepth(root->left), maxDepth(root->right));
}
```

### Same Tree

```cpp
bool isSameTree(TreeNode* p, TreeNode* q) {
    if (!p && !q) return true;
    if (!p || !q) return false;
    return p->val == q->val
        && isSameTree(p->left, q->left)
        && isSameTree(p->right, q->right);
}
```

### Invert Binary Tree

```cpp
TreeNode* invertTree(TreeNode* root) {
    if (!root) return nullptr;
    swap(root->left, root->right);
    invertTree(root->left);
    invertTree(root->right);
    return root;
}
```

### Symmetric Tree

```cpp
bool isSymmetric(TreeNode* root) {
    if (!root) return true;
    return isMirror(root->left, root->right);
}

bool isMirror(TreeNode* l, TreeNode* r) {
    if (!l && !r) return true;
    if (!l || !r) return false;
    return l->val == r->val
        && isMirror(l->left, r->right)
        && isMirror(l->right, r->left);
}
```

---

## 5. Pattern 2: BFS Level-Order Traversal

### Template

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;

    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        int size = q.size();
        vector<int> level;

        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front();
            q.pop();
            level.push_back(node->val);

            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(level);
    }
    return result;
}
```

### Right Side View

```cpp
vector<int> rightSideView(TreeNode* root) {
    vector<int> result;
    if (!root) return result;

    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        int size = q.size();
        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front();
            q.pop();
            if (i == size - 1) result.push_back(node->val);
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    return result;
}
```

### Zigzag Level Order

```cpp
vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
    vector<vector<int>> result;
    if (!root) return result;

    queue<TreeNode*> q;
    q.push(root);
    bool leftToRight = true;

    while (!q.empty()) {
        int size = q.size();
        vector<int> level(size);

        for (int i = 0; i < size; i++) {
            TreeNode* node = q.front();
            q.pop();
            int idx = leftToRight ? i : size - 1 - i;
            level[idx] = node->val;
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        result.push_back(level);
        leftToRight = !leftToRight;
    }
    return result;
}
```

---

## 6. Pattern 3: Height, Depth & Diameter

### Balanced Binary Tree

```cpp
bool isBalanced(TreeNode* root) {
    function<int(TreeNode*)> height = [&](TreeNode* node) -> int {
        if (!node) return 0;
        int lh = height(node->left);
        int rh = height(node->right);
        if (lh == -1 || rh == -1 || abs(lh - rh) > 1) return -1;
        return 1 + max(lh, rh);
    };
    return height(root) != -1;
}
```

### Diameter of Binary Tree

```cpp
int diameterOfBinaryTree(TreeNode* root) {
    int diameter = 0;

    function<int(TreeNode*)> height = [&](TreeNode* node) -> int {
        if (!node) return 0;
        int lh = height(node->left);
        int rh = height(node->right);
        diameter = max(diameter, lh + rh);  // Path through root
        return 1 + max(lh, rh);
    };

    height(root);
    return diameter;
}
```

**Key Insight:** Diameter through any node = left_height + right_height (not necessarily through root).

---

## 7. Pattern 4: Path Sum Problems

### Path Sum (Root to Leaf)

```cpp
bool hasPathSum(TreeNode* root, int targetSum) {
    if (!root) return false;
    if (!root->left && !root->right) return root->val == targetSum;
    return hasPathSum(root->left, targetSum - root->val)
        || hasPathSum(root->right, targetSum - root->val);
}
```

### Path Sum II (All Paths)

```cpp
void dfs(TreeNode* node, int remaining, vector<int>& path, vector<vector<int>>& result) {
    if (!node) return;
    path.push_back(node->val);

    if (!node->left && !node->right && remaining == node->val) {
        result.push_back(path);
    } else {
        dfs(node->left, remaining - node->val, path, result);
        dfs(node->right, remaining - node->val, path, result);
    }
    path.pop_back();  // Backtrack
}
```

### Path Sum III (Any Path, Not Just Root-to-Leaf)

```cpp
int pathSum(TreeNode* root, int targetSum) {
    if (!root) return 0;

    function<int(TreeNode*, long)> dfs = [&](TreeNode* node, long sum) -> int {
        if (!node) return 0;
        int count = (sum == node->val) ? 1 : 0;
        count += dfs(node->left, sum - node->val);
        count += dfs(node->right, sum - node->val);
        return count;
    };

    return dfs(root, targetSum)
         + pathSum(root->left, targetSum)
         + pathSum(root->right, targetSum);
}
```

### Maximum Path Sum (Any Node to Any Node)

```cpp
int maxPathSum(TreeNode* root) {
    int maxSum = INT_MIN;

    function<int(TreeNode*)> gain = [&](TreeNode* node) -> int {
        if (!node) return 0;
        int left = max(gain(node->left), 0);
        int right = max(gain(node->right), 0);
        maxSum = max(maxSum, node->val + left + right);
        return node->val + max(left, right);
    };

    gain(root);
    return maxSum;
}
```

---

## 8. Pattern 5: Lowest Common Ancestor (LCA)

### LCA in Binary Tree

```cpp
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root || root == p || root == q) return root;

    TreeNode* left = lowestCommonAncestor(root->left, p, q);
    TreeNode* right = lowestCommonAncestor(root->right, p, q);

    if (left && right) return root;  // p and q on different sides
    return left ? left : right;
}
```

**Dry Run:**

```
        3
       / \
      5   1
     / \
    6   2
       / \
      7   4

LCA(5, 4) = 5  (one is ancestor of other)
LCA(5, 1) = 3  (different subtrees)
LCA(7, 4) = 2  (same subtree)
```

---

## 9. Pattern 6: Tree Construction from Traversals

### Construct from Preorder + Inorder

```cpp
TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
    unordered_map<int, int> inMap;
    for (int i = 0; i < inorder.size(); i++) inMap[inorder[i]] = i;

    int preIdx = 0;
    function<TreeNode*(int, int)> build = [&](int lo, int hi) -> TreeNode* {
        if (lo > hi) return nullptr;

        int val = preorder[preIdx++];
        TreeNode* node = new TreeNode(val);
        int mid = inMap[val];

        node->left = build(lo, mid - 1);
        node->right = build(mid + 1, hi);
        return node;
    };

    return build(0, inorder.size() - 1);
}
```

**Key Insight:** Preorder gives root, inorder gives left/right split.

---

## 10. Pattern 7: Serialize & Deserialize

```cpp
class Codec {
public:
    string serialize(TreeNode* root) {
        if (!root) return "#";
        return to_string(root->val) + "," + serialize(root->left) + "," + serialize(root->right);
    }

    TreeNode* deserialize(string data) {
        queue<string> nodes;
        string val;
        for (char c : data) {
            if (c == ',') { nodes.push(val); val = ""; }
            else val += c;
        }
        nodes.push(val);
        return buildTree(nodes);
    }

private:
    TreeNode* buildTree(queue<string>& nodes) {
        string val = nodes.front();
        nodes.pop();
        if (val == "#") return nullptr;
        TreeNode* node = new TreeNode(stoi(val));
        node->left = buildTree(nodes);
        node->right = buildTree(nodes);
        return node;
    }
};
```

---

## 11. Pattern 8: Morris Traversal (O(1) Space)

### Morris Inorder Traversal

```cpp
vector<int> morrisInorder(TreeNode* root) {
    vector<int> result;
    TreeNode* curr = root;

    while (curr) {
        if (!curr->left) {
            result.push_back(curr->val);
            curr = curr->right;
        } else {
            TreeNode* pred = curr->left;
            while (pred->right && pred->right != curr) {
                pred = pred->right;
            }
            if (!pred->right) {
                pred->right = curr;  // Create thread
                curr = curr->left;
            } else {
                pred->right = nullptr;  // Remove thread
                result.push_back(curr->val);
                curr = curr->right;
            }
        }
    }
    return result;
}
```

---

## 12. Problem Recognition Framework

```
START: Binary Tree Problem
│
├─ Need sorted order or BST property?
│   └─ INORDER traversal
│
├─ Level-by-level processing?
│   └─ BFS (level-order)
│
├─ Path from root to leaf?
│   └─ DFS with path tracking + backtracking
│
├─ Any path (not necessarily through root)?
│   └─ Postorder, compute gain from subtrees
│
├─ Height/diameter/balanced?
│   └─ Postorder bottom-up recursion
│
├─ Find common ancestor?
│   └─ LCA pattern (return node if found in subtree)
│
├─ Build tree from traversals?
│   └─ Preorder/Inorder or Postorder/Inorder split
│
└─ O(1) space traversal?
    └─ Morris traversal
```

---

## 13. Practice Problems

### Easy
1. Maximum Depth of Binary Tree - https://leetcode.com/problems/maximum-depth-of-binary-tree/
2. Same Tree - https://leetcode.com/problems/same-tree/
3. Invert Binary Tree - https://leetcode.com/problems/invert-binary-tree/
4. Symmetric Tree - https://leetcode.com/problems/symmetric-tree/
5. Binary Tree Level Order Traversal - https://leetcode.com/problems/binary-tree-level-order-traversal/
6. Subtree of Another Tree - https://leetcode.com/problems/subtree-of-another-tree/
7. Path Sum - https://leetcode.com/problems/path-sum/

### Medium
8. Binary Tree Zigzag Level Order - https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/
9. Construct Binary Tree from Preorder and Inorder - https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/
10. Lowest Common Ancestor of a Binary Tree - https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/
11. Binary Tree Right Side View - https://leetcode.com/problems/binary-tree-right-side-view/
12. Count Good Nodes in Binary Tree - https://leetcode.com/problems/count-good-nodes-in-binary-tree/
13. Path Sum II - https://leetcode.com/problems/path-sum-ii/
14. Path Sum III - https://leetcode.com/problems/path-sum-iii/
15. Diameter of Binary Tree - https://leetcode.com/problems/diameter-of-binary-tree/
16. Validate Binary Tree Nodes - https://leetcode.com/problems/validate-binary-tree-nodes/
17. Serialize and Deserialize Binary Tree - https://leetcode.com/problems/serialize-and-deserialize-binary-tree/

### Hard
18. Binary Tree Maximum Path Sum - https://leetcode.com/problems/binary-tree-maximum-path-sum/
19. Serialize and Deserialize Binary Tree - https://leetcode.com/problems/serialize-and-deserialize-binary-tree/
20. Vertical Order Traversal - https://leetcode.com/problems/vertical-order-traversal-of-a-binary-tree/

---

## 14. Common Pitfalls

### Pitfall 1: Null Check Order

```cpp
// WRONG: Access root->val before null check
int val = root->val;
if (!root) return;

// RIGHT: Check null first
if (!root) return 0;
```

### Pitfall 2: Confusing Height vs Depth vs Diameter

```
Height: edges from node to deepest leaf
Depth: edges from root to node
Diameter: longest path between ANY two nodes (may not pass root)
```

### Pitfall 3: Forgetting Backtracking in Path Problems

```cpp
path.push_back(node->val);
// ... recurse ...
path.pop_back();  // MUST backtrack!
```

### Pitfall 4: Integer Overflow in Path Sum

```cpp
// Use long for running sum in path sum III
function<int(TreeNode*, long)> dfs = ...
```

---

## 15. Template Summary

### Universal Recursion
```cpp
ReturnType solve(TreeNode* root) {
    if (!root) return base;
    auto left = solve(root->left);
    auto right = solve(root->right);
    return combine(root->val, left, right);
}
```

### Level-Order BFS
```cpp
queue<TreeNode*> q;
q.push(root);
while (!q.empty()) {
    int size = q.size();
    for (int i = 0; i < size; i++) { /* process level */ }
}
```

### Path Sum with Backtracking
```cpp
path.push_back(node->val);
if (isLeaf && sum == node->val) result.push_back(path);
else { dfs(left); dfs(right); }
path.pop_back();
```

### LCA
```cpp
if (!root || root == p || root == q) return root;
auto l = LCA(root->left, p, q);
auto r = LCA(root->right, p, q);
return l && r ? root : (l ? l : r);
```

### Max Path Sum
```cpp
int gain(TreeNode* node) {
    if (!node) return 0;
    int l = max(gain(node->left), 0);
    int r = max(gain(node->right), 0);
    globalMax = max(globalMax, node->val + l + r);
    return node->val + max(l, r);
}
```

---

## Final Thoughts

**The Power of Binary Tree Patterns:**
- Every tree problem is a recursion problem
- Choose traversal based on WHEN you need the information
- Bottom-up (postorder) for aggregation; top-down (preorder) for propagation
- LCA and path sum are the most common interview patterns

**How to Master:**
1. Draw the tree and trace recursion by hand
2. Identify: top-down or bottom-up?
3. Practice all four traversals (recursive + iterative)
4. Path problems always need backtracking

---

*Trees are recursive — once you see the pattern, every tree problem clicks!*
