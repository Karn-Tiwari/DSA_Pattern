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

**Thinking Process:**
```
Array: [1, 3, 5, 6]
Target: 5

Question: What are we minimizing?
Answer: The MINIMAL index k where nums[k] >= target

Condition: nums[k] >= target
```

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

**Thinking Process:**
```
x = 8

We want: MAXIMUM k where k² <= x
Which equals: MINIMUM k where k² > x, then return k-1

Better approach: MINIMUM k where k² > x is same as
                MAXIMUM k where k² <= x + 1

Let's use: Find maximum k where (k+1)² > x
```

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

**Key Insight - The Monotonicity:**
```
If capacity = 15 works → capacity = 16, 17, 18... all work
If capacity = 10 fails → capacity = 9, 8, 7... all fail

[FFFFFFFFF][TTTTTTTTTTT]
          ^
          minimum capacity that works
```

**Thinking Process:**

1. **Search Space:**
   - Minimum: max(weights) = 10 (must carry heaviest item)
   - Maximum: sum(weights) = 55 (carry everything in 1 day)

2. **Condition Function:**
   ```
   feasible(capacity): Can we ship all packages in D days with this capacity?
   ```

3. **Greedy Simulation:**
   ```
   days = 1, current_load = 0
   
   For each package:
       if (current_load + package <= capacity):
           current_load += package
       else:
           days++
           current_load = package
   
   return days <= D
   ```

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

**Monotonicity:**
```
If K=4 works → K=5,6,7... all work
If K=3 fails → K=2,1 all fail

[FFF][TTTTTTT]
    ^
    minimum K
```

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