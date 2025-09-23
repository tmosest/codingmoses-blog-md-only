---
title: LeetCode 1791. Find Center of Star Graph - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1791 – Find Center of a Star Graph  
**SEO‑optimized guide, code snippets, and interview‑friendly explanation**

---

## 🎯 What is the problem?

You’re given a *star graph* – an undirected graph with a single “center” node that is connected to every other node.  
The graph is described by an array `edges`, where each entry `[u, v]` represents an edge between nodes `u` and `v`.  

**Return the label of the center node.**

> **Constraints**  
> * `3 ≤ n ≤ 10⁵`  
> * `edges.length == n‑1`  
> * Every entry is a pair of distinct integers between `1` and `n`.  
> * The input always represents a valid star graph.

---

## 💡 Intuition – The “one‑liner” trick

In a star graph, **every edge contains the center**.  
Pick any two edges – the center must appear in *both* of them.  

- If `edges[0][0]` is the center, it will also appear in `edges[1]`.  
- Otherwise the other endpoint of the first edge must be the center.

That gives a *constant‑time* (O(1)) solution, no loops, no extra memory.

---

## ✅ Code – 3 Languages

### 1. Java

```java
/**
 * Leetcode 1791 – Find Center of Star Graph
 * O(1) time | O(1) space
 */
public class Solution {
    public int findCenter(int[][] edges) {
        // The center is the common node in the first two edges
        return (edges[0][0] == edges[1][0] || edges[0][0] == edges[1][1])
               ? edges[0][0] : edges[0][1];
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] edges1 = {{1, 2}, {2, 3}, {4, 2}};
        System.out.println(s.findCenter(edges1)); // 2

        int[][] edges2 = {{1, 2}, {5, 1}, {1, 3}, {1, 4}};
        System.out.println(s.findCenter(edges2)); // 1
    }
}
```

### 2. Python 3

```python
class Solution:
    def findCenter(self, edges: List[List[int]]) -> int:
        # O(1) – same logic as Java
        return edges[0][0] if (edges[0][0] == edges[1][0] or
                               edges[0][0] == edges[1][1]) else edges[0][1]


# Demo
if __name__ == "__main__":
    s = Solution()
    print(s.findCenter([[1, 2], [2, 3], [4, 2]]))          # 2
    print(s.findCenter([[1, 2], [5, 1], [1, 3], [1, 4]]))  # 1
```

### 3. C++

```cpp
#include <vector>
using namespace std;

/**
 * Leetcode 1791 – Find Center of Star Graph
 * O(1) time | O(1) space
 */
class Solution {
public:
    int findCenter(vector<vector<int>>& edges) {
        return (edges[0][0] == edges[1][0] || edges[0][0] == edges[1][1])
               ? edges[0][0] : edges[0][1];
    }
};

int main() {
    Solution s;
    vector<vector<int>> e1{{1,2},{2,3},{4,2}};
    vector<vector<int>> e2{{1,2},{5,1},{1,3},{1,4}};
    cout << s.findCenter(e1) << endl; // 2
    cout << s.findCenter(e2) << endl; // 1
}
```

---

## 🕵️‍♂️ The Good, the Bad, the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time complexity** | O(1) – perfect for interview buzz | – | – |
| **Space complexity** | O(1) – no extra structures | – | – |
| **Implementation** | One-liner – clean, readable | None | Over‑engineering with loops or maps wastes time |
| **Robustness** | Works for any valid star graph | Relies on `edges[1]` existing – but `n≥3` guarantees it | If input violated assumptions, throws `ArrayIndexOutOfBounds` |
| **Readability** | Easy to explain: “look at first two edges” | Might seem “magic” to some |  |
| **Edge cases** | Handles any `n` (≥3) | – | – |

> **Tip:** Mention the *observational* property (center appears in every edge) during the interview; it demonstrates you read the statement thoroughly.

---

## 📊 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | O(1) | O(1) | O(1) |
| **Space** | O(1) | O(1) | O(1) |
| **Big‑O** | ✔️ | ✔️ | ✔️ |

All solutions are *amortized* O(1) because they inspect at most two edges.

---

## 🔧 Alternatives & When to Use Them

| Approach | When it Makes Sense | Downsides |
|----------|--------------------|-----------|
| **Full scan** (`for edge in edges`) | If you didn’t notice the star property, still O(n) but acceptable for small `n` | Linear time, unnecessary for `n=10⁵` |
| **Hash map counting** | General graphs where the center may be identified by frequency | O(n) time & space, overkill |
| **Set intersection** | If you want to illustrate set operations | Still O(n) but clearer for non‑star graphs |

---

## 🎉 How This Helps You Land a Job

* **Interview Prep** – Demonstrate a knack for *quick observation* and *constant‑time* tricks.  
* **Code Quality** – One‑liner, no magic numbers, clear comments.  
* **Algorithmic Thinking** – Show you can identify graph properties (center appears in every edge).  
* **Performance** – Emphasize O(1) solution; interviewers love space‑time trade‑offs.  

Add this to your portfolio, share on GitHub, or post a blog entry. Mention the Leetcode ID, the tags (`Easy`, `Graph`, `Arrays`), and highlight the **O(1) time** in the title – SEO gold for recruiters searching “Leetcode 1791 solution”.

---

## 📝 SEO‑Friendly Summary (for your blog)

- **Title**: “Leetcode 1791 – Find Center of Star Graph in O(1): Java, Python, C++ One‑Liner”
- **Meta Description**: “Solve Leetcode 1791 – Find Center of Star Graph using a constant‑time one‑liner in Java, Python, and C++. Learn the trick, time/space complexity, and interview insights.”
- **Keywords**: *Leetcode 1791, Find Center of Star Graph, O(1) solution, Java, Python, C++, graph algorithm, interview question, software engineer interview, job interview prep*

---

## 🚀 Takeaway

The star graph’s defining feature – every edge contains the center – lets you solve Leetcode 1791 in *one line* with **O(1)** time and space.  
Write the clean code, explain the intuition, and you’ll impress interviewers and recruiters alike. Happy coding!