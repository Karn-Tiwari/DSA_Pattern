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

**Your Template Logic:**
- **i**: Left pointer (start of window)
- **j**: Right pointer (end of window)
- **windowSum**: Running sum of current window
- **maxSum**: Tracks maximum sum found

**Flow:**
1. Add `arr[j]` to `windowSum`
2. If window size < k, just increment j
3. If window size == k, update maxSum, remove `arr[i]`, increment both i and j

**Correctness:** Maintains exactly k elements in sum at all times
**Efficiency:** O(n) time, O(1) space

### Example Problem: Maximum Sum Subarray of Size K

**Problem:** Find maximum sum of any contiguous subarray of size k.

**Using Your Template:**
- Replace `maxSum = max(windowSum, maxSum)` with your logic
- Returns the maximum window sum

### Dry Run

```
arr = [1, 4, 2, 10, 23, 3, 1, 0, 20], k = 4

Step 1: j=0, windowSum=1, size=1<4, j=1
Step 2: j=1, windowSum=1+4=5, size=2<4, j=2
Step 3: j=2, windowSum=5+2=7, size=3<4, j=3
Step 4: j=3, windowSum=7+10=17, size=4==4, maxSum=max(INT_MIN,17)=17, windowSum=17-1=16, i=1, j=4
Step 5: j=4, windowSum=16+23=39, size=4==4, maxSum=max(17,39)=39, windowSum=39-4=35, i=2, j=5
... continues similarly ...

Final: maxSum = 39
```

### Fixed Window Problem Examples

#### Problem 2: First Negative Element in Every Window of Size K

**Problem:** Given an array and window size k, print first negative element in every window of size k. If no negative element, print 0.

**Approach:**
- Use a queue to store indices of negative elements
- For each window, the front of queue gives first negative
- Remove elements outside current window

**Solution:**
```cpp
vector<int> firstNegInt(vector<int>& arr, int k) {
        // write code here
        vector<int>ans;
        queue<int>q;
        int i =0, j = 0;
        while(j<arr.size())
        {
            if(arr[j]<0)
            {
                q.push(arr[j]);
            }
            if(j-i+1 == k)
            {
                if(q.empty()==true)
                {
                    ans.push_back(0);
                    i++;
                }
                else
                {
                    if(q.front()==arr[i])
                    {
                        ans.push_back(q.front());
                        q.pop();
                        i++;
                    }else{
                        ans.push_back(q.front());
                        i++;
                    }
                }
                
            }
            j++;
        }
        return ans;
    }
```

**Dry Run:**
```
A = [12, -1, -7, 8, -15, 30, 16, 28], K=3

i=0: A[0]=12>=0, no add, i=0<2, skip
i=1: A[1]=-1<0, q=[1], i=1<2, skip  
i=2: A[2]=-7<0, q=[1,2], i=2>=2, q.front=1, ans=[-1]
i=3: A[3]=8>=0, no add, remove if <=3-3=0, none, i=3>=2, ans=[-1, -1]
i=4: A[4]=-15<0, q=[1,2,4], remove <=4-3=1, remove 1, q=[2,4], i=4>=2, ans=[-1,-1,-7]
... continues

Final: [-1, -1, -7, -15, -15, 0]
```

**Edge Cases:**
- No negatives: All 0s
- All negatives: First negative of each window
- K=1: Each element or 0

#### Problem 3: Maximum of All Subarrays of Size K

**Problem:** Find maximum element in each subarray of size k.

**Approach:**
- Use sliding window to maintain deque of indices in decreasing order
- Front of deque always contains maximum for current window
- Remove elements outside window and smaller than current element

**Solution:**
```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    vector<int> ans;
    if (k > nums.size()) return ans;
    
    deque<int> dq; // stores indices
    int i = 0, j = 0;
    
    while (j < nums.size()) {
        // Remove elements outside window
        while (!dq.empty() && dq.front() <= j - k) dq.pop_front();
        
        // Remove smaller elements from back
        while (!dq.empty() && nums[dq.back()] <= nums[j]) dq.pop_back();
        
        // Add current element index
        dq.push_back(j);
        
        if (j - i + 1 < k) {
            j++;
        } else if (j - i + 1 == k) {
            // Add maximum (front of deque) to answer
            ans.push_back(nums[dq.front()]);
            // Remove left element if it's the front of deque
            if (!dq.empty() && dq.front() == i) dq.pop_front();
            i++;
            j++;
        }
    }
    return ans;
}
```

**Dry Run:**
```
nums = [1,3,-1,-3,5,3,6,7], k=3

j=0: remove <=0-3=-3 none, nums[dq.back() empty] <= nums[0]=1, add 0, dq=[0], size=1<3, j=1
j=1: remove <=1-3=-2 none, nums[0]=1 <= nums[1]=3, pop 0, add 1, dq=[1], size=2<3, j=2
j=2: remove <=2-3=-1 none, nums[1]=3 > nums[2]=-1, no pop, add 2, dq=[1,2], size=3==3, ans=[3], front=1 != i=0, i=1, j=3
j=3: remove <=3-3=0, pop 1 (since 1<=0? wait, 1<=0 is false), nums[2]=-1 <= nums[3]=-3, no pop, add 3, dq=[2,3], size=3==3, ans=[3,3], front=2 != i=1, i=2, j=4
... continues

Final: [3,3,5,5,6,7]
```

**Edge Cases:**
- Increasing array: Each window's last element
- Decreasing array: Each window's first element
- All same: Any element

#### Problem 4: Count Occurrence of Anagrams

**Problem:** Count occurrences of anagrams of string pat in string txt.

**Approach:**
- Use sliding window of size pat.length()
- Maintain frequency arrays for pat and current window
- When frequencies match exactly, it's an anagram

**Solution:**
```cpp
int search(string pat, string txt) {
    int n = txt.length(), m = pat.length();
    if (m > n) return 0;
    
    vector<int> patFreq(26, 0), windowFreq(26, 0);
    for (char c : pat) patFreq[c - 'a']++;
    
    int count = 0;
    int i = 0, j = 0;
    
    while (j < n) {
        windowFreq[txt[j] - 'a']++;
        
        if (j - i + 1 < m) {
            j++;
        } else if (j - i + 1 == m) {
            if (windowFreq == patFreq) count++;
            windowFreq[txt[i] - 'a']--;
            i++;
            j++;
        }
    }
    return count;
}
```

**Dry Run:**
```
txt = "forxxorfxdofr", pat = "for", m=3

patFreq = {'f':1, 'o':1, 'r':1}

j=0: windowFreq={'f':1}, size=1<3, j=1
j=1: windowFreq={'f':1,'o':1}, size=2<3, j=2
j=2: windowFreq={'f':1,'o':1,'r':1}, size=3==3, freqs equal, count=1, windowFreq['f']--, i=1, j=3
j=3: windowFreq={'o':1,'r':1,'x':1}, size=3==3, not equal, windowFreq['o']--, i=2, j=4
... continues

Final: count = 3
```

**Edge Cases:**
- No anagrams: 0
- Multiple same chars: Correct count
- pat longer than txt: 0

#### Problem 5: Check if Any Subarray of Size K has Sum Equal to Target

**Problem:** Check if there exists any subarray of size k with sum equal to target.

**Approach:**
- Use sliding window to maintain sum of current window of size k
- Check if current sum equals target at any point
- Return true if found, false otherwise

**Solution:**
```cpp
bool checkSubarraySum(vector<int>& arr, int k, int target) {
    if (k > arr.size()) return false;
    
    int windowSum = 0;
    int i = 0, j = 0;
    
    while (j < arr.size()) {
        windowSum += arr[j];
        
        if (j - i + 1 < k) {
            j++;
        } else if (j - i + 1 == k) {
            if (windowSum == target) return true;
            windowSum -= arr[i];
            i++;
            j++;
        }
    }
    return false;
}
```

**Dry Run:**
```
arr = [1, 4, 2, 10, 23, 3, 1, 0, 20], k = 4, target = 39

j=0: windowSum=1, size=1<4, j=1
j=1: windowSum=1+4=5, size=2<4, j=2
j=2: windowSum=5+2=7, size=3<4, j=3
j=3: windowSum=7+10=17, size=4==4, 17!=39, windowSum=17-1=16, i=1, j=4
j=4: windowSum=16+23=39, size=4==4, 39==39, return true

Final: true
```

**Edge Cases:**
- Target not found: false
- K > array size: false
- Multiple possible windows: true (any one)

#### Problem 6: Average of All Subarrays of Size K

**Problem:** Find the average of each subarray of size k and return as array.

**Approach:**
- Maintain running sum of current window
- When window size reaches k, calculate average and add to result
- Slide window by removing left element and adding right element

**Solution:**
```cpp
vector<double> findAverages(vector<int>& arr, int k) {
    vector<double> result;
    if (k > arr.size()) return result;
    
    double windowSum = 0;
    int i = 0, j = 0;
    
    while (j < arr.size()) {
        windowSum += arr[j];
        
        if (j - i + 1 < k) {
            j++;
        } else if (j - i + 1 == k) {
            result.push_back(windowSum / k);
            windowSum -= arr[i];
            i++;
            j++;
        }
    }
    return result;
}
```

**Dry Run:**
```
arr = [1, 3, 2, 6, -1, 4, 1, 8, 2], k = 3

j=0: windowSum=1, size=1<3, j=1
j=1: windowSum=1+3=4, size=2<3, j=2
j=2: windowSum=4+2=6, size=3==3, avg=6/3=2.0, result=[2.0], windowSum=6-1=5, i=1, j=3
j=3: windowSum=5+6=11, size=3==3, avg=11/3=3.67, result=[2.0,3.67], windowSum=11-3=8, i=2, j=4
j=4: windowSum=8+(-1)=7, size=3==3, avg=7/3=2.33, result=[2.0,3.67,2.33], windowSum=7-2=5, i=3, j=5
... continues

Final: [2.0, 3.67, 2.33, 3.0, 1.33, 4.33, 3.67]
```

**Edge Cases:**
- K = 1: Each element as average
- K = array size: Single average
- Negative numbers: Correct average calculation

#### Problem 7: Number of Subarrays of Size K with Sum Less Than Target

**Problem:** Count the number of subarrays of size k with sum less than target.

**Approach:**
- Use sliding window to maintain sum of current window
- When window size is k, check if sum < target
- Count valid windows and slide the window

**Solution:**
```cpp
int countSubarrays(vector<int>& arr, int k, int target) {
    if (k > arr.size()) return 0;
    
    int count = 0;
    int windowSum = 0;
    int i = 0, j = 0;
    
    while (j < arr.size()) {
        windowSum += arr[j];
        
        if (j - i + 1 < k) {
            j++;
        } else if (j - i + 1 == k) {
            if (windowSum < target) count++;
            windowSum -= arr[i];
            i++;
            j++;
        }
    }
    return count;
}
```

**Dry Run:**
```
arr = [2, 1, 5, 1, 3, 2], k = 3, target = 8

j=0: windowSum=2, size=1<3, j=1
j=1: windowSum=2+1=3, size=2<3, j=2
j=2: windowSum=3+5=8, size=3==3, 8<8? no, windowSum=8-2=6, i=1, j=3
j=3: windowSum=6+1=7, size=3==3, 7<8? yes, count=1, windowSum=7-1=6, i=2, j=4
j=4: windowSum=6+3=9, size=3==3, 9<8? no, windowSum=9-5=4, i=3, j=5
j=5: windowSum=4+2=6, size=3==3, 6<8? yes, count=2, windowSum=6-1=5, i=4, j=6

Final: count = 2
```

**Edge Cases:**
- No subarrays meet condition: 0
- All subarrays meet condition: Total windows count
- Target very small: 0

#### Problem 8: Find Minimum Sum Subarray of Size K

**Problem:** Find the minimum sum of any contiguous subarray of size k.

**Approach:**
- Use sliding window to maintain sum of current window
- Track minimum sum found when window size is k
- Slide window and update minimum

**Solution:**
```cpp
int minSumSubarray(vector<int>& arr, int k) {
    if (k > arr.size()) return -1; // or handle error
    
    int minSum = INT_MAX;
    int windowSum = 0;
    int i = 0, j = 0;
    
    while (j < arr.size()) {
        windowSum += arr[j];
        
        if (j - i + 1 < k) {
            j++;
        } else if (j - i + 1 == k) {
            minSum = min(minSum, windowSum);
            windowSum -= arr[i];
            i++;
            j++;
        }
    }
    return minSum == INT_MAX ? -1 : minSum;
}
```

**Dry Run:**
```
arr = [10, 4, 2, 5, 6, 3, 8, 1], k = 3

j=0: windowSum=10, size=1<3, j=1
j=1: windowSum=10+4=14, size=2<3, j=2
j=2: windowSum=14+2=16, size=3==3, minSum=min(INT_MAX,16)=16, windowSum=16-10=6, i=1, j=3
j=3: windowSum=6+5=11, size=3==3, minSum=min(16,11)=11, windowSum=11-4=7, i=2, j=4
j=4: windowSum=7+6=13, size=3==3, minSum=min(11,13)=11, windowSum=13-2=11, i=3, j=5
j=5: windowSum=11+3=14, size=3==3, minSum=min(11,14)=11, windowSum=14-5=9, i=4, j=6
j=6: windowSum=9+8=17, size=3==3, minSum=min(11,17)=11, windowSum=17-6=11, i=5, j=7
j=7: windowSum=11+1=12, size=3==3, minSum=min(11,12)=11, windowSum=12-3=9, i=6, j=8

Final: minSum = 11
```

**Edge Cases:**
- All positive numbers: Smallest window sum
- Negative numbers: Could be very small
- K = 1: Smallest element

#### Problem 9: Count Subarrays of Size K with Distinct Elements

**Problem:** Count the number of subarrays of size k that contain all distinct elements.

**Approach:**
- Use sliding window with a set or map to track unique elements
- When window size is k, check if unique count equals k
- Slide window and maintain uniqueness

**Solution:**
```cpp
int countDistinctSubarrays(vector<int>& arr, int k) {
    if (k > arr.size()) return 0;
    
    int count = 0;
    unordered_map<int, int> freq;
    int i = 0, j = 0;
    
    while (j < arr.size()) {
        freq[arr[j]]++;
        
        if (j - i + 1 < k) {
            j++;
        } else if (j - i + 1 == k) {
            if (freq.size() == k) count++;
            freq[arr[i]]--;
            if (freq[arr[i]] == 0) freq.erase(arr[i]);
            i++;
            j++;
        }
    }
    return count;
}
```

**Dry Run:**
```
arr = [1, 2, 1, 3, 4, 2, 3], k = 3

j=0: freq={1:1}, size=1<3, j=1
j=1: freq={1:1,2:1}, size=2<3, j=2
j=2: freq={1:2,2:1}, size=3==3, freq.size=2 !=3, freq[1]--, freq={1:1,2:1}, i=1, j=3
j=3: freq={1:1,2:1,3:1}, size=3==3, freq.size=3==3, count=1, freq[2]--, freq={1:1,3:1}, i=2, j=4
j=4: freq={1:1,3:1,4:1}, size=3==3, freq.size=3==3, count=2, freq[1]--, freq={3:1,4:1}, i=3, j=5
j=5: freq={3:1,4:1,2:1}, size=3==3, freq.size=3==3, count=3, freq[3]--, freq={4:1,2:1}, i=4, j=6
j=6: freq={4:1,2:1,3:1}, size=3==3, freq.size=3==3, count=4, freq[4]--, freq={2:1,3:1}, i=5, j=7

Final: count = 4
```

**Edge Cases:**
- All distinct elements: All windows count
- All same elements: 0
- K = 1: Always count (all single elements are distinct)

#### Problem 10: Maximum Average Subarray of Size K

**Problem:** Find the maximum average of any subarray of size k.

**Approach:**
- Use sliding window to maintain sum of current window
- Track maximum average found when window size is k
- Average = sum / k, so maximum sum gives maximum average

**Solution:**
```cpp
double findMaxAverage(vector<int>& arr, int k) {
    if (k > arr.size()) return 0.0;
    
    double maxAvg = -INFINITY;
    double windowSum = 0;
    int i = 0, j = 0;
    
    while (j < arr.size()) {
        windowSum += arr[j];
        
        if (j - i + 1 < k) {
            j++;
        } else if (j - i + 1 == k) {
            maxAvg = max(maxAvg, windowSum / k);
            windowSum -= arr[i];
            i++;
            j++;
        }
    }
    return maxAvg;
}
```

**Dry Run:**
```
arr = [1, 12, -5, -6, 50, 3], k = 4

j=0: windowSum=1, size=1<4, j=1
j=1: windowSum=1+12=13, size=2<4, j=2
j=2: windowSum=13+(-5)=8, size=3<4, j=3
j=3: windowSum=8+(-6)=2, size=4==4, avg=2/4=0.5, maxAvg=0.5, windowSum=2-1=1, i=1, j=4
j=4: windowSum=1+50=51, size=4==4, avg=51/4=12.75, maxAvg=12.75, windowSum=51-12=39, i=2, j=5
j=5: windowSum=39+3=42, size=4==4, avg=42/4=10.5, maxAvg=12.75, windowSum=42-(-5)=47, i=3, j=6

Final: maxAvg = 12.75
```

**Edge Cases:**
- All negative numbers: Largest (least negative) average
- K = 1: Largest element
- Mixed positive/negative: Correct maximum average

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

### Variable Size Window Problem Examples

#### Problem 1: Largest Subarray of Sum K

**Problem:** Find the length of the largest subarray with sum k.

**Approach:**
- Use variable window, expand right, shrink left when sum > k
- Track maximum length when sum == k

**Solution:**
```cpp
int lenOfLongSubarr(int A[], int N, int K) {
    int left = 0, right = 0, sum = 0, maxLen = 0;
    
    while(right < N) {
        sum += A[right];
        
        while(sum > K && left <= right) {
            sum -= A[left];
            left++;
        }
        
        if(sum == K) {
            maxLen = max(maxLen, right - left + 1);
        }
        
        right++;
    }
    return maxLen;
}
```

**Dry Run:**
```
A = [10, 5, 2, 7, 1, 9], K=15

right=0: sum=10, 10!=15, right=1
right=1: sum=10+5=15, 15==15, maxLen=2, right=2
right=2: sum=15+2=17>15, sum-=10, left=1, sum=7, right=3
right=3: sum=7+7=14!=15, right=4
right=4: sum=14+1=15==15, maxLen=max(2,4)=4, right=5
right=5: sum=15+9=24>15, sum-=5, left=2, sum=19>15, sum-=2, left=3, sum=17>15, sum-=7, left=4, sum=10!=15

Final: maxLen=4 (subarray [5,2,7,1])
```

**Edge Cases:**
- No subarray sums to K: 0
- Multiple subarrays: Largest length
- Negative numbers: More complex, may need different approach

#### Problem 2: Longest Substring with K Unique Characters

**Problem:** Find length of longest substring with exactly K unique characters.

**Approach:**
- Use map to track character frequencies
- Expand right, shrink left when unique chars > K
- Track max length when unique == K

**Solution:**
```cpp
int longestKSubstr(string s, int k) {
    if(k == 0) return 0;
    
    unordered_map<char, int> freq;
    int left = 0, maxLen = -1;
    
    for(int right = 0; right < s.length(); right++) {
        freq[s[right]]++;
        
        while(freq.size() > k && left <= right) {
            freq[s[left]]--;
            if(freq[s[left]] == 0) freq.erase(s[left]);
            left++;
        }
        
        if(freq.size() == k) {
            maxLen = max(maxLen, right - left + 1);
        }
    }
    return maxLen;
}
```

**Dry Run:**
```
s = "aabacbebebe", k=3

right=0: freq={'a':1}, size=1!=3
right=1: freq={'a':2}, size=1!=3
right=2: freq={'a':2,'b':1}, size=2!=3
right=3: freq={'a':2,'b':1,'c':1}, size=3==3, maxLen=4
right=4: freq={'a':2,'b':2,'c':1}, size=3==3, maxLen=5
right=5: freq={'a':2,'b':2,'c':1,'e':1}, size=4>3, shrink, freq={'a':1,'b':2,'c':1,'e':1}, size=4>3, shrink, freq={'b':2,'c':1,'e':1}, size=3==3, maxLen=5
... continues

Final: maxLen=5
```

**Edge Cases:**
- K=0: 0
- K > unique chars: -1
- All same chars: If K=1, whole string

#### Problem 3: Longest Substring Without Repeating Characters

**Problem:** Find length of longest substring without repeating characters.

**Approach:**
- Use set or map to track characters in current window
- Expand right, shrink left when duplicate found
- Track maximum length

**Solution:**
```cpp
int lengthOfLongestSubstring(string s) {
    unordered_set<char> chars;
    int left = 0, maxLen = 0;
    
    for(int right = 0; right < s.length(); right++) {
        while(chars.count(s[right]) && left <= right) {
            chars.erase(s[left]);
            left++;
        }
        chars.insert(s[right]);
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

**Dry Run:**
```
s = "abcabcbb"

right=0: 'a' not in set, insert, maxLen=1
right=1: 'b' not in set, insert, maxLen=2
right=2: 'c' not in set, insert, maxLen=3
right=3: 'a' in set, remove 'a', left=1, insert 'a', maxLen=3
right=4: 'b' in set, remove 'b','c', left=3, insert 'b', maxLen=3
... continues

Final: maxLen=3
```

**Edge Cases:**
- Empty string: 0
- All unique: Length of string
- All same: 1

#### Problem 4: Pick Toys (Maximum Number of Toys with at Most 2 Types)

**Problem:** Find maximum number of toys you can pick with at most 2 different types.

**Approach:**
- Similar to longest substring with at most 2 distinct
- Use sliding window with frequency map
- Track maximum window when distinct <= 2

**Solution:**
```cpp
int totalFruits(vector<int>& fruits) {
    unordered_map<int, int> freq;
    int left = 0, maxFruits = 0;
    
    for(int right = 0; right < fruits.size(); right++) {
        freq[fruits[right]]++;
        
        while(freq.size() > 2 && left <= right) {
            freq[fruits[left]]--;
            if(freq[fruits[left]] == 0) freq.erase(fruits[left]);
            left++;
        }
        
        maxFruits = max(maxFruits, right - left + 1);
    }
    return maxFruits;
}
```

**Dry Run:**
```
fruits = [1,2,1,2,3,2,1]

right=0: freq={1:1}, size=1, maxFruits=1
right=1: freq={1:1,2:1}, size=2, maxFruits=2
right=2: freq={1:2,2:1}, size=2, maxFruits=3
right=3: freq={1:2,2:2}, size=2, maxFruits=4
right=4: freq={1:2,2:2,3:1}, size=3>2, shrink, freq={2:2,3:1}, size=2, maxFruits=4
... continues

Final: maxFruits=4
```

**Edge Cases:**
- All same type: Whole array
- More than 2 types: Maximum window with 2 types

#### Problem 5: Maximum Window Substring

**Problem:** Find minimum window substring that contains all characters of another string.

**Approach:**
- Use two pointers with frequency maps
- Expand right until all characters found
- Shrink left while maintaining all characters
- Track minimum window

**Solution:**
```cpp
string minWindow(string s, string t) {
    unordered_map<char, int> need, window;
    for(char c : t) need[c]++;
    
    int left = 0, right = 0, valid = 0, start = 0, minLen = INT_MAX;
    
    while(right < s.length()) {
        char c = s[right];
        right++;
        
        if(need.count(c)) {
            window[c]++;
            if(window[c] == need[c]) valid++;
        }
        
        while(valid == need.size() && left <= right) {
            if(right - left < minLen) {
                minLen = right - left;
                start = left;
            }
            
            char d = s[left];
            left++;
            
            if(need.count(d)) {
                if(window[d] == need[d]) valid--;
                window[d]--;
            }
        }
    }
    
    return minLen == INT_MAX ? "" : s.substr(start, minLen);
}
```

**Dry Run:**
```
s = "ADOBECODEBANC", t = "ABC"

Build need = {'A':1,'B':1,'C':1}

right moves until valid=3, then shrink left while valid, track min window

Final: "BANC"
```

**Edge Cases:**
- T longer than S: ""
- No valid window: ""
- Multiple possibilities: Smallest window

## 4. Advanced Templates (Shrinkable vs Non-Shrinkable)

### Why Advanced Templates?

Basic sliding window handles most problems, but some require **different shrinking strategies**. The key difference is **WHEN** you shrink the window:

- **Shrinkable**: Shrink immediately when invalid → Track maximum size **during** the process
- **Non-Shrinkable**: Shrink only when necessary → Track maximum size **at the end**

### Shrinkable Template (Keep Window Always Valid)

#### When to Use
Use when you need the **largest valid window encountered** during the sliding process. The window is always kept valid after each expansion.

#### How It Works (Step by Step)
1. **Expand Right**: Add `arr[right]` to your state (sum, frequency, etc.)
2. **Shrink if Invalid**: While window is invalid AND `left ≤ right`, remove `arr[left]` and increment `left`
3. **Update Answer**: After shrinking, update answer with current window size `right - left + 1`
4. **Repeat**: Move `right` to next element

#### Visual Diagram
```
Array: [1, 2, 3, 4, 5]  |  Condition: Sum ≤ 7

Step 1: right=0, add 1, sum=1 ≤7 ✓, ans=max(0,1)=1
Window: [1]

Step 2: right=1, add 2, sum=3 ≤7 ✓, ans=max(1,2)=2
Window: [1,2]

Step 3: right=2, add 3, sum=6 ≤7 ✓, ans=max(2,3)=3
Window: [1,2,3]

Step 4: right=3, add 4, sum=10 >7 ✗
        Shrink: remove 1, sum=9 >7 ✗, remove 2, sum=7 ≤7 ✓
        Window: [3,4], ans=max(3,2)=3
```

#### Template Code
```cpp
int shrinkableTemplate(vector<int>& arr) {
    int left = 0, ans = 0;
    // Initialize state (sum, map, etc.)
    
    for (int right = 0; right < arr.size(); right++) {
        // Add arr[right] to state
        
        // Shrink while invalid
        while (/* invalid condition */ && left <= right) {
            // Remove arr[left] from state
            left++;
        }
        
        // Update answer (window is now valid)
        ans = max(ans, right - left + 1);
    }
    
    return ans;
}
```

#### Example: Longest Substring with At Most K Distinct Characters
```cpp
int lengthOfLongestSubstringKDistinct(string s, int k) {
    unordered_map<char, int> freq;
    int left = 0, ans = 0;
    
    for (int right = 0; right < s.length(); right++) {
        freq[s[right]]++;  // Add character
        
        // Shrink while too many distinct chars
        while (freq.size() > k && left <= right) {
            freq[s[left]]--;
            if (freq[s[left]] == 0) freq.erase(s[left]);
            left++;
        }
        
        // Update answer
        ans = max(ans, right - left + 1);
    }
    
    return ans;
}
```

#### Practice Questions
1. **Q:** In shrinkable template, when does the window become valid again?
   **A:** Immediately after shrinking, before updating the answer.

2. **Q:** What happens if we don't shrink immediately when invalid?
   **A:** The window stays invalid, and we might miss valid sub-windows.

3. **Q:** When would you choose shrinkable over non-shrinkable?
   **A:** When you need the maximum valid window size seen at any point, not just the final one.

### Non-Shrinkable Template (Grow Maximally, Shrink Minimally)

#### When to Use
Use when you want the **largest possible valid window at the end**. Allow the window to grow as much as possible, only shrinking when absolutely necessary.

#### How It Works (Step by Step)
1. **Expand Right**: Add `arr[right]` to your state
2. **Shrink if Invalid**: If window becomes invalid, remove `arr[left]` ONCE and increment `left`
3. **No Answer Update**: Don't update answer during loop - window might be invalid
4. **Final Answer**: Return `arr.size() - left` (final window size from `left` to end)

#### Visual Diagram
```
Array: [1, 2, 3, 4, 5]  |  Condition: Sum ≤ 7

Step 1: right=0, add 1, sum=1 ≤7 ✓, no shrink
Window: [1]

Step 2: right=1, add 2, sum=3 ≤7 ✓, no shrink
Window: [1,2]

Step 3: right=2, add 3, sum=6 ≤7 ✓, no shrink
Window: [1,2,3]

Step 4: right=3, add 4, sum=10 >7 ✗
        Shrink ONCE: remove 1, sum=9, left=1
        Window: [2,3,4], sum=9 >7 (still invalid, but we don't shrink again)

Step 5: right=4, add 5, sum=14 >7 ✗
        Shrink ONCE: remove 2, sum=12, left=2
        Window: [3,4,5], sum=12 >7 (still invalid)

Final: Return 5-2=3 (window [3,4,5] is invalid, but represents max possible)
```

#### Template Code
```cpp
int nonShrinkableTemplate(vector<int>& arr) {
    int left = 0;
    // Initialize state
    
    for (int right = 0; right < arr.size(); right++) {
        // Add arr[right] to state
        
        // Shrink only once if invalid
        if (/* invalid condition */) {
            // Remove arr[left] from state
            left++;
        }
        
        // Don't update answer here
    }
    
    return arr.size() - left;  // Final window size
}
```

#### Example: Frequency of the Most Frequent Element
```cpp
int maxFrequency(vector<int>& nums, int k) {
    sort(nums.begin(), nums.end());
    int left = 0, sum = 0;
    
    for (int right = 0; right < nums.size(); right++) {
        sum += nums[right];
        
        // Operations needed = window_size * rightmost - sum
        if ((right - left + 1) * nums[right] - sum > k) {
            sum -= nums[left];  // Shrink once
            left++;
        }
    }
    
    return nums.size() - left;  // Final window size
}
```

#### Practice Questions
1. **Q:** In non-shrinkable template, how many times do we shrink per right movement?
   **A:** At most once - only when the window becomes invalid.

2. **Q:** Why don't we update the answer inside the loop?
   **A:** Because the window might be invalid at the end of each iteration.

3. **Q:** When is the final window size returned?
   **A:** At the very end, representing the maximum possible valid window we could achieve.

4. **Q:** What's the key difference in shrinking strategy?
   **A:** Shrinkable uses `while` (shrink until valid), Non-shrinkable uses `if` (shrink once).

### Key Differences Summary

| Aspect | Shrinkable Template | Non-Shrinkable Template |
|--------|-------------------|----------------------|
| **Shrinking** | `while` loop - shrink until valid | `if` statement - shrink once |
| **Validity** | Window always valid after shrinking | Window may be invalid at end |
| **Answer Update** | During loop, after shrinking | At the end only |
| **Use Case** | Maximum valid window seen | Maximum possible window |
| **Example** | Longest substring with K distinct | Frequency of most frequent element |

### Common Mistakes
1. **Using wrong template**: Choose based on whether you need "maximum encountered" vs "maximum possible"
2. **Wrong shrinking condition**: `while` vs `if` makes a big difference
3. **Answer timing**: Update during loop for shrinkable, at end for non-shrinkable

### Practice Problems for Advanced Templates

**Shrinkable Template Problems:**
- Longest Substring with At Most K Distinct Characters
- Longest Subarray with Sum ≤ K
- Minimum Window Substring (modified version)

**Non-Shrinkable Template Problems:**
- 1838. Frequency of the Most Frequent Element
- 1493. Longest Subarray of 1's After Deleting One Element
- 713. Subarray Product Less Than K

Master these templates, and you'll handle the most complex sliding window problems! 🎯

## 7. Problem Recognition Framework

### Decision Tree for Sliding Window Problems

```
START: Is it a Sliding Window Problem?

Q1: Does it involve contiguous subarrays/substrings?
├─ YES → Continue
├─ NO → Maybe not sliding window

Q2: Is the window size fixed or variable?
├─ Fixed (k) → Fixed Window Template
├─ Variable → Variable Window Template

Q3: What are we optimizing?
├─ Maximum sum/length → Track max during process
├─ Minimum window → Track min during process
├─ Count valid windows → Count when condition met
├─ All subarrays of size k → Fixed window

Q4: Key indicator words?
├─ "Maximum subarray of size k" → Fixed window
├─ "Longest substring with property" → Variable window
├─ "Minimum window containing all" → Variable window
├─ "All subarrays" → Fixed window
├─ "Sliding window" → Explicitly mentioned!

Q5: What state needs tracking?
├─ Sum only → Simple variables
├─ Frequencies → HashMap/HashSet
├─ Multiple conditions → Custom state object
```

### Pattern Recognition Cheat Sheet

| Problem Contains | Pattern | Template |
|-----------------|---------|----------|
| "maximum sum subarray of size k" | Fixed Window | Basic fixed window |
| "first negative in every window k" | Fixed Window | Queue for negatives |
| "maximum of all subarrays of size k" | Fixed Window | Deque for sliding max |
| "count anagrams" | Fixed Window | Frequency comparison |
| "longest substring with k distinct" | Variable Window | Map size tracking |
| "longest substring without repeating" | Variable Window | Set for uniqueness |
| "minimum window substring" | Variable Window | Two pointers with validity |
| "largest subarray sum <= k" | Variable Window | Sum tracking |
| "pick toys (at most 2 types)" | Variable Window | Type counting |

---

## Key Takeaways & Mental Models

### 1. **Fixed Window = Steady State Machine**
```
Mental Model: Assembly line with fixed conveyor belt
- Window size never changes
- Add new element, remove old element
- Process each position exactly once
- Perfect for: "every subarray of size k"
```

### 2. **Variable Window = Dynamic Boundary**
```
Mental Model: Elastic band stretching and shrinking
- Window grows until condition violated
- Shrinks until condition satisfied again
- Tracks optimal window during expansion
- Perfect for: "longest/shortest with property"
```

### 3. **Advanced Templates = Smart Shrinking Strategy**
```
Shrinkable: Always valid, track max encountered
Non-Shrinkable: Grow maximally, track final size

Choose based on: "Do I need the maximum seen, or maximum possible?"
```

### 4. **Time Complexity Pattern**
```
Fixed Window: O(n) - single pass
Variable Window: O(n) - amortized, each element added/removed once
Advanced: O(n) - same as above

Space: O(1) to O(n) depending on state tracking
```

### 5. **Common State Variables**
```
- Sum: int currentSum
- Frequency: unordered_map<char, int> or vector<int>
- Count: int validCount
- Maximum: deque<int> for sliding window maximum
- Minimum: deque<int> for sliding window minimum
```

---

## Common Pitfalls & How to Avoid

### Pitfall 1: Wrong Window Size Tracking
```cpp
// WRONG: Off-by-one in window size
int windowSize = right - left;  // Missing +1

// RIGHT: Include both boundaries
int windowSize = right - left + 1;
```

### Pitfall 2: Forgetting to Update State on Shrink
```cpp
// WRONG: Only move pointer, forget state
while (condition) {
    left++;  // Forgot to update sum/frequency!
}

// RIGHT: Update state when shrinking
while (condition) {
    sum -= arr[left];
    freq[arr[left]]--;
    left++;
}
```

### Pitfall 3: Using Wrong Template Type
```cpp
// WRONG: Using shrinkable when non-shrinkable needed
// For "maximum frequency after operations"
// Need non-shrinkable to find final maximum window

// RIGHT: Choose based on problem requirement
// "Maximum encountered" → Shrinkable
// "Maximum possible" → Non-Shrinkable
```

### Pitfall 4: Edge Cases in Empty/Invalid Windows
```cpp
// Handle empty windows
if (right - left + 1 == 0) continue;

// Handle invalid states
if (freq.size() > k && k == 0) return 0;
```

### Pitfall 5: Integer Overflow in Sum Calculations
```cpp
// WRONG: May overflow
long long sum = 0;  // Use long long for large arrays
```

### Pitfall 6: Wrong Deque Operations for Sliding Maximum
```cpp
// WRONG: Remove from front without checking
while (!dq.empty() && dq.front() == i - k) dq.pop_front();

// RIGHT: Remove elements outside window
while (!dq.empty() && dq.front() <= i - k) dq.pop_front();
```

---

## Final Thoughts

**The Power of Sliding Window:**
- Transforms O(n²) nested loops into O(n) single pass
- Handles 30% of array/string interview problems
- Once mastered, becomes your default approach for subarray problems

**How to Master Sliding Window:**
1. **Start with Fixed Window** - Easier to understand
2. **Master Variable Window** - Most common in interviews  
3. **Learn Advanced Templates** - For complex optimization
4. **Practice Pattern Recognition** - Identify problems quickly
5. **Focus on State Management** - Correctly add/remove elements

**Remember:** Sliding window isn't just a technique - it's a **mindset**. When you see "subarray" or "substring", think sliding window first!

**Key Insight:** Every sliding window problem can be solved by asking:
- What state do I need to track?
- When do I expand? When do I shrink?
- What do I update at each step?

Master this framework, and you'll solve sliding window problems with confidence! 🚀

---

## General Time and Space Complexity of Sliding Window Patterns

### Time Complexity Analysis

| Pattern Type | Time Complexity | Explanation |
|-------------|----------------|-------------|
| **Fixed Window** | O(n) | Single pass through array |
| **Variable Window** | O(n) | Amortized O(1) per element (each added/removed once) |
| **Advanced Templates** | O(n) | Same as variable window |
| **Sliding Window Maximum** | O(n) | Deque operations are amortized O(1) |

**Why O(n) for all?**
- Each element is processed constant time on average
- Left and right pointers move rightward only
- No backtracking or revisiting elements

### Space Complexity Analysis

| State Tracking | Space Complexity | Examples |
|----------------|------------------|----------|
| **Simple variables** | O(1) | Sum, count, pointers |
| **HashMap/HashSet** | O(min(n, charset)) | Frequency counting, unique elements |
| **Deque** | O(k) | Sliding window maximum (k = window size) |
| **Multiple maps** | O(n) | Complex state tracking |

**Optimization Tips:**
- Use arrays instead of maps for small charsets (26 letters)
- Clear unused map entries to save space
- Use long long for sum calculations to prevent overflow

---

## Practice Leetcode Questions

### Easy Level
1. **Maximum Sum Subarray of Size K** - https://leetcode.com/problems/maximum-sum-subarray-of-size-k/
2. **First Negative in Every Window of Size K** - https://leetcode.com/problems/first-negative-integer-in-every-window-of-size-k/
3. **Count Occurrences of Anagrams** - https://leetcode.com/problems/count-anagrams/
4. **Maximum of All Subarrays of Size K** - https://leetcode.com/problems/sliding-window-maximum/

### Medium Level
1. **Longest Substring with K Distinct Characters** - https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/
2. **Longest Substring Without Repeating Characters** - https://leetcode.com/problems/longest-substring-without-repeating-characters/
3. **Minimum Window Substring** - https://leetcode.com/problems/minimum-window-substring/
4. **Longest Repeating Character Replacement** - https://leetcode.com/problems/longest-repeating-character-replacement/
5. **Subarray Product Less Than K** - https://leetcode.com/problems/subarray-product-less-than-k/
6. **Permutation in String** - https://leetcode.com/problems/permutation-in-string/
7. **Find All Anagrams in a String** - https://leetcode.com/problems/find-all-anagrams-in-a-string/

### Hard Level
1. **Minimum Window Substring** - https://leetcode.com/problems/minimum-window-substring/
2. **Sliding Window Maximum** - https://leetcode.com/problems/sliding-window-maximum/
3. **Substring with Concatenation of All Words** - https://leetcode.com/problems/substring-with-concatenation-of-all-words/
4. **Longest Substring with At Most K Distinct Characters** - https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/
5. **Minimum Size Subarray Sum** - https://leetcode.com/problems/minimum-size-subarray-sum/

### Advanced Practice (Multiple Patterns)
1. **Frequency of the Most Frequent Element** - https://leetcode.com/problems/frequency-of-the-most-frequent-element/
2. **Longest Subarray of 1's After Deleting One Element** - https://leetcode.com/problems/longest-subarray-of-1s-after-deleting-one-element/
3. **Max Consecutive Ones III** - https://leetcode.com/problems/max-consecutive-ones-iii/

**Practice Strategy:**
- Start with fixed window problems
- Move to variable window
- End with advanced templates
- Focus on understanding state management
- Time yourself to build speed

**Master sliding window, and you'll solve array/string problems effortlessly! 🚀**
