# Graph Pattern: A Reasoning-First C++ Guide

A graph models relationships. Vertices represent objects and edges represent connections between them. Most graph problems become manageable after answering three questions:

1. Is the graph directed or undirected?
2. Are edges weighted or unweighted?
3. What does the problem ask for: reachability, ordering, shortest cost, connectivity, or a spanning structure?

The algorithm should follow from those answers, not from memorizing a list of names.

## Table of Contents

1. [Graph Mental Model](#1-graph-mental-model)
2. [Representing a Graph](#2-representing-a-graph)
3. [BFS and DFS](#3-bfs-and-dfs)
4. [Connected Components and Grid Graphs](#4-connected-components-and-grid-graphs)
5. [Cycle Detection](#5-cycle-detection)
6. [Topological Sorting](#6-topological-sorting)
7. [Shortest Paths](#7-shortest-paths)
8. [Disjoint Set Union](#8-disjoint-set-union)
9. [Minimum Spanning Tree](#9-minimum-spanning-tree)
10. [Problem Recognition Checklist](#10-problem-recognition-checklist)
11. [Common Pitfalls](#11-common-pitfalls)
12. [Practice Roadmap](#12-practice-roadmap)

---

## 1. Graph Mental Model

A graph is `G = (V, E)`: a set of vertices and relationships between them.

```text
Undirected:                 Directed:
A ----- B                   A ----> B
 \     /                    ^       |
  \   /                     |       v
    C                       C <----- D
```

An undirected edge means movement works in both directions. A directed edge gives permission in one direction only.

### The Algorithm Decision Map

```text
What is the graph asking for?
        |
        +--> Visit/reach every possible node?       BFS or DFS
        |
        +--> Count separated groups?                BFS/DFS or DSU
        |
        +--> Is there a cycle?                      DFS / indegree / DSU
        |
        +--> Order prerequisites?                   Topological sort
        |
        +--> Unweighted shortest path?              BFS
        |
        +--> Non-negative weighted shortest path?   Dijkstra
        |
        +--> Negative edges allowed?                Bellman-Ford
        |
        +--> All-pairs shortest paths?              Floyd-Warshall
        |
        +--> Connect all nodes as cheaply as possible? MST
```

The word `visited` does not mean the same thing in every algorithm. In ordinary traversal it prevents repeated work; in directed cycle detection, a node can be visited but still be active in the current recursion path.

---

## 2. Representing a Graph

### Adjacency List

Store neighbors for each vertex. This is the usual choice for sparse graphs.

```cpp
vector<vector<int>> graph(n);

for (auto edge : edges) {
    int u = edge[0];
    int v = edge[1];
    graph[u].push_back(v);
    graph[v].push_back(u);  // remove for a directed graph
}
```

Space is `O(V + E)`. Traversing all edges is also `O(V + E)`.

For weighted edges:

```cpp
vector<vector<pair<int, int>>> graph(n); // {neighbor, weight}
graph[u].push_back({v, weight});
```

### Adjacency Matrix

`matrix[u][v]` directly answers whether an edge exists, but costs `O(V^2)` space. Use it when the graph is dense or constant-time edge lookup matters.

### Edge List

A list of `{u, v, weight}` is ideal when sorting edges, as in Kruskal's minimum spanning tree algorithm or Bellman-Ford.

---

## 3. BFS and DFS

### Breadth-First Search: Explore by Distance

BFS uses a queue. Every node is processed after all nodes at the previous distance, so in an unweighted graph the first time a node is reached is through a shortest path.

```cpp
vector<int> bfs(int start, const vector<vector<int>>& graph) {
    vector<int> order;
    vector<bool> visited(graph.size(), false);
    queue<int> nodes;

    visited[start] = true;
    nodes.push(start);

    while (!nodes.empty()) {
        int current = nodes.front();
        nodes.pop();
        order.push_back(current);

        for (int neighbor : graph[current]) {
            if (visited[neighbor]) continue;
            visited[neighbor] = true;
            nodes.push(neighbor);
        }
    }
    return order;
}
```

Mark a node visited when enqueuing, not when dequeuing. Otherwise several parents can enqueue the same node.

### BFS Shortest Path in an Unweighted Graph

```cpp
vector<int> shortestDistance(int start, const vector<vector<int>>& graph) {
    vector<int> distance(graph.size(), -1);
    queue<int> nodes;
    distance[start] = 0;
    nodes.push(start);

    while (!nodes.empty()) {
        int current = nodes.front();
        nodes.pop();
        for (int neighbor : graph[current]) {
            if (distance[neighbor] != -1) continue;
            distance[neighbor] = distance[current] + 1;
            nodes.push(neighbor);
        }
    }
    return distance;
}
```

### Depth-First Search: Explore a Branch Completely

DFS uses recursion or an explicit stack. It is natural for components, cycle detection, topological ordering, and path constraints.

```cpp
void dfs(int current, const vector<vector<int>>& graph,
         vector<bool>& visited, vector<int>& order) {
    visited[current] = true;
    order.push_back(current);

    for (int neighbor : graph[current]) {
        if (!visited[neighbor]) {
            dfs(neighbor, graph, visited, order);
        }
    }
}
```

The recursive call means: "the neighbor's entire reachable region will be solved before I continue." The visited array guarantees each vertex is expanded once.

Complexity for both BFS and DFS is `O(V + E)` with an adjacency list.

---

## 4. Connected Components and Grid Graphs

If the graph may be disconnected, start a traversal from every unvisited vertex.

```cpp
int countComponents(const vector<vector<int>>& graph) {
    vector<bool> visited(graph.size(), false);
    int components = 0;

    for (int vertex = 0; vertex < static_cast<int>(graph.size()); ++vertex) {
        if (visited[vertex]) continue;
        ++components;
        queue<int> nodes;
        nodes.push(vertex);
        visited[vertex] = true;

        while (!nodes.empty()) {
            int current = nodes.front();
            nodes.pop();
            for (int neighbor : graph[current]) {
                if (visited[neighbor]) continue;
                visited[neighbor] = true;
                nodes.push(neighbor);
            }
        }
    }
    return components;
}
```

A grid is an implicit graph: each cell is a vertex and valid neighboring cells are edges. You do not need to build adjacency lists; generate neighbors using directions.

```cpp
const vector<pair<int, int>> directions = {
    {-1, 0}, {1, 0}, {0, -1}, {0, 1}
};

for (auto [rowDelta, columnDelta] : directions) {
    int nextRow = row + rowDelta;
    int nextColumn = column + columnDelta;
    if (nextRow < 0 || nextRow >= rows ||
        nextColumn < 0 || nextColumn >= columns) continue;
    // Process the valid neighboring cell.
}
```

The boundary check exists because the grid is the graph; outside the grid there is no vertex.

---

## 5. Cycle Detection

### Undirected Graph: DFS with Parent

A visited neighbor is a cycle only when it is not the edge used to reach the current node.

```cpp
bool hasCycle(int current, int parent,
              const vector<vector<int>>& graph,
              vector<bool>& visited) {
    visited[current] = true;

    for (int neighbor : graph[current]) {
        if (!visited[neighbor]) {
            if (hasCycle(neighbor, current, graph, visited)) return true;
        } else if (neighbor != parent) {
            return true;
        }
    }
    return false;
}
```

### Directed Graph: Three-State DFS

Use three states:

- `0`: never visited
- `1`: currently in the recursion path
- `2`: completely processed

An edge to state `1` goes back into the active path, so it is a directed cycle.

```cpp
bool hasDirectedCycle(int current, const vector<vector<int>>& graph,
                      vector<int>& state) {
    state[current] = 1;

    for (int neighbor : graph[current]) {
        if (state[neighbor] == 1) return true;
        if (state[neighbor] == 0 && hasDirectedCycle(neighbor, graph, state)) {
            return true;
        }
    }

    state[current] = 2;
    return false;
}
```

The second state is necessary: "visited before" is not enough to distinguish an active ancestor from a finished branch.

---

## 6. Topological Sorting

A topological order exists only for a directed acyclic graph. It places every prerequisite before the work that depends on it.

### Kahn's Algorithm: Indegree Reasoning

`indegree[v]` counts how many prerequisites still point into `v`. A node with indegree zero is currently safe to process.

```cpp
vector<int> topologicalSort(const vector<vector<int>>& graph) {
    vector<int> indegree(graph.size(), 0);
    for (const auto& neighbors : graph) {
        for (int neighbor : neighbors) ++indegree[neighbor];
    }

    queue<int> ready;
    for (int vertex = 0; vertex < static_cast<int>(graph.size()); ++vertex) {
        if (indegree[vertex] == 0) ready.push(vertex);
    }

    vector<int> order;
    while (!ready.empty()) {
        int current = ready.front();
        ready.pop();
        order.push_back(current);

        for (int neighbor : graph[current]) {
            if (--indegree[neighbor] == 0) ready.push(neighbor);
        }
    }

    if (order.size() != graph.size()) return {}; // a cycle exists
    return order;
}
```

Why does the cycle check work? A cycle has no valid first node: every node in it waits for another node in the same cycle, so at least one vertex remains with positive indegree.

DFS can also produce a topological order by pushing a node after all its outgoing neighbors finish, then reversing the result.

---

## 7. Shortest Paths

Choose the algorithm from the edge weights:

| Graph condition | Algorithm | Reason |
|---|---|---|
| Unweighted | BFS | Each edge costs one step |
| Weights are non-negative | Dijkstra | The cheapest unprocessed node is final |
| Negative edges allowed | Bellman-Ford | Repeated relaxation handles negative edges |
| All pairs, small `V` | Floyd-Warshall | Try every intermediate vertex |
| DAG | Topological DP | Process dependencies once |

### Dijkstra's Algorithm

The priority queue always gives the smallest currently known distance. With non-negative weights, no later path can make that finalized distance smaller.

```cpp
vector<long long> dijkstra(
    int start, const vector<vector<pair<int, int>>>& graph) {
    const long long infinity = numeric_limits<long long>::max() / 4;
    vector<long long> distance(graph.size(), infinity);
    priority_queue<pair<long long, int>,
                   vector<pair<long long, int>>,
                   greater<pair<long long, int>>> next;

    distance[start] = 0;
    next.push({0, start});

    while (!next.empty()) {
        auto [currentDistance, current] = next.top();
        next.pop();
        if (currentDistance != distance[current]) continue;

        for (auto [neighbor, weight] : graph[current]) {
            long long candidate = currentDistance + weight;
            if (candidate >= distance[neighbor]) continue;
            distance[neighbor] = candidate;
            next.push({candidate, neighbor});
        }
    }
    return distance;
}
```

The stale-entry check is needed because priority queues do not support decreasing a key in place; an improved distance creates a new entry.

Never use Dijkstra when negative edge weights are possible. Its finalization proof depends on weights being non-negative.

---

## 8. Disjoint Set Union

DSU maintains groups under two operations:

- `find(x)`: identify the representative of `x`'s group.
- `unite(a, b)`: merge two groups.

Path compression and union by size make operations almost constant amortized time.

```cpp
class DisjointSet {
    vector<int> parent;
    vector<int> size;

public:
    explicit DisjointSet(int n) : parent(n), size(n, 1) {
        iota(parent.begin(), parent.end(), 0);
    }

    int find(int vertex) {
        if (parent[vertex] == vertex) return vertex;
        return parent[vertex] = find(parent[vertex]);
    }

    bool unite(int first, int second) {
        first = find(first);
        second = find(second);
        if (first == second) return false;
        if (size[first] < size[second]) swap(first, second);
        parent[second] = first;
        size[first] += size[second];
        return true;
    }
};
```

`unite` returning `false` is useful: the edge connected two vertices already in the same group, so adding it would create a cycle.

---

## 9. Minimum Spanning Tree

For a connected, undirected, weighted graph, an MST connects every vertex with minimum total edge weight and no cycle.

### Kruskal's Algorithm

1. Sort edges by weight.
2. Take the next lightest edge if it connects two different DSU groups.
3. Stop after `V - 1` accepted edges.

The reason this is safe is the cut property: the lightest edge crossing a cut can belong to some MST. DSU makes the cycle check efficient.

Complexity is `O(E log E)` because of sorting.

Prim's algorithm grows one connected tree from a starting vertex, repeatedly taking the cheapest edge leaving the tree. It is a natural fit for adjacency lists and a priority queue.

---

## 10. Problem Recognition Checklist

Before coding, write down:

- Is the graph directed or undirected?
- Are repeated edges or self-loops possible?
- Is the graph weighted? Can weights be negative?
- Is the graph guaranteed connected?
- Do I need one path, all reachable nodes, or all pairs?
- Is the question about minimum number of edges, minimum total cost, or merely reachability?
- What should `visited`, `distance`, `indegree`, or DSU represent?
- What is the complexity in terms of `V` and `E`?

### Complexity Summary

| Task | Typical tool | Complexity |
|---|---|---|
| Traverse graph | BFS / DFS | `O(V + E)` |
| Unweighted shortest path | BFS | `O(V + E)` |
| Topological order | Kahn / DFS | `O(V + E)` |
| Non-negative shortest path | Dijkstra | `O((V + E) log V)` |
| Negative-edge shortest path | Bellman-Ford | `O(VE)` |
| Minimum spanning tree | Kruskal | `O(E log E)` |
| Dynamic connectivity | DSU | Near `O(1)` amortized per operation |

---

## 11. Common Pitfalls

1. **Forgetting disconnected components:** one BFS/DFS from vertex zero may not visit the whole graph.
2. **Marking visited too late:** in BFS, mark when enqueuing to avoid duplicate queue entries.
3. **Using one cycle rule everywhere:** undirected graphs need a parent check; directed graphs need active-path state.
4. **Using Dijkstra with negative weights:** its greedy finalization proof no longer holds.
5. **Confusing shortest edges with shortest weight:** BFS minimizes edge count, not weighted cost.
6. **Mutating a shared grid incorrectly:** mark a cell before exploring it and restore it only when the problem requires path-specific reuse.
7. **Ignoring integer overflow:** use `long long` for path costs.
8. **Assuming a topological order always exists:** a cycle makes the ordering impossible.

---

## 12. Practice Roadmap

1. Number of Islands
2. Flood Fill
3. Clone Graph
4. Rotting Oranges
5. Course Schedule I and II
6. Graph Valid Tree
7. Network Delay Time
8. Cheapest Flights Within K Stops
9. Redundant Connection
10. Min Cost to Connect All Points
11. Word Ladder
12. Critical Connections in a Network

## Key Takeaways

- Model the problem as vertices, edges, and the exact meaning of an edge.
- BFS is the default for unweighted shortest distance; DFS is the natural tool for structure and dependencies.
- A graph's weight rules decide the shortest-path algorithm.
- Cycle detection needs different state for directed and undirected graphs.
- Topological sorting is dependency resolution and is possible only for DAGs.
- DSU tracks changing connectivity; MST algorithms use it to avoid cycles.
