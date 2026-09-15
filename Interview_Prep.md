# DSA Interview Preparation: A Practical Playbook

This guide is for using the pattern notes in a real interview. The goal is not to recognize a familiar title and immediately type a template. The goal is to make your reasoning visible, choose the smallest correct model, and verify it before optimizing.

## 1. The Interview Loop

```text
Understand -> Model -> Brute force -> Improve -> Prove -> Code -> Test -> Analyze
```

Do not skip the first two steps. Most wrong solutions are caused by solving a slightly different problem very quickly.

### Step 1: Clarify the Contract

Ask concise questions:

- What are the input size limits?
- Can values be negative, zero, duplicated, or empty?
- Is the input sorted? Is the graph directed, weighted, or disconnected?
- Should I return an index, a value, all answers, or only whether an answer exists?
- Can I modify the input?
- Are there multiple valid answers, and does their order matter?

Then restate the problem:

> "Given ..., I need to return ...; the important constraints are ...; I will assume ... unless you want a different behavior."

### Step 2: Build a Small Example

Use an example that exposes the decision:

- Include an empty or singleton input when relevant.
- Include duplicates and boundary values.
- For graphs, include a disconnected component or a cycle.
- For DP, write the smallest states and their dependencies.

Do not use only the sample from the prompt. Create one adversarial example yourself.

### Step 3: State the Invariant

An invariant is what remains true while the algorithm runs.

Examples:

- Sliding window: the current window satisfies the required condition.
- Binary search: the answer remains inside the search interval.
- Monotonic stack: items left in the stack are ordered by the chosen property.
- BFS: nodes leave the queue in nondecreasing distance order.
- Greedy interval scheduling: selected intervals are compatible and finish as early as possible.
- DP: `dp[state]` has the exact meaning written in its definition.

Say the invariant before coding. It makes debugging much easier.

## 2. A 35-Minute Timebox

Adjust the minutes to the interview, but keep the order.

| Time | Action | Deliverable |
|---|---|---|
| 0-3 | Clarify and restate | Exact input/output contract |
| 3-7 | Brute-force idea | Correct baseline and complexity |
| 7-12 | Pattern recognition | State, invariant, or data structure |
| 12-17 | Explain optimization | Why the faster method is correct |
| 17-29 | Code | Readable implementation |
| 29-34 | Dry run | Normal and adversarial examples |
| 34-35 | Complexity | Time, space, and tradeoffs |

If the optimal solution is not clear, code the correct baseline first. A working `O(n^2)` solution gives you something to test and improve; an untested clever idea gives you neither.

## 3. Pattern Recognition Matrix

| Signal in the problem | First pattern to consider | Reasoning question |
|---|---|---|
| Sorted data or monotonic answer | Binary search | What predicate changes from false to true? |
| Pair/triplet or opposite ends | Two pointers | Which movement safely removes candidates? |
| Contiguous subarray/substring | Sliding window | When is the window invalid, and how does it recover? |
| Next greater/smaller relationship | Monotonic stack | Which elements are waiting for an answer? |
| Top K, repeatedly choose min/max | Heap | What must remain available after each operation? |
| Locally best choice with no undo | Greedy | Can I prove an exchange or staying-ahead argument? |
| All choices, valid partial solution | Backtracking | What are the choices, constraints, and undo operations? |
| Same state appears repeatedly | Dynamic programming | What variables completely describe the remaining problem? |
| Hierarchy or parent/child structure | Tree DFS/BFS | Does information flow down, up, or by level? |
| Nodes and relationships | Graph traversal | Is this reachability, ordering, shortest path, or connectivity? |
| Dynamic group merging | DSU | Do I only need component membership? |
| Repeated range or interval splits | Interval DP | What operation happens last? |

This table suggests a hypothesis, not a conclusion. Prove the chosen invariant and test it against a counterexample.

## 4. How to Explain the Solution

Use this speaking structure:

1. **Observation:** "The important property is ..."
2. **State:** "I will maintain ...; it means ..."
3. **Transition:** "When ..., I update it by ... because ..."
4. **Correctness:** "This is safe because ..."
5. **Termination:** "When ..., every required candidate has been handled."
6. **Complexity:** "Each ... is processed ..., so time is ... and extra space is ..."

Avoid narrating every keystroke. Explain decisions, invariants, and tradeoffs.

### Example: Sliding Window Explanation

> "I maintain a window with at most `k` distinct values. I expand the right side, record its frequency, and shrink from the left while the invariant is broken. After shrinking, the window is valid, so its length is a candidate answer. Each index enters and leaves once, giving `O(n)` time."

### Example: DP Explanation

> "`dp[i]` is the best answer for the first `i` items. The current item is either excluded or included, so the transition compares those two choices. The base case is the empty prefix. Since every state depends on smaller prefixes, filling left to right is valid."

## 5. Brute Force to Optimal

Before optimizing, name what the brute-force method repeats or wastes.

```text
Brute force                  Remove the waste with
-------------------------------------------------------------
Try every pair              Two pointers, hashing, or sorting
Recompute each window       Sliding window or prefix sums
Repeat the same subproblem  Memoization / DP
Explore invalid branches    Backtracking pruning
Scan all possible answers   Binary search on the answer
Repeated min/max extraction Heap
Repeated component search   DSU
```

An optimization is convincing only when you can say exactly what repeated work disappeared.

## 6. Correctness Proof Shortcuts

### Exchange Argument: Greedy

1. Take an optimal solution that may not use the greedy choice.
2. Replace its first differing choice with the greedy choice.
3. Show feasibility is preserved and quality does not worsen.
4. Repeat for the remaining problem.

If step 3 fails, greedy may be wrong. Try DP or search.

### Induction on a Smaller State: Recursion and DP

1. Define exactly what `solve(state)` returns.
2. Assume recursive answers for smaller states are correct.
3. Show the transition considers every legal choice.
4. Show the base state has the correct direct answer.

### Search Invariant: Binary Search

At the start of every iteration, the answer is still inside `[left, right]` or the chosen half-open interval. Every discarded half must be proven unable to contain the answer.

### State-Space Argument: Backtracking

At each level, enumerate every legal choice. Pruning removes only choices that cannot lead to a valid answer. Undo restores the exact parent state before the next choice.

## 7. Testing Before You Finish

Test categories, not just examples:

- Empty input and the smallest valid input
- One element or one node
- All values equal
- Strictly increasing and strictly decreasing values
- Duplicates
- Negative values and zeros
- Maximum values and overflow risk
- No solution, one solution, and many solutions
- Disconnected graph, cycle, self-loop, and parallel edge when applicable
- Skewed tree and a balanced tree

For every test, state the expected result before running the code. A dry run should follow the invariant, not merely repeat the code aloud.

## 8. Complexity Discipline

Always state both:

- **Time:** include sorting, heap operations, recursion branching, and graph edges.
- **Auxiliary space:** exclude the returned answer unless the interviewer asks for total memory; include recursion stack, queues, maps, and DP tables.

### Guessing the Target Complexity from `n`

The input limit is a strong hint about the intended algorithm. These are rough interview heuristics for a typical 1-2 second limit in C++; constant factors, language, and the actual operation still matter.

| Largest relevant `n` | Usually acceptable first target | Usually suspicious |
|---:|---|---|
| `n <= 10` | Backtracking, `O(2^n)`, `O(n!)` when clearly bounded | No meaningful search at all |
| `n <= 20` | `O(2^n * n)` or meet-in-the-middle | `O(3^n)` without a tight bound |
| `n <= 40` | Meet-in-the-middle, `O(n 2^(n/2))` | Full `O(2^n)` enumeration |
| `n <= 200` | `O(n^3)` may fit; inspect constants | Exponential recursion |
| `n <= 1,000` | `O(n^2)` often fits; `O(n^3)` needs care | `O(n^4)` |
| `n <= 100,000` | `O(n log n)` or `O(n)` | `O(n^2)` |
| `n <= 1,000,000` | `O(n)` or sometimes `O(n log n)` | Nested scans over all pairs |
| `n` is a huge value, answer is monotonic | `O(log n)` or `O(log answer)` | Scanning every possible answer |

Treat this as a direction, not a law. A simple `O(n^2)` loop can beat a complicated `O(n log n)` method for small `n`, and a graph with `V = 10^5` but `E = 10^6` must be analyzed using both `V` and `E`.

### Reading Complexity from Code

```text
One pass over n items                         -> O(n)
Two independent nested passes                 -> O(n^2)
for (i = 0; i < n; i++) for (j = i; j < n; j++)
												-> O(n^2), not O(n!)
Repeatedly halve or double the search range   -> O(log n)
Sort, then scan                              -> O(n log n)
Each item enters and leaves a window/stack    -> O(n) amortized
BFS/DFS with adjacency list                  -> O(V + E)
Two DP dimensions of sizes n and m            -> O(nm) states
Each DP state tries k choices                 -> O(nmk)
Binary recursion with two independent calls   -> often O(2^n)
```

Nested loops are not automatically `O(n^2)`: if the inner loop moves a pointer that never moves backward, the total work can still be `O(n)`. Count total pointer movement, not just indentation.

### Space Complexity: What to Count

Ask: **what memory grows when the input grows?**

| Code structure | Extra space |
|---|---:|
| A few variables | `O(1)` |
| Hash map/set storing input-related values | `O(n)` |
| Output array of `n` values | Usually report separately; auxiliary space is `O(1)` if ignored |
| Recursion depth `h` | `O(h)` stack space |
| BFS queue over a graph | Up to `O(V)` |
| Adjacency list | `O(V + E)` |
| `n x m` DP table | `O(nm)` |
| Backtracking path of maximum depth `d` | `O(d)` excluding returned answers |

If the interviewer asks for total space, include the output. If they ask for auxiliary space, say explicitly that the returned result is excluded. This removes ambiguity instead of hoping the interviewer assumes your convention.

### A Reliable Complexity Routine

1. Name the main input sizes: `n`, `m`, `V`, `E`, or capacity `W`.
2. Count how many times each loop or recursive state executes.
3. Multiply costs only when loops are independent; add costs for sequential phases.
4. Include sorting, hashing, heap operations, and recursion stack.
5. Drop constants and lower-order terms, but mention an important practical constant.
6. State worst case unless the question explicitly asks for average or amortized cost.

Common facts:

- A sort usually costs `O(n log n)`.
- A hash table is expected `O(1)` per operation, not guaranteed worst-case `O(1)`.
- BFS/DFS with adjacency lists is `O(V + E)`.
- A recursion tree may be exponential even when each call is small.
- Space optimization is optional after correctness; never trade a clear correct table for a fragile one-dimensional update too early.

## 9. Interview-Ready Templates to Memorize

Memorize the questions and invariants, not entire solutions:

- Binary search: what is the monotonic predicate?
- Sliding window: when does the window become invalid?
- Two pointers: why can moving this pointer discard candidates safely?
- Stack: what unresolved elements are represented by the stack?
- Heap: what candidates must remain in the top set?
- Greedy: what proof preserves an optimal solution?
- Backtracking: what choice is made, what constraint is checked, and what is undone?
- DP: what does one state mean, and which smaller states does it depend on?
- Tree: does the node need child answers or parent context?
- Graph: what do visited, distance, indegree, or DSU representative mean?

## 10. After-Problem Review

Record this after every serious problem:

```text
Problem:
Pattern I considered:
Pattern that actually worked:
State / invariant:
Why the solution is correct:
Time and auxiliary space:
The first wrong idea I had:
The edge case I missed:
What I will recognize next time:
Revisit date:
```

A problem is not fully learned when the code passes once. It is learned when you can recognize the structure, explain the invariant, re-code it later, and adapt it to a changed constraint.

## Final Interview Checklist

- [ ] I restated the problem and clarified assumptions.
- [ ] I gave a correct baseline before optimizing.
- [ ] I named the invariant or DP state.
- [ ] I explained why the transition is safe.
- [ ] I wrote readable code with meaningful names.
- [ ] I tested an edge case and an adversarial case.
- [ ] I stated time and auxiliary space accurately.
- [ ] I can explain what would change if the constraints changed.
