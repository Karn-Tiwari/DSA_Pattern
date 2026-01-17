# Ultimate String Patterns Guide - Complete C++ Mastery

## Table of Contents
1. [Core String Concepts](#1-core-string-concepts)
2. [Pattern 1: Hash Map / Frequency Count](#2-pattern-1-hash-map--frequency-count)
3. [Pattern 2: Two Pointers on Strings](#3-pattern-2-two-pointers-on-strings)
4. [Pattern 3: Sliding Window on Strings](#4-pattern-3-sliding-window-on-strings)
5. [Pattern 4: Palindrome Problems](#5-pattern-4-palindrome-problems)
6. [Pattern 5: Substring Search (KMP)](#6-pattern-5-substring-search-kmp)
7. [Pattern 6: String Building & Manipulation](#7-pattern-6-string-building--manipulation)
8. [Pattern 7: String DP](#8-pattern-7-string-dp)
9. [Pattern 8: String Parsing & Validation](#9-pattern-8-string-parsing--validation)
10. [Advanced Patterns](#10-advanced-patterns)

---

## 1. Core String Concepts

### What Makes String Problems Unique?

**Key Differences from Arrays:**
```
1. Immutability (in some languages)
2. Character operations (upper/lower, alphanumeric)
3. Patterns (palindromes, anagrams, subsequences)
4. Multiple representations (string vs character array)
```

### Essential String Operations (C++)

```cpp
// Creation & Basic Operations
string s = "hello";
int len = s.length();        // or s.size()
char ch = s[0];              // Access by index
s += "world";                // Concatenation
s.append("!");               // Append
string sub = s.substr(0, 5); // Substring

// Character Classification
isalpha(ch);    // Is alphabetic?
isdigit(ch);    // Is digit?
isalnum(ch);    // Is alphanumeric?
isspace(ch);    // Is whitespace?
isupper(ch);    // Is uppercase?
islower(ch);    // Is lowercase?

// Character Conversion
tolower(ch);    // Convert to lowercase
toupper(ch);    // Convert to uppercase

// Comparison
s1 == s2;       // Equality
s1.compare(s2); // Compare (-1, 0, 1)

// Finding
s.find("lo");        // Find substring
s.find_first_of("l"); // Find first occurrence
s.rfind("l");        // Find last occurrence

// Modification
s.erase(0, 2);       // Remove characters
s.replace(0, 2, "H"); // Replace characters
reverse(s.begin(), s.end()); // Reverse
```

### Time Complexity Reference

| Operation | Time Complexity |
|-----------|----------------|
| Access s[i] | O(1) |
| Concatenation s1 + s2 | O(n + m) |
| Substring | O(k) where k = length |
| Find/Search | O(n*m) naive, O(n+m) KMP |
| Compare | O(min(n,m)) |
| Reverse | O(n) |

---

## 2. Pattern 1: Hash Map / Frequency Count

### Concept

```
Use hash map to count character frequencies
Solves: Anagrams, character matching, grouping

Key Insight: Two strings are anagrams if they have 
            the same character frequency
```

### Template

```cpp
// Frequency Map Template
unordered_map<char, int> getFrequency(string s) {
    unordered_map<char, int> freq;
    for (char c : s) {
        freq[c]++;
    }
    return freq;
}

// Alternative: Array for lowercase letters only
vector<int> getFrequency(string s) {
    vector<int> freq(26, 0);
    for (char c : s) {
        freq[c - 'a']++;
    }
    return freq;
}
```

---

### Problem 1: Valid Anagram

**Problem:** Check if two strings are anagrams.

**Key Insight:**
```
"anagram" and "nagaram" are anagrams
Both have: a=3, n=1, g=1, r=1, m=1

Method 1: Sort both strings and compare
Method 2: Compare frequency maps
```

**C++ Solution (Hash Map):**

```cpp
bool isAnagram(string s, string t) {
    if (s.length() != t.length()) return false;
    
    unordered_map<char, int> count;
    
    // Count characters in s
    for (char c : s) {
        count[c]++;
    }
    
    // Decrement for characters in t
    for (char c : t) {
        count[c]--;
        if (count[c] < 0) return false;
    }
    
    return true;
}
```

**C++ Solution (Array - Faster):**

```cpp
bool isAnagram(string s, string t) {
    if (s.length() != t.length()) return false;
    
    vector<int> count(26, 0);
    
    for (int i = 0; i < s.length(); i++) {
        count[s[i] - 'a']++;
        count[t[i] - 'a']--;
    }
    
    for (int c : count) {
        if (c != 0) return false;
    }
    
    return true;
}
```

**Dry Run:**

```
s = "anagram", t = "nagaram"

Using array approach:
count = [0, 0, 0, ..., 0] (26 zeros)

Processing pairs:
i=0: s[0]='a', t[0]='n'
     count['a'-'a']++ → count[0] = 1
     count['n'-'a']-- → count[13] = -1
     count = [1, 0, 0, ..., -1, ...]

i=1: s[1]='n', t[1]='a'
     count[13]++ → count[13] = 0
     count[0]-- → count[0] = 0
     count = [0, 0, 0, ..., 0, ...]

... continue for all characters ...

Final: All counts are 0 → TRUE
```

**Time:** O(n), **Space:** O(1) for lowercase letters

---

### Problem 2: Group Anagrams

**Problem:** Group strings that are anagrams of each other.

**Key Insight:**
```
Anagrams have same sorted form OR same frequency pattern

["eat","tea","tan","ate","nat","bat"]
Sorted: "aet", "aet", "ant", "aet", "ant", "abt"
Groups: {aet: [eat,tea,ate], ant: [tan,nat], abt: [bat]}
```

**C++ Solution:**

```cpp
vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> groups;
    
    for (string& s : strs) {
        // Create key by sorting
        string key = s;
        sort(key.begin(), key.end());
        
        // Group by key
        groups[key].push_back(s);
    }
    
    // Extract groups
    vector<vector<string>> result;
    for (auto& [key, group] : groups) {
        result.push_back(group);
    }
    
    return result;
}
```

**Alternative: Frequency-based Key**

```cpp
vector<vector<string>> groupAnagrams(vector<string>& strs) {
    unordered_map<string, vector<string>> groups;
    
    for (string& s : strs) {
        // Create frequency key
        vector<int> count(26, 0);
        for (char c : s) {
            count[c - 'a']++;
        }
        
        // Convert frequency to string key
        string key = "";
        for (int i = 0; i < 26; i++) {
            if (count[i] > 0) {
                key += string(1, 'a' + i) + to_string(count[i]);
            }
        }
        
        groups[key].push_back(s);
    }
    
    vector<vector<string>> result;
    for (auto& [key, group] : groups) {
        result.push_back(group);
    }
    
    return result;
}
```

**Dry Run:**

```
strs = ["eat", "tea", "tan", "ate", "nat", "bat"]

Step 1: s = "eat"
        key = sort("eat") = "aet"
        groups = {"aet": ["eat"]}

Step 2: s = "tea"
        key = "aet"
        groups = {"aet": ["eat", "tea"]}

Step 3: s = "tan"
        key = "ant"
        groups = {"aet": ["eat", "tea"], "ant": ["tan"]}

Step 4: s = "ate"
        key = "aet"
        groups = {"aet": ["eat", "tea", "ate"], "ant": ["tan"]}

Step 5: s = "nat"
        key = "ant"
        groups = {"aet": ["eat", "tea", "ate"], "ant": ["tan", "nat"]}

Step 6: s = "bat"
        key = "abt"
        groups = {"aet": ["eat", "tea", "ate"], 
                  "ant": ["tan", "nat"],
                  "abt": ["bat"]}

Result: [["eat","tea","ate"], ["tan","nat"], ["bat"]]
```

**Time:** O(n * k log k) where k = avg string length
**Space:** O(n * k)

---

### Problem 3: Find All Anagrams in a String

**Problem:** Find all start indices of anagrams of p in s.

**Key Insight:**
```
This is a SLIDING WINDOW problem!
Window size = length of p
Check if window is anagram of p

s = "cbaebabacd", p = "abc"
Windows of size 3:
"cba" → anagram? YES → index 0
"bae" → anagram? NO
"aeb" → anagram? NO
"eba" → anagram? YES → index 6
```

**C++ Solution:**

```cpp
vector<int> findAnagrams(string s, string p) {
    vector<int> result;
    if (s.length() < p.length()) return result;
    
    // Frequency of p
    vector<int> pCount(26, 0);
    for (char c : p) {
        pCount[c - 'a']++;
    }
    
    // Sliding window
    vector<int> windowCount(26, 0);
    int windowSize = p.length();
    
    // Build first window
    for (int i = 0; i < windowSize; i++) {
        windowCount[s[i] - 'a']++;
    }
    
    // Check first window
    if (pCount == windowCount) {
        result.push_back(0);
    }
    
    // Slide window
    for (int i = windowSize; i < s.length(); i++) {
        // Add new character
        windowCount[s[i] - 'a']++;
        // Remove old character
        windowCount[s[i - windowSize] - 'a']--;
        
        // Check if anagram
        if (pCount == windowCount) {
            result.push_back(i - windowSize + 1);
        }
    }
    
    return result;
}
```

**Dry Run:**

```
s = "cbaebabacd", p = "abc"
pCount = [1,1,1,0,0,...,0]  (a=1, b=1, c=1)

Initial window [0,2]: "cba"
windowCount = [1,1,1,0,...,0]
windowCount == pCount? YES → result = [0]

Slide to [1,3]: remove 'c', add 'e'
Window: "bae"
windowCount = [1,1,0,0,1,...,0] (a=1, b=1, e=1)
windowCount == pCount? NO

Slide to [2,4]: remove 'b', add 'b'
Window: "aeb"
windowCount = [1,1,0,0,1,...,0]
windowCount == pCount? NO

... continue ...

Slide to [6,8]: "bac"
windowCount = [1,1,1,0,...,0]
windowCount == pCount? YES → result = [0, 6]

Final: [0, 6]
```

**Time:** O(n), **Space:** O(1)

---

## 3. Pattern 2: Two Pointers on Strings

### Concept

```
Use two pointers to traverse string
Applications:
- Palindrome checking
- Removing characters
- Reversing
- Comparing from both ends
```

### Template

```cpp
// Opposite direction (converging)
bool twoPointerOpposite(string s) {
    int left = 0;
    int right = s.length() - 1;
    
    while (left < right) {
        // Process s[left] and s[right]
        
        if (condition) {
            // Found answer
            return true;
        }
        
        left++;
        right--;
    }
    
    return false;
}

// Same direction
string twoPointerSame(string s) {
    int slow = 0;
    
    for (int fast = 0; fast < s.length(); fast++) {
        if (shouldKeep(s[fast])) {
            s[slow] = s[fast];
            slow++;
        }
    }
    
    return s.substr(0, slow);
}
```

---

### Problem 1: Valid Palindrome

**Problem:** Check if string is palindrome (alphanumeric only, case-insensitive).

**Key Insight:**
```
"A man, a plan, a canal: Panama"
Clean: "amanaplanacanalpanama"
Compare from both ends:
a == a? ✓
m == m? ✓
...
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
        
        // Compare (case-insensitive)
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
        tolower('A') == tolower('a')? YES
        left++, right--

Step 2: left=1 (' '), skip
        left=2 ('m'), right=29 ('m')
        'm' == 'm'? YES

Step 3: left=3 ('a'), right=28 ('a')
        'a' == 'a'? YES

... continue until middle ...

All match → TRUE
```

**Time:** O(n), **Space:** O(1)

---

### Problem 2: Reverse Vowels of a String

**Problem:** Reverse only the vowels in the string.

**Key Insight:**
```
"hello" → "holle"
Vowels: e, o
Swap them: o, e

Two pointers: find vowels from both ends and swap
```

**C++ Solution:**

```cpp
string reverseVowels(string s) {
    auto isVowel = [](char c) {
        c = tolower(c);
        return c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u';
    };
    
    int left = 0;
    int right = s.length() - 1;
    
    while (left < right) {
        // Find vowel from left
        while (left < right && !isVowel(s[left])) {
            left++;
        }
        
        // Find vowel from right
        while (left < right && !isVowel(s[right])) {
            right--;
        }
        
        // Swap vowels
        swap(s[left], s[right]);
        left++;
        right--;
    }
    
    return s;
}
```

**Dry Run:**

```
s = "hello"

Step 1: Find vowels
        left=0, not vowel, left++
        left=1, 'e' is vowel ✓
        right=4, 'o' is vowel ✓
        Swap 'e' and 'o'
        s = "holle"
        left++, right--

Step 2: left=2, 'l' not vowel, left++
        left=3, 'l' not vowel, left++
        left=4, right=3
        left >= right, STOP

Final: "holle"
```

---

## 4. Pattern 3: Sliding Window on Strings

### Concept

```
Maintain a window that satisfies certain condition
Expand right to grow, shrink left when invalid

Perfect for:
- Longest substring without repeating
- Minimum window substring
- Character replacement
```

### Template

```cpp
int slidingWindow(string s) {
    unordered_map<char, int> window;
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        // Expand window
        window[s[right]]++;
        
        // Shrink while invalid
        while (invalid(window)) {
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

### Problem 1: Longest Substring Without Repeating Characters

**Problem:** Find length of longest substring without repeating characters.

**Key Insight:**
```
"abcabcbb"

Window grows until duplicate
Then shrink from left until duplicate removed

[a,b,c] add 'a' → duplicate! shrink → [b,c,a]
```

**C++ Solution:**

```cpp
int lengthOfLongestSubstring(string s) {
    unordered_set<char> window;
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        // Shrink while duplicate exists
        while (window.count(s[right])) {
            window.erase(s[left]);
            left++;
        }
        
        // Add current character
        window.insert(s[right]);
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**Alternative: Using Hash Map (More Efficient)**

```cpp
int lengthOfLongestSubstring(string s) {
    unordered_map<char, int> lastSeen;  // char -> last index
    int left = 0;
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        char c = s[right];
        
        // If char seen and within window, move left
        if (lastSeen.count(c) && lastSeen[c] >= left) {
            left = lastSeen[c] + 1;
        }
        
        lastSeen[c] = right;
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**Detailed Dry Run:**

```
s = "abcabcbb"

Using hash map approach:

Step 1: right=0, char='a'
        Window: [a]
        lastSeen = {a: 0}
        left=0, maxLen=1

Step 2: right=1, char='b'
        Window: [a, b]
        lastSeen = {a: 0, b: 1}
        left=0, maxLen=2

Step 3: right=2, char='c'
        Window: [a, b, c]
        lastSeen = {a: 0, b: 1, c: 2}
        left=0, maxLen=3

Step 4: right=3, char='a'
        'a' last seen at 0, within window [0, 3]
        Move left = lastSeen['a'] + 1 = 1
        Window: [b, c, a]
        lastSeen = {a: 3, b: 1, c: 2}
        left=1, maxLen=3

Step 5: right=4, char='b'
        'b' last seen at 1, at window start
        Move left = lastSeen['b'] + 1 = 2
        Window: [c, a, b]
        lastSeen = {a: 3, b: 4, c: 2}
        left=2, maxLen=3

Step 6: right=5, char='c'
        'c' last seen at 2, at window start
        Move left = lastSeen['c'] + 1 = 3
        Window: [a, b, c]
        lastSeen = {a: 3, b: 4, c: 5}
        left=3, maxLen=3

Step 7: right=6, char='b'
        'b' last seen at 4, within window
        Move left = lastSeen['b'] + 1 = 5
        Window: [c, b]
        lastSeen = {a: 3, b: 6, c: 5}
        left=5, maxLen=3

Step 8: right=7, char='b'
        'b' last seen at 6, at window start
        Move left = lastSeen['b'] + 1 = 7
        Window: [b]
        lastSeen = {a: 3, b: 7, c: 5}
        left=7, maxLen=3

Final: maxLen = 3 (substring "abc")
```

**Time:** O(n), **Space:** O(min(n, m)) where m = charset size

---

### Problem 2: Longest Repeating Character Replacement

**Problem:** Find longest substring with same character after replacing at most k characters.

**Key Insight:**
```
"AABABBA", k=1

Window valid if: windowSize - maxFrequency <= k
maxFrequency = most frequent char in window

[A,A,B]: size=3, maxFreq('A')=2, 3-2=1 ≤ 1 ✓
[A,A,B,A]: size=4, maxFreq('A')=3, 4-3=1 ≤ 1 ✓
[A,A,B,A,B]: size=5, maxFreq('A')=3, 5-3=2 > 1 ✗ shrink!
```

**C++ Solution:**

```cpp
int characterReplacement(string s, int k) {
    vector<int> count(26, 0);
    int left = 0;
    int maxCount = 0;  // Max frequency in current window
    int maxLen = 0;
    
    for (int right = 0; right < s.length(); right++) {
        // Add current character
        count[s[right] - 'A']++;
        maxCount = max(maxCount, count[s[right] - 'A']);
        
        // Shrink if invalid
        int windowSize = right - left + 1;
        if (windowSize - maxCount > k) {
            count[s[left] - 'A']--;
            left++;
        }
        
        maxLen = max(maxLen, right - left + 1);
    }
    
    return maxLen;
}
```

**Dry Run:**

```
s = "AABABBA", k = 1

Step 1: right=0, 'A'
        count['A'] = 1, maxCount = 1
        windowSize = 1, 1-1=0 ≤ 1 ✓
        maxLen = 1

Step 2: right=1, 'A'
        count['A'] = 2, maxCount = 2
        windowSize = 2, 2-2=0 ≤ 1 ✓
        maxLen = 2

Step 3: right=2, 'B'
        count['B'] = 1, maxCount = 2
        windowSize = 3, 3-2=1 ≤ 1 ✓
        maxLen = 3

Step 4: right=3, 'A'
        count['A'] = 3, maxCount = 3
        windowSize = 4, 4-3=1 ≤ 1 ✓
        maxLen = 4

Step 5: right=4, 'B'
        count['B'] = 2, maxCount = 3
        windowSize = 5, 5-3=2 > 1 ✗
        Shrink: count['A']--, left++
        Window: "ABABB"
        windowSize = 4, maxLen = 4

Step 6: right=5, 'B'
        count['B'] = 3, maxCount = 3
        windowSize = 5, 5-3=2 > 1 ✗
        Shrink: count['A']--, left++
        maxLen = 4

Step 7: right=6, 'A'
        count['A'] = 2, maxCount = 3
        windowSize = 5, 5-3=2 > 1 ✗
        Shrink: count['B']--, left++
        maxLen = 4

Final: maxLen = 4
```

**Time:** O(n), **Space:** O(1) (fixed 26 letters)

---

## 5. Pattern 4: Palindrome Problems

### Concept

```
Palindrome: Reads same forward and backward
Methods:
1. Two pointers from ends
2. Expand around center
3. Dynamic Programming
```

### Templates

**Method 1: Two Pointers**
```cpp
bool isPalindrome(string s, int left, int right) {
    while (left < right) {
        if (s[left] != s[right]) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

**Method 2: Expand Around Center**
```cpp
int expandAroundCenter(string s, int left, int right) {
    while (left >= 0 && right < s.length() && s[left] == s[right]) {
        left--;
        right++;
    }
    return right - left - 1;  // Length of palindrome
}
```

---

### Problem 1: Longest Palindromic Substring

**Problem:** Find the longest palindromic substring.

**Key Insight:**
```
"babad"

Every palindrome has a center
Center can be:
- Single character: "aba" (center = 'b')
- Between two chars: "abba" (center between 'b' and 'b')

Try all possible centers: O(n)
Expand around each center: O(n)
Total: O(n²)
```

**C++ Solution:**

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        if (s.empty()) return "";
        
        int start = 0;
        int maxLen = 0;
        
        for (int i = 0; i < s.length(); i++) {
            // Odd length palindrome (single center)
            int len1 = expandAroundCenter(s, i, i);
            
            // Even length palindrome (double center)
            int len2 = expandAroundCenter(s, i, i + 1);
            
            int len = max(len1, len2);
            
            if (len > maxLen) {
                maxLen = len;
                start = i - (len - 1) / 2;
            }
        }
        
        return s.substr(start, maxLen);
    }
    
private:
    int expandAroundCenter(string& s, int left, int right) {
        while (left >= 0 && right < s.length() && s[left] == s[right]) {
            left--;
            right++;
        }
        return right - left - 1;
    }
};
```

**Dry Run:**

```
s = "babad"

i=0, char='b':
  Odd: expandAroundCenter(0, 0)
       s[0]='b' → length=1
  Even: expandAroundCenter(0, 1)
        s[0]='b', s[1]='a' → not equal → length=0
  maxLen=1, start=0, current="b"

i=1, char='a':
  Odd: expandAroundCenter(1, 1)
       s[1]='a' → expand
       s[0]='b', s[2]='b' → equal! expand
       s[-1] out of bounds → stop
       length=3
  Even: expandAroundCenter(1, 2)
        s[1]='a', s[2]='b' → not equal → length=0
  maxLen=3, start=0, current="bab"

i=2, char='b':
  Odd: expandAroundCenter(2, 2)
       s[2]='b' → expand
       s[1]='a', s[3]='a' → equal! expand
       s[0]='b', s[4]='d' → not equal → stop
       length=3
  Even: length=0
  maxLen=3 (no change)

i=3, char='a':
  Odd: expandAroundCenter(3, 3)
       s[3]='a' → expand
       s[2]='b', s[4]='d' → not equal → stop
       length=1
  Even: length=0
  maxLen=3 (no change)

i=4, char='d':
  length=1
  maxLen=3 (no change)

Final: "bab" (or "aba" both valid)
```

**Time:** O(n²), **Space:** O(1)

---

### Problem 2: Palindromic Substrings

**Problem:** Count all palindromic substrings.

**Key Insight:**
```
Same approach as longest palindrome
But count all palindromes instead of finding longest

"abc"
Palindromes: "a", "b", "c" → count = 3

"aaa"
Palindromes: "a", "a", "a", "aa", "aa", "aaa" → count = 6
```

**C++ Solution:**

```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int count = 0;
        
        for (int i = 0; i < s.length(); i++) {
            // Odd length
            count += countPalindromes(s, i, i);
            // Even length
            count += countPalindromes(s, i, i + 1);
        }
        
        return count;
    }
    
private:
    int countPalindromes(string& s, int left, int right) {
        int count = 0;
        
        while (left >= 0 && right < s.length() && s[left] == s[right]) {
            count++;
            left--;
            right++;
        }
        
        return count;
    }
};
```

**Dry Run:**

```
s = "aaa"

i=0:
  Odd (0,0): "a" → count=1
             left=-1, stop → total=1
  Even (0,1): "aa" → count=1
              "aaa" → count=2
              left=-1, stop → total=2
  count = 3

i=1:
  Odd (1,1): "a" → count=1
             "aaa" → count=2
             out of bounds → total=2
  Even (1,2): "aa" → count=1
              right=3, stop → total=1
  count = 3 + 3 = 6

i=2:
  Odd (2,2): "a" → count=1
  Even (2,3): out of bounds
  count = 6 + 1 = 7

Wait, let me recalculate:

i=0:
  Odd: expand(0,0) → "a" → 1
  Even: expand(0,1) → "aa" → 1
  count = 2

i=1:
  Odd: expand(1,1) → "a" → 1
       expand(0,2) → "aaa" → 2 total
  Even: expand(1,2) → "aa" → 1
  count = 2 + 3 = 5

i=2:
  Odd: expand(2,2) → "a" → 1
  Even: out of bounds
  count = 5 + 1 = 6

Final count = 6
Palindromes: "a"(3), "aa"(2), "aaa"(1)
```

---

## 6. Pattern 5: Substring Search (KMP Algorithm)

### Concept

```
Find pattern in text efficiently
Naive: O(n*m)
KMP: O(n + m) using "Longest Prefix Suffix" array

Key: When mismatch, don't start over
Use LPS to know where to resume
```

### KMP Template

```cpp
class KMP {
public:
    // Build LPS (Longest Prefix Suffix) array
    vector<int> buildLPS(string pattern) {
        int m = pattern.length();
        vector<int> lps(m, 0);
        int len = 0;  // Length of previous longest prefix suffix
        int i = 1;
        
        while (i < m) {
            if (pattern[i] == pattern[len]) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        
        return lps;
    }
    
    // Find all occurrences of pattern in text
    vector<int> search(string text, string pattern) {
        vector<int> result;
        vector<int> lps = buildLPS(pattern);
        
        int i = 0;  // Index for text
        int j = 0;  // Index for pattern
        
        while (i < text.length()) {
            if (text[i] == pattern[j]) {
                i++;
                j++;
            }
            
            if (j == pattern.length()) {
                result.push_back(i - j);
                j = lps[j - 1];
            } else if (i < text.length() && text[i] != pattern[j]) {
                if (j != 0) {
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }
        
        return result;
    }
};
```

### Problem: Implement strStr() / Find Needle in Haystack

**C++ Solution:**

```cpp
int strStr(string haystack, string needle) {
    if (needle.empty()) return 0;
    
    // Build LPS array
    int m = needle.length();
    vector<int> lps(m, 0);
    int len = 0;
    
    for (int i = 1; i < m; i++) {
        while (len > 0 && needle[i] != needle[len]) {
            len = lps[len - 1];
        }
        if (needle[i] == needle[len]) {
            len++;
        }
        lps[i] = len;
    }
    
    // Search
    int i = 0, j = 0;
    while (i < haystack.length()) {
        if (haystack[i] == needle[j]) {
            i++;
            j++;
        }
        
        if (j == m) {
            return i - j;  // Found at index i-j
        } else if (i < haystack.length() && haystack[i] != needle[j]) {
            if (j != 0) {
                j = lps[j - 1];
            } else {
                i++;
            }
        }
    }
    
    return -1;
}
```

**Dry Run (LPS Building):**

```
pattern = "ABABC"

Building LPS array:

i=0: lps[0] = 0 (always)

i=1: pattern[1]='B', pattern[0]='A'
     B != A, lps[1] = 0
     lps = [0, 0]

i=2: pattern[2]='A', pattern[0]='A'
     A == A, len++, lps[2] = 1
     lps = [0, 0, 1]

i=3: pattern[3]='B', pattern[1]='B'
     B == B, len++, lps[3] = 2
     lps = [0, 0, 1, 2]

i=4: pattern[4]='C', pattern[2]='A'
     C != A, len = lps[1] = 0
     C != A, lps[4] = 0
     lps = [0, 0, 1, 2, 0]

Final LPS = [0, 0, 1, 2, 0]

Meaning:
Position 0: no prefix-suffix
Position 1: no prefix-suffix
Position 2: "A" is both prefix and suffix
Position 3: "AB" is both prefix and suffix
Position 4: no prefix-suffix
```

---

## 7. Pattern 6: String Building & Manipulation

### Problem 1: String Compression

**Problem:** Compress string using character counts.

**C++ Solution:**

```cpp
int compress(vector<char>& chars) {
    int write = 0;
    int read = 0;
    
    while (read < chars.size()) {
        char currentChar = chars[read];
        int count = 0;
        
        // Count consecutive characters
        while (read < chars.size() && chars[read] == currentChar) {
            read++;
            count++;
        }
        
        // Write character
        chars[write++] = currentChar;
        
        // Write count if > 1
        if (count > 1) {
            string countStr = to_string(count);
            for (char c : countStr) {
                chars[write++] = c;
            }
        }
    }
    
    return write;
}
```

**Dry Run:**

```
chars = ['a','a','b','b','c','c','c']

read=0, write=0:
  currentChar='a', count=2
  write 'a' at 0
  write '2' at 1
  chars = ['a','2','b','b','c','c','c']
  read=2, write=2

read=2, write=2:
  currentChar='b', count=2
  write 'b' at 2
  write '2' at 3
  chars = ['a','2','b','2','c','c','c']
  read=4, write=4

read=4, write=4:
  currentChar='c', count=3
  write 'c' at 4
  write '3' at 5
  chars = ['a','2','b','2','c','3','c']
  read=7, write=6

Return 6
Result: ['a','2','b','2','c','3']
```

---

### Problem 2: Reverse Words in a String

**Problem:** Reverse the order of words.

**C++ Solution:**

```cpp
string reverseWords(string s) {
    // Step 1: Reverse entire string
    reverse(s.begin(), s.end());
    
    // Step 2: Reverse each word
    int n = s.length();
    int start = 0;
    int end = 0;
    int i = 0;
    
    while (i < n) {
        // Skip spaces
        while (i < n && s[i] == ' ') i++;
        
        if (i == n) break;
        
        // Mark word start
        if (end != 0) s[end++] = ' ';
        start = end;
        
        // Copy word
        while (i < n && s[i] != ' ') {
            s[end++] = s[i++];
        }
        
        // Reverse this word
        reverse(s.begin() + start, s.begin() + end);
    }
    
    // Resize to remove extra characters
    s.resize(end);
    return s;
}
```

**Dry Run:**

```
s = "the sky is blue"

Step 1: Reverse entire string
        s = "eulb si yks eht"

Step 2: Reverse each word
        "eulb" → "blue"
        "si" → "is"
        "yks" → "sky"
        "eht" → "the"
        
        s = "blue is sky the"

Final: "blue is sky the"
```

---

## 8. Pattern 7: String DP

### Problem: Longest Common Subsequence

**Problem:** Find length of longest common subsequence.

**C++ Solution:**

```cpp
int longestCommonSubsequence(string text1, string text2) {
    int m = text1.length();
    int n = text2.length();
    
    vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));
    
    for (int i = 1; i <= m; i++) {
        for (int j = 1; j <= n; j++) {
            if (text1[i-1] == text2[j-1]) {
                dp[i][j] = dp[i-1][j-1] + 1;
            } else {
                dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }
    }
    
    return dp[m][n];
}
```

**Dry Run:**

```
text1 = "abcde"
text2 = "ace"

DP Table:
    ""  a  c  e
""   0  0  0  0
a    0  1  1  1
b    0  1  1  1
c    0  1  2  2
d    0  1  2  2
e    0  1  2  3

LCS = "ace", length = 3
```

---

## 9. Pattern 8: String Parsing & Validation

### Problem: Valid Parentheses

**C++ Solution:**

```cpp
bool isValid(string s) {
    stack<char> st;
    unordered_map<char, char> pairs = {
        {')', '('},
        {'}', '{'},
        {']', '['}
    };
    
    for (char c : s) {
        if (pairs.count(c)) {
            // Closing bracket
            if (st.empty() || st.top() != pairs[c]) {
                return false;
            }
            st.pop();
        } else {
            // Opening bracket
            st.push(c);
        }
    }
    
    return st.empty();
}
```

---

## 10. Advanced Patterns

### Pattern: Rabin-Karp (Rolling Hash)

**Used for:** Multiple pattern matching

```cpp
class RabinKarp {
private:
    const int BASE = 256;
    const int MOD = 1e9 + 7;
    
public:
    vector<int> search(string text, string pattern) {
        vector<int> result;
        int m = pattern.length();
        int n = text.length();
        
        if (m > n) return result;
        
        // Calculate hash of pattern
        long long patternHash = 0;
        long long textHash = 0;
        long long h = 1;
        
        // h = BASE^(m-1) % MOD
        for (int i = 0; i < m - 1; i++) {
            h = (h * BASE) % MOD;
        }
        
        // Calculate initial hashes
        for (int i = 0; i < m; i++) {
            patternHash = (BASE * patternHash + pattern[i]) % MOD;
            textHash = (BASE * textHash + text[i]) % MOD;
        }
        
        // Slide pattern over text
        for (int i = 0; i <= n - m; i++) {
            // Check if hashes match
            if (patternHash == textHash) {
                // Verify actual match
                bool match = true;
                for (int j = 0; j < m; j++) {
                    if (text[i + j] != pattern[j]) {
                        match = false;
                        break;
                    }
                }
                if (match) {
                    result.push_back(i);
                }
            }
            
            // Calculate hash for next window
            if (i < n - m) {
                textHash = (BASE * (textHash - text[i] * h) + text[i + m]) % MOD;
                if (textHash < 0) {
                    textHash += MOD;
                }
            }
        }
        
        return result;
    }
};
```

---

## Problem Recognition Framework

### Decision Tree

```
STRING PROBLEM

Q1: What operation?
├─ Finding characters/patterns
│  ├─ Anagram → Hash Map
│  ├─ Palindrome → Two Pointers / Expand Center
│  └─ Pattern matching → KMP / Rabin-Karp
│
├─ Substring/Window
│  ├─ Fixed size → Fixed Sliding Window
│  ├─ Variable size → Variable Sliding Window
│  └─ All substrings → Nested loop / DP
│
├─ Modification
│  ├─ In-place → Two Pointers (Same Direction)
│  ├─ Build new → StringBuilder / Vector
│  └─ Compression → Two Pointers
│
└─ Comparison
   ├─ Two strings → DP (LCS, Edit Distance)
   ├─ Multiple patterns → Trie
   └─ Validation → Stack / State Machine
```

---

## Complete Pattern Summary

| Pattern | When to Use | Time | Space | Example |
|---------|------------|------|-------|---------|
| Hash Map | Anagrams, frequency | O(n) | O(k) | Valid Anagram |
| Two Pointers | Palindrome, in-place | O(n) | O(1) | Valid Palindrome |
| Sliding Window | Substring problems | O(n) | O(k) | Longest Substring |
| Expand Center | Palindromic substrings | O(n²) | O(1) | Longest Palindrome |
| KMP | Pattern matching | O(n+m) | O(m) | strStr() |
| Rabin-Karp | Multiple patterns | O(n+m) | O(1) | Find All Anagrams |
| DP | Subsequence, edit | O(n*m) | O(n*m) | LCS, Edit Distance |
| Stack | Parentheses, parsing | O(n) | O(n) | Valid Parentheses |
| Trie | Prefix matching | O(m) | O(ALPHABET*n) | Word Search II |

---

## Key Takeaways

1. **Pattern Recognition is Key**
   - Most string problems fit into 8-10 patterns
   - Learn to identify pattern from problem statement

2. **Data Structures Matter**
   - Hash Map: O(1) lookup for characters
   - Stack: For nested structures
   - Deque: For sliding window max/min

3. **Common Tricks**
   - Array[26] faster than HashMap for lowercase
   - Two pointers eliminates extra space
   - Sliding window reduces O(n²) to O(n)

4. **Edge Cases**
   - Empty string
   - Single character
   - All same characters
   - Case sensitivity
   - Special characters

---

*Master these patterns and you'll solve any string problem with confidence!*

---

## Problem Recognition Framework

### Decision Tree for String Problems

```
START: Is it a String Problem?

Q1: What type of string manipulation?
├─ Character counting/frequency → Hash Map Pattern
├─ Two pointers traversal → Two Pointers Pattern
├─ Window-based substring → Sliding Window Pattern
├─ Pattern matching → KMP/Z-algorithm
├─ Other → Check specific patterns

Q2: What are we looking for?
├─ Anagrams/permutations → Frequency counting
├─ Palindromes → Two pointers from ends
├─ Longest substring → Sliding window
├─ Substring search → KMP algorithm
├─ Character replacement → Sliding window

Q3: Key constraints and indicators?
├─ "Anagram" → Frequency map comparison
├─ "Palindrome" → Two pointers convergence
├─ "Without repeating" → Set or sliding window
├─ "Minimum window" → Sliding window with validity
├─ "K distinct characters" → Frequency map size
├─ "Case insensitive" → Convert to lowercase

Q4: Time complexity requirements?
├─ O(n) required → Efficient algorithms
├─ O(n²) acceptable → Brute force approaches
├─ O(n*m) acceptable → Multiple string operations
```

### Pattern Recognition Cheat Sheet

| Problem Contains | Primary Pattern | Secondary Pattern |
|-----------------|----------------|-------------------|
| "valid anagram" | Hash Map | Frequency Count |
| "group anagrams" | Hash Map | Sorting |
| "palindrome" | Two Pointers | Character skipping |
| "longest substring without repeating" | Sliding Window | Set tracking |
| "longest substring with k distinct" | Sliding Window | Frequency map |
| "minimum window substring" | Sliding Window | Validity counter |
| "character replacement" | Sliding Window | Frequency tracking |
| "find all anagrams" | Sliding Window | Rolling hash |
| "longest palindromic substring" | Two Pointers | Expansion |
| "string compression" | Two Pointers | In-place modification |

---

## Key Takeaways & Mental Models

### 1. **Hash Map = Character Intelligence**
```
Mental Model: Character frequency detective

Tracks what characters appear and how often
Perfect for: anagrams, permutations, character-based conditions
Fast lookups, frequency comparisons, grouping operations
```

### 2. **Two Pointers = String Scanner**
```
Mental Model: Two fingers scanning from ends or same direction

Opposite ends: palindromes, convergence problems
Same direction: in-place modification, removal operations
Perfect for: validation, cleaning, comparison tasks
```

### 3. **Sliding Window = Substring Explorer**
```
Mental Model: Adjustable frame examining contiguous segments

Grows and shrinks to find optimal substrings
Tracks state within current window boundaries
Perfect for: longest/shortest substring problems
```

### 4. **Time Complexity Pattern**
```
Hash Map Operations: O(n) for building, O(1) for lookups
Two Pointers: O(n) single pass, O(1) space
Sliding Window: O(n) amortized, O(min(n,k)) space
String Building: O(n) time and space
```

### 5. **Space Optimization Strategies**
```
- Use arrays[26] instead of maps for lowercase letters
- Rolling hash for substring comparisons
- In-place modifications to save space
- Bit manipulation for character sets
```

---

## Common Pitfalls & How to Avoid

### Pitfall 1: Case Sensitivity Issues
```cpp
// WRONG: Case sensitive comparison
if (s[i] == t[j])  // 'A' != 'a'

// RIGHT: Convert to same case
if (tolower(s[i]) == tolower(t[j]))
```

### Pitfall 2: Off-by-One in Window Boundaries
```cpp
// WRONG: Wrong substring extraction
string result = s.substr(left, right);  // Missing length calculation

// RIGHT: Calculate length properly
string result = s.substr(left, right - left + 1);
```

### Pitfall 3: Not Handling Empty Strings
```cpp
// WRONG: Crash on empty string
if (s.length() < t.length()) return "";  // Good
// But forgot to handle s.empty() or t.empty() specifically
```

### Pitfall 4: Wrong Frequency Map Updates
```cpp
// WRONG: Not updating frequency correctly
freq[s[i]]--;  // Should be freq[s[i] - 'a'] for arrays

// RIGHT: Correct indexing
vector<int> freq(26, 0);
freq[s[i] - 'a']++;
```

### Pitfall 5: Inefficient String Concatenation
```cpp
// WRONG: O(n²) concatenation
string result = "";
for (char c : s) result += c;  // Quadratic time!

// RIGHT: Use string constructor or reserve
string result(s.begin(), s.end());
```

### Pitfall 6: Not Skipping Invalid Characters
```cpp
// WRONG: Processing all characters
for (char c : s) {
    if (!isalnum(c)) continue;  // Good
    // But forgot to handle in palindrome checks
}
```

---

## Final Thoughts

**The Power of String Patterns:**
- String problems are 40% of coding interviews
- Master these 3 patterns, solve 80% of string problems
- Each pattern has O(n) optimal solutions

**How to Master String Problems:**
1. **Start with Hash Maps** - Character counting and frequency
2. **Master Two Pointers** - Palindromes and in-place operations
3. **Conquer Sliding Window** - Substring optimization problems
4. **Practice Pattern Recognition** - Identify problems quickly
5. **Focus on Edge Cases** - Empty strings, single characters, special chars

**Remember:** String problems aren't about memorizing algorithms - they're about recognizing which tool to use for which job!

**Key Insight:** Every string problem can be solved by asking:
- What characters matter (frequency, order, uniqueness)?
- Do I need to modify the string in-place?
- Am I looking for a substring or whole string property?
- What's the optimal traversal strategy?

Master this framework, and you'll approach any string problem with confidence! 🚀

---

## General Time and Space Complexity of String Patterns

### Time Complexity Analysis

| String Pattern | Time Complexity | Explanation |
|----------------|----------------|-------------|
| **Hash Map/Frequency** | O(n) | Single pass counting/lookup |
| **Two Pointers** | O(n) | Linear traversal from ends or same direction |
| **Sliding Window** | O(n) | Amortized O(1) per character |
| **String Building** | O(n) | Linear construction |
| **Sorting-based** | O(n log n) | Sort + operations |
| **KMP Algorithm** | O(n + m) | Pattern preprocessing + search |

**Why O(n) is Common:**
- Most string problems require examining each character
- Optimal algorithms avoid nested loops
- Single pass solutions dominate

### Space Complexity Analysis

| Pattern Type | Space Complexity | Examples |
|--------------|------------------|----------|
| **Frequency Arrays** | O(1) | vector[26] for letters |
| **Frequency Maps** | O(min(n, charset)) | unordered_map for Unicode |
| **Two Pointers** | O(1) | Only pointers, no extra space |
| **Sliding Window** | O(min(n, k)) | Window state storage |
| **Result Strings** | O(n) | Building new strings |
| **Stack/Queue** | O(n) | Parentheses validation |

**Optimization Tips:**
- Use arrays over maps for ASCII characters
- Prefer in-place modifications when possible
- Reserve string capacity to avoid reallocations
- Use rolling hash for substring comparisons

---

## Practice Leetcode Questions

### Easy Level (Foundation)
1. **Valid Anagram** - https://leetcode.com/problems/valid-anagram/
2. **Valid Palindrome** - https://leetcode.com/problems/valid-palindrome/
3. **Reverse String** - https://leetcode.com/problems/reverse-string/
4. **Reverse Vowels of a String** - https://leetcode.com/problems/reverse-vowels-of-a-string/
5. **Implement strStr()** - https://leetcode.com/problems/implement-strstr/

### Medium Level (Core Patterns)
1. **Group Anagrams** - https://leetcode.com/problems/group-anagrams/
2. **Longest Substring Without Repeating Characters** - https://leetcode.com/problems/longest-substring-without-repeating-characters/
3. **Longest Substring with At Most K Distinct Characters** - https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/
4. **Minimum Window Substring** - https://leetcode.com/problems/minimum-window-substring/
5. **Find All Anagrams in a String** - https://leetcode.com/problems/find-all-anagrams-in-a-string/
6. **Longest Palindromic Substring** - https://leetcode.com/problems/longest-palindromic-substring/
7. **Longest Repeating Character Replacement** - https://leetcode.com/problems/longest-repeating-character-replacement/

### Hard Level (Advanced)
1. **Substring with Concatenation of All Words** - https://leetcode.com/problems/substring-with-concatenation-of-all-words/
2. **Minimum Window Substring** - https://leetcode.com/problems/minimum-window-substring/
3. **Valid Number** - https://leetcode.com/problems/valid-number/
4. **Basic Calculator** - https://leetcode.com/problems/basic-calculator/
5. **Text Justification** - https://leetcode.com/problems/text-justification/

### Pattern-Specific Practice
**Hash Map Focus:**
- Isomorphic Strings - https://leetcode.com/problems/isomorphic-strings/
- Word Pattern - https://leetcode.com/problems/word-pattern/
- Ransom Note - https://leetcode.com/problems/ransom-note/

**Two Pointers Focus:**
- Valid Palindrome II - https://leetcode.com/problems/valid-palindrome-ii/
- Backspace String Compare - https://leetcode.com/problems/backspace-string-compare/
- Merge Strings Alternately - https://leetcode.com/problems/merge-strings-alternately/

**Sliding Window Focus:**
- Permutation in String - https://leetcode.com/problems/permutation-in-string/
- Subarray Product Less Than K - https://leetcode.com/problems/subarray-product-less-than-k/
- Maximum Number of Vowels in a Substring of Given Length - https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/

**Practice Strategy:**
- Start with easy problems to build confidence
- Focus on one pattern at a time
- Practice edge cases: empty strings, single characters, special characters
- Time yourself to build recognition speed
- Review solutions to understand optimal approaches

Master string patterns, and you'll handle text processing problems effortlessly! 🎯