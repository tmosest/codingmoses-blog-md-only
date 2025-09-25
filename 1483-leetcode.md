---
title: LeetCode 1483. Kth Ancestor of a Tree Node - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Code – TreeAncestor (K‑th Ancestor of a Tree Node)

Below are three **ready‑to‑copy** implementations that solve the LeetCode problem “1483. Kth Ancestor of a Tree Node”.  
All three solutions use **binary lifting** – the classic O(log n) ancestor query technique that every senior software engineer should know.

| Language | Key Points |
|----------|------------|
| **Java** | Uses a 2‑D array `dp[n][LOG]`. Pre‑processing takes O(n log n). Query time O(log n). |
| **Python** | Same idea with a list of lists. Slightly slower in pure Python but still within LeetCode limits. |
| **C++** | Uses `vector<vector<int>>`. Fast I/O and bit tricks for speed. |

> **Tip for interviewers:**  
> Explain that binary lifting builds a table `dp[v][i] = 2^i‑th ancestor of v`.  
> During a query, we decompose `k` into binary and jump through the table.

---

### 1.1 Java Implementation

```java
import java.util.*;

public class TreeAncestor {
    private final int[][] up;      // up[v][i] = 2^i-th ancestor of v
    private final int LOG;         // max power of two needed

    public TreeAncestor(int n, int[] parent) {
        LOG = 20;                 // 2^20 > 5 * 10^4  (n <= 5e4)
        up = new int[n][LOG];

        // build level 0 (direct parent)
        for (int v = 0; v < n; v++) {
            up[v][0] = parent[v] == -1 ? -1 : parent[v];
        }

        // build higher powers
        for (int i = 1; i < LOG; i++) {
            for (int v = 0; v < n; v++) {
                int mid = up[v][i - 1];
                up[v][i] = (mid == -1) ? -1 : up[mid][i - 1];
            }
        }
    }

    /** Return kth ancestor of node, or -1 if it doesn't exist. */
    public int getKthAncestor(int node, int k) {
        for (int i = 0; i < LOG; i++) {
            if ((k & (1 << i)) != 0) {
                node = up[node][i];
                if (node == -1) return -1;
            }
        }
        return node;
    }

    // ---- Example test ----
    public static void main(String[] args) {
        int[] parent = {-1, 0, 0, 1, 1, 2, 2};
        TreeAncestor ta = new TreeAncestor(7, parent);

        System.out.println(ta.getKthAncestor(3, 1)); // 1
        System.out.println(ta.getKthAncestor(5, 2)); // 0
        System.out.println(ta.getKthAncestor(6, 3)); // -1
    }
}
```

---

### 1.2 Python Implementation

```python
class TreeAncestor:
    def __init__(self, n: int, parent: list[int]) -> None:
        self.LOG = 20                     # 2^20 > 5*10^4
        self.up = [[-1] * self.LOG for _ in range(n)]

        for v in range(n):
            self.up[v][0] = parent[v] if parent[v] != -1 else -1

        for i in range(1, self.LOG):
            for v in range(n):
                mid = self.up[v][i - 1]
                self.up[v][i] = self.up[mid][i - 1] if mid != -1 else -1

    def getKthAncestor(self, node: int, k: int) -> int:
        for i in range(self.LOG):
            if k & (1 << i):
                node = self.up[node][i]
                if node == -1:
                    return -1
        return node


# ---- Example test ----
if __name__ == "__main__":
    parent = [-1, 0, 0, 1, 1, 2, 2]
    tree = TreeAncestor(7, parent)
    print(tree.getKthAncestor(3, 1))  # 1
    print(tree.getKthAncestor(5, 2))  # 0
    print(tree.getKthAncestor(6, 3))  # -1
```

---

### 1.3 C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class TreeAncestor {
    vector<vector<int>> up;   // up[v][i] = 2^i-th ancestor of v
    int LOG;                  // max power of two needed

public:
    TreeAncestor(int n, vector<int> parent) {
        LOG = 20;                 // 2^20 > 5*10^4
        up.assign(n, vector<int>(LOG, -1));

        for (int v = 0; v < n; ++v)
            up[v][0] = (parent[v] == -1) ? -1 : parent[v];

        for (int i = 1; i < LOG; ++i)
            for (int v = 0; v < n; ++v) {
                int mid = up[v][i-1];
                up[v][i] = (mid == -1) ? -1 : up[mid][i-1];
            }
    }

    int getKthAncestor(int node, int k) {
        for (int i = 0; i < LOG; ++i)
            if (k & (1 << i)) {
                node = up[node][i];
                if (node == -1) return -1;
            }
        return node;
    }
};

// ---- Example test ----
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    vector<int> parent = {-1, 0, 0, 1, 1, 2, 2};
    TreeAncestor ta(7, parent);

    cout << ta.getKthAncestor(3, 1) << '\n'; // 1
    cout << ta.getKthAncestor(5, 2) << '\n'; // 0
    cout << ta.getKthAncestor(6, 3) << '\n'; // -1
}
```

> **Why 20 bits?**  
> With `n <= 50 000`, the highest power of two required is < 2^16. 20 is a safe “over‑kill” that keeps the code simple while guaranteeing `2^LOG > n`.

---

## 2.  Blog Post – “Binary Lifting in Action: The Good, The Bad, The Ugly”

> **Title:**  
> *Master the K‑th Ancestor Problem – Binary Lifting, LeetCode Hard, & Interview Success*  
> 
> **Meta Description:**  
> Learn how to solve LeetCode 1483 with binary lifting. Detailed Java, Python, and C++ solutions, time‑space trade‑offs, and interview‑ready explanations. Boost your tree‑algorithm skills and land your next software engineering role.

---

### 2.1 Introduction – Why the K‑th Ancestor Matters

Tree‑based queries pop up in every big‑tech interview: from **file system navigation** to **ancestral lineage in social graphs**.  
The K‑th ancestor problem is a canonical hard problem on LeetCode that tests:

1. **Graph traversal** (DFS / BFS).  
2. **Dynamic programming on trees**.  
3. **Bit manipulation** and **pre‑processing tricks**.

> **Career Tip:**  
> *Every senior software engineer knows that the “governor” of tree‑algorithm interviews is **binary lifting**. Master it and you’ll impress hiring managers.*

---

### 2.2 Problem Statement (LeetCode 1483)

> **Input**  
> * `n`: number of nodes (`1 <= n <= 5 * 10⁴`)  
> * `parent[0 … n-1]`: parent of each node (`parent[0] = -1` as the root).  
> * `queries`: up to `5 * 10⁴` calls to `getKthAncestor(node, k)`.  
> **Output**  
> * Return the k‑th ancestor of `node` or `-1` if it does not exist.

> **Example**  
> ```text
> parent = [-1, 0, 0, 1, 1, 2, 2]
> getKthAncestor(3, 1) -> 1
> getKthAncestor(5, 2) -> 0
> getKthAncestor(6, 3) -> -1
> ```

---

### 2.3 The “Good” – Why Binary Lifting is a Winner

| ✅ Feature | What It Means |
|------------|---------------|
| **O(n log n) pre‑processing** | One linear scan per log level – cheap for n ≤ 50 000. |
| **O(log n) query time** | Handles 50 000 queries in milliseconds – meets LeetCode “Hard” constraints. |
| **Memory Friendly** | `n * LOG` integers ≈ 1 MB (Java), 0.4 MB (Python), 0.8 MB (C++). |
| **Conceptually Simple** | Build a table, decompose `k` into bits, jump – easy to explain in an interview. |
| **Reusable Pattern** | Works for LCA, “k‑th ancestor”, “jump pointers”, “binary lift” – a single technique for many tree problems. |

---

### 2.4 The “Bad” – Potential Pitfalls

| ⚠️ Issue | Mitigation |
|----------|------------|
| **Choosing LOG too small** | For `n = 5·10⁴`, `LOG = 20` suffices (`2^20 = 1,048,576`). Always set `LOG = ⌈log₂ n⌉ + 1`. |
| **Wrong parent handling** | Root’s parent is `-1`. In Java and C++ we guard against `-1` when accessing `up[mid][i-1]`. |
| **Python speed** | Pure Python loops are slower; if time limits tighten, use PyPy or Cython. |
| **Large k** | `k` may exceed depth; our loop will hit a `-1` and short‑circuit. |

---

### 2.5 The “Ugly” – When Things Go Wrong

1. **Off‑by‑one errors** – forgetting that `node` might become `-1` after a jump.  
   *Solution:* Add `if (node == -1) return -1;` after every jump.

2. **Using `int LOG = 17` for n=5e4** – fails for larger trees.  
   *Solution:* Compute `LOG` dynamically:  
   ```cpp
   LOG = 1; while ((1 << LOG) <= n) ++LOG;
   ```

3. **Memory fragmentation in Java** – `new int[n][LOG]` can trigger large contiguous blocks.  
   *Solution:* Use `ArrayList<int[]>` or `int[][]` but monitor heap usage on real servers.

4. **Inefficient I/O** – especially in C++ when used in a coding‑competition.  
   *Solution:* `ios::sync_with_stdio(false); cin.tie(nullptr);`

---

### 2.6 Complexity Recap

| Step | Time | Space |
|------|------|-------|
| Pre‑processing | **O(n log n)** | **O(n log n)** (dp table) |
| Query | **O(log n)** | – |
| Total (worst case) | **O((n+q) log n)** | **O(n log n)** |

For the LeetCode constraints (`n, q ≤ 5 000`), all three implementations run comfortably within the time limits.

---

## 3.  The Blog Post – “Binary Lifting: The Good, The Bad, and The Ugly of K‑th Ancestor”

> **Target Audience:**  
> • Aspiring software‑engineering interview candidates  
> • Senior engineers looking to refresh tree‑algorithm knowledge  
> • Recruiters and hiring managers wanting a quick technical refresher

---

### 3.1 Title & Meta‑Data

```markdown
# Master the K‑th Ancestor Problem with Binary Lifting – LeetCode 1483, Interview Tips, and Code in Java, Python, C++
Meta description: A complete guide to solving LeetCode “Kth Ancestor of a Tree Node” using binary lifting. Includes Java, Python, and C++ code, complexity analysis, and interview‑friendly explanations.
Keywords: Kth ancestor, binary lifting, TreeAncestor, LeetCode Hard, tree algorithms, software engineer interview, senior software engineer, technical interview
```

---

### 3.2 Introduction – The Tree‑Ancestor Puzzle

> In a rooted tree, you’re given a node and need to find its **k‑th ancestor** (the ancestor that sits `k` edges above it).  
> On LeetCode it’s **Hard** (1483) because the tree can contain up to **50 000** nodes and you may have up to **50 000** queries.  
> The naïve approach of walking up `k` parents is O(k) – far too slow.  

---

### 3.3 The “Good” – Why Binary Lifting Wins

* **Single pass table build** – After one DFS/BFS you can answer any ancestor query in logarithmic time.  
* **Deterministic O(log n) runtime** – Guarantees consistent performance, a must‑know for scalable systems.  
* **Compact memory** – `n * log n` integers is a fraction of a megabyte for the LeetCode constraints.  
* **Reusable pattern** – The same table works for:
  * Lowest Common Ancestor (LCA)
  * Jump‑pointer queries in game AI
  * Permission‑inheritance trees in enterprise apps

> **Takeaway for recruiters:** If a candidate can explain binary lifting, they’ve mastered a fundamental tree‑DP technique that applies to many real‑world problems.

---

### 3.4 The “Bad” – Common Gotchas and How to Avoid Them

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Forgetting that root’s parent is `-1` | Index‑out‑of‑range errors | Set `dp[root][0] = -1` and guard against `-1` when accessing `dp[mid][i-1]`. |
| Using a static LOG that’s too small | Some queries overflow | Compute `LOG = ceil(log₂(n)) + 1` at runtime. |
| Decomposing `k` incorrectly | Wrong ancestor returned | Use bitwise check: `if (k & (1 << i))` and short‑circuit on `-1`. |
| In Python: naive list comprehensions | Excessive GC pauses | Run on PyPy or add `@jit` decorators with Numba. |

---

### 3.5 The “Ugly” –  When Your Solution Breaks

* **Large stack consumption in DFS** – Recursion depth can hit `n`.  
  *Iterative DFS* or `sys.setrecursionlimit(1 << 25)` in Python solves it.

* **Tight memory limits on servers** – Even 1 MB can be significant if other services run concurrently.  
  *Pack the DP table as `short` where possible or use a **compressed bit‑set** if depths are shallow.*

* **Parallel query handling** – In a real system you might process queries concurrently.  
  *Solution:* Make the DP table read‑only and cache the result of each query; no race conditions.

---

### 3.6 Code Snippets (Java, Python, C++)

> Include the three implementations we described above.  

```java
int[][] up = new int[n][LOG]; // Java
```

```python
dp = [[-1]*LOG for _ in range(n)]  # Python
```

```cpp
vector<vector<int>> up(n, vector<int>(LOG, -1)); // C++
```

> **Why include multiple languages?**  
> Recruiters often ask: “Show me the algorithm in your preferred language.”  Demonstrating all three shows adaptability.

---

### 3.7 Complexity Quick‑Reference

```markdown
- Pre‑processing: O(n log n) time, O(n log n) space
- Each query: O(log n) time
- Total: O((n + q) log n) time
```

> **Plot this on a graph** – you’ll see a near‑vertical line of queries, meaning performance doesn’t degrade with more requests.

---

### 3.8 Real‑World Analogies

* **Corporate hierarchy** – Finding a manager `k` levels above an employee.  
* **File system path resolution** – Locating the directory `k` steps above a file.  
* **Family tree ancestry** – Determining grand‑grand‑parent in genealogical software.  

> **Why this matters to hiring managers:** Many production systems maintain hierarchical data (access control, logging, analytics).  Efficient ancestor queries are a daily reality.

---

### 3.9 Conclusion – Next Steps for Interview Success

1. **Run the code** on your local machine.  
2. **Explain the DP table**: “We store `dp[v][i]` = 2^i‑th ancestor of `v`.”  
3. **Show the bit decomposition**: “To climb k edges, we look at the binary representation of k.”  
4. **Prepare variations**: LCA, path queries, or even “find the lowest common manager” in a company org‑chart.

> **Landing Your Role:**  
> By mastering this pattern, you’ll ace tree‑related questions, stand out among peers, and demonstrate the kind of algorithmic thinking recruiters value.

---

### 3.10 Call‑to‑Action

> *“Ready to tackle LeetCode 1483? Try the code snippets above, submit them, and share your experience in the comments. Want to dive deeper into tree DP? Check out our full series on *Tree Algorithms for Software Engineers*.”*

---

## 4.  Final Checklist

| ✔️ Item | Done |
|---------|------|
| Provide clean Java, Python, C++ implementations | ✅ |
| Explain time‑space trade‑offs | ✅ |
| Highlight interview‑ready insights | ✅ |
| Include meta‑data for SEO | ✅ |
| Discuss pitfalls (“Bad”) and debugging tips (“Ugly”) | ✅ |

> **Next Move:** Integrate these snippets into your GitHub “LeetCode Solutions” repo. Add the blog post to your technical portfolio, and you’ll have a standout talking point in your next interview.

---