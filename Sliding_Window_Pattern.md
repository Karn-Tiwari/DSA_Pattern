# Ultimate Sliding Window Pattern

## Table of Contents
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [Fixed Size Window](#2-fixed-size-window)
3. [Variable Size Window](#3-variable-size-window)
4. [Advanced Templates (Shrinkable vs Non-Shrinkable)](#4-advanced-templates-shrinkable-vs-non-shrinkable)
5. [Universal Substring Template](#5-universal-substring-template)
6. [Problem-Solving Framework](#6-problem-solving-framework)

---

## 1. Core Concept & Philosophy

### What Sliding Window Really Is

**Traditional Understanding (WRONG):**
"Sliding window is just for finding maximum sum subarrays"

**True Understanding (CORRECT):**
"Sliding window maintains a **contiguous subset** of elements that satisfy certain **conditions**, using two pointers to efficiently track and update the window"

### The Generalized Form

```
Maintain a window [left, right] that satisfies condition
Expand right to grow window
Shrink left when condition violated
Track optimal window during process
```

This is the KEY insight that makes sliding window a universal pattern for array/string problems.

### Detailed Explanation

#### 1. **What is a "Window"?**

A window is a **contiguous segment** of the array/string defined by two pointers (left and right). The window represents the current subset we're considering.

**Visual Representation:**
```
Array: [a, b, c, d, e, f, g]
Window:   ^-----^
         left  right
```

#### 2. **Why Two Pointers?**

- **Right pointer**: Expands the window to include more elements
- **Left pointer**: Shrinks the window to exclude elements when conditions are violated
- **Movement**: Right moves rightward, left moves rightward (never left)

#### 3. **The Core Algorithm**

```
Initialize: left = 0, right = 0
While right < size:
    Add A[right] to window state
    While condition violated AND left <= right:
        Remove A[left] from window state
        left++
    Update answer with current window
    right++
```

#### 4. **Types of Sliding Windows**

**Fixed Size Window:**
- Window size is constant (k)
- Move both pointers together
- Used for: maximum sum of size k, etc.

**Variable Size Window:**
- Window size changes based on condition
- Move pointers independently
- Used for: longest substring with property, etc.

#### 5. **Why This Pattern Works**

**Efficiency:** O(n) time complexity - each element is added and removed at most once
**Simplicity:** Reduces complex nested loops to a single pass
**Versatility:** Solves 30% of array/string interview problems

## 2. Fixed Size Window

### Thought Process

**When to Use:** When the problem requires examining **every possible subarray of exactly size k**.

**Key Insight:** Instead of recalculating sum for each window (O(n*k)), maintain a running sum by adding new element and removing old element (O(n)).

**Mental Model:**
```
Window of size k slides from left to right
At each position: add right element, remove left element, update answer
```

### Universal Template

```cpp
int fixedWindowTemplate(vector<int>& arr, int k) {
    // code here
    int i = 0, j = 0;
    int windowSum = 0;
    int maxSum = INT_MIN;
    while(j<arr.size())
    {
        //Calculation
        windowSum +=arr[j];
        
        //Increment window till valid window size reach
        if(j-i+1<k)
        {
            j++;
        }
        //When valid window reached then we will calculate the ans from the Calculation
        else if(j-i+1==k)
        {
            maxSum = max(windowSum, maxSum);
            windowSum -= arr[i];
            i++;
            j++;
        }
    }
    return maxSum;
}
```

### Why This Template Works

**Initialization:** First loop builds the complete sum for the initial window
**Sliding:** Each iteration adds one element and removes one, maintaining O(1) per step
**Correctness:** Window always contains exactly k elements
**Efficiency:** O(n) time, O(1) space

### Example Problem: Maximum Sum Subarray of Size K

**Problem:** Given an array of integers and an integer k, find the maximum sum of any contiguous subarray of size k.

**Approach:**
1. Handle edge case: if n < k, return -1
2. Calculate sum of first k elements
3. Slide window: for each new position, add arr[i], subtract arr[i-k]
4. Track maximum sum

### Dry Run

```
arr = [1, 4, 2, 10, 23, 3, 1, 0, 20], k = 4

Step 1: Initialize first window [0..3]
windowSum = 1 + 4 + 2 + 10 = 17
maxSum = 17

Step 2: i=4, arr[4]=23
windowSum = 17 + 23 - 1 = 39
maxSum = max(17, 39) = 39

Step 3: i=5, arr[5]=3
windowSum = 39 + 3 - 4 = 38
maxSum = max(39, 38) = 39

Step 4: i=6, arr[6]=1
windowSum = 38 + 1 - 2 = 37
maxSum = max(39, 37) = 39

Step 5: i=7, arr[7]=0
windowSum = 37 + 0 - 10 = 27
maxSum = max(39, 27) = 39

Step 6: i=8, arr[8]=20
windowSum = 27 + 20 - 23 = 24
maxSum = max(39, 24) = 39

Final: maxSum = 39
```

### Edge Cases to Focus

1. **n < k**: Return -1 or handle appropriately
2. **n == k**: Only one window, return its sum
3. **All negative numbers**: Still works, returns maximum sum
4. **k = 1**: Returns maximum element
5. **Empty array**: Handle with n == 0 check

## 3. Variable Size Window

### Thought Process

**When to Use:** When the window size is **not fixed** and depends on a condition (longest/shortest valid window).

**Key Insight:** Window grows by moving right pointer, shrinks by moving left pointer when condition violated. Track optimal window during the process.

**Mental Model:**
```
Right pointer expands window
When condition violated, left pointer shrinks until valid
Update answer at each valid window
```

### Universal Template

```cpp
int variableWindowTemplate(vector<int>& arr) {
    int left = 0, maxLen = 0;
    // State variables (sum, count, map, etc.)
    
    for (int right = 0; right < arr.size(); right++) {
        // Add arr[right] to state
        
        // Shrink window while condition violated
        while (conditionViolated() && left <= right) {
            // Remove arr[left] from state
            left++;
        }
        
        // Update answer with current window [left, right]
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

### Why This Template Works

**Expansion:** Right pointer moves right, adding elements to window
**Shrinking:** Left pointer moves right only when needed to maintain validity
**Correctness:** Window always valid after shrinking
**Efficiency:** Each element visited at most twice (once by each pointer)

### Example Problem: Longest Substring with At Most K Distinct Characters

**Problem:** Find the length of the longest substring with at most K distinct characters.

**Approach:**
1. Use a map to track character frequencies
2. Expand right pointer, add characters
3. When distinct chars > K, shrink left until <= K
4. Track maximum length

### Dry Run

```
s = "eceba", k=2

Initialize: left=0, right=0, maxLen=0, map={}

Step 1: right=0, char='e'
map = {'e':1}, distinct=1 <=2 ✓
maxLen = max(0, 0-0+1) = 1

Step 2: right=1, char='c'
map = {'e':1, 'c':1}, distinct=2 <=2 ✓
maxLen = max(1, 1-0+1) = 2

Step 3: right=2, char='e'
map = {'e':2, 'c':1}, distinct=2 <=2 ✓
maxLen = max(2, 2-0+1) = 3

Step 4: right=3, char='b'
map = {'e':2, 'c':1, 'b':1}, distinct=3 >2 ✗
Shrink: left=0, remove 'e', map={'e':1, 'c':1, 'b':1}, distinct=3 >2
Shrink: left=1, remove 'c', map={'e':1, 'b':1}, distinct=2 <=2 ✓
maxLen = max(3, 3-1+1) = 3

Step 5: right=4, char='a'
map = {'e':1, 'b':1, 'a':1}, distinct=3 >2 ✗
Shrink: left=1, remove 'e', map={'b':1, 'a':1}, distinct=2 <=2 ✓
maxLen = max(3, 4-1+1) = 4? Wait, 4-1+1=4, but window is [b,a], length=2
Wait, correction: after shrink, left=2, window="ba", length=4-2+1=3? Wait.

Wait, let's recalculate:
After step 4: left=1, right=3, window="ceb", length=3
Then right=4, add 'a', map={'e':1,'b':1,'a':1}, distinct=3>2
Shrink: remove s[1]='c', map={'e':1,'b':1,'a':1}, still 3>2
Shrink: remove s[2]='e', map={'b':1,'a':1}, distinct=2<=2
left=3, window="ba", length=4-3+1=2
maxLen = max(3,2)=3

Yes, correct.
```

### Edge Cases to Focus

1. **k = 0**: Only empty string, length 0
2. **k >= length**: Whole string
3. **All same characters**: Length of string
4. **All distinct characters**: Min of length and k
5. **Empty string**: Return 0

## 4. Advanced Templates (Shrinkable vs Non-Shrinkable)

### Shrinkable Template (Maximum at Each Step)

**Thought Process:** Keep window valid after each addition by shrinking until valid. Track maximum window size seen.

**Template:**
```cpp
int shrinkableTemplate(vector<int>& arr) {
    int left = 0, ans = 0;
    for (int right = 0; right < arr.size(); right++) {
        // Add arr[right] to state
        while (invalid() && left <= right) {
            // Remove arr[left] from state
            left++;
        }
        ans = max(ans, right - left + 1);
    }
    return ans;
}
```

**Example:** Longest Substring with At Most K Distinct (as above)

### Non-Shrinkable Template (Final Maximum)

**Thought Process:** Allow window to grow maximally, only shrink when invalid. Return final window size.

**Template:**
```cpp
int nonShrinkableTemplate(vector<int>& arr) {
    int left = 0;
    for (int right = 0; right < arr.size(); right++) {
        // Add arr[right] to state
        if (invalid()) {
            // Remove arr[left] from state
            left++;
        }
    }
    return arr.size() - left;  // Final window size
}
```

**Example:** Frequency of Most Frequent Element (as in file)
    map['a'] = 1, size=3 >2 ✗ (invalid)
    Shrink: i=0, remove 'c', map['c']=0 (erase), size=2 <=2 ✓
    ans = max(3, 4-0+1) = 4? Wait, no: after shrink, window is [e,b,a], size=3-0+1=4, but we need to check again
    Wait, actually in code, after inner loop, ans = max(ans, j-i+1) = max(3,4-0+1)=4, but that's wrong!

Wait, correction: In the template, the inner loop runs until valid, then ans is updated.
But in this case, after adding 'a', invalid, shrink once to i=1, window [c,e,b,a], still size=3>2, shrink again to i=2, window [e,b,a], size=3>2, shrink to i=3, window [b,a], size=2<=2 ✓
Then ans = max(3, 4-3+1) = max(3,2)=3

Yes, correct. The dry run shows the process.
```

#### 2. **Non-Shrinkable Template** (Tracks Final Maximum)

**When to Use:** Use when you need to find the **absolute maximum valid window size** at the end. The window grows as much as possible and only shrinks when necessary.

**How it Works:**
- Expand right pointer (`j++`)
- Update state with `A[j]`
- **If invalid**, increment left pointer once (`i++`) and update state
- Window may be invalid at the end of iteration
- Return `j - i` at the end (final window size)

**Key Points:**
- Single for loop
- Window grows when valid, shifts when invalid
- Returns the final maximum window size
- Used for problems like "frequency of the most frequent element"

**Template Code:**
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

**Dry Run Example:** Frequency of the Most Frequent Element
```
Input: [1,2,5], k=5 (sorted: [1,2,5])

Initialize: i=0, j=0, sum=0

Step 1: j=0, A[j]=1, sum=1
    Operations needed: (0-0+1)*1 - 1 = 0 <=5 ✓ (valid)
    No shrink, window [1], size=1

Step 2: j=1, A[j]=2, sum=1+2=3
    Operations: (1-0+1)*2 - 3 = 4-3=1 <=5 ✓ (valid)
    No shrink, window [1,2], size=2

Step 3: j=2, A[j]=5, sum=3+5=8
    Operations: (2-0+1)*5 - 8 = 15-8=7 >5 ✗ (invalid)
    Shrink: sum -= A[0]=1, sum=7, i=1
    Now window [2,5], operations: (2-1+1)*5 - 7 = 10-7=3 <=5 ✓
    Final window size: 2-1+1=2

Return: j-i = 2-1=1? Wait, no: return j-i, but j=3, i=1, 3-1=2, yes.

Wait, in code: return j - i; // j=3, i=1, 3-1=2, but window is [1,2] indices 1 and 2, size 2.

Yes.
```

**Key Difference Summary:**
- **Shrinkable**: Keeps window valid after each addition, tracks max size seen
- **Non-Shrinkable**: Allows window to grow maximally, tracks final size
- Choose based on whether you need the maximum encountered or the final possible size

---

## 5. Universal Substring Template

### Thought Process

**When to Use:** Most substring problems with complex conditions (minimum window, at most k distinct, etc.)

**Key Insight:** Use counter to track validity, two pointers with map for character frequency.

### Universal Template

```cpp
string universalSubstring(string s, string t) {
    vector<int> map(128, 0);
    int counter = 0, begin = 0, end = 0, len = INT_MAX, head = 0;
    
    // Initialize map with t
    for (char c : t) map[c]++;
    
    while (end < s.size()) {
        if (map[s[end++]]-- > 0) counter--;  // Found a char in t
        
        while (counter <= 0) {  // Valid window
            // Update answer for minimum window
            if (end - begin < len) {
                len = end - begin;
                head = begin;
            }
            if (map[s[begin++]]++ >= 0) counter++;  // Make invalid
        }
    }
    
    return len == INT_MAX ? "" : s.substr(head, len);
}
```

### Why This Template Works

**Counter Logic:** Tracks how many characters still needed
**Two While Loops:** Outer expands, inner shrinks when valid
**Map Usage:** Negative values indicate extra occurrences

### Example Problem: Minimum Window Substring

**Problem:** Find minimum window in S containing all characters of T.

**Dry Run:** (As in original file)

### Edge Cases

1. **T longer than S**: Impossible
2. **T empty**: Return empty string
3. **No valid window**: Return empty string
4. **Multiple occurrences**: Counter handles correctly

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

## 6. Problem-Solving Framework

### Step-by-Step Approach

1. **Identify Window Type:** Fixed or variable size?
2. **Define State:** What to track (sum, frequency, count)?
3. **Define Condition:** When is window valid/invalid?
4. **Choose Template:** Based on problem requirements
5. **Handle Edge Cases:** Empty, single element, etc.
6. **Implement Carefully:** Update state correctly

### Common Patterns

- **Fixed Sum:** Track current sum
- **Distinct Elements:** Use map/set
- **No Repeats:** Use map for last seen
- **At Most K:** Track count of violations

### Practice Problems

**Easy:**
- Maximum Sum Subarray of Size K
- Contains Duplicate II

**Medium:**
- Longest Substring Without Repeating Characters
- Minimum Window Substring
- Longest Substring with At Most K Distinct

**Hard:**
- Minimum Window Substring
- Substring with Concatenation of All Words

**Master sliding window, and you'll solve array/string problems effortlessly! 🚀**
