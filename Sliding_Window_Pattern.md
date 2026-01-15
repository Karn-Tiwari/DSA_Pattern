# Sliding Window Pattern

## Ultimate Guide to Sliding Window Technique in DSA

> Master the Sliding Window technique - one of the most powerful patterns for solving array and string problems efficiently.

## 📋 Table of Contents

- [🚀 Quick Start](#-quick-start)
- [🎯 Core Concept](#-core-concept)
- [🔧 Two Types of Sliding Window](#-two-types-of-sliding-window)
- [📖 Templates](#-templates)
- [💡 Problem Examples](#-problem-examples)
- [🎯 Practice Problems](#-practice-problems)
- [⚠️ Common Pitfalls](#️-common-pitfalls)

## 🚀 Quick Start

Sliding Window is used for **contiguous subarray/substring problems** where you need to find:

- Maximum/minimum sum of subarrays
- Longest/shortest substring with certain properties
- Number of subarrays meeting conditions

**Time Complexity:** O(n) - Single pass through the array/string

## 🎯 Core Concept

```
Maintain a "window" [left, right] that satisfies condition
Expand right to grow window
Shrink left when condition violated

[a, b, c, d, e, f, g]
 ^-----^
left  right
      (window)
```

## 🔧 Two Types of Sliding Window

### 1. Fixed Size Window
```
Window size k is fixed
Slide window by moving both pointers

[1, 2, 3, 4, 5], k=3
 [----]          sum = 1+2+3 = 6
    [----]       sum = 2+3+4 = 9
       [----]    sum = 3+4+5 = 12
```

### 2. Variable Size Window
```
Window grows/shrinks based on condition
"Find longest/shortest window satisfying X"

Expand: right++
Shrink: left++
```

## 📖 Templates

### Fixed Window Template

```cpp
int fixedWindowTemplate(vector<int>& arr, int k) {
    int windowSum = 0;
    
    // Build initial window
    for (int i = 0; i < k; i++) {
        windowSum += arr[i];
    }
    
    int maxSum = windowSum;
    
    // Slide the window
    for (int right = k; right < arr.size(); right++) {
        windowSum += arr[right];      // Add new element
        windowSum -= arr[right - k];  // Remove old element
        maxSum = max(maxSum, windowSum);
    }
    
    return maxSum;
}
```

### Variable Window Template

```cpp
int variableWindowTemplate(string s) {
    unordered_map<char, int> window;
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        // Expand window
        window[s[right]]++;
        
        // Shrink window while condition violated
        while (conditionViolated(window)) {
            window[s[left]]--;
            if (window[s[left]] == 0) {
                window.erase(s[left]);
            }
            left++;
        }
        
        // Update answer
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

---

### Problem 1: Maximum Sum Subarray of Size K

**Problem:** Find maximum sum of any contiguous subarray of size k.

**C++ Solution:**

```cpp
int maxSumSubarray(vector<int>& arr, int k) {
    int n = arr.size();
    if (n < k) return -1;
    
    // Calculate sum of first window
    int windowSum = 0;
    for (int i = 0; i < k; i++) {
        windowSum += arr[i];
    }
    
    int maxSum = windowSum;
    
    // Slide the window
    for (int i = k; i < n; i++) {
        windowSum += arr[i] - arr[i - k];
        maxSum = max(maxSum, windowSum);
    }
    
    return maxSum;
}
```

**Dry Run:**

```
arr = [1, 4, 2, 10, 23, 3, 1, 0, 20], k = 4

Step 1: Build first window [0..3]
        [1, 4, 2, 10]
         ^--------^
        windowSum = 1+4+2+10 = 17
        maxSum = 17

Step 2: Slide to [1..4]
        Remove arr[0]=1, Add arr[4]=23
        [4, 2, 10, 23]
         ^---------^
        windowSum = 17 - 1 + 23 = 39
        maxSum = 39

Step 3: Slide to [2..5]
        Remove arr[1]=4, Add arr[5]=3
        [2, 10, 23, 3]
        windowSum = 39 - 4 + 3 = 38
        maxSum = 39 (no change)

... continue ...

Final: maxSum = 39
```

**Time Complexity:** O(n) - Single pass with sliding

**Why Not O(n*k)?** We don't recalculate sum each time!

---

### Problem 2: Longest Substring Without Repeating Characters

**Problem:** Find length of longest substring without repeating characters.

**Key Insight:**
```
"abcabcbb"

Window grows until duplicate found
Then shrink from left until duplicate removed

[a,b,c] → add 'a' → duplicate! → shrink → [b,c,a]
```

**C++ Solution:**

```cpp
int lengthOfLongestSubstring(string s) {
    unordered_map<char, int> charIndex;  // char -> last seen index
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        // If char seen before and within window
        if (charIndex.count(c) && charIndex[c] >= left) {
            left = charIndex[c] + 1;  // Move left past duplicate
        }
        
        charIndex[c] = right;
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**Detailed Dry Run:**

```
s = "abcabcbb"

Step 1: right=0, char='a'
        Window: [a]
        left=0, maxLen=1
        charIndex: {a:0}

Step 2: right=1, char='b'
        Window: [a,b]
        left=0, maxLen=2
        charIndex: {a:0, b:1}

Step 3: right=2, char='c'
        Window: [a,b,c]
        left=0, maxLen=3
        charIndex: {a:0, b:1, c:2}

Step 4: right=3, char='a'
        Duplicate! 'a' at index 0
        left = charIndex['a'] + 1 = 1
        Window: [b,c,a]
        maxLen=3 (no change)
        charIndex: {a:3, b:1, c:2}

Step 5: right=4, char='b'
        Duplicate! 'b' at index 1
        left = charIndex['b'] + 1 = 2
        Window: [c,a,b]
        maxLen=3
        charIndex: {a:3, b:4, c:2}

Step 6: right=5, char='c'
        Duplicate! 'c' at index 2
        left = charIndex['c'] + 1 = 3
        Window: [a,b,c]
        maxLen=3
        charIndex: {a:3, b:4, c:5}

Step 7: right=6, char='b'
        Duplicate! 'b' at index 4
        left = charIndex['b'] + 1 = 5
        Window: [c,b]
        maxLen=3
        charIndex: {a:3, b:6, c:5}

Step 8: right=7, char='b'
        Duplicate! 'b' at index 6
        left = charIndex['b'] + 1 = 7
        Window: [b]
        maxLen=3
        charIndex: {a:3, b:7, c:5}

Final: maxLen = 3 (substring "abc")
```

---

### Problem 3: Minimum Window Substring

**Problem:** Find minimum window in S containing all characters of T.

**Hard Problem - But Pattern is Same!**

**Key Insight:**
```
S = "ADOBECODEBANC", T = "ABC"

Expand right until valid window (contains all T)
Shrink left while still valid
Track minimum window
```

**C++ Solution:**

```cpp
string minWindow(string s, string t) {
    if (s.empty() || t.empty()) return "";
    
    // Count characters in t
    unordered_map<char, int> need;
    for (char c : t) {
        need[c]++;
    }
    
    unordered_map<char, int> window;
    int left = 0, right = 0;
    int valid = 0;  // Number of satisfied characters
    int start = 0, minLen = INT_MAX;
    
    while (right < s.length()) {
        char c = s[right];
        right++;
        
        // Expand window
        if (need.count(c)) {
            window[c]++;
            if (window[c] == need[c]) {
                valid++;
            }
        }
        
        // Shrink window while valid
        while (valid == need.size()) {
            // Update minimum window
            if (right - left < minLen) {
                start = left;
                minLen = right - left;
            }
            
            char d = s[left];
            left++;
            
            if (need.count(d)) {
                if (window[d] == need[d]) {
                    valid--;
                }
                window[d]--;
            }
        }
    }
    
    return minLen == INT_MAX ? "" : s.substr(start, minLen);
}
```

**Dry Run:**

```
S = "ADOBECODEBANC", T = "ABC"
need = {A:1, B:1, C:1}

Step 1-5: Expand until valid
right=0: A → window={A:1}, valid=1
right=1: D → window={A:1,D:1}, valid=1
right=2: O → window={A:1,D:1,O:1}, valid=1
right=3: B → window={A:1,D:1,O:1,B:1}, valid=2
right=4: E → window={A:1,D:1,O:1,B:1,E:1}, valid=2
right=5: C → window={A:1,D:1,O:1,B:1,E:1,C:1}, valid=3 ✓

Now valid! Window = "ADOBEC"

Step 6: Shrink from left
left=0: Remove A
        window={D:1,O:1,B:1,E:1,C:1}, valid=2
        Not valid anymore

Step 7-11: Continue expanding right...

... pattern continues ...

Final: "BANC" (length 4)
```

---

## 🎯 Practice Problems

### Easy
1. [Maximum Sum Subarray of Size K](https://leetcode.com/problems/maximum-sum-subarray-of-size-k/)
2. [Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/)
3. [Find All Anagrams in a String](https://leetcode.com/problems/find-all-anagrams-in-a-string/)

### Medium
1. [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
2. [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)
3. [Longest Repeating Character Replacement](https://leetcode.com/problems/longest-repeating-character-replacement/)
4. [Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)
5. [Number of Subarrays with Bounded Maximum](https://leetcode.com/problems/number-of-subarrays-with-bounded-maximum/)
6. [Find K-th Smallest Pair Distance](https://leetcode.com/problems/find-k-th-smallest-pair-distance/)
7. [Moving Stones Until Consecutive II](https://leetcode.com/problems/moving-stones-until-consecutive-ii/)
8. [Count Pairs of Nodes](https://leetcode.com/problems/count-pairs-of-nodes/)
9. [Count Binary Substrings](https://leetcode.com/problems/count-binary-substrings/)
10. [K-diff Pairs in an Array](https://leetcode.com/problems/k-diff-pairs-in-an-array/)
11. [Implement strStr()](https://leetcode.com/problems/implement-strstr/)
12. [Longest Word in Dictionary through Deleting](https://leetcode.com/problems/longest-word-in-dictionary-through-deleting/)
13. [Long Pressed Name](https://leetcode.com/problems/long-pressed-name/)
14. [Longest Uncommon Subsequence II](https://leetcode.com/problems/longest-uncommon-subsequence-ii/)
15. [Compare Version Numbers](https://leetcode.com/problems/compare-version-numbers/)
16. [Camelcase Matching](https://leetcode.com/problems/camelcase-matching/)
17. [Expressive Words](https://leetcode.com/problems/expressive-words/)

### Hard
1. [Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/)
2. [Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)


## ⚠️ Common Pitfalls

### Pitfall 1: Off-by-One in Window Bounds
```cpp
// WRONG: right - left + 1 might be incorrect
int length = right - left;

// RIGHT: Depends on inclusive/exclusive
int length = right - left + 1;  // If both inclusive
```

### Pitfall 2: Forgetting to Update Window State
```cpp
// When shrinking, update all relevant variables
window[s[left]]--;
if (window[s[left]] == 0) window.erase(s[left]);
left++;
```

### Pitfall 3: Wrong Shrink Condition
```cpp
// Shrink while condition is violated
while (window.size() > k) {  // For k distinct chars
    // shrink
}
```

### Pitfall 4: Not Handling Edge Cases
- Empty string/array
- Window size larger than input
- All elements same
- No valid window exists

---

**Master sliding window, and you'll solve 30% of array/string problems effortlessly! 🚀**
