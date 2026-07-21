# Ultimate Greedy Algorithm Pattern Guide - From Basics to Mastery

## Table of Contents

### Part A: Foundation
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [Greedy vs Dynamic Programming](#2-greedy-vs-dynamic-programming)
3. [Proving Greedy Correctness](#3-proving-greedy-correctness)

### Part B: Core Patterns
4. [Pattern 1: Activity Selection / Interval Scheduling](#4-pattern-1-activity-selection--interval-scheduling)
5. [Pattern 2: Jump Game / Reachability](#5-pattern-2-jump-game--reachability)
6. [Pattern 3: Partition Labels / Grouping](#6-pattern-3-partition-labels--grouping)
7. [Pattern 4: Coin Change (Greedy When Valid)](#7-pattern-4-coin-change-greedy-when-valid)

### Part C: Advanced Patterns
8. [Pattern 5: Huffman Coding / Merge Patterns](#8-pattern-5-huffman-coding--merge-patterns)
9. [Pattern 6: Gas Station / Circular Greedy](#9-pattern-6-gas-station--circular-greedy)
10. [Pattern 7: Minimum Arrows / Points Covering](#10-pattern-7-minimum-arrows--points-covering)
11. [Pattern 8: Task Scheduling Greedy](#11-pattern-8-task-scheduling-greedy)

### Part D: Problem Solving
12. [Problem Recognition Framework](#12-problem-recognition-framework)
13. [Practice Problems](#13-practice-problems)
14. [Common Pitfalls](#14-common-pitfalls)
15. [Template Summary](#15-template-summary)

---

## 1. Core Concept & Philosophy

### What is Greedy Really About?

**Wrong Understanding:**
"Greedy means always pick the maximum or minimum value"

**Correct Understanding:**
"Greedy builds a solution by making the **locally optimal choice at each step**, hoping it leads to a **globally optimal solution** — works when the problem has **greedy choice property** and **optimal substructure**"

### The Greedy Mindset

```
At each step, ask:
"What is the BEST choice RIGHT NOW that won't block future optimal choices?"

Key: Prove that local optimum → global optimum
If you can't prove it, try DP instead
```

### Greedy Template

```cpp
// General greedy framework
sort(input);  // Often sort first
Result result = initialize();

for (each element in input) {
    if (isValidChoice(element, result)) {
        makeChoice(element, result);
    }
}
return result;
```

### When Does Greedy Work?

**Key Indicators:**
1. Problem asks for **minimum/maximum** with local choices
2. **Sorting** reveals the greedy order
3. Making a choice **doesn't require reconsidering** past choices
4. **Exchange argument** or **matroid** structure exists
5. Interval/scheduling/coverage problems

---

## 2. Greedy vs Dynamic Programming

```
                    GREEDY                    DYNAMIC PROGRAMMING
Decision:           One choice, no backtrack   Try all, pick best
Proof needed:       Greedy choice property     Optimal substructure + overlapping
Time:               Usually O(n log n)         Often O(n²) or O(n*W)
Examples:           Activity selection         Knapsack, LIS, Edit Distance
                    Huffman coding             Coin change (general)
                    Jump Game II               Longest Common Subsequence
```

### Decision Flowchart

```
Can making a local optimal choice ever require undoing it?
│
├─ NO → Try GREEDY
│   └─ Can you prove with exchange argument?
│       ├─ YES → Use Greedy ✓
│       └─ NO → Try DP
│
└─ YES → Use DP
```

---

## 3. Proving Greedy Correctness

### Exchange Argument

```
Claim: Greedy solution G is optimal.
Proof: Suppose optimal O differs from G.
       Show we can transform O into G without worsening cost.
       Therefore G is also optimal.
```

### Example: Activity Selection

```
Activities sorted by end time.
Greedy picks activity with earliest end time.

Proof: If optimal O picks different first activity A,
       and greedy picks G (earliest end):
       - G finishes no later than A
       - Replace A with G in O → still valid, same or more room
       - Repeat → O transforms to G
```

### Stay Ahead Argument

```
Show greedy choice is never worse than any alternative at each step.
Greedy "stays ahead" of any other solution.
```

---

## 4. Pattern 1: Activity Selection / Interval Scheduling

### Concept

```
Maximize number of non-overlapping intervals:
Sort by END time, greedily pick earliest finishing.

Why end time? Finishing early leaves most room for future intervals.
```

### Template: Non-Overlapping Intervals

```cpp
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end(), [](auto& a, auto& b) {
        return a[1] < b[1];  // Sort by END time
    });

    int count = 0;
    int end = INT_MIN;

    for (auto& interval : intervals) {
        if (interval[0] >= end) {
            end = interval[1];  // Take this interval
        } else {
            count++;  // Remove overlapping interval
        }
    }
    return count;
}
```

### Meeting Rooms (Can Attend All?)

```cpp
bool canAttendMeetings(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());
    for (int i = 1; i < intervals.size(); i++) {
        if (intervals[i][0] < intervals[i-1][1]) return false;
    }
    return true;
}
```

### Minimum Arrows to Burst Balloons

```cpp
int findMinArrowShots(vector<vector<int>>& points) {
    sort(points.begin(), points.end(), [](auto& a, auto& b) {
        return a[1] < b[1];
    });

    int arrows = 1;
    int end = points[0][1];

    for (int i = 1; i < points.size(); i++) {
        if (points[i][0] > end) {
            arrows++;
            end = points[i][1];
        }
    }
    return arrows;
}
```

**Dry Run:**

```
Points: [[10,16],[2,8],[1,6],[7,12]]
Sorted by end: [[1,6],[2,8],[7,12],[10,16]]

Arrow at x=6: covers [1,6],[2,8]
Arrow at x=12: covers [7,12],[10,16]
Answer: 2 arrows ✓
```

---

## 5. Pattern 2: Jump Game / Reachability

### Jump Game I — Can Reach End?

```cpp
bool canJump(vector<int>& nums) {
    int maxReach = 0;

    for (int i = 0; i < nums.size(); i++) {
        if (i > maxReach) return false;  // Can't reach here
        maxReach = max(maxReach, i + nums[i]);
        if (maxReach >= nums.size() - 1) return true;
    }
    return true;
}
```

**Key Insight:** Track farthest reachable index. If current index > maxReach, stuck.

### Jump Game II — Minimum Jumps

```cpp
int jump(vector<int>& nums) {
    int jumps = 0, currentEnd = 0, farthest = 0;

    for (int i = 0; i < nums.size() - 1; i++) {
        farthest = max(farthest, i + nums[i]);

        if (i == currentEnd) {
            jumps++;
            currentEnd = farthest;
        }
    }
    return jumps;
}
```

**Dry Run:**

```
nums = [2, 3, 1, 1, 4]

i=0: farthest=2, currentEnd=0 → jump! jumps=1, currentEnd=2
i=1: farthest=4
i=2: farthest=4, currentEnd=2 → jump! jumps=2, currentEnd=4
Reached end with 2 jumps ✓
```

---

## 6. Pattern 3: Partition Labels / Grouping

### Partition Labels

```cpp
vector<int> partitionLabels(string s) {
    vector<int> last(26);
    for (int i = 0; i < s.size(); i++) {
        last[s[i] - 'a'] = i;
    }

    vector<int> result;
    int start = 0, end = 0;

    for (int i = 0; i < s.size(); i++) {
        end = max(end, last[s[i] - 'a']);

        if (i == end) {
            result.push_back(end - start + 1);
            start = i + 1;
        }
    }
    return result;
}
```

**Key Insight:** Extend partition boundary to last occurrence of every character seen.

### Merge Intervals (Greedy Merge)

```cpp
vector<vector<int>> merge(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> result;

    for (auto& interval : intervals) {
        if (result.empty() || result.back()[1] < interval[0]) {
            result.push_back(interval);
        } else {
            result.back()[1] = max(result.back()[1], interval[1]);
        }
    }
    return result;
}
```

---

## 7. Pattern 4: Coin Change (Greedy When Valid)

### When Greedy Works for Coin Change

```
Greedy works when coin system is CANONICAL (e.g., US coins: 1,5,10,25).
Greedy FAILS for: coins = [1, 3, 4], amount = 6
  Greedy: 4+1+1 = 3 coins
  Optimal: 3+3 = 2 coins → Need DP!
```

### Greedy Coin Change (Valid Systems)

```cpp
int coinChangeGreedy(vector<int>& coins, int amount) {
    sort(coins.rbegin(), coins.rend());  // Largest first
    int count = 0;

    for (int coin : coins) {
        count += amount / coin;
        amount %= coin;
    }
    return amount == 0 ? count : -1;
}
```

### Assign Cookies

```cpp
int findContentChildren(vector<int>& g, vector<int>& s) {
    sort(g.begin(), g.end());
    sort(s.begin(), s.end());

    int child = 0, cookie = 0;
    while (child < g.size() && cookie < s.size()) {
        if (s[cookie] >= g[child]) child++;
        cookie++;
    }
    return child;
}
```

---

## 8. Pattern 5: Huffman Coding / Merge Patterns

### Minimum Cost to Connect Sticks

```cpp
int connectSticks(vector<int>& sticks) {
    priority_queue<int, vector<int>, greater<int>> pq(sticks.begin(), sticks.end());
    int cost = 0;

    while (pq.size() > 1) {
        int first = pq.top(); pq.pop();
        int second = pq.top(); pq.pop();
        int merged = first + second;
        cost += merged;
        pq.push(merged);
    }
    return cost;
}
```

**Greedy Insight:** Always merge two smallest sticks first (like Huffman coding).

---

## 9. Pattern 6: Gas Station / Circular Greedy

```cpp
int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
    int totalTank = 0, currentTank = 0, start = 0;

    for (int i = 0; i < gas.size(); i++) {
        totalTank += gas[i] - cost[i];
        currentTank += gas[i] - cost[i];

        if (currentTank < 0) {
            start = i + 1;
            currentTank = 0;
        }
    }
    return totalTank >= 0 ? start : -1;
}
```

**Key Insight:** If total gas ≥ total cost, solution exists. If tank goes negative at i, no station 0..i can be start.

---

## 10. Pattern 7: Minimum Arrows / Points Covering

Already covered in Pattern 1 — sort by end, greedy coverage.

### Video Stitching

```cpp
int videoStitching(vector<vector<int>>& clips, int time) {
    sort(clips.begin(), clips.end());
    int clips_needed = 0, current_end = 0, next_end = 0, i = 0;

    while (current_end < time) {
        while (i < clips.size() && clips[i][0] <= current_end) {
            next_end = max(next_end, clips[i][1]);
            i++;
        }
        if (next_end == current_end) return -1;
        current_end = next_end;
        clips_needed++;
    }
    return clips_needed;
}
```

---

## 11. Pattern 8: Task Scheduling Greedy

### Lemonade Change

```cpp
bool lemonadeChange(vector<int>& bills) {
    int five = 0, ten = 0;

    for (int bill : bills) {
        if (bill == 5) five++;
        else if (bill == 10) { five--; ten++; }
        else {
            if (ten > 0) { ten--; five--; }
            else five -= 3;
        }
        if (five < 0) return false;
    }
    return true;
}
```

### Queue Reconstruction by Height

```cpp
vector<vector<int>> reconstructQueue(vector<vector<int>>& people) {
    sort(people.begin(), people.end(), [](auto& a, auto& b) {
        if (a[0] != b[0]) return a[0] > b[0];  // Height descending
        return a[1] < b[1];  // Count ascending
    });

    vector<vector<int>> result;
    for (auto& p : people) {
        result.insert(result.begin() + p[1], p);
    }
    return result;
}
```

---

## 12. Problem Recognition Framework

```
START: Can I use Greedy?
│
├─ Interval scheduling / maximum non-overlapping?
│   └─ YES → Sort by END time, greedy pick
│
├─ Minimum coverage (arrows, intervals, clips)?
│   └─ YES → Sort by end, extend coverage greedily
│
├─ Jump / reachability with minimum steps?
│   └─ YES → Track farthest reach, jump at boundary
│
├─ Partition into valid groups?
│   └─ YES → Track last occurrence boundaries
│
├─ Merge/combine with minimum cost?
│   └─ YES → Always combine smallest (min-heap)
│
├─ Assign resources to satisfy constraints?
│   └─ YES → Sort both, match greedily
│
└─ Coin change with arbitrary denominations?
    └─ NO → Use DP, not Greedy!
```

### Pattern Selection Table

| Problem Type | Greedy Strategy | Sort Key | Time |
|-------------|-----------------|----------|------|
| Non-overlapping intervals | Pick earliest end | End time | O(n log n) |
| Jump Game II | Jump at window end | N/A | O(n) |
| Partition labels | Extend to last occurrence | N/A | O(n) |
| Min arrows | Shoot at interval end | End point | O(n log n) |
| Connect sticks | Merge two smallest | N/A (heap) | O(n log n) |
| Gas station | Reset start on negative | N/A | O(n) |
| Assign cookies | Smallest sufficient cookie | Both arrays | O(n log n) |

---

## 13. Practice Problems

### Easy
1. Assign Cookies - https://leetcode.com/problems/assign-cookies/
2. Lemonade Change - https://leetcode.com/problems/lemonade-change/
3. Best Time to Buy and Sell Stock II - https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/
4. Can Place Flowers - https://leetcode.com/problems/can-place-flowers/
5. Maximum Subarray (Kadane's) - https://leetcode.com/problems/maximum-subarray/

### Medium
6. Jump Game - https://leetcode.com/problems/jump-game/
7. Jump Game II - https://leetcode.com/problems/jump-game-ii/
8. Non-overlapping Intervals - https://leetcode.com/problems/non-overlapping-intervals/
9. Partition Labels - https://leetcode.com/problems/partition-labels/
10. Gas Station - https://leetcode.com/problems/gas-station/
11. Minimum Number of Arrows to Burst Balloons - https://leetcode.com/problems/minimum-number-of-arrows-to-burst-balloons/
12. Queue Reconstruction by Height - https://leetcode.com/problems/queue-reconstruction-by-height/
13. Task Scheduler - https://leetcode.com/problems/task-scheduler/
14. Reorganize String - https://leetcode.com/problems/reorganize-string/
15. Video Stitching - https://leetcode.com/problems/video-stitching/
16. Minimum Add to Make Parentheses Valid - https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/

### Hard
17. Candy - https://leetcode.com/problems/candy/
18. Create Maximum Number - https://leetcode.com/problems/create-maximum-number/
19. Minimum Number of Refueling Stops - https://leetcode.com/problems/minimum-number-of-refueling-stops/
20. IPO - https://leetcode.com/problems/ipo/

---

## 14. Common Pitfalls

### Pitfall 1: Using Greedy When DP is Needed

```cpp
// Coin Change: coins = [1,3,4], amount = 6
// Greedy gives 3 coins (4+1+1), optimal is 2 (3+3)
// MUST use DP for arbitrary coin systems
```

### Pitfall 2: Wrong Sort Key for Intervals

```cpp
// WRONG: Sort by start for non-overlapping count
sort(intervals.begin(), intervals.end());

// RIGHT: Sort by END for maximum non-overlapping
sort(intervals.begin(), intervals.end(), [](auto& a, auto& b) {
    return a[1] < b[1];
});
```

### Pitfall 3: Not Handling Edge Cases in Jump Game

```cpp
// WRONG: Don't check if i > maxReach
for (int i = 0; i < n; i++) {
    maxReach = max(maxReach, i + nums[i]);
}

// RIGHT: Check reachability first
if (i > maxReach) return false;
```

### Pitfall 4: Greedy Without Proof

```
Always ask: "Why can't a locally worse choice lead to globally better result?"
If you can't answer, test counterexamples or switch to DP.
```

---

## 15. Template Summary

### Interval Scheduling (Sort by End)
```cpp
sort(intervals.begin(), intervals.end(), [](auto& a, auto& b) {
    return a[1] < b[1];
});
int end = INT_MIN, count = 0;
for (auto& iv : intervals) {
    if (iv[0] >= end) { end = iv[1]; count++; }
}
```

### Jump Game II
```cpp
int jumps = 0, curEnd = 0, farthest = 0;
for (int i = 0; i < n - 1; i++) {
    farthest = max(farthest, i + nums[i]);
    if (i == curEnd) { jumps++; curEnd = farthest; }
}
```

### Partition Labels
```cpp
// Track last occurrence, extend end, cut at end
end = max(end, last[s[i] - 'a']);
if (i == end) result.push_back(end - start + 1);
```

### Two-Pointer Greedy Match
```cpp
sort(a.begin(), a.end());
sort(b.begin(), b.end());
while (i < a.size() && j < b.size()) {
    if (match(a[i], b[j])) { i++; }
    j++;
}
```

### Merge Smallest (Min-Heap)
```cpp
while (pq.size() > 1) {
    int a = pq.top(); pq.pop();
    int b = pq.top(); pq.pop();
    cost += a + b;
    pq.push(a + b);
}
```

---

## Final Thoughts

**The Power of Greedy:**
- Often O(n log n) vs O(n²) or worse for DP
- Elegant solutions when they work
- Sorting is the most common preprocessing step
- Exchange argument is your proof tool

**How to Master:**
1. Try greedy first for optimization problems
2. Sort with the RIGHT key (start vs end matters!)
3. Test counterexamples before committing
4. Know when to fall back to DP (coin change, knapsack)

---

*Greedy is fast and elegant — but only when you can prove it works!*
