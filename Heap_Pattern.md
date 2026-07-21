# Ultimate Heap Pattern Guide - From Basics to Mastery

## Table of Contents

### Part A: Foundation
1. [Core Concept & Philosophy](#1-core-concept--philosophy)
2. [Heap Implementation & Properties](#2-heap-implementation--properties)
3. [Priority Queue in C++](#3-priority-queue-in-c)

### Part B: Core Patterns
4. [Pattern 1: Top K Elements](#4-pattern-1-top-k-elements)
5. [Pattern 2: Merge K Sorted Lists/Arrays](#5-pattern-2-merge-k-sorted-listsarrays)
6. [Pattern 3: Two Heaps (Running Median)](#6-pattern-3-two-heaps-running-median)
7. [Pattern 4: Heap Sort](#7-pattern-4-heap-sort)

### Part C: Advanced Patterns
8. [Pattern 5: Dijkstra with Priority Queue](#8-pattern-5-dijkstra-with-priority-queue)
9. [Pattern 6: Scheduling & Greedy with Heap](#9-pattern-6-scheduling--greedy-with-heap)
10. [Pattern 7: Custom Comparator Patterns](#10-pattern-7-custom-comparator-patterns)

### Part D: Problem Solving
11. [Problem Recognition Framework](#11-problem-recognition-framework)
12. [Practice Problems](#12-practice-problems)
13. [Common Pitfalls](#13-common-pitfalls)
14. [Template Summary](#14-template-summary)

---

## 1. Core Concept & Philosophy

### What is a Heap Really About?

**Wrong Understanding:**
"A heap is just a sorted array"

**Correct Understanding:**
"A heap is a **complete binary tree** stored in an array that maintains the **heap property**: parent is always ≥ (max-heap) or ≤ (min-heap) its children, enabling **O(1) peek** and **O(log n) insert/extract**"

### Heap Visual Structure

```
MAX-HEAP (parent ≥ children):

           50
          /  \
        30    40
       / \   /
      10 20 35

Array representation: [50, 30, 40, 10, 20, 35]

Index relationships (0-indexed):
  parent(i)   = (i - 1) / 2
  left(i)     = 2*i + 1
  right(i)    = 2*i + 2
```

### When to Use Heap?

**Key Indicators:**
1. Need **K largest/smallest** elements
2. **Streaming data** — maintain top K on the fly
3. **Merge K sorted** sequences
4. **Running median** or percentile
5. **Shortest path** (Dijkstra) or scheduling
6. **Continuously extract min/max** efficiently

### Min-Heap vs Max-Heap

```
MIN-HEAP: root = minimum     → K largest (size K min-heap)
MAX-HEAP: root = maximum     → K smallest (size K max-heap)

C++ default priority_queue = MAX-HEAP
For min-heap: priority_queue<int, vector<int>, greater<int>>
```

---

## 2. Heap Implementation & Properties

### Core Operations

```
INSERT (push):     Add to end, bubble UP (heapify up)     O(log n)
EXTRACT (pop):     Remove root, bubble DOWN (heapify down) O(log n)
PEEK (top):        Return root element                     O(1)
BUILD HEAP:        Heapify all nodes from bottom up      O(n)
HEAP SORT:         Extract n times                         O(n log n)
```

### Heapify Down

```cpp
void heapifyDown(vector<int>& heap, int i, int n) {
    int largest = i;
    int left = 2 * i + 1;
    int right = 2 * i + 2;

    if (left < n && heap[left] > heap[largest]) largest = left;
    if (right < n && heap[right] > heap[largest]) largest = right;

    if (largest != i) {
        swap(heap[i], heap[largest]);
        heapifyDown(heap, largest, n);
    }
}
```

### Build Heap — O(n)

```cpp
void buildMaxHeap(vector<int>& arr) {
    int n = arr.size();
    for (int i = n / 2 - 1; i >= 0; i--) {
        heapifyDown(arr, i, n);
    }
}
```

**Why O(n)?** Most nodes are near bottom with small subtrees.

---

## 3. Priority Queue in C++

### Max-Heap (Default)

```cpp
#include <queue>
priority_queue<int> maxHeap;
maxHeap.push(10);
maxHeap.push(30);
maxHeap.top();   // 30
maxHeap.pop();
```

### Min-Heap

```cpp
priority_queue<int, vector<int>, greater<int>> minHeap;
minHeap.push(10);
minHeap.push(30);
minHeap.top();   // 10
```

### Custom Comparator

```cpp
// Sort pairs by second element (min-heap on second)
auto cmp = [](const pair<int,int>& a, const pair<int,int>& b) {
    return a.second > b.second;  // Reverse for min-heap
};
priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);
```

### Common Patterns

```cpp
// Max-heap of pairs (by first element)
priority_queue<pair<int,int>> pq;  // Default: max on first

// Min-heap for intervals sorted by end time
auto cmp = [](vector<int>& a, vector<int>& b) { return a[1] > b[1]; };
priority_queue<vector<int>, vector<vector<int>>, decltype(cmp)> pq(cmp);
```

---

## 4. Pattern 1: Top K Elements

### Concept

```
To find K LARGEST: maintain min-heap of size K
  - If new element > heap min → replace min
  - After processing, heap contains K largest

To find K SMALLEST: maintain max-heap of size K
  - If new element < heap max → replace max
```

### Template: Kth Largest Element

```cpp
int findKthLargest(vector<int>& nums, int k) {
    priority_queue<int, vector<int>, greater<int>> minHeap;

    for (int num : nums) {
        minHeap.push(num);
        if (minHeap.size() > k) {
            minHeap.pop();
        }
    }
    return minHeap.top();
}
```

**Why Min-Heap for K Largest?**

```
nums = [3, 2, 1, 5, 6, 4], k = 2

Process 3: heap=[3]
Process 2: heap=[2,3]
Process 1: heap=[2,3] (1 < 2, skip)
Process 5: heap=[3,5] (pop 2, push 5)
Process 6: heap=[5,6] (pop 3, push 6)
Process 4: heap=[5,6] (4 < 5, skip)

Top = 5 = 2nd largest ✓
```

### Top K Frequent Elements

```cpp
vector<int> topKFrequent(vector<int>& nums, int k) {
    unordered_map<int, int> freq;
    for (int n : nums) freq[n]++;

    // Min-heap by frequency
    auto cmp = [&](int a, int b) { return freq[a] > freq[b]; };
    priority_queue<int, vector<int>, decltype(cmp)> pq(cmp);

    for (auto& [num, count] : freq) {
        pq.push(num);
        if (pq.size() > k) pq.pop();
    }

    vector<int> result;
    while (!pq.empty()) {
        result.push_back(pq.top());
        pq.pop();
    }
    return result;
}
```

### Quick Select Alternative — O(n) average

```cpp
int findKthLargestQuickSelect(vector<int>& nums, int k) {
    k = nums.size() - k;  // Convert to kth smallest (0-indexed)

    function<int(int, int)> partition = [&](int lo, int hi) {
        int pivot = nums[hi], i = lo;
        for (int j = lo; j < hi; j++) {
            if (nums[j] <= pivot) swap(nums[i++], nums[j]);
        }
        swap(nums[i], nums[hi]);
        return i;
    };

    int lo = 0, hi = nums.size() - 1;
    while (true) {
        int p = partition(lo, hi);
        if (p == k) return nums[p];
        else if (p < k) lo = p + 1;
        else hi = p - 1;
    }
}
```

---

## 5. Pattern 2: Merge K Sorted Lists/Arrays

### Concept

```
Push head of each list into min-heap.
Repeatedly pop smallest, push its next node.
Like merge sort but with K pointers managed by heap.
```

### Template

```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    auto cmp = [](ListNode* a, ListNode* b) { return a->val > b->val; };
    priority_queue<ListNode*, vector<ListNode*>, decltype(cmp)> pq(cmp);

    for (ListNode* head : lists) {
        if (head) pq.push(head);
    }

    ListNode dummy(0);
    ListNode* tail = &dummy;

    while (!pq.empty()) {
        ListNode* node = pq.top();
        pq.pop();
        tail->next = node;
        tail = tail->next;

        if (node->next) pq.push(node->next);
    }
    return dummy.next;
}
```

**Complexity:** O(N log K) where N = total elements, K = number of lists.

---

## 6. Pattern 3: Two Heaps (Running Median)

### Concept

```
Maintain two heaps:
  maxHeap (left half)  — lower 50%
  minHeap (right half) — upper 50%

Balance: |maxHeap.size - minHeap.size| <= 1
Median: top of larger heap, or average of both tops
```

### Template

```cpp
class MedianFinder {
    priority_queue<int> maxLeft;  // Lower half
    priority_queue<int, vector<int>, greater<int>> minRight;  // Upper half

public:
    void addNum(int num) {
        maxLeft.push(num);

        // Balance: every maxLeft element should be <= minRight
        minRight.push(maxLeft.top());
        maxLeft.pop();

        // Ensure maxLeft has equal or one more element
        if (maxLeft.size() < minRight.size()) {
            maxLeft.push(minRight.top());
            minRight.pop();
        }
    }

    double findMedian() {
        if (maxLeft.size() > minRight.size()) return maxLeft.top();
        return (maxLeft.top() + minRight.top()) / 2.0;
    }
};
```

**Dry Run:**

```
addNum(1): maxLeft=[1], minRight=[]
addNum(2): maxLeft=[1], minRight=[2]
addNum(3): maxLeft=[2,1], minRight=[3]
Median = 2 ✓

addNum(4): maxLeft=[2,1], minRight=[3,4]
Median = (2+3)/2 = 2.5 ✓
```

---

## 7. Pattern 4: Heap Sort

### Algorithm

```
1. Build max-heap from array     O(n)
2. Repeatedly swap root with last element, reduce heap size, heapify down
```

```cpp
void heapSort(vector<int>& arr) {
    int n = arr.size();
    buildMaxHeap(arr);

    for (int i = n - 1; i > 0; i--) {
        swap(arr[0], arr[i]);
        heapifyDown(arr, 0, i);
    }
}
```

**Properties:**
- Time: O(n log n) worst case
- Space: O(1) in-place
- Not stable

---

## 8. Pattern 5: Dijkstra with Priority Queue

### Concept

```
Shortest path in weighted graph:
Use min-heap keyed by distance.
Always process closest unvisited node first.
```

```cpp
vector<int> dijkstra(vector<vector<pair<int,int>>>& graph, int src) {
    int n = graph.size();
    vector<int> dist(n, INT_MAX);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;

    dist[src] = 0;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [d, node] = pq.top();
        pq.pop();

        if (d > dist[node]) continue;  // Stale entry

        for (auto [neighbor, weight] : graph[node]) {
            if (dist[node] + weight < dist[neighbor]) {
                dist[neighbor] = dist[node] + weight;
                pq.push({dist[neighbor], neighbor});
            }
        }
    }
    return dist;
}
```

---

## 9. Pattern 6: Scheduling & Greedy with Heap

### Meeting Rooms II

```cpp
int minMeetingRooms(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());

    priority_queue<int, vector<int>, greater<int>> minHeap;  // End times

    for (auto& interval : intervals) {
        if (!minHeap.empty() && minHeap.top() <= interval[0]) {
            minHeap.pop();  // Reuse room
        }
        minHeap.push(interval[1]);
    }
    return minHeap.size();
}
```

### Task Scheduler (with cooldown)

Use max-heap for task frequencies + queue for cooldown tracking.

---

## 10. Pattern 7: Custom Comparator Patterns

### Sort by Multiple Criteria

```cpp
struct Event {
    int time;
    bool isStart;
};

auto cmp = [](Event& a, Event& b) {
    if (a.time != b.time) return a.time > b.time;
    return a.isStart < b.isStart;  // End before start at same time
};
priority_queue<Event, vector<Event>, decltype(cmp)> pq(cmp);
```

### K Closest Points to Origin

```cpp
vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
    auto cmp = [](vector<int>& a, vector<int>& b) {
        return a[0]*a[0] + a[1]*a[1] > b[0]*b[0] + b[1]*b[1];
    };
    priority_queue<vector<int>, vector<vector<int>>, decltype(cmp)> pq(cmp);

    for (auto& p : points) {
        pq.push(p);
        if (pq.size() > k) pq.pop();
    }

    vector<vector<int>> result;
    while (!pq.empty()) { result.push_back(pq.top()); pq.pop(); }
    return result;
}
```

---

## 11. Problem Recognition Framework

```
START: Should I use a Heap?
│
├─ Need K largest/smallest elements?
│   └─ YES → Size-K heap (min for largest, max for smallest)
│
├─ Merge K sorted sequences?
│   └─ YES → Min-heap with K elements
│
├─ Running median or two-stream median?
│   └─ YES → Two heaps pattern
│
├─ Shortest path in weighted graph?
│   └─ YES → Dijkstra with min-heap
│
├─ Scheduling / interval overlap?
│   └─ YES → Sort + min-heap on end times
│
├─ Continuously need global min/max?
│   └─ YES → Heap or multiset
│
└─ Top K by custom criteria?
    └─ YES → Custom comparator heap
```

### Pattern Selection Table

| Problem Type | Heap Type | Time | Space |
|-------------|-----------|------|-------|
| Kth largest | Min-heap size K | O(n log k) | O(k) |
| Top K frequent | Min-heap size K | O(n log k) | O(k) |
| Merge K lists | Min-heap size K | O(N log k) | O(k) |
| Running median | Two heaps | O(n log n) | O(n) |
| Dijkstra | Min-heap | O(E log V) | O(V) |
| Meeting rooms | Min-heap end times | O(n log n) | O(n) |
| Heap sort | Max-heap in-place | O(n log n) | O(1) |

---

## 12. Practice Problems

### Easy
1. Kth Largest Element in an Array - https://leetcode.com/problems/kth-largest-element-in-an-array/
2. Last Stone Weight - https://leetcode.com/problems/last-stone-weight/
3. Kth Largest Element in a Stream - https://leetcode.com/problems/kth-largest-element-in-a-stream/

### Medium
4. Top K Frequent Elements - https://leetcode.com/problems/top-k-frequent-elements/
5. K Closest Points to Origin - https://leetcode.com/problems/k-closest-points-to-origin/
6. Merge K Sorted Lists - https://leetcode.com/problems/merge-k-sorted-lists/
7. Find Median from Data Stream - https://leetcode.com/problems/find-median-from-data-stream/
8. Task Scheduler - https://leetcode.com/problems/task-scheduler/
9. Meeting Rooms II - https://leetcode.com/problems/meeting-rooms-ii/
10. Reorganize String - https://leetcode.com/problems/reorganize-string/
11. Sort Characters By Frequency - https://leetcode.com/problems/sort-characters-by-frequency/
12. Ugly Number II - https://leetcode.com/problems/ugly-number-ii/
13. Network Delay Time - https://leetcode.com/problems/network-delay-time/
14. Cheapest Flights Within K Stops - https://leetcode.com/problems/cheapest-flights-within-k-stops/

### Hard
15. Find Median from Data Stream - https://leetcode.com/problems/find-median-from-data-stream/
16. Sliding Window Median - https://leetcode.com/problems/sliding-window-median/
17. Trapping Rain Water II - https://leetcode.com/problems/trapping-rain-water-ii/
18. Minimum Cost to Hire K Workers - https://leetcode.com/problems/minimum-cost-to-hire-k-workers/

---

## 13. Common Pitfalls

### Pitfall 1: Wrong Heap Type for Top K

```cpp
// WRONG: Max-heap for K largest (keeps K smallest!)
priority_queue<int> pq;

// RIGHT: Min-heap for K largest
priority_queue<int, vector<int>, greater<int>> pq;
```

### Pitfall 2: Comparator Direction in C++

```cpp
// C++ priority_queue is MAX-heap by default
// For min-heap on pairs, comparator must return true if a > b
auto cmp = [](int a, int b) { return a > b; };  // "a is worse than b"
```

### Pitfall 3: Not Handling Stale Entries in Dijkstra

```cpp
// WRONG: Process without checking
auto [d, node] = pq.top(); pq.pop();
// process node...

// RIGHT: Skip stale entries
if (d > dist[node]) continue;
```

### Pitfall 4: Forgetting to Maintain Heap Size K

```cpp
// WRONG: Heap grows unbounded
pq.push(num);

// RIGHT: Maintain size K
pq.push(num);
if (pq.size() > k) pq.pop();
```

---

## 14. Template Summary

### Top K Largest (Min-Heap)
```cpp
priority_queue<int, vector<int>, greater<int>> pq;
for (int num : nums) {
    pq.push(num);
    if (pq.size() > k) pq.pop();
}
return pq.top();
```

### Merge K Sorted
```cpp
auto cmp = [](Node* a, Node* b) { return a->val > b->val; };
priority_queue<Node*, vector<Node*>, decltype(cmp)> pq(cmp);
// push heads, pop min, push next
```

### Two Heaps Median
```cpp
priority_queue<int> maxLeft;
priority_queue<int, vector<int>, greater<int>> minRight;
// add to maxLeft, balance to minRight, rebalance sizes
```

### Dijkstra
```cpp
priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
pq.push({0, src});
while (!pq.empty()) {
    auto [d, u] = pq.top(); pq.pop();
    if (d > dist[u]) continue;
    // relax neighbors
}
```

---

## Final Thoughts

**The Power of Heap Patterns:**
- O(n log k) beats O(n log n) sorting when k << n
- Two heaps elegantly maintain running median
- Priority queue is Dijkstra's best friend
- Custom comparators unlock complex scheduling problems

**How to Master:**
1. Remember: min-heap for K largest, max-heap for K smallest
2. Size-K heap is almost always better than full sort
3. Practice custom comparators until they're automatic
4. Know when Quick Select beats heap (one-time Kth vs streaming)

---

*Heaps give you instant access to extremes — master them for Top K dominance!*
