---
title: LeetCode 3387. Maximize Amount After Two Days of Conversions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📦 1. Code Solutions

Below you’ll find three full‑stack solutions for the **LeetCode** problem **“3387. Maximize Amount After Two Days of Conversions”** – one each in **Java**, **Python**, and **C++**.  
All three use the same *BFS‑based* idea:  
1. Build a bidirectional weighted graph for Day 1.  
2. Run BFS to compute the maximum amount reachable from the start currency.  
3. Re‑run BFS on Day 2, starting with the amounts from Day 1.  
4. Return the final amount of the original currency.

> **Why BFS?**  
> With ≤ 10 edges per day the graph is tiny – BFS guarantees we visit each node only when we find a better amount.  
> It’s easier to understand, faster in practice for these constraints, and avoids the risk of floating‑point over‑flows that can come with exhaustive DFS or DP.

---

### 1.1 Java (LeetCode‑style)

```java
import java.util.*;

class Solution {

    public double maxAmount(String initialCurrency,
                            List<List<String>> pairs1, double[] rates1,
                            List<List<String>> pairs2, double[] rates2) {

        // Day 1 graph
        Map<String, Map<String, Double>> g1 = buildGraph(pairs1, rates1);
        // Day 2 graph
        Map<String, Map<String, Double>> g2 = buildGraph(pairs2, rates2);

        // Max amounts after day 1
        Map<String, Double> day1 = bfs(initialCurrency, g1, null);

        // Max amounts after day 2 – start with day‑1 results
        Map<String, Double> day2 = bfs(initialCurrency, g2, day1);

        return day2.getOrDefault(initialCurrency, 1.0);
    }

    /** Build a bidirectional weighted graph */
    private Map<String, Map<String, Double>> buildGraph(
            List<List<String>> pairs, double[] rates) {

        Map<String, Map<String, Double>> graph = new HashMap<>();

        for (int i = 0; i < pairs.size(); ++i) {
            String a = pairs.get(i).get(0);
            String b = pairs.get(i).get(1);
            double r = rates[i];

            graph.computeIfAbsent(a, k -> new HashMap<>()).put(b, r);
            graph.computeIfAbsent(b, k -> new HashMap<>()).put(a, 1.0 / r);
        }
        return graph;
    }

    /**
     * BFS that keeps the best amount seen so far.
     * If init is not null we seed the queue with all keys of init.
     */
    private Map<String, Double> bfs(
            String start,
            Map<String, Map<String, Double>> graph,
            Map<String, Double> init) {

        Map<String, Double> best = new HashMap<>();
        if (init != null) {
            best.putAll(init);
        } else {
            best.put(start, 1.0);
        }

        Queue<String> q = new LinkedList<>(best.keySet());

        while (!q.isEmpty()) {
            String cur = q.poll();
            double curAmt = best.get(cur);

            for (Map.Entry<String, Double> e :
                 graph.getOrDefault(cur, Collections.emptyMap()).entrySet()) {

                String nxt = e.getKey();
                double newAmt = curAmt * e.getValue();

                if (newAmt > best.getOrDefault(nxt, 0.0)) {
                    best.put(nxt, newAmt);
                    q.offer(nxt);
                }
            }
        }
        return best;
    }
}
```

---

### 1.2 Python 3

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def maxAmount(
        self,
        initialCurrency: str,
        pairs1: List[List[str]],
        rates1: List[float],
        pairs2: List[List[str]],
        rates2: List[float]
    ) -> float:

        g1 = self.build_graph(pairs1, rates1)
        g2 = self.build_graph(pairs2, rates2)

        day1 = self.bfs(initialCurrency, g1, None)
        day2 = self.bfs(initialCurrency, g2, day1)

        return day2.get(initialCurrency, 1.0)

    def build_graph(
        self, pairs: List[List[str]], rates: List[float]
    ) -> defaultdict:
        g = defaultdict(dict)
        for (a, b), r in zip(pairs, rates):
            g[a][b] = r
            g[b][a] = 1.0 / r
        return g

    def bfs(
        self,
        start: str,
        graph: defaultdict,
        init: dict | None,
    ) -> dict:
        best = init.copy() if init else {start: 1.0}
        q = deque(best.keys())

        while q:
            cur = q.popleft()
            cur_amt = best[cur]
            for nxt, rate in graph.get(cur, {}).items():
                new_amt = cur_amt * rate
                if new_amt > best.get(nxt, 0.0):
                    best[nxt] = new_amt
                    q.append(nxt)
        return best
```

---

### 1.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    double maxAmount(string initialCurrency,
                     vector<vector<string>>& pairs1, vector<double>& rates1,
                     vector<vector<string>>& pairs2, vector<double>& rates2) {

        auto g1 = buildGraph(pairs1, rates1);
        auto g2 = buildGraph(pairs2, rates2);

        unordered_map<string, double> day1 = bfs(initialCurrency, g1, {});
        unordered_map<string, double> day2 = bfs(initialCurrency, g2, day1);

        return day2.count(initialCurrency) ? day2[initialCurrency] : 1.0;
    }

private:
    // Graph: node -> list of {neighbor, weight}
    vector<unordered_map<string, double>> buildGraph(
            const vector<vector<string>>& pairs, const vector<double>& rates) {
        unordered_map<string, unordered_map<string,double>> adj;
        for (size_t i = 0; i < pairs.size(); ++i) {
            string a = pairs[i][0];
            string b = pairs[i][1];
            double r = rates[i];
            adj[a][b] = r;
            adj[b][a] = 1.0 / r;
        }

        // Convert to vector form for easier BFS
        vector<unordered_map<string,double>> g;
        for (const auto& [node, neighbors] : adj)
            g.emplace_back(neighbors);
        return g;
    }

    unordered_map<string,double> bfs(
            const string& start,
            const vector<unordered_map<string,double>>& graph,
            const unordered_map<string,double>* init) {

        unordered_map<string,double> best;
        queue<string> q;

        if (init) {
            best = *init;          // seed with day‑1 results
            for (const auto& [k, v] : *init) q.push(k);
        } else {
            best[start] = 1.0;
            q.push(start);
        }

        while (!q.empty()) {
            string cur = q.front(); q.pop();
            double curAmt = best[cur];
            for (auto it = graph[cur].cbegin(); it != graph[cur].cend(); ++it) {
                const string& nxt = it->first;
                double rate = it->second;
                double newAmt = curAmt * rate;
                if (newAmt > best[nxt]) {
                    best[nxt] = newAmt;
                    q.push(nxt);
                }
            }
        }
        return best;
    }
};
```

> **Note**: The C++ version keeps the graph in a vector of maps for speed. With the tiny input sizes, the `vector<unordered_map<...>>` representation is more than fast enough.

---

## 📘 2. Blog Article – “The Good, the Bad & the Ugly of Two‑Day Currency Conversions”

> **Title**  
> **Maximize Amount After Two Days of Conversions – A Deep Dive (Java | Python | C++)**  

---

### Table of Contents

1. **Problem Overview**  
2. **Why This Problem Is a Gold‑Mine for Graph Algorithms**  
3. **The BFS Strategy – The Good**  
   - 3.1 Building the Bidirectional Graph  
   - 3.2 First‑Day BFS  
   - 3.3 Second‑Day BFS  
4. **Alternative Approaches – The Bad & the Ugly**  
   - 4.1 Exhaustive DFS  
   - 4.2 Dynamic Programming on Edge Sequences  
   - 4.3 Bellman‑Ford & Floyd‑Warshall (overkill here)  
5. **Complexity Analysis**  
6. **Corner Cases & Pitfalls**  
7. **Code Walk‑through (Java, Python, C++)**  
8. **Take‑away & Final Thoughts**  

---

### 2.1 Problem Overview

You’re given two *independent* sets of currency exchange rates – one for **Day 1** and one for **Day 2**.  
Starting with **1 unit** of the `initialCurrency`, you may:

1. Convert along any chain of exchanges on Day 1.  
2. Hold the resulting currencies overnight.  
3. Convert again on Day 2 using that day’s rates.  

Your goal: **return the maximum possible amount of the original currency after the two days**.

*Constraints*:  
- 2 ≤ |pairs| ≤ 10 per day.  
- Exchange rates are real numbers > 0.  
- Graphs are fully bidirectional (you can always reverse a trade).  

---

### 2.2 Why Graph Algorithms?

At first glance the problem looks like a dynamic‑programming or “knapsack‑style” puzzle, but it is, in fact, a **weighted graph reachability** problem:

- **Nodes** – currencies.  
- **Edges** – direct exchange rates, weight = multiplier.  
- **Goal** – maximise the product of weights along a path.  

Because you can reverse any exchange, every edge is bidirectional.  
With only up to 10 edges per day, the graph has at most 11 nodes, making **breadth‑first search (BFS)** an ideal fit.

---

### 2.3 The BFS Strategy – The Good

| Step | What we do | Why it works |
|------|------------|--------------|
| **Build the graph** | For each rate `a → b` add `b → a` with weight `1/r`. | Keeps the graph symmetric; we never miss a reverse conversion. |
| **Day 1 BFS** | Start at `initialCurrency` with amount = 1. | Each time we find a better product for a neighbour we push it onto the queue. |
| **Day 2 BFS** | Seed the queue with **all** amounts from Day 1. | Overnight you can start from any currency you own. |
| **Result** | `amount[initialCurrency]` after Day 2. | The product of the best Day 1 amount with the best Day 2 multiplier. |

#### Why BFS is *good*

- **Simplicity** – no recursion, no stack depth issues.  
- **Optimality** – every time we see a larger product we immediately propagate it.  
- **Speed** – With ≤ 10 edges per day, BFS finishes in microseconds.  
- **Numerical safety** – We avoid deep recursion that could lead to floating‑point under‑/overflow.

---

### 2.4 Alternative Approaches – The Bad & Ugly

| Approach | When it *might* be useful | Downsides for this problem |
|----------|--------------------------|----------------------------|
| **Exhaustive DFS** | Small depth, all possible permutations | Exponential blow‑up, many repeated calculations, stack overflow risk. |
| **Dynamic Programming** (DP over edges) | Larger graphs, need to track *states* | Requires `O(n^2)` memory for all possible intermediate states – overkill for ≤ 10 edges. |
| **Bellman–Ford** | Detects negative cycles (in log‑space) | Overkill; algorithmic overhead of `O(VE)` for tiny graphs. |
| **Floyd–Warshall** | All‑pairs reachability | `O(V^3)` – unnecessary with such few nodes. |

> **Bottom line**: *BFS* strikes the perfect balance of clarity, performance, and low memory usage for the given limits.

---

### 2.5 Complexity Analysis

| Phase | Operations | Complexity |
|-------|------------|------------|
| Build graph (Day 1) | O(n) edges | **O(n)** |
| Build graph (Day 2) | O(m) edges | **O(m)** |
| BFS Day 1 | O(V + E) per update | **O(V + E)** (≤ 11 + 10) |
| BFS Day 2 | Same | **O(V + E)** |
| **Total** | **O(n + m)** | **~O(20)** – practically constant time. |

Memory usage is also negligible – just a handful of maps containing at most 11 keys.

---

### 2.6 Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Forgetting to add the *reverse* edge (`1/r`) | `graph.putIfAbsent(b, new HashMap<>()); graph.get(b).put(a, 1.0 / r);` |
| Returning 0.0 for currencies that cannot be reached | Default to `1.0` (original amount) if key missing |
| Using recursion on a cyclic graph | Prefer BFS/queue or iterative DFS to avoid stack overflow |

---

### 2.7 Test Harness (Optional)

```java
public static void main(String[] args) {
    Solution s = new Solution();
    List<List<String>> p1 = List.of(
        List.of("USD", "EUR"),
        List.of("EUR", "JPY")
    );
    double[] r1 = {0.9, 120};

    List<List<String>> p2 = List.of(
        List.of("USD", "GBP"),
        List.of("GBP", "JPY")
    );
    double[] r2 = {0.8, 150};

    System.out.println(s.maxAmount("USD", p1, r1, p2, r2));
}
```

The above will print the maximum amount of **USD** you can end up with after the two days.

---

## 📚 2. Blog Article – SEO‑Optimized Guide

> **Maximize Amount After Two Days of Conversions – Solve the LeetCode Problem in Java, Python & C++**

---

### 2.1 Introduction  

LeetCode’s **3387 – “Maximize Amount After Two Days of Conversions”** is a classic *graph* puzzle that blends real‑world finance with algorithmic thinking. The task: start with one unit of a currency, swap it for others over **two days**, and finish with the **largest possible amount** of your original currency.  

Below we dissect the problem, walk through the most efficient solution (BFS), and provide fully commented code in **Java, Python, and C++**. Whether you’re a coding interview prepper, a competitive programmer, or just a curious coder, this article is your quick‑start manual.

---

### 2.2 Problem Snapshot  

- **Input**  
  - `initialCurrency` – a string like `"USD"`.  
  - `pairsDay1` – list of direct exchanges for Day 1.  
  - `ratesDay1` – corresponding multipliers (> 0).  
  - `pairsDay2` & `ratesDay2` – same for Day 2.  

- **Operation**  
  1. Convert along any path on Day 1.  
  2. Overnight you can *hold* any currency you end up with.  
  3. Convert again on Day 2 using that day’s rates.  

- **Goal** – return **maximum** amount of `initialCurrency` after both days.

**Why it matters**: The problem tests your ability to model a financial system as a graph, handle bidirectional edges, and maximise a product of real numbers – a neat intersection of math and coding.

---

### 2.3 The “Good” – Breadth‑First Search  

We’ll treat each day as a **separate weighted directed graph** (bidirectional edges). BFS naturally fits because:

- **Small graph**: ≤ 11 nodes, ≤ 10 edges per day.  
- **No recursion**: eliminates stack overflow risk.  
- **Immediate propagation**: a better product is pushed right away.

**Pseudocode**:

```
buildGraph(pairs, rates):
    for each (a, b, r):
        add edge a->b weight r
        add edge b->a weight 1/r

BFS(startNode):
    queue = [(startNode, 1.0)]
    best = {startNode: 1.0}
    while queue not empty:
        node, amt = queue.pop()
        for each neighbour, w in graph[node]:
            newAmt = amt * w
            if newAmt > best[neighbour]:
                best[neighbour] = newAmt
                queue.push((neighbour, newAmt))
    return best
```

Apply `BFS` twice: once for Day 1 (starting from `initialCurrency`), once for Day 2 (starting from *all* currencies you own after Day 1). The answer is simply `best[initialCurrency]` after the second BFS.

---

### 2.4 Why Not DFS or DP?  

| Technique | Use‑case | Why It’s not ideal for Day‑Swap |
|-----------|----------|--------------------------------|
| **DFS** | Small depth, exhaustive path search | Exponential time, redundant work, risk of deep recursion. |
| **Dynamic Programming** | Large state space | Requires `O(V^2)` memory – unnecessary for ≤ 10 edges. |
| **Bellman‑Ford** | Cycle detection in log‑space | Adds complexity; no cycles to detect. |
| **Floyd–Warshall** | All‑pairs distances | `O(V^3)` – overkill for this tiny graph. |

The BFS method is *lighter*, *faster*, and *clearer* – exactly what interviewers look for.

---

### 2.5 Code Walk‑through  

Below you’ll find the full, annotated implementations in **Java**, **Python**, and **C++**. Each follows the same blueprint:

1. **Build the graph** (bidirectional).  
2. **Run Day 1 BFS** to populate `amountDay1`.  
3. **Run Day 2 BFS** seeded with `amountDay1`.  
4. **Return** `amountDay2[initialCurrency]`.

> *See the “Test Harness” section for a runnable example.*

---

### 2.6 Edge Cases & Numerical Safety  

- **Unreachable currencies** – default to original amount (`1.0`).  
- **Zero division** – never happen because all rates > 0.  
- **Precision** – Using doubles/floats is fine; product of ≤ 20 multipliers stays within range.  

---

### 2.7 Final Take‑away  

LeetCode 3387 is a *graph problem disguised as a finance puzzle*. The optimal, interviewer‑friendly solution is an **iterative BFS** that respects the bidirectional nature of trades.  

Whether you write it in **Java** with `HashMap`, **Python** with dictionaries, or **C++** with `unordered_map`, the underlying logic remains identical. The code snippets above illustrate the *exact* same algorithm, tailored to language idioms.

**Happy coding!**

---

### 2.8 References & Further Reading

- LeetCode Problem 3387 – [Link](https://leetcode.com/problems/maximize-amount-after-two-days-of-conversions/)  
- Graph Traversal: BFS vs DFS – [GeeksforGeeks](https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/)  
- Bellman‑Ford Algorithm – [Wikipedia](https://en.wikipedia.org/wiki/Bellman%E2%80%93Ford_algorithm)  
- Floyd–Warshall All‑Pairs Shortest Path – [Coursera](https://www.coursera.org/lecture/algorithms-ongraphs/floyd-warshall-shortest-paths-5B6g8)  

---

> *By mastering the BFS strategy for LeetCode 3387, you’ll be ready to tackle any graph‑based exchange problem that comes your way.*  

---

**Happy Solving!**