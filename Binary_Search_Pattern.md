# Ultimate Binary Search Template

## Table of Contents
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [The Universal Template](#2-the-universal-template)
3. [Why This Template Works](#3-why-this-template-works)
4. [Mental Model & Pattern Recognition](#4-mental-model--pattern-recognition)
5. [Basic Applications](#5-basic-applications)
6. [Advanced Applications](#6-advanced-applications)
7. [Problem-Solving Framework](#7-problem-solving-framework)

---

## 1. Core Concept & Philosophy

### What Binary Search Really Is

**Traditional Understanding (WRONG):**
"Binary search only works on sorted arrays to find a specific element"

**True Understanding (CORRECT):**
"Binary search finds the **MINIMAL k** such that **condition(k) is TRUE** in a monotonic search space"

### The Generalized Form

```
Minimize k, such that condition(k) is True
```

This is the KEY insight that transforms binary search from a simple algorithm into a powerful problem-solving pattern.

### Detailed Explanation of the True Understanding

Let's break down this revolutionary concept step by step:

#### 1. **What is a "Monotonic Search Space"?**

A monotonic search space means that as you move through the possible values, the condition either:
- **Always stays FALSE until it becomes TRUE and stays TRUE** (non-decreasing)
- **Always stays TRUE until it becomes FALSE and stays FALSE** (non-increasing)

**Example of Monotonicity:**
```
Values: 1, 2, 3, 4, 5, 6, 7, 8
Condition: "Can I eat all bananas in 8 hours at speed k?"

For k=1: Takes 27 hours → FALSE
For k=2: Takes 14 hours → FALSE  
For k=3: Takes 10 hours → FALSE
For k=4: Takes 8 hours → TRUE
For k=5: Takes 7 hours → TRUE
For k=6: Takes 6 hours → TRUE

Once it becomes TRUE (at k=4), it stays TRUE for all larger k.
This is monotonic!
```

#### 2. **What does "MINIMAL k" mean?**

"Minimal k" means the **smallest possible value** that satisfies the condition. It's the first point where the condition flips from FALSE to TRUE.

**Visual Representation:**
```
Search Space: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
Condition:    [F, F, F, T, T, T, T, T, T, T]
                     ^
                     Minimal k = 4
```

#### 3. **Why "condition(k) is TRUE"?**

The condition function is a **boolean function** that defines what "success" means for your problem. It must be:
- **Deterministic**: Same input always gives same output
- **Monotonic**: Once TRUE, stays TRUE (or once FALSE, stays FALSE)
- **Efficient**: Can be computed quickly

**Examples of Condition Functions:**
- For square root: `condition(k) = (k * k > x)` → Find minimal k where k² > x
- For ship capacity: `condition(capacity) = canShipInDDays(capacity)` → Find minimal capacity that works
- For eating speed: `condition(speed) = canFinishInHours(speed)` → Find minimal speed that works

#### 4. **How Binary Search Finds This Minimal k**

The algorithm maintains two pointers (left and right) and repeatedly:
1. **Calculates mid** = (left + right) / 2
2. **Checks condition(mid)**
3. **If TRUE**: The answer might be mid or smaller, so search left half (right = mid)
4. **If FALSE**: The answer must be larger, so search right half (left = mid + 1)

**Why this works:**
- When condition(mid) is TRUE, we don't discard mid because it might be the minimal k
- When condition(mid) is FALSE, we discard mid and everything smaller because they can't be the answer
- Eventually, left converges to the minimal k

#### 6. **Why This Changes Everything**

**Old Way (Traditional Binary Search):**
- Only works on sorted arrays
- Only finds exact matches or insertion points
- Limited to array searching

**New Way (Generalized Binary Search):**
- Works on ANY monotonic search space
- Can solve optimization problems
- Can find minimum/maximum satisfying conditions
- Can solve "feasibility" problems

**Problems this enables:**
- Find minimum speed to eat bananas
- Find minimum capacity to ship packages
- Find minimum days to make bouquets
- Find kth smallest element
- And hundreds more!

#### 7. **The Mathematical Foundation**

This is essentially finding the **lower bound** in a monotonic function:

```
f(k) is monotonic (non-decreasing)
We want: min {k | f(k) = TRUE}
```

Binary search efficiently finds this boundary by exploiting the monotonicity property.

#### 8. **Common Mistakes to Avoid**

1. **Non-monotonic conditions**: If your condition can be TRUE, FALSE, TRUE again, binary search won't work
2. **Wrong search bounds**: Must include ALL possible answers
3. **Off-by-one in return value**: Usually return `left`, sometimes `left-1`
4. **Integer overflow**: Use `left + (right - left) / 2` instead of `(left + right) / 2`

This understanding transforms binary search from a "search algorithm" into a "decision algorithm" that can solve any problem with a monotonic decision boundary.

---

## 2. The Universal Template

### C++ Implementation

```cpp
int binarySearch(vector<int>& nums) {
    int left = // minimum possible answer
    int right = // maximum possible answer
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (condition(mid)) {
            right = mid;      // condition is satisfied, try smaller
        } else {
            left = mid + 1;   // condition not satisfied, must go larger
        }
    }
    
    return left;  // left is the minimal k satisfying condition
}
```

### Three Critical Components

**1. Initialize Boundaries (left and right)**
- Rule: Include ALL possible elements in the search space
- Think: "What's the smallest possible answer? What's the largest?"

**2. Design the Condition Function**
- This is where 90% of the problem-solving happens
- Returns TRUE/FALSE based on whether current value satisfies requirement
- Must be monotonic: if condition(k) is True, then condition(k+1) is also True

**3. Return Value**
- Usually `return left` (minimal k satisfying condition)
- Sometimes `return left - 1` (maximal k NOT satisfying condition)

---

## 3. Why This Template Works

### The Loop Invariant

At every iteration:
- Everything in [left, right) is a potential answer
- Everything before left does NOT satisfy condition
- Everything at right and after DOES satisfy condition

```
Not Satisfied | Boundary | Satisfied
[....0000000] [????????] [11111111....]
             left       right
```

### Why `left < right` (not `left <= right`)?

```
When left == right:
[0000000][X]
        left
        right
```
We've converged! No need to check further.

### Why `right = mid` and `left = mid + 1`?

**Case 1: condition(mid) is TRUE**
```
[00000][M][111111]
      mid
```
- mid might be the answer (it satisfies condition)
- We keep mid by doing `right = mid`
- We search left half: [left, mid]

**Case 2: condition(mid) is FALSE**
```
[00000][M][??????]
      mid
```
- mid is NOT the answer
- We discard mid by doing `left = mid + 1`
- We search right half: [mid+1, right]

---

## 4. Mental Model & Pattern Recognition

### When to Use Binary Search?

Ask yourself these questions:

1. **Is there a search space?**
   - Array indices? Range of values? Time? Capacity?

2. **Is there monotonicity?**
   - If x works, does x+1 also work? (or vice versa)
   - Can I draw a line separating "yes" from "no"?

3. **Am I looking for a boundary?**
   - First/last position?
   - Minimum/maximum value satisfying condition?

### Visual Pattern Recognition

```
Problem Pattern 1: Find First Valid
[FFFFFFF][TTTTTTT]
        ^
        answer (first T)

Problem Pattern 2: Find Last Valid  
[TTTTTTT][FFFFFFF]
       ^
       answer (last T)
       
Problem Pattern 3: Minimize while satisfying
Values:  [1  2  3  4  5  6  7  8]
Valid?   [N  N  N  N  Y  Y  Y  Y]
                    ^
                    minimum valid = 5
```

---

## 5. Basic Applications

### Example 1: Search Insert Position

**Problem:** Given sorted array, find the index where target should be inserted.

**Approach:**

**Key Insights:**
- **Naive Approach:** Linear search through the array to find the insertion point, O(n) time.
- **Optimized Approach:** Use binary search to find the minimal index k where nums[k] >= target. The search space is monotonic: for smaller indices, nums[k] may be < target, but once >= target, all larger indices also satisfy (since sorted). This finds the leftmost position for insertion.

**Step-by-Step Approach:**
1. **Initialization:** left = 0, right = nums.size() (inclusive upper bound to allow insertion at end).
2. **Loop:** While left < right, compute mid = left + (right - left)/2.
3. **Check Condition:** If nums[mid] >= target, the condition is satisfied, so search left half (right = mid) to find smaller index.
4. **Else:** nums[mid] < target, search right half (left = mid + 1).
5. **Termination:** When left == right, that's the insertion index.
6. **Edge Cases:** Target smaller than all (insert at 0), larger than all (insert at end), target exists (insert at existing position).

**Algorithm Flow (Pseudocode):**
```
left = 0, right = n
While left < right:
    mid = left + (right - left) / 2
    If nums[mid] >= target:
        right = mid  // try smaller index
    Else:
        left = mid + 1  // too small, go right
Return left
```

**Why It Works:**
- **Time Complexity:** O(log n) as we halve the search space each time.
- **Space Complexity:** O(1) extra space.
- The monotonicity ensures the boundary is found correctly; left converges to the first index where nums[left] >= target.

**C++ Solution:**

```cpp
int searchInsert(vector<int>& nums, int target) {
    int left = 0;
    int right = nums.size(); // Note: size(), not size()-1
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        if (nums[mid] >= target) {
            right = mid;  // Found one, try smaller index
        } else {
            left = mid + 1;  // Too small, go right
        }
    }
    
    return left;
}
```

**Dry Run:**

```
nums = [1, 3, 5, 6], target = 5

Initial: left=0, right=4
        [1, 3, 5, 6]
         ^        ^
       left     right

Step 1: mid = 0+(4-0)/2 = 2
        nums[2] = 5 >= 5? YES
        right = mid = 2
        [1, 3, 5, 6]
         ^     ^
       left  right

Step 2: mid = 0+(2-0)/2 = 1
        nums[1] = 3 >= 5? NO
        left = mid + 1 = 2
        [1, 3, 5, 6]
              ^
            left
            right

Loop ends (left == right)
Return left = 2
```

**Why right = nums.size() not nums.size()-1?**

Consider target = 7:
```
[1, 3, 5, 6] | target = 7
             ^
             Should insert here at index 4

If right = size()-1 = 3, we can't return 4!
If right = size() = 4, we can return 4 ✓
```

---

### Example 2: Sqrt(x)

**Problem:** Find integer square root (floor of sqrt).   

**Approach:**

**Key Insights:**
- **Naive Approach:** Check integers from 1 to sqrt(x) or use linear search, but inefficient for large x.
- **Optimized Approach:** Binary search to find the maximum k where k*k <= x (floor of square root). The condition k*k <= x is monotonic: for small k, false; for large k, true. We find the rightmost k where condition holds.

**Step-by-Step Approach:**
1. **Initialization:** If x == 0, return 0. left = 1, right = x (since sqrt(x) <= x for x >= 1).
2. **Loop:** While left < right, mid = left + (right - left)/2.
3. **Check Condition:** If mid > x/mid (to avoid overflow, equivalent to mid*mid > x), then mid is too big, right = mid.
4. **Else:** mid*mid <= x, so left = mid + 1 (try larger).
5. **Termination:** left == right, return left - 1 (since loop finds first k where k*k > x, so k-1 is floor sqrt).
6. **Edge Cases:** x=0 (0), x=1 (1), large x (up to 2^31-1).

**Algorithm Flow (Pseudocode):**
```
If x == 0: return 0
left = 1, right = x
While left < right:
    mid = left + (right - left) / 2
    If mid > x / mid:  // mid*mid > x
        right = mid
    Else:
        left = mid + 1
Return left - 1  // max k where k*k <= x
```

**Why It Works:**
- **Time Complexity:** O(log x) as search space halves each iteration.
- **Space Complexity:** O(1).
- Exploits monotonicity of k*k vs x; mid > x/mid avoids integer overflow in comparison.

**C++ Solution:**

```cpp
int mySqrt(int x) {
    if (x == 0) return 0;
    
    int left = 1;
    int right = x;
    
    while (left < right) {
        int mid = left + (right - left) / 2;
        
        // Condition: mid > x/mid means mid² > x
        if (mid > x / mid) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    
    return left - 1;  // Return maximum k where k² <= x
}
```

**Dry Run:**

```
x = 8

Initial: left=1, right=8
        [1, 2, 3, 4, 5, 6, 7, 8]
         ^                    ^
       left                 right

Step 1: mid = 1+(8-1)/2 = 4
        4 > 8/4? 4 > 2? YES
        right = 4
        [1, 2, 3, 4]
         ^        ^

Step 2: mid = 1+(4-1)/2 = 2
        2 > 8/2? 2 > 4? NO
        left = 3
        [1, 2, 3, 4]
               ^  ^

Step 3: mid = 3+(4-3)/2 = 3
        3 > 8/3? 3 > 2? YES
        right = 3
        [1, 2, 3]
               ^
             left
             right

Loop ends
Return left - 1 = 3 - 1 = 2
Answer: 2 (because 2² = 4 ≤ 8 < 3² = 9)
```

---

## 6. Advanced Applications

### Example 3: Capacity To Ship Packages Within D Days

**Problem:** 
```
weights = [1,2,3,4,5,6,7,8,9,10]
D = 5 days

Find MINIMUM ship capacity to ship all packages in D days.
```

**Approach:**

**Key Insights:**
- **Naive Approach:** Try all possible capacities from max weight to total sum, check each if feasible in D days, O(sum * n) time.
- **Optimized Approach:** Binary search on capacity. The minimum capacity is monotonic: if a capacity works, all larger work; if not, all smaller fail. Find minimal capacity where we can ship all packages in <= D days.

**Step-by-Step Approach:**
1. **Initialization:** left = max(weights), right = sum(weights).
2. **Loop:** While left < right, mid = left + (right - left)/2.
3. **Check Condition:** If feasible(mid) (can ship in <= D days), right = mid (try smaller capacity).
4. **Else:** left = mid + 1 (need larger capacity).
5. **Feasible Function:** Simulate shipping: track current load and days used, increment days when load exceeds capacity.
6. **Edge Cases:** D = 1 (capacity = sum), D = n (capacity = max), single package.

**Algorithm Flow (Pseudocode):**
```
left = max(weights), right = sum(weights)
While left < right:
    mid = left + (right - left) / 2
    If feasible(mid):  // can ship in <= D days
        right = mid
    Else:
        left = mid + 1
Return left

feasible(capacity):
    days_used = 1, current = 0
    For each weight:
        If current + weight > capacity:
            days_used++
            current = weight
        Else:
            current += weight
    Return days_used <= D
```

**Why It Works:**
- **Time Complexity:** O(n log sum) where sum is total weight.
- **Space Complexity:** O(1) extra space.
- Monotonicity ensures binary search finds the minimal feasible capacity.

**C++ Solution:**

```cpp
class Solution {
public:
    int shipWithinDays(vector<int>& weights, int days) {
        int left = *max_element(weights.begin(), weights.end());
        int right = accumulate(weights.begin(), weights.end(), 0);
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            
            if (feasible(weights, days, mid)) {
                right = mid;  // Can do it, try smaller capacity
            } else {
                left = mid + 1;  // Can't do it, need bigger capacity
            }
        }
        
        return left;
    }
    
private:
    bool feasible(vector<int>& weights, int D, int capacity) {
        int daysNeeded = 1;
        int currentLoad = 0;
        
        for (int weight : weights) {
            if (currentLoad + weight > capacity) {
                daysNeeded++;
                currentLoad = 0;
            }
            currentLoad += weight;
        }
        
        return daysNeeded <= D;
    }
};
```

**Detailed Dry Run:**

```
weights = [1,2,3,4,5,6,7,8,9,10], D = 5

Initial: left=10 (max weight), right=55 (sum)

===== ITERATION 1 =====
mid = 10 + (55-10)/2 = 32

feasible(capacity=32, days=5)?
  Day 1: [1,2,3,4,5,6,7,8] = 36 > 32? NO, [1,2,3,4,5,6,7] = 28 ✓
  Day 2: [8,9,10] = 27 ✓
  Days needed = 2 <= 5? YES ✓

Result: feasible=TRUE, right=32
        [10......32......55]
         ^       ^
       left    right

===== ITERATION 2 =====
mid = 10 + (32-10)/2 = 21

feasible(capacity=21, days=5)?
  Day 1: [1,2,3,4,5,6] = 21 ✓
  Day 2: [7,8] = 15 ✓
  Day 3: [9,10] = 19 ✓
  Days needed = 3 <= 5? YES ✓

Result: feasible=TRUE, right=21
        [10....21]
         ^     ^

===== ITERATION 3 =====
mid = 10 + (21-10)/2 = 15

feasible(capacity=15, days=5)?
  Day 1: [1,2,3,4,5] = 15 ✓
  Day 2: [6,7] = 13 ✓
  Day 3: [8] = 8 ✓
  Day 4: [9] = 9 ✓
  Day 5: [10] = 10 ✓
  Days needed = 5 <= 5? YES ✓

Result: feasible=TRUE, right=15
        [10..15]
         ^   ^

===== ITERATION 4 =====
mid = 10 + (15-10)/2 = 12

feasible(capacity=12, days=5)?
  Day 1: [1,2,3,4] = 10 ✓ + 5? 15 > 12, so [1,2,3,4] only
  Day 2: [5,6] = 11 ✓
  Day 3: [7] = 7 ✓ + 8? 15 > 12, so [7] only
  Day 4: [8] = 8 ✓
  Day 5: [9] = 9 ✓
  Day 6: [10] = 10 ✓
  Days needed = 6 <= 5? NO ✗

Result: feasible=FALSE, left=13
        [13.15]
         ^  ^

===== ITERATION 5 =====
mid = 13 + (15-13)/2 = 14

feasible(capacity=14, days=5)?
  Day 1: [1,2,3,4] = 10 ✓ + 5? 15 > 14, stay 10
  Day 2: [5,6] = 11 ✓
  Day 3: [7] = 7 ✓
  Day 4: [8] = 8 ✓
  Day 5: [9] = 9 ✓
  Day 6: [10] = 10 ✓
  Days needed = 6 <= 5? NO ✗

Result: feasible=FALSE, left=15
        [15]
         ^
        left==right

Return left = 15
```

**Why 15 is the answer:**
- Capacity 14: Need 6 days (fails)
- Capacity 15: Need 5 days (works)
- 15 is the MINIMUM capacity that works

---

### Example 4: Koko Eating Bananas

**Problem:**
```
piles = [3, 6, 7, 11]
h = 8 hours

Koko eats at speed K bananas/hour.
Find MINIMUM K to finish all piles in h hours.
```

**Approach:**

**Key Insights:**
- **Naive Approach:** Try speeds from 1 to max pile, compute total hours for each, O(max * n) time.
- **Optimized Approach:** Binary search on eating speed K. Find minimal K where total hours needed <= h. Monotonic: if K works, all larger K work; if not, all smaller fail.

**Step-by-Step Approach:**
1. **Initialization:** left = 1, right = max(piles).
2. **Loop:** While left < right, mid = left + (right - left)/2.
3. **Check Condition:** If feasible(mid) (total hours <= h), right = mid (try smaller speed).
4. **Else:** left = mid + 1 (need faster speed).
5. **Feasible Function:** For each pile, hours = ceil(pile / mid), sum hours, check <= h.
6. **Edge Cases:** h = piles.size() (K = max), h = 1 (K = sum), large piles.

**Algorithm Flow (Pseudocode):**
```
left = 1, right = max(piles)
While left < right:
    mid = left + (right - left) / 2
    If feasible(mid):  // total hours <= h
        right = mid
    Else:
        left = mid + 1
Return left

feasible(speed):
    total_hours = 0
    For each pile:
        total_hours += ceil(pile / speed)
    Return total_hours <= h
```

**Why It Works:**
- **Time Complexity:** O(n log max_pile) as feasible takes O(n).
- **Space Complexity:** O(1).
- Monotonicity of speed vs feasibility ensures correct minimal speed.

**C++ Solution:**

```cpp 
class Solution {
public:
    int minEatingSpeed(vector<int>& piles, int h) {
        int left = 1;
        int right = *max_element(piles.begin(), piles.end());
        
        while (left < right) {
            int mid = left + (right - left) / 2;
            
            if (feasible(piles, h, mid)) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        
        return left;
    }
    
private:
    bool feasible(vector<int>& piles, int h, int speed) {
        long long hours = 0;
        
        for (int pile : piles) {
            hours += (pile + speed - 1) / speed;  // Ceiling division
        }
        
        return hours <= h;
    }
};
```

**Dry Run:**

```
piles = [3,6,7,11], h = 8

Initial: left=1, right=11

===== ITERATION 1 =====
mid = 1 + (11-1)/2 = 6

feasible(speed=6, hours=8)?
  pile[0]=3: ceil(3/6) = 1 hour
  pile[1]=6: ceil(6/6) = 1 hour
  pile[2]=7: ceil(7/6) = 2 hours
  pile[3]=11: ceil(11/6) = 2 hours
  Total: 1+1+2+2 = 6 <= 8? YES ✓

Result: right=6
        [1...6]

===== ITERATION 2 =====
mid = 1 + (6-1)/2 = 3

feasible(speed=3, hours=8)?
  pile[0]=3: ceil(3/3) = 1 hour
  pile[1]=6: ceil(6/3) = 2 hours
  pile[2]=7: ceil(7/3) = 3 hours
  pile[3]=11: ceil(11/3) = 4 hours
  Total: 1+2+3+4 = 10 <= 8? NO ✗

Result: left=4
        [4.6]

===== ITERATION 3 =====
mid = 4 + (6-4)/2 = 5

feasible(speed=5, hours=8)?
  pile[0]=3: ceil(3/5) = 1 hour
  pile[1]=6: ceil(6/5) = 2 hours
  pile[2]=7: ceil(7/5) = 2 hours
  pile[3]=11: ceil(11/5) = 3 hours
  Total: 1+2+2+3 = 8 <= 8? YES ✓

Result: right=5
        [4,5]

===== ITERATION 4 =====
mid = 4 + (5-4)/2 = 4

feasible(speed=4, hours=8)?
  pile[0]=3: ceil(3/4) = 1 hour
  pile[1]=6: ceil(6/4) = 2 hours
  pile[2]=7: ceil(7/4) = 2 hours
  pile[3]=11: ceil(11/4) = 3 hours
  Total: 1+2+2+3 = 8 <= 8? YES ✓

Result: right=4
        [4]
        left==right

Return 4
```

---

## 7. Problem-Solving Framework

### Step-by-Step Approach

**Step 1: Identify if Binary Search Applies**
```
Questions to ask:
□ Is there a search space?
□ Is there monotonicity?
□ Am I finding a boundary/minimum/maximum?
```

**Step 2: Define Search Space**
```
left = minimum possible answer
right = maximum possible answer + 1 (or just maximum)

Remember: Include ALL possibilities
```

**Step 3: Design Condition Function**
```
bool condition(int k) {
    // Return TRUE if k satisfies the requirement
    // Must be monotonic!
}
```

**Step 4: Determine Return Value**
```
Finding minimum that satisfies? → return left
Finding maximum that satisfies? → return left - 1
```

**Step 5: Write the Template**
```cpp
while (left < right) {
    int mid = left + (right - left) / 2;
    if (condition(mid)) {
        right = mid;
    } else {
        left = mid + 1;
    }
}
return left;  // or left - 1
```

### Common Patterns Checklist

**Pattern 1: Find First Position**
- Example: First bad version, search insert position
- Condition: `x >= target`
- Return: `left`

**Pattern 2: Find Minimum Satisfying**
- Example: Ship capacity, split array, Koko bananas
- Condition: `canAchieveWithValue(x)`
- Return: `left`

**Pattern 3: Find Kth Smallest**
- Example: Kth smallest in multiplication table
- Condition: `countLessOrEqual(x) >= k`
- Return: `left`

**Pattern 4: Maximize Minimum**
- Example: Magnetic force between balls
- Condition: `canPlaceWithMinDist(x)`
- Return: `left - 1`

---

## Key Takeaways

1. **Binary search is about finding boundaries in monotonic spaces**
   - Not just for sorted arrays!

2. **The template eliminates off-by-one errors**
   - Use `left < right` consistently
   - Use `right = mid` and `left = mid + 1`

3. **Condition function is everything**
   - Must be monotonic
   - Clear definition of what "satisfies" means

4. **Search space initialization is critical**
   - Include all possibilities
   - Think carefully about edge cases

5. **Practice recognizing patterns**
   - "Can we do X?" → Binary search on X
   - "Minimum Y such that..." → Binary search on Y
   - "Kth smallest" → Binary search on value range

---

## Practice Problems (Ordered by Difficulty)

**Easy:**
- First Bad Version
- Sqrt(x)
- Search Insert Position
- Valid Perfect Square

**Medium:**
- Capacity To Ship Packages (LC 1011)
- Koko Eating Bananas (LC 875)
- Minimum Days to Make m Bouquets (LC 1482)
- Find Smallest Divisor Given Threshold (LC 1283)
- Split Array Largest Sum (LC 410)

**Hard:**
- Kth Smallest in Multiplication Table (LC 668)
- Find K-th Smallest Pair Distance (LC 719)
- Ugly Number III (LC 1201)
- Minimum Cost to Make Array Equal (LC 2448)

---

## Problem Recognition Framework

### Decision Tree for Binary Search Problems

```
START: Is it a Binary Search Problem?

Q1: What are we searching for?
├─ Specific element in sorted array → Classic binary search
├─ Minimum/maximum value satisfying condition → Binary search on answer
├─ Kth smallest/largest → Binary search on value range
├─ Optimal allocation/division → Binary search on capacity/speed
├─ Other → Check monotonicity

Q2: Is the search space monotonic?
├─ YES → Binary search is applicable
├─ NO → Different algorithm needed
├─ Unsure → Test with examples

Q3: What type of condition?
├─ "Can we do X with Y resources?" → Binary search on Y
├─ "Minimum Z such that condition" → Binary search on Z
├─ "Maximum Z such that condition" → Binary search on Z
├─ "Find position where property changes" → Binary search on position

Q4: Search space boundaries?
├─ Array indices → 0 to n-1
├─ Value ranges → min_possible to max_possible
├─ Time/speed/capacity → 1 to max_constraint
├─ Precision required → Consider floating point vs integer

Q5: Edge cases to consider?
├─ Empty array/input
├─ Single element
├─ All elements same
├─ Boundary conditions
├─ Impossible cases
```

### Pattern Recognition Cheat Sheet

| Problem Contains | Primary Pattern | Search Space | Condition Function |
|-----------------|----------------|--------------|-------------------|
| "first bad version" | Classic BS | Version numbers | isBadVersion(mid) |
| "sqrt(x)" | Classic BS | 0 to x | mid*mid <= x |
| "capacity to ship" | BS on Answer | 1 to max_weight | canShipInDays(mid) |
| "koko eating bananas" | BS on Answer | 1 to max_pile | canEatAll(mid) |
| "minimum days" | BS on Answer | 1 to max_days | canMakeBouquets(mid) |
| "split array" | BS on Answer | max_element to sum | canSplitInto(mid) |
| "kth smallest" | BS on Value | min_val to max_val | countElements <= k |
| "search in rotated" | Modified BS | 0 to n-1 | nums[mid] vs target |
| "peak element" | BS on Index | 0 to n-1 | nums[mid] > neighbors |
| "median of arrays" | BS on Partition | 0 to min(n,m) | partition balance |

---

## Key Takeaways & Mental Models

### 1. **Binary Search = Decision Boundary Finder**
```
Mental Model: Finding the exact point where possibility becomes impossibility

Like finding the highest floor you can safely jump from
Each check tells you: too low, too high, or just right
The boundary between "possible" and "impossible" is what we seek
```

### 2. **Monotonicity = The Magic Ingredient**
```
Mental Model: A function that never goes backwards

As input increases: either condition stays false→true→true
Or condition stays true→false→false
No zigzagging allowed - that's what makes binary search work!
```

### 3. **Search Space = The Playing Field**
```
Mental Model: The range of all possible answers

Classic: array indices 0 to n-1
Answer-based: minimum possible to maximum possible
Always include ALL possibilities, exclude none
```

### 4. **Condition Function = The Judge**
```
Mental Model: The yes/no referee

Takes a candidate answer, returns true/false
Must be monotonic for binary search to work
Defines what "good enough" means for your problem
```

### 5. **Time Complexity Wisdom**
```
O(log n) is incredible power:
n=1e9 → only 30 operations!
n=1e18 → only 60 operations!
Each step eliminates half the possibilities
```

### 6. **Edge Case Handling**
```
Always consider:
- Empty input → return appropriate default
- Single element → direct check
- All same values → any valid answer
- Boundary conditions → off-by-one prevention
```

---

## Common Pitfalls & How to Avoid

### Pitfall 1: Wrong Search Space Boundaries
```cpp
// WRONG: Too narrow search space
int left = 0, right = n-1;  // For value-based problems
// Missing: should be min_val to max_val

// RIGHT: Include all possibilities
int left = 1, right = 1000000;  // For capacity problems
```

### Pitfall 2: Incorrect Mid Calculation (Integer Overflow)
```cpp
// WRONG: Can overflow for large numbers
int mid = (left + right) / 2;

// RIGHT: Safe calculation
int mid = left + (right - left) / 2;
```

### Pitfall 3: Wrong Loop Condition
```cpp
// WRONG: Infinite loop potential
while (left < right) {
    // No progress guarantee
}

// RIGHT: Always make progress
while (left <= right) {  // For finding exact element
while (left < right) {   // For finding boundary
```

### Pitfall 4: Not Handling Edge Cases
```cpp
// WRONG: Crash on empty array
if (nums.empty()) return -1;  // Good
// But forgot: if (target < nums[0]) return 0;

// RIGHT: Handle all boundaries
if (nums.empty()) return 0;
if (target <= nums[0]) return 0;
if (target > nums.back()) return nums.size();
```

### Pitfall 5: Wrong Condition Function
```cpp
// WRONG: Non-monotonic condition
bool canEat(int speed) {
    // Sometimes true, sometimes false randomly
    return rand() % 2;  // Not monotonic!
}

// RIGHT: Clear monotonic condition
bool canEat(int speed) {
    long long hours = 0;
    for (int pile : piles) {
        hours += (pile + speed - 1) / speed;
    }
    return hours <= h;
}
```

### Pitfall 6: Floating Point Precision Issues
```cpp
// WRONG: Direct equality with floats
if (abs(mid * mid - x) < 1e-9) return mid;

// RIGHT: Use epsilon comparison
double eps = 1e-6;
if (abs(mid * mid - x) <= eps) return mid;
```

### Pitfall 7: Off-by-One in Boundary Updates
```cpp
// WRONG: Wrong boundary updates
if (condition(mid)) {
    left = mid;      // Should be left = mid + 1
} else {
    right = mid - 1; // Should be right = mid
}

// RIGHT: Correct boundary movement
if (condition(mid)) {
    right = mid;  // Try smaller values
} else {
    left = mid + 1;  // Need larger values
}
```

---

## Final Thoughts

**The Power of Binary Search:**
- Binary search isn't just for sorted arrays
- It's a universal pattern for optimization problems
- O(log n) makes impossible problems possible

**How to Master Binary Search:**
1. **Understand the Philosophy** - Minimize k such that condition(k) is true
2. **Master the Template** - The universal binary search structure
3. **Practice Pattern Recognition** - Spot monotonic search spaces
4. **Build Condition Functions** - Clear, correct, monotonic conditions
5. **Handle Edge Cases** - Empty inputs, boundaries, overflow
6. **Debug Systematically** - Check search space, condition, boundaries

**Key Insight:** Binary search is about finding boundaries between possible and impossible, not just finding elements in arrays!

**Remember:** Every binary search problem can be solved by asking:
- What's my search space? (indices, values, capacities)
- What's my condition function? (must be monotonic)
- What am I minimizing/maximizing?
- What's the decision boundary I'm looking for?

Master this framework, and you'll see binary search opportunities everywhere! 🚀

---

## General Time and Space Complexity of Binary Search Patterns

### Time Complexity Analysis

| Binary Search Type | Time Complexity | Operations |
|-------------------|----------------|------------|
| **Classic Binary Search** | O(log n) | ~30 for n=1e9 |
| **Binary Search on Answer** | O(log R) | R = search range |
| **Binary Search with Verification** | O(log R * O(verify)) | Depends on verify cost |
| **2D Binary Search** | O(log n * log m) | Matrix search |
| **Exponential Search** | O(log n) | Unbounded search |

**Why O(log n) is Powerful:**
- n=1e9 elements → only 30 comparisons
- n=1e18 possibilities → only 60 operations
- Each step eliminates half the search space
- Scales incredibly well for large inputs

### Space Complexity Analysis

| Pattern Type | Space Complexity | Examples |
|--------------|------------------|----------|
| **Classic BS** | O(1) | Only pointers |
| **BS on Answer** | O(1) | No extra space |
| **BS with Arrays** | O(n) | Input storage |
| **BS with Maps/Sets** | O(n) | Auxiliary structures |
| **Recursive BS** | O(log n) | Call stack |

**Optimization Strategies:**
- Use iterative over recursive to save stack space
- Prefer arrays over vectors when possible
- Use fast I/O for large inputs
- Consider memory constraints for verification functions

**Common Complexity Patterns:**
- **O(log n)**: Simple binary search
- **O(log n * n)**: BS with linear verification
- **O(log n * log n)**: BS with binary verification
- **O(log n * k)**: BS with constant-time verification

---

## Practice Leetcode Questions

### Easy Level (Foundation)
1. **Binary Search** - https://leetcode.com/problems/binary-search/
2. **First Bad Version** - https://leetcode.com/problems/first-bad-version/
3. **Search Insert Position** - https://leetcode.com/problems/search-insert-position/
4. **Sqrt(x)** - https://leetcode.com/problems/sqrtx/
5. **Valid Perfect Square** - https://leetcode.com/problems/valid-perfect-square/
6. **Guess Number Higher or Lower** - https://leetcode.com/problems/guess-number-higher-or-lower/

### Medium Level (Core Patterns)
1. **Search in Rotated Sorted Array** - https://leetcode.com/problems/search-in-rotated-sorted-array/
2. **Find Minimum in Rotated Sorted Array** - https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/
3. **Capacity To Ship Packages Within D Days** - https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/
4. **Koko Eating Bananas** - https://leetcode.com/problems/koko-eating-bananas/
5. **Minimum Number of Days to Make m Bouquets** - https://leetcode.com/problems/minimum-number-of-days-to-make-m-bouquets/
6. **Find the Smallest Divisor Given a Threshold** - https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/
7. **Split Array Largest Sum** - https://leetcode.com/problems/split-array-largest-sum/
8. **Peak Index in a Mountain Array** - https://leetcode.com/problems/peak-index-in-a-mountain-array/
9. **Find Peak Element** - https://leetcode.com/problems/find-peak-element/
10. **Single Element in a Sorted Array** - https://leetcode.com/problems/single-element-in-a-sorted-array/

### Hard Level (Advanced)
1. **Median of Two Sorted Arrays** - https://leetcode.com/problems/median-of-two-sorted-arrays/
2. **Find K-th Smallest Pair Distance** - https://leetcode.com/problems/find-k-th-smallest-pair-distance/
3. **Kth Smallest Element in a Sorted Matrix** - https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/
4. **Kth Smallest in Multiplication Table** - https://leetcode.com/problems/kth-smallest-number-in-multiplication-table/
5. **Ugly Number III** - https://leetcode.com/problems/ugly-number-iii/
6. **Minimum Cost to Make Array Equal** - https://leetcode.com/problems/minimum-cost-to-make-array-equal/
7. **Maximize Distance to Closest Person** - https://leetcode.com/problems/maximize-distance-to-closest-person/
8. **Minimum Time to Complete Trips** - https://leetcode.com/problems/minimum-time-to-complete-trips/

### Pattern-Specific Practice
**Classic Binary Search:**
- Search in Rotated Sorted Array II - https://leetcode.com/problems/search-in-rotated-sorted-array-ii/
- Find First and Last Position - https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/

**Binary Search on Answer:**
- Minimize Max Distance to Gas Station - https://leetcode.com/problems/minimize-max-distance-to-gas-station/
- Swim in Rising Water - https://leetcode.com/problems/swim-in-rising-water/
- Path with Minimum Effort - https://leetcode.com/problems/path-with-minimum-effort/

**Binary Search with Verification:**
- Aggressive Cows - https://leetcode.com/problems/aggressive-cows/
- Allocate Books - https://leetcode.com/problems/allocate-minimum-number-of-pages/
- Painter's Partition Problem - https://leetcode.com/problems/painter-s-partition-problem/

**Practice Strategy:**
- Start with classic binary search problems
- Move to rotated array variations
- Master binary search on answer pattern
- Practice with different verification functions
- Focus on edge cases and boundary conditions
- Time yourself to build recognition speed

Master binary search, and you'll solve optimization problems with incredible efficiency! 🎯