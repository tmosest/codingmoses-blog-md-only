---
title: LeetCode 582. Kill Process - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 582 – “Kill Process”  
**Languages:** Java | Python | C++  
**Keywords:** LeetCode 582, Kill Process, Java DFS, Python BFS, C++ solution, interview algorithm, tree, graph, job interview

---

### 1️⃣ Problem Recap

You are given two integer arrays:

| Index | `pid[i]` | `ppid[i]` |
|-------|----------|-----------|
| 0 … n‑1 | ID of the i‑th process | Parent ID of the i‑th process |

Only one process has `ppid[i] = 0` (the root).  
When a process is killed, **all of its descendant processes are killed too**.  
Given an integer `kill` (guaranteed to be in `pid`), return a list of all process IDs that will be killed.

**Constraints**

* `1 ≤ n ≤ 5 × 10⁴`
* All `pid` values are unique
* `kill` exists in `pid`

---

### 2️⃣ Why This Is a Classic Interview Question

| The Good | The Bad | The Ugly |
|----------|---------|----------|
| *Shows* you can map relationships & traverse trees/graphs | *Often* hidden in the wording – you must recognize it as a tree traversal problem | *Recursion can blow the stack* if not handled carefully |
| *Demonstrates* BFS vs DFS, queue vs stack | *Some candidates* think they need to build the full tree | *Complexity pitfalls* if you don’t pre‑process the adjacency list |
| *Fits* into a “one‑liner” in interviews | *Edge cases* (kill the root, no children) can trip you up | *Performance matters* – O(n) is the sweet spot, anything worse is frowned upon |

---

### 3️⃣ High‑Level Strategy

1. **Build a parent → children map** (adjacency list).  
   `unordered_map<int, vector<int>>` (C++), `HashMap<Integer, List<Integer>>` (Java), or `defaultdict(list)` (Python).
2. **Traverse the subtree** rooted at `kill`.  
   - DFS (recursion or stack)  
   - BFS (queue)  
   Both give the same answer; BFS is a bit safer against stack over‑flows for deep trees.
3. **Collect & return** all visited node IDs.

Time Complexity: **O(n)** – each process is visited once.  
Space Complexity: **O(n)** – adjacency list + result list.

---

### 4️⃣ Code Implementations

> **All three solutions have identical logic, just expressed in different syntax.**

#### 📌 Java (DFS with an explicit stack)

```java
import java.util.*;

public class Solution {
    public List<Integer> killProcess(List<Integer> pid, List<Integer> ppid, int kill) {
        // Build parent → children map
        Map<Integer, List<Integer>> children = new HashMap<>();
        for (int i = 0; i < ppid.size(); i++) {
            int parent = ppid.get(i);
            if (parent == 0) continue;          // root has no parent
            children.computeIfAbsent(parent, k -> new ArrayList<>())
                    .add(pid.get(i));
        }

        // DFS using a stack (iterative)
        List<Integer> result = new ArrayList<>();
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(kill);

        while (!stack.isEmpty()) {
            int cur = stack.pop();
            result.add(cur);
            List<Integer> ch = children.get(cur);
            if (ch != null) {
                for (int child : ch) stack.push(child);
            }
        }
        return result;
    }
}
```

#### 📌 Python (BFS with `collections.deque`)

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def killProcess(self, pid: List[int], ppid: List[int], kill: int) -> List[int]:
        # Build adjacency list
        children = defaultdict(list)
        for child, parent in zip(pid, ppid):
            if parent != 0:
                children[parent].append(child)

        # BFS traversal
        queue = deque([kill])
        killed = []

        while queue:
            cur = queue.popleft()
            killed.append(cur)
            queue.extend(children[cur])   # children[cur] is empty list if no kids

        return killed
```

#### 📌 C++ (DFS with explicit stack)

```cpp
#include <vector>
#include <unordered_map>
#include <stack>

class Solution {
public:
    std::vector<int> killProcess(std::vector<int>& pid,
                                std::vector<int>& ppid,
                                int kill) {
        // Build adjacency list
        std::unordered_map<int, std::vector<int>> children;
        for (size_t i = 0; i < ppid.size(); ++i) {
            if (ppid[i] == 0) continue;          // root
            children[ppid[i]].push_back(pid[i]);
        }

        // DFS using a stack
        std::stack<int> st;
        st.push(kill);
        std::vector<int> ans;

        while (!st.empty()) {
            int cur = st.top(); st.pop();
            ans.push_back(cur);
            auto it = children.find(cur);
            if (it != children.end()) {
                for (int child : it->second) st.push(child);
            }
        }
        return ans;
    }
};
```

---

### 5️⃣ Edge‑Case Checklist

| Edge Case | What to Watch For |
|-----------|-------------------|
| **Kill the root** | The root’s parent is `0`; the algorithm still works. |
| **Process with no children** | `children[pid]` returns an empty vector/list. |
| **Deep trees (≈ 5 × 10⁴ levels)** | Prefer iterative DFS/BFS over recursion to avoid stack overflow. |
| **Large inputs** | All solutions run in O(n) time and memory; keep the adjacency list compact. |

---

### 6️⃣ Interview‑Ready Talking Points

1. **Explain the graph model**: “We treat `pid`‑`ppid` pairs as edges in a directed tree; killing a process is just a subtree traversal.”
2. **Choice of traversal**: “I’ll use BFS because it guarantees O(n) time and no recursion depth issues, but DFS is perfectly fine.”
3. **Complexity analysis**: “Time O(n), Space O(n).”
4. **Corner cases**: “What if the killed process is the root? What if it has no children?”
5. **Testing**: “Add unit tests for the two sample cases, a single node tree, and a deep chain.”

---

### 7️⃣ SEO‑Friendly Conclusion

If you’re eyeing a software‑engineering role, mastering **LeetCode 582 – Kill Process** demonstrates your ability to translate a real‑world process‑management scenario into clean algorithmic code.  
- **Java** and **Python** solutions show you’re comfortable with high‑level collections.  
- **C++** proves you can handle low‑level data structures efficiently.  

Practice these patterns and you’ll be ready to discuss “tree traversal” and “graph algorithms” confidently in any technical interview. Happy coding! 🚀

---