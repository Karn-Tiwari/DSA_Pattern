# Ultimate Recursion Patterns - Complete C++ Guide

## Table of Contents
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [The Universal Recursion Framework](#2-the-universal-recursion-framework)
3. [Pattern 1: Simple Recursion](#3-pattern-1-simple-recursion)
4. [Pattern 2: Divide and Conquer](#4-pattern-2-divide-and-conquer)
5. [Pattern 3: Backtracking](#5-pattern-3-backtracking)
6. [Pattern 4: Dynamic Programming (Memoization)](#6-pattern-4-dynamic-programming-memoization)
7. [Pattern 5: Tree Recursion](#7-pattern-5-tree-recursion)
8. [Problem Recognition Framework](#8-problem-recognition-framework)

---

## 1. Core Concept & Philosophy

### What is Recursion Really?

**Wrong Understanding:**
"Recursion is when a function calls itself"

**Correct Understanding:**
"Recursion is **breaking a problem into identical smaller subproblems**, solving each subproblem recursively, and **combining their solutions** to solve the original problem"

### The Recursive Mindset

```
Instead of thinking: "How do I solve this entire problem?"
Think: "If I had the solution to a smaller version, 
        how would I use it to solve the full problem?"
```

### Three Core Questions for Any Recursive Problem

```
1. BASE CASE: When is the problem simple enough to solve directly?
2. RECURSIVE CASE: How do I break the problem into smaller versions?
3. COMBINATION: How do I combine subproblem solutions?
```

---

## 2. The Universal Recursion Framework

### The Anatomy of Recursion

```cpp
ReturnType recursiveFunction(parameters) {
    // 1. BASE CASE - Stop condition
    if (simplestCase) {
        return directSolution;
    }
    
    // 2. RECURSIVE CASE - Break problem down
    smallerProblem1 = reduceSize(parameters);
    smallerProblem2 = reduceSize(parameters);
    
    // 3. RECURSIVE CALLS
    result1 = recursiveFunction(smallerProblem1);
    result2 = recursiveFunction(smallerProblem2);
    
    // 4. COMBINE RESULTS
    return combine(result1, result2);
}
```

### The Recursion Tree Mental Model

```
Every recursive call creates a tree:

                    f(n)
                   /    \
               f(n-1)  f(n-2)
               /   \    /   \
           f(n-2) f(n-3) ...
           
Each node represents a function call
Leaves are base cases
Internal nodes combine children's results
```

### Understanding the Call Stack

```
Function Call:        Stack Growth:
factorial(3)      →   [factorial(3)]
  factorial(2)    →   [factorial(3), factorial(2)]
    factorial(1)  →   [factorial(3), factorial(2), factorial(1)]
      return 1    ←   [factorial(3), factorial(2)] (pop)
    return 2      ←   [factorial(3)] (pop)
  return 6        ←   [] (pop)
```

---

## 3. Pattern 1: Simple Recursion

### Concept

```
Linear recursion: Problem reduces to ONE smaller subproblem
f(n) depends ONLY on f(n-1) or f(n-k)

Examples: Factorial, Sum of array, Print numbers
```

### Template

```cpp
ReturnType simpleRecursion(int n) {
    // Base case
    if (n == 0 || n == 1) {
        return baseValue;
    }
    
    // Recursive case: reduce by 1 or k
    return operation(n, simpleRecursion(n - 1));
}
```

---

### Problem 1: Factorial

**Problem:** Calculate n! = n × (n-1) × (n-2) × ... × 1

**Recursive Insight:**
```
n! = n × (n-1)!

Base: 0! = 1, 1! = 1
Recursive: n! = n × (n-1)!
```

**C++ Solution:**

```cpp
int factorial(int n) {
    // Base case
    if (n == 0 || n == 1) {
        return 1;
    }
    
    // Recursive case
    return n * factorial(n - 1);
}
```

**Detailed Call Stack Trace:**

```
Call: factorial(4)

Stack Visualization:
===================

Step 1: factorial(4) called
        Check: 4 == 0 or 4 == 1? NO
        Calculate: 4 * factorial(3)
        [factorial(4): waiting for factorial(3)]

Step 2: factorial(3) called
        Check: 3 == 0 or 3 == 1? NO
        Calculate: 3 * factorial(2)
        [factorial(4), factorial(3): waiting for factorial(2)]

Step 3: factorial(2) called
        Check: 2 == 0 or 2 == 1? NO
        Calculate: 2 * factorial(1)
        [factorial(4), factorial(3), factorial(2): waiting for factorial(1)]

Step 4: factorial(1) called
        Check: 1 == 0 or 1 == 1? YES
        Return: 1 (BASE CASE)
        [factorial(4), factorial(3), factorial(2)]

Unwinding:
==========

Step 5: factorial(2) receives 1
        Returns: 2 * 1 = 2
        [factorial(4), factorial(3)]

Step 6: factorial(3) receives 2
        Returns: 3 * 2 = 6
        [factorial(4)]

Step 7: factorial(4) receives 6
        Returns: 4 * 6 = 24
        []

Final Answer: 24
```

**Time Complexity:** O(n) - n recursive calls
**Space Complexity:** O(n) - call stack depth

---

### Problem 2: Sum of Array

**Problem:** Find sum of all elements in array.

**Recursive Insight:**
```
sum([1,2,3,4,5]) = 1 + sum([2,3,4,5])

Base: Empty array → sum = 0
Recursive: arr[0] + sum(remaining array)
```

**C++ Solution:**

```cpp
int sumArray(vector<int>& arr, int index) {
    // Base case: reached end
    if (index >= arr.size()) {
        return 0;
    }
    
    // Recursive case: current + rest
    return arr[index] + sumArray(arr, index + 1);
}

// Wrapper function
int sumArray(vector<int>& arr) {
    return sumArray(arr, 0);
}
```

**Dry Run:**

```
arr = [1, 2, 3, 4]

Call: sumArray(arr, 0)

Stack Trace:
============

sumArray(arr, 0)
  → 1 + sumArray(arr, 1)
         → 2 + sumArray(arr, 2)
                → 3 + sumArray(arr, 3)
                       → 4 + sumArray(arr, 4)
                              → 0 (base case)
                       ← 4 + 0 = 4
                ← 3 + 4 = 7
         ← 2 + 7 = 9
  ← 1 + 9 = 10

Final: 10
```

---

### Problem 3: Reverse a String

**Problem:** Reverse string recursively.

**Recursive Insight:**
```
reverse("hello") = reverse("ello") + "h"

Base: Empty or 1 char → return as is
Recursive: reverse(rest) + first char
```

**C++ Solution:**

```cpp
string reverseString(string s) {
    // Base case
    if (s.length() <= 1) {
        return s;
    }
    
    // Recursive case
    return reverseString(s.substr(1)) + s[0];
}
```

**Detailed Trace:**

```
Input: "ABCD"

Call Stack:
===========

reverseString("ABCD")
  = reverseString("BCD") + 'A'
  
    reverseString("BCD")
    = reverseString("CD") + 'B'
    
      reverseString("CD")
      = reverseString("D") + 'C'
      
        reverseString("D")
        = "D" (BASE CASE)
        
      = "D" + 'C' = "DC"
      
    = "DC" + 'B' = "DCB"
    
  = "DCB" + 'A' = "DCBA"

Output: "DCBA"
```

---

## 4. Pattern 2: Divide and Conquer

### Concept

```
Split problem into MULTIPLE independent subproblems
Solve each recursively
MERGE results

Key: Subproblems don't overlap (unlike DP)
```

### Template

```cpp
ReturnType divideAndConquer(Problem problem) {
    // Base case
    if (problem.isSimple()) {
        return solveDirect(problem);
    }
    
    // DIVIDE: Split into subproblems
    vector<SubProblem> subproblems = divide(problem);
    
    // CONQUER: Solve recursively
    vector<Solution> subSolutions;
    for (auto& sub : subproblems) {
        subSolutions.push_back(divideAndConquer(sub));
    }
    
    // COMBINE: Merge solutions
    return combine(subSolutions);
}
```

### The Three Steps

```
1. DIVIDE: Break into smaller subproblems
2. CONQUER: Solve subproblems recursively
3. COMBINE: Merge solutions

Example: Merge Sort
[5,2,8,1,9,3]
       |
   DIVIDE
   /    \
[5,2,8] [1,9,3]
   |       |
  CONQUER CONQUER
   |       |
[2,5,8] [1,3,9]
      \  /
     COMBINE
       |
 [1,2,3,5,8,9]
```

---

### Problem 1: Merge Sort

**Problem:** Sort an array using divide and conquer.

**Recursive Insight:**
```
1. Divide array into two halves
2. Recursively sort each half
3. Merge two sorted halves

Base: Array of size 0 or 1 is already sorted
```

**C++ Solution:**

```cpp
void merge(vector<int>& arr, int left, int mid, int right) {
    vector<int> temp(right - left + 1);
    int i = left, j = mid + 1, k = 0;
    
    // Merge two sorted halves
    while (i <= mid && j <= right) {
        if (arr[i] <= arr[j]) {
            temp[k++] = arr[i++];
        } else {
            temp[k++] = arr[j++];
        }
    }
    
    // Copy remaining
    while (i <= mid) temp[k++] = arr[i++];
    while (j <= right) temp[k++] = arr[j++];
    
    // Copy back
    for (i = left, k = 0; i <= right; i++, k++) {
        arr[i] = temp[k];
    }
}

void mergeSort(vector<int>& arr, int left, int right) {
    // Base case: single element or empty
    if (left >= right) {
        return;
    }
    
    // DIVIDE: Find middle
    int mid = left + (right - left) / 2;
    
    // CONQUER: Sort both halves
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    
    // COMBINE: Merge sorted halves
    merge(arr, left, mid, right);
}
```

**Detailed Dry Run:**

```
arr = [5, 2, 8, 1]

Visual Recursion Tree:
======================

                [5,2,8,1]
                   |
            DIVIDE at mid=1
              /         \
          [5,2]          [8,1]
           |              |
       DIVIDE          DIVIDE
       /    \          /    \
     [5]    [2]      [8]    [1]
     
     BASE   BASE    BASE   BASE
       \    /          \    /
      MERGE            MERGE
        |                |
      [2,5]            [1,8]
           \          /
            \        /
             MERGE
               |
          [1,2,5,8]

Step-by-Step Execution:
========================

1. mergeSort([5,2,8,1], 0, 3)
   mid = 1
   
2. mergeSort([5,2], 0, 1)  ← Left half
   mid = 0
   
3. mergeSort([5], 0, 0)  ← Base case, return
   
4. mergeSort([2], 1, 1)  ← Base case, return
   
5. merge([5,2], 0, 0, 1)
   Compare 5 and 2 → [2,5]
   
6. mergeSort([8,1], 2, 3)  ← Right half
   mid = 2
   
7. mergeSort([8], 2, 2)  ← Base case
   
8. mergeSort([1], 3, 3)  ← Base case
   
9. merge([8,1], 2, 2, 3)
   Compare 8 and 1 → [1,8]
   
10. merge([2,5,1,8], 0, 1, 3)
    Merge [2,5] and [1,8]
    Compare: 2 vs 1 → pick 1
    Compare: 2 vs 8 → pick 2
    Compare: 5 vs 8 → pick 5
    Pick remaining: 8
    Result: [1,2,5,8]

Time: O(n log n)
Space: O(n)
```

---

### Problem 2: Quick Sort

**Problem:** Sort using pivot partitioning.

**Recursive Insight:**
```
1. Choose pivot
2. Partition: elements < pivot | pivot | elements > pivot
3. Recursively sort both partitions

Base: Empty or single element
```

**C++ Solution:**

```cpp
int partition(vector<int>& arr, int low, int high) {
    int pivot = arr[high];  // Choose last element as pivot
    int i = low - 1;  // Index of smaller element
    
    for (int j = low; j < high; j++) {
        if (arr[j] < pivot) {
            i++;
            swap(arr[i], arr[j]);
        }
    }
    
    swap(arr[i + 1], arr[high]);
    return i + 1;
}

void quickSort(vector<int>& arr, int low, int high) {
    // Base case
    if (low >= high) {
        return;
    }
    
    // DIVIDE: Partition array
    int pivotIndex = partition(arr, low, high);
    
    // CONQUER: Sort both sides
    quickSort(arr, low, pivotIndex - 1);
    quickSort(arr, pivotIndex + 1, high);
    
    // COMBINE: Nothing needed (in-place)
}
```

**Dry Run:**

```
arr = [3, 7, 8, 5, 2, 1, 9, 5, 4]

Step 1: Initial call quickSort(arr, 0, 8)
        pivot = arr[8] = 4
        
Partition Process:
==================
[3, 7, 8, 5, 2, 1, 9, 5, 4]
 ^                       ^
 i=-1                  pivot=4

j=0: arr[0]=3 < 4? YES → i=0, swap arr[0] with arr[0]
     [3, 7, 8, 5, 2, 1, 9, 5, 4]
      ^

j=1: arr[1]=7 < 4? NO
j=2: arr[2]=8 < 4? NO
j=3: arr[3]=5 < 4? NO

j=4: arr[4]=2 < 4? YES → i=1, swap arr[1] with arr[4]
     [3, 2, 8, 5, 7, 1, 9, 5, 4]
         ^

j=5: arr[5]=1 < 4? YES → i=2, swap arr[2] with arr[5]
     [3, 2, 1, 5, 7, 8, 9, 5, 4]
            ^

j=6: arr[6]=9 < 4? NO
j=7: arr[7]=5 < 4? NO

Final swap: arr[i+1=3] with arr[high=8]
[3, 2, 1, 4, 7, 8, 9, 5, 5]
          ^
      pivot at index 3

Step 2: quickSort([3,2,1], 0, 2)
        pivot = 1
        Result: [1, 2, 3]

Step 3: quickSort([7,8,9,5,5], 4, 8)
        pivot = 5
        Result: [5, 5, 7, 8, 9]

Final: [1, 2, 3, 4, 5, 5, 7, 8, 9]
```

---

### Problem 3: Maximum Subarray (Divide & Conquer Approach)

**Problem:** Find maximum sum of contiguous subarray.

**Recursive Insight:**
```
Max subarray either:
1. Entirely in left half
2. Entirely in right half
3. Crosses the middle

Answer = max(left_max, right_max, cross_max)
```

**C++ Solution:**

```cpp
int maxCrossingSum(vector<int>& arr, int left, int mid, int right) {
    // Find max sum in left half including mid
    int leftSum = INT_MIN;
    int sum = 0;
    for (int i = mid; i >= left; i--) {
        sum += arr[i];
        leftSum = max(leftSum, sum);
    }
    
    // Find max sum in right half including mid+1
    int rightSum = INT_MIN;
    sum = 0;
    for (int i = mid + 1; i <= right; i++) {
        sum += arr[i];
        rightSum = max(rightSum, sum);
    }
    
    return leftSum + rightSum;
}

int maxSubarrayDC(vector<int>& arr, int left, int right) {
    // Base case: single element
    if (left == right) {
        return arr[left];
    }
    
    // DIVIDE
    int mid = left + (right - left) / 2;
    
    // CONQUER: Find max in three cases
    int leftMax = maxSubarrayDC(arr, left, mid);
    int rightMax = maxSubarrayDC(arr, mid + 1, right);
    int crossMax = maxCrossingSum(arr, left, mid, right);
    
    // COMBINE: Return maximum
    return max({leftMax, rightMax, crossMax});
}
```

**Dry Run:**

```
arr = [-2, 1, -3, 4, -1, 2, 1, -5, 4]

                [-2,1,-3,4,-1,2,1,-5,4]
                        |
                    mid = 4
                   /    |    \
              leftMax crossMax rightMax
                 /       |         \
        [-2,1,-3,4]  [-1,2,1]  [-1,2,1,-5,4]
           |                        |
        mid=1                    mid=6
         /  \                     /  \
    [-2,1] [-3,4]          [-1,2,1] [-5,4]

Calculation:
============

Left half: [-2,1,-3,4]
  Further split...
  Max = 4

Right half: [-1,2,1,-5,4]
  Further split...
  Max = 3

Crossing: Starting from mid=4 (arr[-1])
  Left side max (including -1): 4+-1+2+1 = 6
  Right side max (including 2): 2+1 = 3
  Cross max = 6

Final: max(4, 3, 6) = 6
Answer: [4,-1,2,1] has sum 6
```

---

## 5. Pattern 3: Backtracking

### Concept

```
Backtracking = DFS + Constraint checking
Build solution incrementally
Abandon path when constraint violated
"Try all possibilities, but intelligently"
```

### The Backtracking Framework

```cpp
void backtrack(state, choices, result) {
    // BASE CASE: Found complete solution
    if (isComplete(state)) {
        result.push_back(state);
        return;
    }
    
    // EXPLORE: Try each choice
    for (choice in choices) {
        // MAKE CHOICE
        state.add(choice);
        
        // PRUNE: Skip if invalid
        if (!isValid(state)) {
            state.remove(choice);
            continue;
        }
        
        // RECURSE: Explore further
        backtrack(state, newChoices, result);
        
        // UNDO CHOICE (Backtrack)
        state.remove(choice);
    }
}
```

### The Decision Tree Model

```
Every backtracking problem is traversing a decision tree:

                    []
            /       |       \
          [1]      [2]      [3]
         /  \      /  \     /  \
      [1,2][1,3][2,1][2,3][3,1][3,2]
      
Leaves = Complete solutions
Pruning = Stop exploring when constraint violated
```

---

### Problem 1: Subsets (Power Set)

**Problem:** Generate all possible subsets of a set.

**Recursive Insight:**
```
For each element: include it OR exclude it

subsets([1,2,3]):
  include 1: [1] + subsets([2,3])
  exclude 1: [] + subsets([2,3])
```

**C++ Solution:**

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> current;
        backtrack(nums, 0, current, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& nums, int start, 
                   vector<int>& current, 
                   vector<vector<int>>& result) {
        // BASE: Every state is a valid subset
        result.push_back(current);
        
        // EXPLORE: Try including each remaining element
        for (int i = start; i < nums.size(); i++) {
            // MAKE CHOICE: Include nums[i]
            current.push_back(nums[i]);
            
            // RECURSE: Explore with this choice
            backtrack(nums, i + 1, current, result);
            
            // UNDO CHOICE: Backtrack
            current.pop_back();
        }
    }
};
```

**Detailed Recursion Tree:**

```
nums = [1, 2, 3]

Decision Tree:
==============

                        []  ← result.add([])
                    /   |   \
                  +1   +2    +3
                  /     |     \
               [1]     [2]    [3]  ← result.add each
              / \       |      
            +2  +3     +3      
            /     \     |       
         [1,2]  [1,3] [2,3]  ← result.add each
          /
        +3
        /
     [1,2,3]  ← result.add

Complete Trace:
===============

Call 1: backtrack(nums, start=0, current=[], result)
        Add [] to result
        
        Loop i=0:
          current = [1]
          Call 2: backtrack(nums, start=1, current=[1], result)
                  Add [1] to result
                  
                  Loop i=1:
                    current = [1,2]
                    Call 3: backtrack(nums, start=2, current=[1,2], result)
                            Add [1,2] to result
                            
                            Loop i=2:
                              current = [1,2,3]
                              Call 4: backtrack(nums, start=3, current=[1,2,3], result)
                                      Add [1,2,3] to result
                                      Loop: (start=3 >= size=3, no iterations)
                                      Return
                              current.pop_back() → [1,2]
                            
                            Return
                    current.pop_back() → [1]
                    
                    Loop i=2:
                      current = [1,3]
                      Call 5: backtrack(nums, start=3, current=[1,3], result)
                              Add [1,3] to result
                              Return
                      current.pop_back() → [1]
                  
                  Return
          current.pop_back() → []
          
        Loop i=1:
          current = [2]
          Call 6: backtrack(nums, start=2, current=[2], result)
                  Add [2] to result
                  
                  Loop i=2:
                    current = [2,3]
                    Call 7: backtrack(nums, start=3, current=[2,3], result)
                            Add [2,3] to result
                            Return
                    current.pop_back() → [2]
                  
                  Return
          current.pop_back() → []
        
        Loop i=2:
          current = [3]
          Call 8: backtrack(nums, start=3, current=[3], result)
                  Add [3] to result
                  Return
          current.pop_back() → []
        
        Return

Result: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

---

### Problem 2: Permutations

**Problem:** Generate all permutations of an array.

**Recursive Insight:**
```
Fix first element, permute rest
Try each element as first

perm([1,2,3]):
  1 + perm([2,3]) = [1,2,3], [1,3,2]
  2 + perm([1,3]) = [2,1,3], [2,3,1]
  3 + perm([1,2]) = [3,1,2], [3,2,1]
```

**C++ Solution:**

```cpp
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> current;
        vector<bool> used(nums.size(), false);
        backtrack(nums, current, used, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& nums, 
                   vector<int>& current,
                   vector<bool>& used,
                   vector<vector<int>>& result) {
        // BASE: Complete permutation
        if (current.size() == nums.size()) {
            result.push_back(current);
            return;
        }
        
        // EXPLORE: Try each unused number
        for (int i = 0; i < nums.size(); i++) {
            if (used[i]) continue;  // Skip if already used
            
            // MAKE CHOICE
            current.push_back(nums[i]);
            used[i] = true;
            
            // RECURSE
            backtrack(nums, current, used, result);
            
            // UNDO CHOICE
            current.pop_back();
            used[i] = false;
        }
    }
};
```

**Detailed Trace:**

```
nums = [1, 2, 3]

Decision Tree (showing first few branches):
============================================

                      []
            /         |         \
          +1         +2         +3
          /           |           \
        [1]         [2]          [3]
       /   \       /   \        /   \
     +2    +3    +1    +3     +1    +2
     /      \    /      \     /      \
  [1,2]   [1,3] [2,1]  [2,3] [3,1]  [3,2]
    |       |     |      |     |      |
   +3      +2    +3     +1    +2     +1
    |       |     |      |     |      |
[1,2,3] [1,3,2][2,1,3][2,3,1][3,1,2][3,2,1]

Step-by-Step Execution:
========================

Initial: current=[], used=[F,F,F]

i=0: nums[0]=1
     current=[1], used=[T,F,F]
     
     i=0: used[0]=T, skip
     i=1: nums[1]=2
          current=[1,2], used=[T,T,F]
          
          i=0: used[0]=T, skip
          i=1: used[1]=T, skip
          i=2: nums[2]=3
               current=[1,2,3], used=[T,T,T]
               SIZE=3, ADD TO RESULT ✓
               BACKTRACK: current=[1,2], used=[T,T,F]
          
          BACKTRACK: current=[1], used=[T,F,F]
     
     i=2: nums[2]=3
          current=[1,3], used=[T,F,T]
          
          i=0: used[0]=T, skip
          i=1: nums[1]=2
               current=[1,3,2], used=[T,T,T]
               SIZE=3, ADD TO RESULT ✓
               BACKTRACK: current=[1,3], used=[T,F,T]
          i=2: used[2]=T, skip
          
          BACKTRACK: current=[1], used=[T,F,F]
     
     BACKTRACK: current=[], used=[F,F,F]

... (continue for i=1 and i=2)

Result: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

---

### Problem 3: N-Queens

**Problem:** Place N queens on N×N board such that no two attack each other.

**Recursive Insight:**
```
Place queen row by row
For each row, try each column
Check if placement is valid
Backtrack if no valid placement
```

**C++ Solution:**

```cpp
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> result;
        vector<string> board(n, string(n, '.'));
        backtrack(board, 0, result);
        return result;
    }
    
private:
    bool isValid(vector<string>& board, int row, int col) {
        int n = board.size();
        
        // Check column
        for (int i = 0; i < row; i++) {
            if (board[i][col] == 'Q') return false;
        }
        
        // Check upper-left diagonal
        for (int i = row - 1, j = col - 1; i >= 0 && j >= 0; i--, j--) {
            if (board[i][j] == 'Q') return false;
        }
        
        // Check upper-right diagonal
        for (int i = row - 1, j = col + 1; i >= 0 && j < n; i--, j++) {
            if (board[i][j] == 'Q') return false;
        }
        
        return true;
    }
    
    void backtrack(vector<string>& board, int row, 
                   vector<vector<string>>& result) {
        // BASE: All queens placed
        if (row == board.size()) {
            result.push_back(board);
            return;
        }
        
        // EXPLORE: Try placing queen in each column
        for (int col = 0; col < board.size(); col++) {
            // PRUNE: Check if valid placement
            if (!isValid(board, row, col)) {
                continue;
            }
            
            // MAKE CHOICE
            board[row][col] = 'Q';
            
            // RECURSE
            backtrack(board, row + 1, result);
            
            // UNDO CHOICE
            board[row][col] = '.';
        }
    }
};
```

**Dry Run (4-Queens):**

```
n = 4

Decision Tree (showing valid placements only):
==============================================

Row 0: Try each column
       
       Col 0: Place Q at (0,0)
       . . . .     Row 1: Try columns
       Q . . .           Col 1: Invalid (column attack)
       . . . .           Col 2: Place Q at (1,2)
       . . . .           
                         . . . .
                         Q . . .    Row 2: Try columns
                         . . Q .          Col 0: Invalid (column)
                         . . . .          Col 1: Place Q at (2,1)
                                          
                                          . . . .
                                          Q . . .
                                          . . Q .    Row 3: All invalid
                                          . Q . .    BACKTRACK
                         
                         Try Col 3 at Row 2...
                         All fail, BACKTRACK to Row 1
       
       Try Col 3 at Row 1...
       
       Col 1: Place Q at (0,1)
       . . . .     Row 1: Try columns
       . Q . .           Col 0: Invalid (column)
       . . . .           Col 2: Invalid (column)
       . . . .           Col 3: Place Q at (1,3)
                         
                         . . . .
                         . Q . .    Row 2: Try columns
                         . . . Q          Col 0: Place Q at (2,0)
                         . . . .          
                                          
                                          . . . .
                                          . Q . .
                                          . . . Q
                                          Q . . .    Row 3: Try columns
                                                           Col 2: Valid!
                                          
                                          . . . .
                                          . Q . .
                                          . . . Q
                                          Q . Q .    ✓ SOLUTION FOUND!

Visual of Solution 1:
=====================
. Q . .
. . . Q
Q . . .
. . Q .

Complete Trace for First Solution:
===================================

backtrack(board, row=0)
  col=0: Invalid path (leads to no solution)
  col=1: board[0][1] = 'Q'
         
         backtrack(board, row=1)
           col=0: Invalid (column attack)
           col=1: Invalid (column attack)
           col=2: Invalid (diagonal attack)
           col=3: board[1][3] = 'Q'
                  
                  backtrack(board, row=2)
                    col=0: board[2][0] = 'Q'
                           
                           backtrack(board, row=3)
                             col=0: Invalid
                             col=1: Invalid
                             col=2: board[3][2] = 'Q'
                                    
                                    backtrack(board, row=4)
                                      row==4, ADD SOLUTION ✓
                                    
                             board[3][2] = '.'
                           
                    board[2][0] = '.'
           
           board[1][3] = '.'
         
  board[0][1] = '.'

Time Complexity: O(N!) - Try N positions in row 1, ~N in row 2, etc.
Space Complexity: O(N) - Call stack depth
```

---

### Problem 4: Subsets II (with Duplicates) - C++

**Problem:** Given an integer array `nums` that may contain duplicates, return all possible subsets (the power set) without duplicate subsets.

**Recursive Insight:**
```
Sort the array to handle duplicates
Skip duplicates by checking if current == previous and not starting new
```

**C++ Solution:**

```cpp
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> current;
        sort(nums.begin(), nums.end());
        backtrack(nums, 0, current, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& nums, int start, vector<int>& current, vector<vector<int>>& result) {
        result.push_back(current);
        
        for (int i = start; i < nums.size(); i++) {
            if (i > start && nums[i] == nums[i - 1]) continue;  // Skip duplicates
            current.push_back(nums[i]);
            backtrack(nums, i + 1, current, result);
            current.pop_back();
        }
    }
};
```

**Dry Run:**

```
nums = [1, 2, 2] (sorted)

Start: backtrack([1,2,2], 0, [], result)
  Add [] to result
  
  i=0: current = [1], backtrack([1,2,2], 1, [1], result)
    Add [1] to result
    
    i=1: current = [1,2], backtrack([1,2,2], 2, [1,2], result)
      Add [1,2] to result
      
      i=2: nums[2]=2 == nums[1]=2, i=2 > 1, skip
      
      pop 2, current = [1]
    
    i=2: nums[2]=2 == nums[1]=2, i=2 > 1, skip
    
    pop 1, current = []
  
  i=1: current = [2], backtrack([1,2,2], 2, [2], result)
    Add [2] to result
    
    i=2: nums[2]=2 == nums[1]=2, i=2 > 1, skip
    
    pop 2, current = []
  
  i=2: nums[2]=2 == nums[1]=2, i=2 > 1, skip

Final result: [[], [1], [1,2], [2]]
```

---

### Problem 5: Permutations II (with Duplicates) - C++

**Problem:** Given a collection of numbers that might contain duplicates, return all possible unique permutations.

**Recursive Insight:**
```
Sort array, use used array
Skip if current == previous and previous not used
```

**C++ Solution:**

```cpp
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> current;
        vector<bool> used(nums.size(), false);
        sort(nums.begin(), nums.end());
        backtrack(nums, current, used, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& nums, vector<int>& current, vector<bool>& used, vector<vector<int>>& result) {
        if (current.size() == nums.size()) {
            result.push_back(current);
            return;
        }
        
        for (int i = 0; i < nums.size(); i++) {
            if (used[i] || (i > 0 && nums[i] == nums[i - 1] && !used[i - 1])) continue;
            used[i] = true;
            current.push_back(nums[i]);
            backtrack(nums, current, used, result);
            used[i] = false;
            current.pop_back();
        }
    }
};
```

**Dry Run:**

```
nums = [1, 1, 2] (sorted)

Start: backtrack([1,1,2], [], [F,F,F], result)
  size=0 !=3
  
  i=0: used[0]=F, nums[0]=1, i=0 no skip, used=[T,F,F], current=[1]
    backtrack([1,1,2], [1], [T,F,F], result)
      size=1!=3
      
      i=0: used[0]=T, skip
      i=1: nums[1]=1 == nums[0]=1, !used[0]=F, skip
      i=2: nums[2]=2, used=[T,F,T], current=[1,2]
        backtrack([1,1,2], [1,2], [T,F,T], result)
          size=2!=3
          
          i=0: used[0]=T, skip
          i=1: nums[1]=1 == nums[0]=1, !used[0]=F, skip
          i=2: used[2]=T, skip
          
          pop 2, current=[1]
      
      pop 2, current=[1]
    
    pop 1, current=[]
  
  i=1: nums[1]=1 == nums[0]=1, i=1>0, !used[0]=T, skip
  
  i=2: nums[2]=2, used=[F,F,T], current=[2]
    backtrack([1,1,2], [2], [F,F,T], result)
      size=1!=3
      
      i=0: used=[T,F,T], current=[2,1]
        backtrack([1,1,2], [2,1], [T,F,T], result)
          size=2!=3
          
          i=0: used[0]=T, skip
          i=1: nums[1]=1 == nums[0]=1, !used[0]=F, skip
          i=2: used[2]=T, skip
          
          pop 1, current=[2]
      
      i=1: nums[1]=1 == nums[0]=1, i=1>0, !used[0]=T, skip
      i=2: used[2]=T, skip
      
      pop 2, current=[]

Final result: [[1,1,2], [1,2,1], [2,1,1]]
```

---

### Problem 6: Combination Sum - C++

**Problem:** Given an array of distinct integers `candidates` and a target integer `target`, return a list of all unique combinations of `candidates` where the chosen numbers sum to `target`. The same number may be chosen from `candidates` an unlimited number of times.

**Recursive Insight:**
```
Sort candidates
Use backtracking with remaining sum
Allow reuse by not incrementing start
```

**C++ Solution:**

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> result;
        vector<int> current;
        sort(candidates.begin(), candidates.end());
        backtrack(candidates, target, 0, current, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& candidates, int remain, int start, vector<int>& current, vector<vector<int>>& result) {
        if (remain < 0) return;
        if (remain == 0) {
            result.push_back(current);
            return;
        }
        
        for (int i = start; i < candidates.size(); i++) {
            current.push_back(candidates[i]);
            backtrack(candidates, remain - candidates[i], i, current, result);  // Note: i, not i+1
            current.pop_back();
        }
    }
};
```

**Dry Run:**

```
candidates = [2, 3, 6, 7], target = 7

Start: backtrack([2,3,6,7], 7, 0, [], result)
  remain=7 >0
  
  i=0: current=[2], backtrack([2,3,6,7],5,0,[2],result)
    remain=5>0
    
    i=0: current=[2,2], backtrack([2,3,6,7],3,0,[2,2],result)
      remain=3>0
      
      i=0: current=[2,2,2], backtrack([2,3,6,7],1,0,[2,2,2],result)
        remain=1>0
        
        i=0: current=[2,2,2,2], backtrack([2,3,6,7],-1,0,[2,2,2,2],result)
          remain<0, return
        
        pop 2, current=[2,2,2]
      
      i=1: current=[2,2,3], backtrack([2,3,6,7],0,1,[2,2,3],result)
        remain=0, add [2,2,3] to result
      
      pop 3, current=[2,2]
    
    i=1: current=[2,3], backtrack([2,3,6,7],2,1,[2,3],result)
      remain=2>0
      
      i=1: current=[2,3,3], backtrack([2,3,6,7],-1,1,[2,3,3],result)
        remain<0, return
      
      pop 3, current=[2,3]
    
    pop 3, current=[2]
  
  i=1: current=[3], backtrack([2,3,6,7],4,1,[3],result)
    remain=4>0
    
    i=1: current=[3,3], backtrack([2,3,6,7],1,1,[3,3],result)
      remain=1>0
      
      i=1: current=[3,3,3], backtrack([2,3,6,7],-2,1,[3,3,3],result)
        remain<0, return
      
      pop 3, current=[3,3]
    
    pop 3, current=[3]
  
  i=2: current=[6], backtrack([2,3,6,7],1,2,[6],result)
    remain=1>0
    
    i=2: current=[6,6], backtrack([2,3,6,7],-5,2,[6,6],result)
      remain<0, return
    
    pop 6, current=[6]
  
  i=3: current=[7], backtrack([2,3,6,7],0,3,[7],result)
    remain=0, add [7] to result

Final result: [[2,2,3], [7]]
```

---

### Problem 7: Combination Sum II (No Reuse) - C++

**Problem:** Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sum to `target`. Each number in `candidates` may only be used once in the combination.

**Recursive Insight:**
```
Sort candidates
Skip duplicates
Use i+1 to prevent reuse
```

**C++ Solution:**

```cpp
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> result;
        vector<int> current;
        sort(candidates.begin(), candidates.end());
        backtrack(candidates, target, 0, current, result);
        return result;
    }
    
private:
    void backtrack(vector<int>& candidates, int remain, int start, vector<int>& current, vector<vector<int>>& result) {
        if (remain < 0) return;
        if (remain == 0) {
            result.push_back(current);
            return;
        }
        
        for (int i = start; i < candidates.size(); i++) {
            if (i > start && candidates[i] == candidates[i - 1]) continue;  // Skip duplicates
            current.push_back(candidates[i]);
            backtrack(candidates, remain - candidates[i], i + 1, current, result);  // i+1, no reuse
            current.pop_back();
        }
    }
};
```

**Dry Run:**

```
candidates = [10,1,2,7,6,1,5], target = 8 (sorted: [1,1,2,5,6,7,10])

Start: backtrack([1,1,2,5,6,7,10], 8, 0, [], result)
  remain=8>0
  
  i=0: current=[1], backtrack([1,1,2,5,6,7,10],7,1,[1],result)
    remain=7>0
    
    i=1: candidates[1]=1 == candidates[0]=1, i=1>0, skip
    i=2: current=[1,2], backtrack([1,1,2,5,6,7,10],5,3,[1,2],result)
      remain=5>0
      
      i=3: current=[1,2,5], backtrack([1,1,2,5,6,7,10],0,4,[1,2,5],result)
        remain=0, add [1,2,5] to result
      
      pop 5, current=[1,2]
    
    i=3: current=[1,5], backtrack([1,1,2,5,6,7,10],2,4,[1,5],result)
      remain=2>0
      
      i=4: current=[1,5,6], backtrack([1,1,2,5,6,7,10],-4,5,[1,5,6],result)
        remain<0, return
      
      pop 6, current=[1,5]
    
    pop 5, current=[1]
  
  i=1: candidates[1]=1 == candidates[0]=1, i=1>0, skip
  
  i=2: current=[2], backtrack([1,1,2,5,6,7,10],6,3,[2],result)
    remain=6>0
    
    i=3: current=[2,5], backtrack([1,1,2,5,6,7,10],1,4,[2,5],result)
      remain=1>0
      
      i=4: current=[2,5,6], backtrack([1,1,2,5,6,7,10],-5,5,[2,5,6],result)
        remain<0, return
      
      pop 6, current=[2,5]
    
    pop 5, current=[2]
  
  i=3: current=[5], backtrack([1,1,2,5,6,7,10],3,4,[5],result)
    remain=3>0
    
    i=4: current=[5,6], backtrack([1,1,2,5,6,7,10],-3,5,[5,6],result)
      remain<0, return
    
    pop 6, current=[5]
  
  i=4: current=[6], backtrack([1,1,2,5,6,7,10],2,5,[6],result)
    remain=2>0
    
    i=5: current=[6,7], backtrack([1,1,2,5,6,7,10],-5,6,[6,7],result)
      remain<0, return
    
    pop 7, current=[6]
  
  i=5: current=[7], backtrack([1,1,2,5,6,7,10],1,6,[7],result)
    remain=1>0
    
    i=6: current=[7,10], backtrack([1,1,2,5,6,7,10],-9,7,[7,10],result)
      remain<0, return
    
    pop 10, current=[7]

Final result: [[1,2,5]]
```

---

### Problem 8: Palindrome Partitioning - C++

**Problem:** Given a string `s`, partition `s` such that every substring of the partition is a palindrome. Return all possible palindrome partitioning of `s`.

**Recursive Insight:**
```
Try all possible partitions
Check if substring from start to i is palindrome
If yes, recurse on remaining string
```

**C++ Solution:**

```cpp
class Solution {
public:
    vector<vector<string>> partition(string s) {
        vector<vector<string>> result;
        vector<string> current;
        backtrack(s, 0, current, result);
        return result;
    }
    
private:
    void backtrack(string& s, int start, vector<string>& current, vector<vector<string>>& result) {
        if (start == s.length()) {
            result.push_back(current);
            return;
        }
        
        for (int i = start; i < s.length(); i++) {
            if (isPalindrome(s, start, i)) {
                current.push_back(s.substr(start, i - start + 1));
                backtrack(s, i + 1, current, result);
                current.pop_back();
            }
        }
    }
    
    bool isPalindrome(string& s, int low, int high) {
        while (low < high) {
            if (s[low++] != s[high--]) return false;
        }
        return true;
    }
};
```

**Dry Run:**

```
s = "aab"

Start: backtrack("aab", 0, [], result)
  start=0 !=3
  
  i=0: isPalindrome("a",0,0)=true, current=["a"], backtrack("aab",1,["a"],result)
    start=1!=3
    
    i=1: isPalindrome("a",1,1)=true, current=["a","a"], backtrack("aab",2,["a","a"],result)
      start=2!=3
      
      i=2: isPalindrome("b",2,2)=true, current=["a","a","b"], backtrack("aab",3,["a","a","b"],result)
        start=3==3, add ["a","a","b"] to result
      
      pop "b", current=["a","a"]
    
    i=2: isPalindrome("ab",1,2)="ab" != "ba", false
    
    pop "a", current=["a"]
  
  i=1: isPalindrome("aa",0,1)="aa" == "aa", true, current=["aa"], backtrack("aab",2,["aa"],result)
    start=2!=3
    
    i=2: isPalindrome("b",2,2)=true, current=["aa","b"], backtrack("aab",3,["aa","b"],result)
      start=3==3, add ["aa","b"] to result
    
    pop "b", current=["aa"]
  
  i=2: isPalindrome("aab",0,2)="aab" != "baa", false
  
  pop "aa", current=[]

Final result: [["a","a","b"], ["aa","b"]]
```

---

### Additional Backtracking Examples in Java

Backtracking is a powerful technique for solving problems that require exploring all possible solutions while pruning invalid paths. Below are additional examples implemented in Java, with detailed explanations and dry runs.

#### Java Backtracking Template

```java
public void backtrack(List<List<Integer>> result, List<Integer> tempList, int[] nums, int start) {
    // Base case: add current combination to result
    result.add(new ArrayList<>(tempList));
    
    // Explore each choice starting from 'start'
    for (int i = start; i < nums.length; i++) {
        // Make choice: add nums[i] to tempList
        tempList.add(nums[i]);
        
        // Recurse with next start position
        backtrack(result, tempList, nums, i + 1);
        
        // Undo choice: remove last element
        tempList.remove(tempList.size() - 1);
    }
}
```

This template is used for generating subsets and combinations.

---

#### Problem 4: Subsets (Java Implementation)

**Problem:** Given an integer array `nums` of unique elements, return all possible subsets (the power set).

**Explanation in Simple Terms:**
- We want to find all possible combinations of the elements, including the empty set and the full set.
- For each element, we have two choices: include it in the subset or exclude it.
- We use backtracking to explore all possibilities.

**Java Solution:**

```java
public List<List<Integer>> subsets(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);  // Optional, for consistent order
    backtrack(list, new ArrayList<>(), nums, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, int start) {
    // Add current subset to result
    list.add(new ArrayList<>(tempList));
    
    // Try adding each remaining element
    for (int i = start; i < nums.length; i++) {
        tempList.add(nums[i]);  // Include nums[i]
        backtrack(list, tempList, nums, i + 1);  // Recurse
        tempList.remove(tempList.size() - 1);  // Backtrack
    }
}
```

**Dry Run:**

```
nums = [1, 2, 3]

Start: backtrack([], [], [1,2,3], 0)
  Add [] to result  → result = [[]]
  
  i=0: tempList = [1], backtrack([[]], [1], [1,2,3], 1)
    Add [1] to result  → result = [[], [1]]
    
    i=1: tempList = [1,2], backtrack([[],[1]], [1,2], [1,2,3], 2)
      Add [1,2] to result  → result = [[], [1], [1,2]]
      
      i=2: tempList = [1,2,3], backtrack([[],[1],[1,2]], [1,2,3], [1,2,3], 3)
        Add [1,2,3] to result  → result = [[], [1], [1,2], [1,2,3]]
        No more i, return
      
      Remove 3, tempList = [1,2]
    
    i=2: tempList = [1,3], backtrack([[],[1],[1,2],[1,2,3]], [1,3], [1,2,3], 3)
      Add [1,3] to result  → result = [[], [1], [1,2], [1,2,3], [1,3]]
      No more i, return
    
    Remove 3, tempList = [1]
  
  i=1: tempList = [2], backtrack([[],[1],[1,2],[1,2,3],[1,3]], [2], [1,2,3], 2)
    Add [2] to result  → result = [[], [1], [1,2], [1,2,3], [1,3], [2]]
    
    i=2: tempList = [2,3], backtrack(..., [2,3], [1,2,3], 3)
      Add [2,3] to result  → result = [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3]]
      No more i, return
    
    Remove 3, tempList = [2]
  
  i=2: tempList = [3], backtrack(..., [3], [1,2,3], 3)
    Add [3] to result  → result = [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
    No more i, return
  
  Remove 3, tempList = []

Final result: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

---

#### Problem 5: Subsets II (with Duplicates)

**Problem:** Given an integer array `nums` that may contain duplicates, return all possible subsets (the power set) without duplicate subsets.

**Explanation in Simple Terms:**
- Same as subsets, but we need to avoid duplicate subsets.
- Sort the array first, then skip duplicates by checking if current element is same as previous and we're not starting from the beginning.

**Java Solution:**

```java
public List<List<Integer>> subsetsWithDup(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, int start) {
    list.add(new ArrayList<>(tempList));
    for (int i = start; i < nums.length; i++) {
        if (i > start && nums[i] == nums[i - 1]) continue;  // Skip duplicates
        tempList.add(nums[i]);
        backtrack(list, tempList, nums, i + 1);
        tempList.remove(tempList.size() - 1);
    }
}
```

**Dry Run:**

```
nums = [1, 2, 2] (sorted)

Start: backtrack([], [], [1,2,2], 0)
  Add [] to result
  
  i=0: tempList = [1], backtrack([[]], [1], [1,2,2], 1)
    Add [1] to result
    
    i=1: tempList = [1,2], backtrack([[],[1]], [1,2], [1,2,2], 2)
      Add [1,2] to result
      
      i=2: nums[2]=2 == nums[1]=2, and i=2 > 1, so continue (skip)
      
      Remove 2, tempList = [1]
    
    i=2: nums[2]=2 == nums[1]=2, and i=2 > 1, so continue (skip)
    
    Remove 1, tempList = []
  
  i=1: tempList = [2], backtrack([[],[1],[1,2]], [2], [1,2,2], 2)
    Add [2] to result
    
    i=2: nums[2]=2 == nums[1]=2, and i=2 > 1, so continue (skip)
    
    Remove 2, tempList = []
  
  i=2: nums[2]=2 == nums[1]=2, and i=2 > 1, so continue (skip)

Final result: [[], [1], [1,2], [2]]
(Note: No duplicate [1,2] or [2,2])
```

---

#### Problem 6: Permutations

**Problem:** Given an array `nums` of distinct integers, return all possible permutations.

**Explanation in Simple Terms:**
- We need to generate all arrangements of the numbers.
- For each position, try placing each unused number.
- Use a list to track used numbers.

**Java Solution:**

```java
public List<List<Integer>> permute(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    backtrack(list, new ArrayList<>(), nums);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums) {
    if (tempList.size() == nums.length) {
        list.add(new ArrayList<>(tempList));
        return;
    }
    
    for (int i = 0; i < nums.length; i++) {
        if (tempList.contains(nums[i])) continue;  // Already used
        tempList.add(nums[i]);
        backtrack(list, tempList, nums);
        tempList.remove(tempList.size() - 1);
    }
}
```

**Dry Run:**

```
nums = [1, 2, 3]

Start: backtrack([], [], [1,2,3])
  tempList.size() = 0 != 3
  
  i=0: tempList = [1], backtrack([],[1],[1,2,3])
    size=1 !=3
    
    i=0: contains(1), skip
    i=1: tempList=[1,2], backtrack([],[1,2],[1,2,3])
      size=2!=3
      
      i=0: contains(1), skip
      i=1: contains(2), skip
      i=2: tempList=[1,2,3], backtrack([],[1,2,3],[1,2,3])
        size=3==3, add [1,2,3] to result
        return
      
      remove 3, tempList=[1,2]
    
    i=2: tempList=[1,3], backtrack([],[1,3],[1,2,3])
      size=2!=3
      
      i=0: contains(1), skip
      i=1: tempList=[1,3,2], backtrack([],[1,3,2],[1,2,3])
        size=3==3, add [1,3,2] to result
        return
      
      remove 2, tempList=[1,3]
    
    remove 3, tempList=[1]
  
  i=1: tempList=[2], backtrack([[1,2,3],[1,3,2]],[2],[1,2,3])
    Similar process, adds [2,1,3], [2,3,1]
  
  i=2: tempList=[3], adds [3,1,2], [3,2,1]

Final result: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

---

#### Problem 7: Permutations II (with Duplicates)

**Problem:** Given a collection of numbers that might contain duplicates, return all possible unique permutations.

**Explanation in Simple Terms:**
- Similar to permutations, but avoid duplicate permutations.
- Sort the array and use a used array to track usage.
- Skip if current number is same as previous and previous is not used.

**Java Solution:**

```java
public List<List<Integer>> permuteUnique(int[] nums) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, new boolean[nums.length]);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, boolean[] used) {
    if (tempList.size() == nums.length) {
        list.add(new ArrayList<>(tempList));
        return;
    }
    
    for (int i = 0; i < nums.length; i++) {
        if (used[i] || (i > 0 && nums[i] == nums[i - 1] && !used[i - 1])) continue;
        used[i] = true;
        tempList.add(nums[i]);
        backtrack(list, tempList, nums, used);
        used[i] = false;
        tempList.remove(tempList.size() - 1);
    }
}
```

**Dry Run:**

```
nums = [1, 1, 2] (sorted)

Start: backtrack([], [], [1,1,2], [F,F,F])
  size=0 !=3
  
  i=0: used[0]=F, nums[0]=1, i=0 no prev, add 1, used=[T,F,F], tempList=[1]
    backtrack([],[1],[1,1,2],[T,F,F])
      size=1!=3
      
      i=0: used[0]=T, skip
      i=1: nums[1]=1 == nums[0]=1, and !used[0]=F, so skip
      i=2: nums[2]=2, add 2, used=[T,F,T], tempList=[1,2]
        backtrack([],[1,2],[1,1,2],[T,F,T])
          size=2!=3
          
          i=0: used[0]=T, skip
          i=1: nums[1]=1 == nums[0]=1, !used[0]=F, skip
          i=2: used[2]=T, skip
          
          remove 2, tempList=[1]
      
      remove 2, tempList=[1]
    
    remove 1, tempList=[]
  
  i=1: nums[1]=1 == nums[0]=1, i=1>0, !used[0]=T, so skip
  
  i=2: nums[2]=2, add 2, used=[F,F,T], tempList=[2]
    backtrack([],[2],[1,1,2],[F,F,T])
      size=1!=3
      
      i=0: add 1, used=[T,F,T], tempList=[2,1]
        backtrack([],[2,1],[1,1,2],[T,F,T])
          size=2!=3
          
          i=0: used[0]=T, skip
          i=1: nums[1]=1 == nums[0]=1, !used[0]=F, skip
          i=2: used[2]=T, skip
          
          remove 1, tempList=[2]
      
      i=1: nums[1]=1 == nums[0]=1, i=1>0, !used[0]=T, skip
      i=2: used[2]=T, skip
      
      remove 2, tempList=[]

Final result: [[1,1,2],[1,2,1],[2,1,1]]
```

---

#### Problem 8: Combination Sum

**Problem:** Given an array of distinct integers `candidates` and a target integer `target`, return a list of all unique combinations of `candidates` where the chosen numbers sum to `target`. You may return the combinations in any order. The same number may be chosen from `candidates` an unlimited number of times.

**Explanation in Simple Terms:**
- Find all combinations that sum to target.
- Each number can be used multiple times.
- Sort the array, and use backtracking with remaining sum.

**Java Solution:**

```java
public List<List<Integer>> combinationSum(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, int remain, int start) {
    if (remain < 0) return;
    else if (remain == 0) list.add(new ArrayList<>(tempList));
    else {
        for (int i = start; i < nums.length; i++) {
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, remain - nums[i], i);  // Note: i, not i+1 (reuse allowed)
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

**Dry Run:**

```
nums = [2, 3, 6, 7], target = 7

Start: backtrack([], [], [2,3,6,7], 7, 0)
  remain=7 >0
  
  i=0: tempList=[2], backtrack([],[2],[2,3,6,7],5,0)
    remain=5>0
    
    i=0: tempList=[2,2], backtrack([],[2,2],[2,3,6,7],3,0)
      remain=3>0
      
      i=0: tempList=[2,2,2], backtrack([],[2,2,2],[2,3,6,7],1,0)
        remain=1>0
        
        i=0: tempList=[2,2,2,2], backtrack([],[2,2,2,2],[2,3,6,7],-1,0)
          remain<0, return
        
        remove 2, tempList=[2,2,2]
      
      i=1: tempList=[2,2,3], backtrack([],[2,2,3],[2,3,6,7],0,1)
        remain=0, add [2,2,3] to result
      
      remove 3, tempList=[2,2]
    
    i=1: tempList=[2,3], backtrack([],[2,3],[2,3,6,7],2,1)
      remain=2>0
      
      i=1: tempList=[2,3,3], backtrack([],[2,3,3],[2,3,6,7],-1,1)
        remain<0, return
      
      remove 3, tempList=[2,3]
    
    remove 3, tempList=[2]
  
  i=1: tempList=[3], backtrack([],[3],[2,3,6,7],4,1)
    remain=4>0
    
    i=1: tempList=[3,3], backtrack([],[3,3],[2,3,6,7],1,1)
      remain=1>0
      
      i=1: tempList=[3,3,3], backtrack([],[3,3,3],[2,3,6,7],-2,1)
        remain<0, return
      
      remove 3, tempList=[3,3]
    
    remove 3, tempList=[3]
  
  i=2: tempList=[6], backtrack([],[6],[2,3,6,7],1,2)
    remain=1>0
    
    i=2: tempList=[6,6], backtrack([],[6,6],[2,3,6,7],-5,2)
      remain<0, return
    
    remove 6, tempList=[6]
  
  i=3: tempList=[7], backtrack([],[7],[2,3,6,7],0,3)
    remain=0, add [7] to result

Final result: [[2,2,3], [7]]
```

---

#### Problem 9: Combination Sum II (No Reuse)

**Problem:** Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sum to `target`. Each number in `candidates` may only be used once in the combination.

**Explanation in Simple Terms:**
- Similar to Combination Sum, but each number can be used only once.
- Sort and skip duplicates.

**Java Solution:**

```java
public List<List<Integer>> combinationSum2(int[] nums, int target) {
    List<List<Integer>> list = new ArrayList<>();
    Arrays.sort(nums);
    backtrack(list, new ArrayList<>(), nums, target, 0);
    return list;
}

private void backtrack(List<List<Integer>> list, List<Integer> tempList, int[] nums, int remain, int start) {
    if (remain < 0) return;
    else if (remain == 0) list.add(new ArrayList<>(tempList));
    else {
        for (int i = start; i < nums.length; i++) {
            if (i > start && nums[i] == nums[i - 1]) continue;  // Skip duplicates
            tempList.add(nums[i]);
            backtrack(list, tempList, nums, remain - nums[i], i + 1);  // i+1, no reuse
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

**Dry Run:**

```
nums = [10,1,2,7,6,1,5], target = 8 (sorted: [1,1,2,5,6,7,10])

Start: backtrack([], [], [1,1,2,5,6,7,10], 8, 0)
  remain=8>0
  
  i=0: tempList=[1], backtrack([],[1],[...],7,1)
    remain=7>0
    
    i=1: nums[1]=1 == nums[0]=1, i=1>0, skip
    i=2: tempList=[1,2], backtrack([],[1,2],[...],5,3)
      remain=5>0
      
      i=3: tempList=[1,2,5], backtrack([],[1,2,5],[...],0,4)
        remain=0, add [1,2,5] to result
      
      remove 5, tempList=[1,2]
    
    i=3: tempList=[1,5], backtrack([],[1,5],[...],2,4)
      remain=2>0
      
      i=4: tempList=[1,5,6], backtrack([],[1,5,6],[...],-4,5)
        remain<0, return
      
      remove 6, tempList=[1,5]
    
    remove 5, tempList=[1]
  
  i=1: nums[1]=1 == nums[0]=1, i=1>0, skip
  
  i=2: tempList=[2], backtrack([],[2],[...],6,3)
    remain=6>0
    
    i=3: tempList=[2,5], backtrack([],[2,5],[...],1,4)
      remain=1>0
      
      i=4: tempList=[2,5,6], backtrack([],[2,5,6],[...],-5,5)
        remain<0, return
      
      remove 6, tempList=[2,5]
    
    remove 5, tempList=[2]
  
  i=3: tempList=[5], backtrack([],[5],[...],3,4)
    remain=3>0
    
    i=4: tempList=[5,6], backtrack([],[5,6],[...],-3,5)
      remain<0, return
    
    remove 6, tempList=[5]
  
  i=4: tempList=[6], backtrack([],[6],[...],2,5)
    remain=2>0
    
    i=5: tempList=[6,7], backtrack([],[6,7],[...],-5,6)
      remain<0, return
    
    remove 7, tempList=[6]
  
  i=5: tempList=[7], backtrack([],[7],[...],1,6)
    remain=1>0
    
    i=6: tempList=[7,10], backtrack([],[7,10],[...],-9,7)
      remain<0, return
    
    remove 10, tempList=[7]

Final result: [[1,2,5]]
```

---

#### Problem 10: Palindrome Partitioning

**Problem:** Given a string `s`, partition `s` such that every substring of the partition is a palindrome. Return all possible palindrome partitioning of `s`.

**Explanation in Simple Terms:**
- Split the string into substrings where each is a palindrome.
- Use backtracking to try different partitions.
- Check if substring is palindrome.

**Java Solution:**

```java
public List<List<String>> partition(String s) {
    List<List<String>> list = new ArrayList<>();
    backtrack(list, new ArrayList<>(), s, 0);
    return list;
}

public void backtrack(List<List<String>> list, List<String> tempList, String s, int start) {
    if (start == s.length())
        list.add(new ArrayList<>(tempList));
    else {
        for (int i = start; i < s.length(); i++) {
            if (isPalindrome(s, start, i)) {
                tempList.add(s.substring(start, i + 1));
                backtrack(list, tempList, s, i + 1);
                tempList.remove(tempList.size() - 1);
            }
        }
    }
}

public boolean isPalindrome(String s, int low, int high) {
    while (low < high)
        if (s.charAt(low++) != s.charAt(high--)) return false;
    return true;
}
```

**Dry Run:**

```
s = "aab"

Start: backtrack([], [], "aab", 0)
  start=0 !=3
  
  i=0: isPalindrome("a",0,0)=true, tempList=["a"], backtrack([],[ "a" ],"aab",1)
    start=1!=3
    
    i=1: isPalindrome("a",1,1)=true, tempList=["a","a"], backtrack([],[ "a","a" ],"aab",2)
      start=2!=3
      
      i=2: isPalindrome("b",2,2)=true, tempList=["a","a","b"], backtrack([],[ "a","a","b" ],"aab",3)
        start=3==3, add ["a","a","b"] to result
      
      remove "b", tempList=["a","a"]
    
    i=2: isPalindrome("ab",1,2)="ab" != "ba", false
    
    remove "a", tempList=["a"]
  
  i=1: isPalindrome("aa",0,1)="aa" == "aa", true, tempList=["aa"], backtrack([],[ "aa" ],"aab",2)
    start=2!=3
    
    i=2: isPalindrome("b",2,2)=true, tempList=["aa","b"], backtrack([],[ "aa","b" ],"aab",3)
      start=3==3, add ["aa","b"] to result
    
    remove "b", tempList=["aa"]
  
  i=2: isPalindrome("aab",0,2)="aab" != "baa", false
  
  remove "aa", tempList=[]

Final result: [["a","a","b"], ["aa","b"]]
```

---

## 6. Pattern 4: Dynamic Programming (Memoization)

## 6. Pattern 4: Dynamic Programming (Memoization)

### Concept

```
Recursion + Caching = Dynamic Programming

Key difference from Divide & Conquer:
- Subproblems OVERLAP (same subproblem solved multiple times)
- Cache results to avoid recomputation
```

### Template

```cpp
// 1D DP
unordered_map<int, int> memo;

int dpRecursive(int n) {
    // Base case
    if (n <= 1) return baseValue;
    
    // Check cache
    if (memo.count(n)) {
        return memo[n];
    }
    
    // Compute and cache
    memo[n] = compute(dpRecursive(n-1), dpRecursive(n-2));
    return memo[n];
}

// 2D DP
map<pair<int,int>, int> memo;

int dpRecursive(int i, int j) {
    // Base case
    if (baseCaseCondition) return baseValue;
    
    // Check cache
    if (memo.count({i, j})) {
        return memo[{i, j}];
    }
    
    // Compute and cache
    memo[{i, j}] = compute(dpRecursive(i-1, j), dpRecursive(i, j-1));
    return memo[{i, j}];
}
```

---

### Problem 1: Fibonacci with Memoization

**Problem:** Calculate nth Fibonacci number.

**Why Memoization Needed:**
```
fib(5) without memoization:

                    fib(5)
                   /      \
              fib(4)      fib(3)
             /     \      /     \
        fib(3)  fib(2) fib(2) fib(1)
        /   \    /  \   /  \
    fib(2) fib(1) ...  ...
    
Notice: fib(3) computed TWICE
        fib(2) computed THREE times!
        
With memoization: Each computed ONCE
```

**C++ Solution:**

```cpp
class Solution {
private:
    unordered_map<int, int> memo;
    
public:
    int fib(int n) {
        // Base cases
        if (n <= 1) return n;
        
        // Check cache
        if (memo.count(n)) {
            return memo[n];
        }
        
        // Compute and store
        memo[n] = fib(n - 1) + fib(n - 2);
        return memo[n];
    }
};
```

**Comparison:**

```
Without Memoization: fib(6)
============================
Calls made:
fib(6) → fib(5), fib(4)
fib(5) → fib(4), fib(3)  ← fib(4) again!
fib(4) → fib(3), fib(2)  ← fib(3) again!
...

Total calls: 25 (exponential growth)

With Memoization: fib(6)
=========================
First call:
fib(6) → compute fib(5), compute fib(4)
fib(5) → compute fib(4), compute fib(3)  ← fib(4) from cache!
fib(4) → compute fib(3), compute fib(2)
fib(3) → compute fib(2), compute fib(1)
fib(2) → compute fib(1), compute fib(0)

Subsequent fib(4), fib(3), fib(2): FROM CACHE

Total calls: 7 (linear)

Time: O(2^n) → O(n)
```

---

### Problem 2: Climbing Stairs

**Problem:** You can climb 1 or 2 steps. How many ways to reach nth step?

**Recursive Insight:**
```
ways(n) = ways(n-1) + ways(n-2)

To reach step n:
  - Come from step n-1 (1 step)
  - Come from step n-2 (2 steps)

Base: ways(0) = 1, ways(1) = 1
```

**C++ Solution:**

```cpp
class Solution {
private:
    unordered_map<int, int> memo;
    
public:
    int climbStairs(int n) {
        // Base cases
        if (n <= 1) return 1;
        
        // Check cache
        if (memo.count(n)) {
            return memo[n];
        }
        
        // Compute: ways from n-1 + ways from n-2
        memo[n] = climbStairs(n - 1) + climbStairs(n - 2);
        return memo[n];
    }
};
```

**Dry Run:**

```
n = 5

Call Tree (with memoization):
==============================

climbStairs(5)
  Not in memo
  = climbStairs(4) + climbStairs(3)
  
    climbStairs(4)
      Not in memo
      = climbStairs(3) + climbStairs(2)
      
        climbStairs(3)
          Not in memo
          = climbStairs(2) + climbStairs(1)
          
            climbStairs(2)
              Not in memo
              = climbStairs(1) + climbStairs(0)
              
                climbStairs(1) → 1 (base)
                climbStairs(0) → 1 (base)
              
              = 1 + 1 = 2
              memo[2] = 2
            
            climbStairs(1) → 1 (base)
          
          = 2 + 1 = 3
          memo[3] = 3
        
        climbStairs(2) → 2 (FROM CACHE!)
      
      = 3 + 2 = 5
      memo[4] = 5
    
    climbStairs(3) → 3 (FROM CACHE!)
  
  = 5 + 3 = 8
  memo[5] = 8

Answer: 8 ways

The 8 ways are:
1+1+1+1+1
1+1+1+2
1+1+2+1
1+2+1+1
2+1+1+1
1+2+2
2+1+2
2+2+1
```

---

### Problem 3: Longest Common Subsequence (LCS)

**Problem:** Find length of longest common subsequence of two strings.

**Recursive Insight:**
```
LCS(s1, s2, i, j):
  If s1[i] == s2[j]:
    1 + LCS(s1, s2, i+1, j+1)  ← Match found
  Else:
    max(LCS(s1, s2, i+1, j),   ← Skip s1[i]
        LCS(s1, s2, i, j+1))   ← Skip s2[j]

Base: i == len(s1) or j == len(s2) → 0
```

**C++ Solution:**

```cpp
class Solution {
private:
    map<pair<int,int>, int> memo;
    
public:
    int longestCommonSubsequence(string text1, string text2) {
        return lcs(text1, text2, 0, 0);
    }
    
private:
    int lcs(string& s1, string& s2, int i, int j) {
        // Base case: reached end of either string
        if (i >= s1.length() || j >= s2.length()) {
            return 0;
        }
        
        // Check cache
        if (memo.count({i, j})) {
            return memo[{i, j}];
        }
        
        int result;
        if (s1[i] == s2[j]) {
            // Characters match: include and move both
            result = 1 + lcs(s1, s2, i + 1, j + 1);
        } else {
            // Characters don't match: try skipping each
            result = max(lcs(s1, s2, i + 1, j),     // Skip s1[i]
                        lcs(s1, s2, i, j + 1));    // Skip s2[j]
        }
        
        memo[{i, j}] = result;
        return result;
    }
};
```

**Detailed Dry Run:**

```
s1 = "ABCD"
s2 = "ACBD"

Call: lcs(s1, s2, 0, 0)

Decision Tree:
==============

               lcs(0,0)
               s1[0]='A', s2[0]='A' → MATCH!
                    |
            1 + lcs(1,1)
               s1[1]='B', s2[1]='C' → NO MATCH
                /              \
          lcs(2,1)          lcs(1,2)
          s1[2]='C'         s1[1]='B'
          s2[1]='C'         s2[2]='B'
          MATCH!            MATCH!
             |                 |
        1+lcs(3,2)        1+lcs(2,3)
        s1[3]='D'         s1[2]='C'
        s2[2]='B'         s2[3]='D'
        NO MATCH          NO MATCH
          /    \            /    \
     lcs(4,2) lcs(3,3)  lcs(3,3) lcs(2,4)
        =0    s1[3]='D'  (cache)   =0
              s2[3]='D'
              MATCH!
                 |
            1+lcs(4,4)
                =0

Computation:
============

lcs(0,0): s1[0]='A' == s2[0]='A'
          = 1 + lcs(1,1)
          
  lcs(1,1): s1[1]='B' != s2[1]='C'
            = max(lcs(2,1), lcs(1,2))
            
    lcs(2,1): s1[2]='C' == s2[1]='C'
              = 1 + lcs(3,2)
              
      lcs(3,2): s1[3]='D' != s2[2]='B'
                = max(lcs(4,2), lcs(3,3))
                
        lcs(4,2): i=4 >= len(s1), return 0
        
        lcs(3,3): s1[3]='D' == s2[3]='D'
                  = 1 + lcs(4,4)
                  
          lcs(4,4): return 0
          
                  = 1 + 0 = 1
        
                = max(0, 1) = 1
              
              = 1 + 1 = 2
              memo[{2,1}] = 2
    
    lcs(1,2): s1[1]='B' == s2[2]='B'
              = 1 + lcs(2,3)
              
      lcs(2,3): s1[2]='C' != s2[3]='D'
                = max(lcs(3,3), lcs(2,4))
                
        lcs(3,3): FROM CACHE = 1
        lcs(2,4): j=4 >= len(s2), return 0
        
                = max(1, 0) = 1
              
              = 1 + 1 = 2
              memo[{1,2}] = 2
    
            = max(2, 2) = 2
            memo[{1,1}] = 2
  
          = 1 + 2 = 3
          memo[{0,0}] = 3

Answer: 3
LCS: "ABD" or "ACD" (both have length 3)
```

---

## 7. Pattern 5: Tree Recursion

### Concept

```
Trees are naturally recursive structures
Each node's problem = same problem on subtrees

Binary Tree Pattern:
  solve(node) = combine(solve(left), solve(right))
```

### Template

```cpp
ReturnType treeRecursion(TreeNode* root) {
    // Base case: null node
    if (!root) {
        return baseValue;
    }
    
    // Recursive case: process subtrees
    ReturnType leftResult = treeRecursion(root->left);
    ReturnType rightResult = treeRecursion(root->right);
    
    // Combine results with current node
    return combine(root->val, leftResult, rightResult);
}
```

---

### Problem 1: Maximum Depth of Binary Tree

**Problem:** Find height of binary tree.

**Recursive Insight:**
```
depth(node) = 1 + max(depth(left), depth(right))

Base: null node → depth = 0
```

**C++ Solution:**

```cpp
int maxDepth(TreeNode* root) {
    // Base case
    if (!root) return 0;
    
    // Recursive case
    int leftDepth = maxDepth(root->left);
    int rightDepth = maxDepth(root->right);
    
    // Combine
    return 1 + max(leftDepth, rightDepth);
}
```

**Dry Run:**

```
Tree:
        3
       / \
      9  20
         / \
        15  7

Execution:
==========

maxDepth(3)
  leftDepth = maxDepth(9)
               → maxDepth(null) = 0 (left)
               → maxDepth(null) = 0 (right)
               → 1 + max(0,0) = 1
  
  rightDepth = maxDepth(20)
                → leftDepth = maxDepth(15)
                               → 1 + max(0,0) = 1
                → rightDepth = maxDepth(7)
                                → 1 + max(0,0) = 1
                → 1 + max(1,1) = 2
  
  return 1 + max(1,2) = 3

Answer: 3
```

---

### Problem 2: Path Sum

**Problem:** Check if tree has root-to-leaf path with given sum.

**Recursive Insight:**
```
hasPathSum(node, sum):
  If leaf: check if node->val == sum
  Else: check left OR right with (sum - node->val)
```

**C++ Solution:**

```cpp
bool hasPathSum(TreeNode* root, int targetSum) {
    // Base case: null node
    if (!root) return false;
    
    // Base case: leaf node
    if (!root->left && !root->right) {
        return root->val == targetSum;
    }
    
    // Recursive case: check left or right subtree
    int remaining = targetSum - root->val;
    return hasPathSum(root->left, remaining) || 
           hasPathSum(root->right, remaining);
}
```

**Dry Run:**

```
Tree:              targetSum = 22
      5
     / \
    4   8
   /   / \
  11  13  4
 / \       \
7   2       1

Execution:
==========

hasPathSum(5, 22)
  Not leaf
  remaining = 22 - 5 = 17
  
  Left: hasPathSum(4, 17)
         Not leaf
         remaining = 17 - 4 = 13
         
         Left: hasPathSum(11, 13)
                Not leaf
                remaining = 13 - 11 = 2
                
                Left: hasPathSum(7, 2)
                       Leaf: 7 == 2? NO
                       return false
                
                Right: hasPathSum(2, 2)
                        Leaf: 2 == 2? YES ✓
                        return true
                
                return false || true = true
         
         return true
  
  return true

Path found: 5 → 4 → 11 → 2 (sum = 22)
```

---

## 8. Problem Recognition Framework

### Decision Tree

```
RECURSION PROBLEM?

Q1: Can problem be broken into smaller identical subproblems?
├─ YES → Continue
└─ NO → Try iterative approach

Q2: Do subproblems overlap?
├─ YES → Use Memoization (DP Pattern)
└─ NO → Continue

Q3: What structure?
├─ Tree/Graph → Tree Recursion
├─ Array/String → Continue to Q4
└─ Choices/Combinations → Backtracking

Q4: How to split problem?
├─ Try all possibilities → Backtracking
├─ Split in half → Divide & Conquer
└─ Reduce by 1 → Simple Recursion
```

### Pattern Recognition Table

| Problem Contains | Pattern | Key Indicator |
|-----------------|---------|---------------|
| "all subsets" | Backtracking | Include/exclude each |
| "all permutations" | Backtracking | Try each position |
| "all combinations" | Backtracking | Choose k from n |
| "sort array" | Divide & Conquer | Split & merge |
| "fibonacci" | DP Memoization | Overlapping subproblems |
| "ways to reach" | DP Memoization | Count paths |
| "tree depth/height" | Tree Recursion | Process subtrees |
| "factorial/sum" | Simple Recursion | Reduce by 1 |
| "binary search" | Divide & Conquer | Eliminate half |
| "N-Queens/Sudoku" | Backtracking | Place with constraints |

---

## Common Patterns Summary

### 1. Simple Recursion Template
```cpp
int simple(int n) {
    if (n <= base) return baseValue;
    return operation(n, simple(n-1));
}
// Use: Factorial, sum, print, reverse
```

### 2. Divide & Conquer Template
```cpp
ReturnType dc(vector<int>& arr, int left, int right) {
    if (left >= right) return baseSolution;
    
    int mid = left + (right - left) / 2;
    auto leftResult = dc(arr, left, mid);
    auto rightResult = dc(arr, mid+1, right);
    
    return merge(leftResult, rightResult);
}
// Use: Merge sort, quick sort, binary search
```

### 3. Backtracking Template
```cpp
void backtrack(State& state, vector<Result>& result) {
    if (isComplete(state)) {
        result.push_back(state);
        return;
    }
    
    for (auto choice : choices) {
        state.add(choice);
        if (isValid(state)) {
            backtrack(state, result);
        }
        state.remove(choice);
    }
}
// Use: Subsets, permutations, combinations, N-Queens
```

### 4. DP Memoization Template
```cpp
unordered_map<Key, Value> memo;

Value dp(Key key) {
    if (baseCase) return baseValue;
    if (memo.count(key)) return memo[key];
    
    memo[key] = compute(dp(smallerKey));
    return memo[key];
}
// Use: Fibonacci, climbing stairs, LCS, knapsack
```

### 5. Tree Recursion Template
```cpp
ReturnType tree(TreeNode* root) {
    if (!root) return baseValue;
    
    auto left = tree(root->left);
    auto right = tree(root->right);
    
    return combine(root->val, left, right);
}
// Use: Tree traversal, depth, diameter, path sum
```

---

## Key Takeaways

### 1. **The Recursive Leap of Faith**
```
Don't try to trace entire recursion in your head
Trust that recursive call solves smaller problem
Focus on:
  - Base case
  - How to use subproblem solution
  - Combination logic
```

### 2. **Base Case is Critical**
```
Always define stopping condition
Usually the simplest case
Missing base case = infinite recursion
```

### 3. **Memoization Transforms Complexity**
```
Fibonacci without memo: O(2^n)
Fibonacci with memo: O(n)

Always check for overlapping subproblems!
```

### 4. **Backtracking = Structured Trial & Error**
```
Make choice → Recurse → Undo choice

The "undo" step is what makes it backtracking
```

### 5. **Space Complexity Matters**
```
Recursion uses call stack
Depth = space complexity
Consider iterative if stack overflow risk
```

---

## Practice Roadmap

### Week 1: Master Simple Recursion
1. Factorial (Easy)
2. Sum of Array (Easy)
3. Reverse String (Easy)
4. Power(x, n) (Medium)
5. Print all binary strings (Medium)

### Week 2: Divide & Conquer
1. Binary Search (Easy)
2. Merge Sort (Medium)
3. Quick Sort (Medium)
4. Maximum Subarray (Medium)
5. Kth Largest Element (Medium)

### Week 3: Backtracking
1. Subsets (Medium)
2. Permutations (Medium)
3. Combinations (Medium)
4. Letter Combinations of Phone (Medium)
5. N-Queens (Hard)

### Week 4: DP with Recursion
1. Fibonacci (Easy)
2. Climbing Stairs (Easy)
3. House Robber (Medium)
4. Longest Common Subsequence (Medium)
5. Word Break (Medium)

### Week 5: Tree Recursion
1. Max Depth (Easy)
2. Invert Tree (Easy)
3. Path Sum (Easy)
4. Diameter of Tree (Easy)
5. Lowest Common Ancestor (Medium)

---

*Master these patterns, and recursion becomes your most powerful problem-solving tool!*