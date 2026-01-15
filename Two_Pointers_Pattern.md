# Ultimate Two Pointers Pattern Guide

## Table of Contents
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [The Two Major Categories](#2-the-two-major-categories)
3. [Pattern 1: Opposite Direction (Converging)](#3-pattern-1-opposite-direction-converging)
4. [Pattern 2: Same Direction (Chasing/Lagging)](#4-pattern-2-same-direction-chasinglagging)
5. [Practice Problems](#5-practice-problems)
6. [Problem Recognition Framework](#6-problem-recognition-framework)

---

## 1. Core Concept & Philosophy

### What is Two Pointers Really About?

**Wrong Understanding:**
"Two pointers means having two indices in an array"

**Correct Understanding:**
"Two pointers is an **optimization technique** that uses **two indices** moving with **different speeds or directions** to avoid nested loops and reduce time complexity from O(n²) to O(n)"

### The Power of Two Pointers

```
Brute Force: Check every pair
for i in 0..n:
    for j in i+1..n:
        check(i, j)
Time: O(n²)

Two Pointers: Smart traversal
left = 0, right = n-1
while left < right:
    process(left, right)
    adjust pointers based on condition
Time: O(n)
```

### When to Use Two Pointers?

**Key Indicators:**
1. Input is **sorted** or can be sorted
2. Problem involves **pairs, triplets, or subarrays**
3. Need to find **two elements satisfying a condition**
4. Can we make a decision to **move left or right** based on current values?
5. Looking for **optimized subarray/substring**

---

## 2. The Two Major Categories

Based on pointer movement direction:

```
Category 1: OPPOSITE DIRECTION (Converging Pointers)
    ├─ Two Sum variants
    ├─ Palindrome problems
    ├─ Container/Area problems
    └─ Partition problems

Category 2: SAME DIRECTION (Chasing/Lagging Pointers)
    ├─ Remove duplicates
    ├─ Move zeros
    ├─ Merge sorted arrays
    └─ Partition arrays
```

---

## 3. Pattern 1: Opposite Direction (Converging)

### Concept

```
Two pointers start at opposite ends and move toward each other

[1, 2, 3, 4, 5, 6, 7, 8, 9]
 ^                       ^
left                   right
 
Decision: Move left++ or right-- based on condition
```

### Template

```cpp
int oppositeDirection(vector<int>& arr, int target) {
    int left = 0;
    int right = arr.size() - 1;
    
    while (left < right) {
        int current = compute(arr[left], arr[right]);
        
        if (current == target) {
            return answer;
        } else if (current < target) {
            left++;     // Need larger value
        } else {
            right--;    // Need smaller value
        }
    }
    
    return -1;
}
```

### Problem 1: Two Sum (Sorted Array)

**Problem:** Find two numbers that add up to target in a sorted array.

**Why This Works:**
```
Array: [2, 7, 11, 15], target = 9

If sum < target: Need larger number → move left pointer right
If sum > target: Need smaller number → move right pointer left
If sum == target: Found answer!
```

**C++ Solution:**

```cpp
vector<int> twoSum(vector<int>& numbers, int target) {
    int left = 0;
    int right = numbers.size() - 1;
    
    while (left < right) {
        int sum = numbers[left] + numbers[right];
        
        if (sum == target) {
            return {left + 1, right + 1};  // 1-indexed
        } else if (sum < target) {
            left++;   // Need larger sum
        } else {
            right--;  // Need smaller sum
        }
    }
    
    return {-1, -1};
}
```

**Dry Run:**

```
numbers = [2, 7, 11, 15], target = 9

Step 1: left=0, right=3
        [2, 7, 11, 15]
         ^          ^
        sum = 2 + 15 = 17 > 9
        right-- (need smaller)

Step 2: left=0, right=2
        [2, 7, 11, 15]
         ^      ^
        sum = 2 + 11 = 13 > 9
        right--

Step 3: left=0, right=1
        [2, 7, 11, 15]
         ^  ^
        sum = 2 + 7 = 9 == 9 ✓
        Return [1, 2]
```

**Why O(n)?** Each pointer moves at most n times, never backtracks.

---

### Problem 2: Valid Palindrome

**Problem:** Check if string is palindrome (ignoring non-alphanumeric).

**Key Insight:**
```
Palindrome property: s[i] == s[n-1-i] for all i

Instead of creating new string:
Compare from both ends moving inward
```

**C++ Solution:**

```cpp
bool isPalindrome(string s) {
    int left = 0;
    int right = s.length() - 1;
    
    while (left < right) {
        // Skip non-alphanumeric from left
        while (left < right && !isalnum(s[left])) {
            left++;
        }
        
        // Skip non-alphanumeric from right
        while (left < right && !isalnum(s[right])) {
            right--;
        }
        
        // Compare characters (case-insensitive)
        if (tolower(s[left]) != tolower(s[right])) {
            return false;
        }
        
        left++;
        right--;
    }
    
    return true;
}
```

**Dry Run:**

```
s = "A man, a plan, a canal: Panama"

Step 1: left=0 ('A'), right=30 ('a')
        A == a? YES ✓
        left++, right--

Step 2: left=1 (' '), skip spaces
        left=2 ('m'), right=29 ('m')
        m == m? YES ✓

... continue until middle ...

All characters match → TRUE
```

---

### Problem 3: Container With Most Water

**Problem:** Find two lines that form container with maximum water.

**Key Insight:**
```
Area = min(height[left], height[right]) * (right - left)

Greedy: Always move pointer with smaller height
Why? Smaller height limits area, moving it might find better
```

**C++ Solution:**

```cpp
int maxArea(vector<int>& height) {
    int left = 0;
    int right = height.size() - 1;
    int maxWater = 0;
    
    while (left < right) {
        // Calculate current area
        int width = right - left;
        int h = min(height[left], height[right]);
        int area = width * h;
        
        maxWater = max(maxWater, area);
        
        // Move pointer with smaller height
        if (height[left] < height[right]) {
            left++;
        } else {
            right--;
        }
    }
    
    return maxWater;
}
```

**Dry Run:**

```
height = [1,8,6,2,5,4,8,3,7]
         0 1 2 3 4 5 6 7 8

Step 1: left=0 (h=1), right=8 (h=7)
        area = min(1,7) * 8 = 8
        maxWater = 8
        height[0] < height[8] → left++

Step 2: left=1 (h=8), right=8 (h=7)
        area = min(8,7) * 7 = 49
        maxWater = 49
        height[1] > height[8] → right--

Step 3: left=1 (h=8), right=7 (h=3)
        area = min(8,3) * 6 = 18
        maxWater = 49 (no change)
        height[1] > height[7] → right--

... continue ...

Final: maxWater = 49
```

**Why Greedy Works:**
```
If height[left] < height[right]:
    Moving right-- can only decrease width
    And area is limited by height[left] anyway
    So moving left++ might find taller line
```

---

## 4. Pattern 2: Same Direction (Chasing/Lagging)

### Concept

```
Both pointers start at beginning, move in same direction
One pointer "chases" or "lags behind" the other

[1, 2, 2, 3, 3, 3, 4, 5]
 ^  ^
slow fast

slow: marks position to write
fast: explores elements
```

### Template

```cpp
int sameDirection(vector<int>& arr) {
    int slow = 0;  // Write position
    int fast = 0;  // Read position
    
    while (fast < arr.size()) {
        if (condition(arr[fast])) {
            arr[slow] = arr[fast];
            slow++;
        }
        fast++;
    }
    
    return slow;  // New length
}
```

### Problem 1: Remove Duplicates from Sorted Array

**Problem:** Remove duplicates in-place, return new length.

**Key Insight:**
```
sorted: [1,1,2,2,3,3]
unique: [1,2,3,_,_,_]

slow: position to place next unique element
fast: explores array looking for new elements
```

**C++ Solution:**

```cpp
int removeDuplicates(vector<int>& nums) {
    if (nums.empty()) return 0;
    
    int slow = 0;  // Position for next unique element
    
    for (int fast = 1; fast < nums.size(); fast++) {
        // Found new unique element
        if (nums[fast] != nums[slow]) {
            slow++;
            nums[slow] = nums[fast];
        }
    }
    
    return slow + 1;  // Length of unique array
}
```

**Detailed Dry Run:**

```
nums = [1, 1, 2, 2, 3, 3]

Initial: slow=0, fast=1
        [1, 1, 2, 2, 3, 3]
         ^  ^
       slow fast

Step 1: nums[1]=1 == nums[0]=1? YES
        Skip (duplicate)
        fast++
        [1, 1, 2, 2, 3, 3]
         ^     ^
       slow  fast

Step 2: nums[2]=2 != nums[0]=1? YES
        Found new unique!
        slow++, nums[slow] = nums[fast]
        [1, 2, 2, 2, 3, 3]
            ^     ^
          slow  fast

Step 3: nums[3]=2 == nums[1]=2? YES
        Skip
        fast++
        [1, 2, 2, 2, 3, 3]
            ^        ^
          slow     fast

Step 4: nums[4]=3 != nums[1]=2? YES
        slow++, nums[slow] = nums[fast]
        [1, 2, 3, 2, 3, 3]
               ^     ^
             slow  fast

Step 5: nums[5]=3 == nums[2]=3? YES
        Skip
        fast++
        
Return slow + 1 = 3
Result: [1, 2, 3, ...]
```

**Invariant:**
- Elements [0...slow] are unique
- fast explores ahead
- slow never needs to go back

---

### Problem 2: Move Zeros

**Problem:** Move all zeros to end, maintain relative order.

**Key Insight:**
```
[0,1,0,3,12]
   
slow: position for next non-zero
fast: finds non-zero elements

Process: Copy non-zeros forward, fill rest with zeros
```

**C++ Solution:**

```cpp
void moveZeroes(vector<int>& nums) {
    int slow = 0;  // Position for next non-zero
    
    // Move all non-zeros to front
    for (int fast = 0; fast < nums.size(); fast++) {
        if (nums[fast] != 0) {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    
    // Fill remaining with zeros
    while (slow < nums.size()) {
        nums[slow] = 0;
        slow++;
    }
}
```

**Alternative: Swap Version (More Efficient)**

```cpp
void moveZeroes(vector<int>& nums) {
    int slow = 0;
    
    for (int fast = 0; fast < nums.size(); fast++) {
        if (nums[fast] != 0) {
            swap(nums[slow], nums[fast]);
            slow++;
        }
    }
}
```

**Dry Run:**

```
nums = [0, 1, 0, 3, 12]

Step 1: slow=0, fast=0
        nums[0]=0? YES, skip
        [0, 1, 0, 3, 12]
         ^
      slow,fast

Step 2: slow=0, fast=1
        nums[1]=1? Non-zero!
        swap(nums[0], nums[1])
        slow++
        [1, 0, 0, 3, 12]
            ^  ^
          slow fast

Step 3: slow=1, fast=2
        nums[2]=0? YES, skip

Step 4: slow=1, fast=3
        nums[3]=3? Non-zero!
        swap(nums[1], nums[3])
        slow++
        [1, 3, 0, 0, 12]
               ^     ^
             slow  fast

Step 5: slow=2, fast=4
        nums[4]=12? Non-zero!
        swap(nums[2], nums[4])
        [1, 3, 12, 0, 0]

Final: [1, 3, 12, 0, 0]
```
### Problem 3: Dutch National Flag

**Problem:** Sort an array containing only 0s, 1s, and 2s in-place, with 0s first, then 1s, then 2s.

**Key Insight:**
```
[0,1,2,1,0,2,1,0]

low: boundary for 0s
mid: current element
high: boundary for 2s

Process: Partition into three regions
```

**C++ Solution:**

```cpp
void sortColors(vector<int>& nums) {
    int low = 0, mid = 0, high = nums.size() - 1;
    
    while (mid <= high) {
        if (nums[mid] == 0) {
            swap(nums[low], nums[mid]);
            low++;
            mid++;
        } else if (nums[mid] == 1) {
            mid++;
        } else { // nums[mid] == 2
            swap(nums[mid], nums[high]);
            high--;
            // mid stays, because swapped element needs checking
        }
    }
}
```

**Dry Run:**

```
nums = [2, 0, 2, 1, 1, 0]

Initial: low=0, mid=0, high=5
        [2, 0, 2, 1, 1, 0]
         ^     ^     ^
        low  mid  high

Step 1: nums[0]=2? ==2, swap with high
        swap(nums[0], nums[5]) -> [0, 0, 2, 1, 1, 2]
        high-- -> high=4
        mid stays 0
        [0, 0, 2, 1, 1, 2]
         ^  ^  ^
        low mid high

Step 2: nums[0]=0? ==0, swap with low (low=0, mid=0, swap self)
        low++, mid++ -> low=1, mid=1
        [0, 0, 2, 1, 1, 2]
            ^  ^  ^
           low mid high

Step 3: nums[1]=0? ==0, swap with low
        swap(nums[1], nums[1]) self
        low=2, mid=2
        [0, 0, 2, 1, 1, 2]
               ^  ^  ^
              low mid high

Step 4: nums[2]=2? ==2, swap with high
        swap(nums[2], nums[4]) -> [0, 0, 1, 1, 2, 2]
        high=3
        mid stays 2
        [0, 0, 1, 1, 2, 2]
               ^  ^  ^
              low mid high

Step 5: nums[2]=1? ==1, mid++ -> mid=3
        [0, 0, 1, 1, 2, 2]
                  ^  ^  ^
                 low mid high

Step 6: nums[3]=1? ==1, mid++ -> mid=4
        [0, 0, 1, 1, 2, 2]
                     ^  ^
                    low mid high

Step 7: mid=4 > high=3? Stop

Final: [0, 0, 1, 1, 2, 2]
```

---

## 5. Practice Problems

### Opposite Direction (Converging) Problems
1. [Two Sum II - Input Array Is Sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)
2. [3Sum](https://leetcode.com/problems/3sum/)
3. [4Sum](https://leetcode.com/problems/4sum/)
4. [Number of Subsequences That Satisfy the Given Sum Condition](https://leetcode.com/problems/number-of-subsequences-that-satisfy-the-given-sum-condition/)
5. [Two Sum IV - Input is a BST](https://leetcode.com/problems/two-sum-iv-input-is-a-bst/)
6. [Sum of Square Numbers](https://leetcode.com/problems/sum-of-square-numbers/)
7. [Boats to Save People](https://leetcode.com/problems/boats-to-save-people/)
8. [Minimize Maximum Pair Sum in Array](https://leetcode.com/problems/minimize-maximum-pair-sum-in-array/)
9. [3Sum With Multiplicity](https://leetcode.com/problems/3sum-with-multiplicity/)
10. [Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)
11. [Container With Most Water](https://leetcode.com/problems/container-with-most-water/)
12. [Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
13. [Reverse String](https://leetcode.com/problems/reverse-string/)
14. [Reverse Vowels of a String](https://leetcode.com/problems/reverse-vowels-of-a-string/)
15. [Valid Palindrome II](https://leetcode.com/problems/valid-palindrome-ii/)
16. [Reverse Only Letters](https://leetcode.com/problems/reverse-only-letters/)
17. [Bag of Tokens](https://leetcode.com/problems/bag-of-tokens/)
18. [DI String Match](https://leetcode.com/problems/di-string-match/)
19. [Minimum Length of String After Deleting Similar Ends](https://leetcode.com/problems/minimum-length-of-string-after-deleting-similar-ends/)
20. [Sentence Similarity III](https://leetcode.com/problems/sentence-similarity-iii/)
21. [Find K Closest Elements](https://leetcode.com/problems/find-k-closest-elements/)
22. [Shortest Distance to a Character](https://leetcode.com/problems/shortest-distance-to-a-character/)
23. [3Sum Closest](https://leetcode.com/problems/3sum-closest/)
24. [Valid Triangle Number](https://leetcode.com/problems/valid-triangle-number/)

### Same Direction (Chasing/Lagging) Problems
1. [Remove Duplicates from Sorted Array](https://leetcode.com/problems/remove-duplicates-from-sorted-array/)
2. [Move Zeros](https://leetcode.com/problems/move-zeroes/)
3. [Remove Element](https://leetcode.com/problems/remove-element/)
4. [Sort Colors](https://leetcode.com/problems/sort-colors/)
5. [Remove Duplicates from Sorted Array II](https://leetcode.com/problems/remove-duplicates-from-sorted-array-ii/)
6. [Flipping an Image](https://leetcode.com/problems/flipping-an-image/)
7. [Squares of a Sorted Array](https://leetcode.com/problems/squares-of-a-sorted-array/)
8. [Sort Array By Parity](https://leetcode.com/problems/sort-array-by-parity/)
9. [Sort Array By Parity II](https://leetcode.com/problems/sort-array-by-parity-ii/)
10. [Pancake Sorting](https://leetcode.com/problems/pancake-sorting/)
11. [Reverse Prefix of Word](https://leetcode.com/problems/reverse-prefix-of-word/)
12. [Reverse String II](https://leetcode.com/problems/reverse-string-ii/)
13. [Reverse Words in a String](https://leetcode.com/problems/reverse-words-in-a-string/)
14. [Reverse Words in a String III](https://leetcode.com/problems/reverse-words-in-a-string-iii/)
15. [Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/)
16. [Duplicate Zeros](https://leetcode.com/problems/duplicate-zeros/)
17. [Statistics from a Large Sample](https://leetcode.com/problems/statistics-from-a-large-sample/)
18. [Partition Labels](https://leetcode.com/problems/partition-labels/)
19. [Magical String](https://leetcode.com/problems/magical-string/)
20. [Friends Of Appropriate Ages](https://leetcode.com/problems/friends-of-appropriate-ages/)
21. [Longest Mountain in Array](https://leetcode.com/problems/longest-mountain-in-array/)
22. [Shortest Subarray to be Removed to Make Array Sorted](https://leetcode.com/problems/shortest-subarray-to-be-removed-to-make-array-sorted/)
23. [Merge Sorted Array](https://leetcode.com/problems/merge-sorted-array/)
24. [Heaters](https://leetcode.com/problems/heaters/)
25. [Find the Distance Value Between Two Arrays](https://leetcode.com/problems/find-the-distance-value-between-two-arrays/)
26. [Partition List](https://leetcode.com/problems/partition-list/)
27. [Sort List](https://leetcode.com/problems/sort-list/)
28. [Rotating the Box](https://leetcode.com/problems/rotating-the-box/)
29. [Rotate Array](https://leetcode.com/problems/rotate-array/)
30. [String Compression](https://leetcode.com/problems/string-compression/)
31. [Last Substring in Lexicographical Order](https://leetcode.com/problems/last-substring-in-lexicographical-order/)
32. [Shortest Unsorted Continuous Subarray](https://leetcode.com/problems/shortest-unsorted-continuous-subarray/)
33. [Most Profit Assigning Work](https://leetcode.com/problems/most-profit-assigning-work/)
34. [Largest Merge Of Two Strings](https://leetcode.com/problems/largest-merge-of-two-strings/)
35. [Swap Adjacent in LR String](https://leetcode.com/problems/swap-adjacent-in-lr-string/)
36. [Partition Array into Two Arrays to Minimize Sum Difference](https://leetcode.com/problems/partition-array-into-two-arrays-to-minimize-sum-difference/)
37. [Closest Subsequence Sum](https://leetcode.com/problems/closest-subsequence-sum/)
38. [Ways to Split Array into Three Subarrays](https://leetcode.com/problems/ways-to-split-array-into-three-subarrays/)

## 6. Problem Recognition Framework

### Decision Tree for Two Pointers

```
START: Two Pointers Problem?

Q1: What's the data structure?
├─ Array/String → Continue
├─ Linked List → Fast & Slow Pattern (separate guide)
└─ Other → Maybe not two pointers

Q2: Is it sorted (or can be sorted)?
├─ YES → Likely Opposite Direction
└─ NO → Likely Same Direction

Q3: What are we looking for?
├─ Pair/Triple with sum → Opposite Direction
├─ Remove/Move elements → Same Direction
├─ Subarray/Substring → Sliding Window (separate guide)
├─ Cycle/Middle → Fast & Slow (separate guide)
```

### Pattern Recognition Cheat Sheet

| Problem Contains | Pattern | Example |
|-----------------|---------|---------|
| "two numbers that sum" | Opposite Direction | Two Sum II |
| "palindrome" | Opposite Direction | Valid Palindrome |
| "remove duplicates" | Same Direction | Remove Duplicates |
| "move zeros" | Same Direction | Move Zeros |
| "sort 0,1,2" | Same Direction | Sort Colors |

---

## ⚠️ Common Pitfalls

### Pitfall 1: Wrong Pointer Initialization
```cpp
// WRONG: Both pointers at 0
int left = 0, right = 0;

// RIGHT: Depends on pattern
// Opposite: left=0, right=n-1
// Same: left=0, right=1 or left=0, right=0
```

### Pitfall 2: Off-by-One in Loop Conditions
```cpp
// WRONG: May miss last element
while (left < right) { // for opposite direction

// RIGHT: Depends on inclusive/exclusive
while (left <= right) { // if both inclusive
```

### Pitfall 3: Not Updating Pointers Correctly
```cpp
// For opposite direction, move based on sum
if (nums[left] + nums[right] < target) left++;
else right--;

// For same direction, usually move right, conditionally move left
```

### Pitfall 4: Not Handling Edge Cases
- Empty array
- Single element
- All elements same
- No solution exists

---

**Master these two patterns, and you'll solve most array/string optimization problems! 🎯**

