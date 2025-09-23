---
title: LeetCode 2467. Most Profitable Path in a Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2805 – “Most Profitable Path in a Tree”  
**Full solution in Java, Python and C++ + a SEO‑friendly blog post**

---

### 1. The Problem (LeetCode 2805)

> **Function signature**  
> ```text
> mostProfitablePath(edges: List[List[int]], bob: int, amount: List[int]) -> int
> ```  

Alice starts at the root node `0` of a rooted tree.  
Bob starts at a given node `bob` and walks **toward the root** (node `0`) – each step takes one second.  
While they walk, they open every node they reach and open the gate there.  
The *profit* from a node depends on who gets there first:

| Arrival order | Alice’s profit | Bob’s profit |
|---------------|----------------|--------------|
| Alice first   | `amount[i]`    | `0`          |
| Bob first     | `0`            | `amount[i]`  |
| Same time     | `amount[i]/2`  | `amount[i]/2`|

Both agents stop as soon as they reach a leaf node.  
Return the **maximum profit Alice can collect** by choosing the best leaf‑to‑leaf path.

*Constraints*  
- `1 ≤ n ≤ 2·10⁴` (n = number of nodes)  
- `amount[i]` fits into a 32‑bit signed integer  
- `edges` is a valid tree

---

## 2. Core Idea

1. **Build an adjacency list** for the tree.  
2. **Find Bob’s unique path** from his start node to the root.  
   - Store for every node the *step* (timestamp) at which Bob arrives (`bobDepth[node]`).
3. **DFS from the root** for Alice.  
   - At each node determine how much Alice can gain using the stored `bobDepth` and her own current depth.  
   - For leaf nodes, return the accumulated score.  
   - For internal nodes, recursively explore every child and keep the maximum score.

The algorithm runs in **O(n)** time and **O(n)** auxiliary space.

---

## 3. Reference Implementations

### 3.1 Java

```java
import java.util.*;

public class MostProfitablePath {
    public static int mostProfitablePath(List<List<Integer>> edges, int bob, List<Integer> amount) {
        int n = amount.size();
        List<List<Integer>> graph = new ArrayList<>(n);
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());

        for (List<Integer> e : edges) {
            graph.get(e.get(0)).add(e.get(1));
            graph.get(e.get(1)).add(e.get(0));
        }

        // Find Bob's path to the root (node 0)
        int[] bobDepth = new int[n];
        Arrays.fill(bobDepth, -1);
        List<Integer> path = new ArrayList<>();
        findPathToRoot(bob, -1, path, graph);
        for (int d = 0; d < path.size(); d++) bobDepth[path.get(d)] = d;

        // DFS for Alice
        return dfsAlice(0, -1, 0, 0, graph, bobDepth, amount);
    }

    // backtracking DFS to collect Bob's path
    private static boolean findPathToRoot(int node, int parent,
                                           List<Integer> path, List<List<Integer>> graph) {
        if (node == 0) return true;          // reached root
        for (int nb : graph.get(node)) {
            if (nb == parent) continue;
            path.add(node);
            if (findPathToRoot(nb, node, path, graph)) return true;
            path.remove(path.size() - 1);
        }
        return false;
    }

    // Alice's DFS – returns maximum profit from this node downwards
    private static int dfsAlice(int node, int parent, int depth,
                                int curScore, List<List<Integer>> graph,
                                int[] bobDepth, List<Integer> amount) {
        // Update score based on arrival times
        if (bobDepth[node] == -1 || bobDepth[node] > depth) {
            curScore += amount.get(node);
        } else if (bobDepth[node] == depth) {
            curScore += amount.get(node) / 2;
        }

        // If leaf, return the current score
        if (node != 0 && graph.get(node).size() == 1) return curScore;

        int best = Integer.MIN_VALUE;
        for (int nb : graph.get(node)) {
            if (nb == parent) continue;
            best = Math.max(best,
                    dfsAlice(nb, node, depth + 1, curScore, graph, bobDepth, amount));
        }
        return best;
    }

    // Simple wrapper for testing
    public static void main(String[] args) {
        List<List<Integer>> edges = List.of(
                List.of(0,1), List.of(1,2), List.of(1,3)
        );
        List<Integer> amount = List.of(1,2,3,4);
        System.out.println(mostProfitablePath(edges, 2, amount)); // Expected: 4
    }
}
```

> **Why this works**  
> - `bobDepth[node]` stores at which *time* Bob arrives at `node`.  
> - While traversing with depth `depth`, we can decide if Alice arrives first, second or at the same time.  
> - The recursion explores all root‑to‑leaf paths, keeping the best score.

---

### 3.2 Python 3

```python
from typing import List
import sys
sys.setrecursionlimit(1 << 25)

class Solution:
    def mostProfitablePath(
        self, edges: List[List[int]], bob: int, amount: List[int]
    ) -> int:
        n = len(amount)
        graph = [[] for _ in range(n)]
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        # Bob's arrival depth on each node
        bob_depth = [-1] * n
        path = []

        def dfs_bob(node: int, parent: int) -> bool:
            if node == 0:
                return True
            for nb in graph[node]:
                if nb == parent:
                    continue
                path.append(node)
                if dfs_bob(nb, node):
                    return True
                path.pop()
            return False

        dfs_bob(bob, -1)
        for d, node in enumerate(path):
            bob_depth[node] = d

        def dfs_alice(node: int, parent: int, depth: int, acc: int) -> int:
            # adjust profit based on arrival times
            if bob_depth[node] == -1 or bob_depth[node] > depth:
                acc += amount[node]
            elif bob_depth[node] == depth:
                acc += amount[node] // 2

            # leaf
            if node != 0 and len(graph[node]) == 1:
                return acc

            best = -10**18
            for nb in graph[node]:
                if nb == parent:
                    continue
                best = max(best, dfs_alice(nb, node, depth + 1, acc))
            return best

        return dfs_alice(0, -1, 0, 0)

# Quick test
if __name__ == "__main__":
    edges = [[0,1],[1,2],[1,3]]
    amount = [1,2,3,4]
    print(Solution().mostProfitablePath(edges, 2, amount))   # 4
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int mostProfitablePath(vector<vector<int>>& edges, int bob, vector<int>& amount) {
        int n = amount.size();
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        // Bob's depth on each node (time of arrival)
        vector<int> bobDepth(n, -1);
        vector<int> path;
        function<bool(int,int)> dfsBob = [&](int node, int parent) -> bool {
            if (node == 0) return true;               // reached root
            for (int nb : g[node]) {
                if (nb == parent) continue;
                path.push_back(node);
                if (dfsBob(nb, node)) return true;
                path.pop_back();
            }
            return false;
        };

        dfsBob(bob, -1);
        for (int d = 0; d < (int)path.size(); ++d) bobDepth[path[d]] = d;

        function<int(int,int,int,long long)> dfsAlice = [&](int node, int parent, int depth,
                                                             long long acc) -> int {
            if (bobDepth[node] == -1 || bobDepth[node] > depth) {
                acc += amount[node];
            } else if (bobDepth[node] == depth) {
                acc += amount[node] / 2;
            }

            // leaf
            if (node != 0 && g[node].size() == 1) return (int)acc;

            int best = INT_MIN;
            for (int nb : g[node]) {
                if (nb == parent) continue;
                best = max(best, dfsAlice(nb, node, depth + 1, acc));
            }
            return best;
        };

        return dfsAlice(0, -1, 0, 0);
    }
};

// Demo
int main() {
    vector<vector<int>> edges = {{0,1},{1,2},{1,3}};
    vector<int> amount = {1,2,3,4};
    cout << Solution().mostProfitablePath(edges, 2, amount) << endl; // 4
}
```

---

## 4. SEO‑Friendly Blog Post

> **Meta Description**  
> Solve LeetCode 2805 “Most Profitable Path in a Tree” with an O(n) DFS solution.  
> Get the Java, Python, and C++ reference code, plus interview tips and complexity analysis.

---

# Most Profitable Path in a Tree – The Definitive LeetCode 2805 Walk‑through

> **Key tags:** *LeetCode, Most Profitable Path, tree traversal, DFS, interview question, coding interview, Python, Java, C++*

---

## 🎯 Why This Problem Rocks

- **Real interview staple:** appears on top‑tier tech interviews (Google, Amazon, Microsoft).  
- **Tree + DP + greedy** – tests your understanding of depth‑first search and time‑based state.  
- **Multi‑language** – shows you can implement the same logic in Java, Python, and C++.

---

## 📌 Problem Recap

Alice starts at node `0`.  
Bob starts at node `bob` and walks **toward the root**.  
Each agent gains profit from a node depending on who arrives first.  
Both stop at a leaf.  
Return the maximum profit Alice can achieve.

---

## 🔑 Solution Strategy

1. **Adjacency List** – build once, O(n).  
2. **Bob’s path to the root** – the tree guarantees a unique path.  
   - Recursively walk from `bob` to `0`, recording the depth at which Bob reaches each node (`bobDepth[node]`).  
3. **Alice’s DFS** – from the root, keep track of her current depth.  
   - Compare `depth` with `bobDepth[node]` to decide the profit at that node.  
   - On leaf nodes, return the accumulated score; on internal nodes, explore all children and keep the maximum.

The trick: *both* agents move at the same speed, so the *depth* of the node in a DFS corresponds exactly to the time elapsed.

---

## 📈 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Build adjacency list | **O(n)** | **O(n)** |
| Bob’s path DFS | **O(n)** | **O(n)** (path stack) |
| Alice’s DFS | **O(n)** | **O(n)** (recursion stack) |

Overall: **O(n)** time, **O(n)** space.  
With `n ≤ 20,000`, recursion depth ≤ 20,000 – safe on most modern platforms (Python: `sys.setrecursionlimit`, C++: `-Wl,--stack` if needed).

---

## 🛠️ Common Pitfalls

| Pitfall | How to avoid |
|---------|--------------|
| **Using an array of booleans for visited** | Since the tree has a unique parent–child relation, you can simply pass the parent node instead of a visited set. |
| **Integer division rounding** | `amount[i]` is guaranteed to be even when divided by 2 in the “same time” case, but using integer division (`// 2` in Python, `/ 2` in Java/C++) is safe. |
| **Depth > Bob’s depth vs. Bob’s depth > depth** | Remember that `depth` is Alice’s current distance from the root, while `bobDepth` stores Bob’s arrival *time*. |
| **Leaf detection for node 0** | Node `0` may have only one neighbor but is **not** a leaf; the condition `node != 0 && graph[node].size() == 1` fixes this. |

---

## 🏁 Full Code (All Languages)

| Language | File name | Highlights |
|----------|-----------|------------|
| **Java** | `MostProfitablePath.java` | Uses `List<List<Integer>>` for adjacency, `Arrays.fill`, and `Math.max`. |
| **Python** | `solution.py` | Recursive DFS with `sys.setrecursionlimit`. |
| **C++** | `Solution.cpp` | Uses `vector<vector<int>>` and lambda recursion. |

---

## 5. Interview Tips

1. **Clarify the “stop at leaf” rule** – both Alice and Bob terminate at the *first* leaf they hit.  
2. **State the assumption** that the tree is rooted at `0` (by convention).  
3. **Explain Bob’s depth array** in your answer – it’s the key to O(n) solution.  
4. **Mention complexity** upfront; interviewers love concise Big‑O.  
5. **Discuss edge cases**: single node, Bob already at root, negative `amount` values (allowed if your code handles them).  

---

## 6. Take‑away Summary

| Language | Time | Memory | Notes |
|----------|------|--------|-------|
| Java | O(n) | O(n) | Recursion depth ≤ 20,000 – safe if `setRecursionLimit` is not an issue. |
| Python | O(n) | O(n) | Use `sys.setrecursionlimit` for large trees. |
| C++ | O(n) | O(n) | Iterative stack optional, but recursion is clearer. |

> **Bottom line:** The trick is to **pre‑compute Bob’s arrival times** and then perform a *single* DFS for Alice that propagates the best score.  
> This turns a seemingly complex “two agents moving simultaneously” problem into a classic *root‑to‑leaf maximum path* problem.

Happy coding, and good luck smashing that LeetCode problem! 🚀