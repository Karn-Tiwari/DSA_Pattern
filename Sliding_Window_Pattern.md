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

## 🔧 Advanced Sliding Window Templates

### Shrinkable vs Non-Shrinkable Windows

Sliding window problems can be solved using two main template variations:

1. **Shrinkable Template**: Keeps shrinking the left pointer until the window becomes valid again. Used when we want the maximum window size at each step.

2. **Non-Shrinkable Template**: Only increments the left pointer when invalid, allowing the window to grow when valid. Used when we want the final maximum window.

### Template 1: Sliding Window (Shrinkable)

```cpp
int i = 0, j = 0, ans = 0;
for (; j < N; ++j) {
    // CODE: use A[j] to update state which might make the window invalid
    for (; invalid(); ++i) { // when invalid, keep shrinking the left edge until it's valid again
        // CODE: update state using A[i]
    }
    ans = max(ans, j - i + 1); // the window [i, j] is the maximum window we've found thus far
}
return ans;
```

**Key Points:**
- Window remains valid at the end of each outer loop
- Inner loop shrinks left pointer until valid
- Tracks maximum window size found

### Template 2: Sliding Window (Non-Shrinkable)

```cpp
int i = 0, j = 0;
for (; j < N; ++j) {
    // CODE: use A[j] to update state which might make the window invalid
    if (invalid()) { // Increment the left edge ONLY when the window is invalid
        // CODE: update state using A[i]
        ++i;
    }
    // after `++j` in the for loop, this window `[i, j)` of length `j - i` MIGHT be valid.
}
return j - i; // There must be a maximum window of size `j - i`.
```

**Key Points:**
- Single for loop
- Window grows when valid, shifts when invalid
- Returns the final maximum window size

---

## 🔧 Universal Substring Template

This 10-line template can solve most substring problems using a hashmap with two pointers.

### Template Code

```cpp
int findSubstring(string s) {
    vector<int> map(128, 0);
    int counter; // check whether the substring is valid
    int begin = 0, end = 0; // two pointers, one points to tail and one to head
    int d; // the length of substring

    // Initialize the hash map here
    for() { /* initialize the hash map here */ }

    while(end < s.size()) {
        if(map[s[end++]]-- ?){ /* modify counter here */ }

        while(/* counter condition */){ 
            /* update d here if finding minimum */

            // increase begin to make it invalid/valid again
            if(map[s[begin++]]++ ?){ /* modify counter here */ }
        }  

        /* update d here if finding maximum */
    }
    return d;
}
```

### Key Guidelines

- **For Maximum Substring**: Update `d` after the inner `while` loop to ensure the substring is valid
- **For Minimum Substring**: Update `d` inside the inner `while` loop
- **Counter Logic**: 
  - `counter` tracks validity condition
  - Increment when window becomes invalid
  - Decrement when window becomes valid again

### Example: Minimum Window Substring

```cpp
string minWindow(string s, string t) {
    vector<int> map(128, 0);
    for(auto c : t) map[c]++;
    int counter = t.size(), begin = 0, end = 0, d = INT_MAX, head = 0;
    while(end < s.size()) {
        if(map[s[end++]]-- > 0) counter--; // in t
        while(counter == 0) { // valid
            if(end - begin < d) d = end - (head = begin);
            if(map[s[begin++]]++ == 0) counter++; // make it invalid
        }  
    }
    return d == INT_MAX ? "" : s.substr(head, d);
}
```

### Example: Longest Substring with At Most Two Distinct Characters

```cpp
int lengthOfLongestSubstringTwoDistinct(string s) {
    vector<int> map(128, 0);
    int counter = 0, begin = 0, end = 0, d = 0; 
    while(end < s.size()) {
        if(map[s[end++]]++ == 0) counter++;
        while(counter > 2) if(map[s[begin++]]-- == 1) counter--;
        d = max(d, end - begin);
    }
    return d;
}
```

### Example: Longest Substring Without Repeating Characters

```cpp
int lengthOfLongestSubstring(string s) {
    vector<int> map(128, 0);
    int counter = 0, begin = 0, end = 0, d = 0; 
    while(end < s.size()) {
        if(map[s[end++]]++ > 0) counter++; 
        while(counter > 0) if(map[s[begin++]]-- > 1) counter--;
        d = max(d, end - begin); // while valid, update d
    }
    return d;
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

### Problem 4: Longest Substring with At Most K Distinct Characters

**Problem:** Find the length of the longest substring with at most K distinct characters.

**Key Insight:**
```
"eceba", K=2

Window expands until > K distinct chars
Then shrinks from left until <= K distinct

[e,c,e] → 2 distinct ✓
[e,c,e,b] → 3 distinct ✗ → shrink → [c,e,b] → 3 ✗ → [e,b] → 2 ✓
[e,b,a] → 3 ✗ → shrink → [b,a] → 2 ✓
```

**C++ Solution:**

```cpp
int lengthOfLongestSubstringKDistinct(string s, int k) {
    unordered_map<char, int> window;
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        // Expand window
        window[s[right]]++;
        
        // Shrink window while too many distinct chars
        while (window.size() > k) {
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

**Dry Run:**

```
s = "eceba", k=2

Step 1: right=0, char='e'
        Window: [e]
        window={e:1}, size=1 <=2
        maxLen=1

Step 2: right=1, char='c'
        Window: [e,c]
        window={e:1,c:1}, size=2 <=2
        maxLen=2

Step 3: right=2, char='e'
        Window: [e,c,e]
        window={e:2,c:1}, size=2 <=2
        maxLen=3

Step 4: right=3, char='b'
        Window: [e,c,e,b]
        window={e:2,c:1,b:1}, size=3 >2 ✗
        Shrink: remove 'e', window={e:1,c:1,b:1}, size=3 >2
        Shrink: remove 'c', window={e:1,b:1}, size=2 <=2
        Window: [e,b]
        maxLen=3

Step 5: right=4, char='a'
        Window: [e,b,a]
        window={e:1,b:1,a:1}, size=3 >2 ✗
        Shrink: remove 'e', window={b:1,a:1}, size=2 <=2
        Window: [b,a]
        maxLen=3

Final: maxLen = 3 (substring "ece")
```

---

### Problem 5: Minimum Size Subarray Sum

**Problem:** Find the minimum length of a contiguous subarray of which the sum is at least target.

**Key Insight:**
```
nums = [2,3,1,2,4,3], target=7

Expand right until sum >= target
Shrink left while sum still >= target
Track minimum length
```

**C++ Solution:**

```cpp
int minSubArrayLen(int target, vector<int>& nums) {
    int left = 0;
    int minLen = INT_MAX;
    int currentSum = 0;
    
    for (int right = 0; right < nums.size(); right++) {
        // Expand window
        currentSum += nums[right];
        
        // Shrink window while sum >= target
        while (currentSum >= target && left <= right) {
            // Update minimum length
            minLen = min(minLen, right - left + 1);
            
            // Shrink from left
            currentSum -= nums[left];
            left++;
        }
    }
    
    return minLen == INT_MAX ? 0 : minLen;
}
```

**Dry Run:**

```
nums = [2,3,1,2,4,3], target=7

Step 1: right=0, currentSum=2 <7
        Window: [2]

Step 2: right=1, currentSum=2+3=5 <7
        Window: [2,3]

Step 3: right=2, currentSum=5+1=6 <7
        Window: [2,3,1]

Step 4: right=3, currentSum=6+2=8 >=7 ✓
        Shrink: minLen = 4-0+1=4, currentSum=8-2=6, left=1
        Window: [3,1,2]

Step 5: right=4, currentSum=6+4=10 >=7 ✓
        Shrink: minLen = min(4,5-1+1=5)=4, currentSum=10-3=7, left=2
        Shrink: minLen=4, currentSum=7-1=6, left=3
        Window: [2,4]

Step 6: right=5, currentSum=6+3=9 >=7 ✓
        Shrink: minLen=4, currentSum=9-2=7, left=4
        Shrink: minLen=4, currentSum=7-4=3, left=5
        Window: [3]

Final: minLen = 4 (subarray [2,3,1,2])
```

---

### Problem 6: Frequency of the Most Frequent Element

**Problem:** Given an array of integers `nums` and an integer `k`, find the maximum possible frequency of an element after performing at most `k` operations. In each operation, you can increment any element by 1.

**Key Insight:**
```
Sort the array first
Use sliding window to find maximum window where we can make all elements equal to the rightmost element
Operations needed = window_size * rightmost - current_sum
```

**C++ Solution (Shrinkable Template):**

```cpp
class Solution {
public:
    int maxFrequency(vector<int>& A, int k) {
        sort(begin(A), end(A));
        long i = 0, N = A.size(), ans = 1, sum = 0;
        for (int j = 0; j < N; ++j) {
            sum += A[j];
            while ((j - i + 1) * A[j] - sum > k) sum -= A[i++];
            ans = max(ans, j - i + 1);
        }
        return ans;
    }
};
```

**C++ Solution (Non-Shrinkable Template):**

```cpp
class Solution {
public:
    int maxFrequency(vector<int>& A, int k) {
        sort(begin(A), end(A));
        long i = 0, j = 0, N = A.size(), sum = 0;
        for (; j < N; ++j) {
            sum += A[j];
            if ((j - i + 1) * A[j] - sum > k) sum -= A[i++];
        }
        return j - i;
    }
};
```

**Dry Run:**

```
A = [1,2,5], k=5 (sorted: [1,2,5])

Step 1: j=0, sum=1
        (1*1) - 1 = 0 <=5 ✓
        ans=1

Step 2: j=1, sum=1+2=3
        (2*2) - 3 = 1 <=5 ✓
        ans=2

Step 3: j=2, sum=3+5=8
        (3*5) - 8 = 7 >5 ✗
        sum -= A[0]=1, sum=7, i=1
        (2*5) - 7 = 3 <=5 ✓
        ans=2

Final: ans=2 (can make [2,5] both 5 with 3 operations)
```

**Problems Solvable with These Templates:**

- **1838. Frequency of the Most Frequent Element** (as above)
- **1493. Longest Subarray of 1's After Deleting One Element**
- **3. Longest Substring Without Repeating Characters**
- **713. Subarray Product Less Than K**
- **159. Longest Substring with At Most Two Distinct Characters**
- **340. Longest Substring with At Most K Distinct Characters**
- **424. Longest Repeating Character Replacement**
- And many more...

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
5. [Frequency of the Most Frequent Element](https://leetcode.com/problems/frequency-of-the-most-frequent-element/)
6. [Longest Substring with At Most Two Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/)
7. [Subarray Product Less Than K](https://leetcode.com/problems/subarray-product-less-than-k/)

### Hard
1. [Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/) (if not done)
2. [Substring with Concatenation of All Words](https://leetcode.com/problems/substring-with-concatenation-of-all-words/)
3. [Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)
4. [Longest Subarray of 1's After Deleting One Element](https://leetcode.com/problems/longest-subarray-of-1s-after-deleting-one-element/)

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
