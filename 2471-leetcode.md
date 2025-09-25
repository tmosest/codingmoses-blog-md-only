---
title: LeetCode 2471. Minimum Number of Operations to Sort a Binary Tree by Level - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 2471 – **Minimum Number of Operations to Sort a Binary Tree by Level**  
> **Time Limit**: 2 s (Java, Python, C++)  
> **Memory Limit**: 256 MB  

---

### 1.1 Problem Recap  

You’re given a binary tree whose node values are **unique**.  
In **one operation** you can choose *any two nodes on the same depth level* and swap their values.  
The task:  

*Return the minimal number of swaps needed so that the values on every depth level are strictly increasing.*

> Example  
> ```
> Input:  root = [1,4,3,7,6,8,5,null,null,null,null,9,null,10]
> Output: 3
> Explanation: … (see problem statement)
> ```

---

### 1.2 High‑level idea  

1. **Breadth‑first search (BFS)** – visit the tree level‑by‑level.  
2. For each level we have an array `A` of the node values.  
3. In a single level the optimal strategy is *sorting* the array.  
   The question becomes: *How many swaps are needed to turn `A` into its sorted version?*  
4. This is a classic “minimum swaps to sort an array” problem, solved in linear time by detecting cycles in the permutation that maps the current order to the sorted order.  
5. Sum the swaps for every level → answer.

The algorithm runs in **O(N log N)** time (the `log N` comes from sorting each level) and uses **O(N)** additional space (for the BFS queue and temporary arrays).

---

### 1.3 Detailed algorithm  

```
answer = 0
queue ← [root]

while queue not empty:
    levelSize ← queue.size
    values ← empty list
    for i in 1 … levelSize:
        node ← queue.pop()
        values.add(node.val)
        if node.left  exists: queue.add(node.left)
        if node.right exists: queue.add(node.right)

    # ----- minimum swaps for this level -----
    sortedVals ← values sorted increasingly
    posMap ← { value → index in sortedVals }      # HashMap / unordered_map
    permutation ← [posMap[value] for value in values]
    visited ← [false] * levelSize
    swapsForLevel ← 0

    for i in 0 … levelSize-1:
        if visited[i] or permutation[i] == i: continue
        cycleSize ← 0
        j ← i
        while not visited[j]:
            visited[j] ← true
            j ← permutation[j]
            cycleSize += 1
        swapsForLevel += cycleSize - 1

    answer += swapsForLevel

return answer
```

---

### 1.4 Complexity analysis  

| Step | Time | Space |
|------|------|-------|
| BFS traversal | **O(N)** | **O(N)** (queue) |
| Sorting each level | ∑ `k_i log k_i`  (kᵢ = nodes on level i)  ≤ **O(N log N)** | **O(kᵢ)** temporary |
| Cycle detection | **O(N)** | **O(N)** temporary |
| **Total** | **O(N log N)** | **O(N)** |

For a balanced tree the height is `O(log N)` and each level has at most `2^h` nodes, so the practical running time is close to linear.

---

## 2. Code Implementations  

### 2.1 Java  

```java
import java.util.*;

/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left, right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    public int minimumOperations(TreeNode root) {
        if (root == null) return 0;
        int answer = 0;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);

        while (!q.isEmpty()) {
            int levelSize = q.size();
            int[] values = new int[levelSize];
            int idx = 0;

            // collect the values of this level
            for (int i = 0; i < levelSize; i++) {
                TreeNode node = q.poll();
                values[idx++] = node.val;
                if (node.left != null)  q.offer(node.left);
                if (node.right != null) q.offer(node.right);
            }

            // build sorted array and mapping value -> sorted index
            int[] sorted = values.clone();
            Arrays.sort(sorted);
            Map<Integer, Integer> pos = new HashMap<>();
            for (int i = 0; i < sorted.length; i++) pos.put(sorted[i], i);

            // permutation that maps current positions to sorted positions
            int[] perm = new int[values.length];
            for (int i = 0; i < values.length; i++)
                perm[i] = pos.get(values[i]);

            // cycle detection
            boolean[] visited = new boolean[perm.length];
            int swaps = 0;
            for (int i = 0; i < perm.length; i++) {
                if (visited[i] || perm[i] == i) continue;
                int cycle = 0;
                int j = i;
                while (!visited[j]) {
                    visited[j] = true;
                    j = perm[j];
                    cycle++;
                }
                swaps += cycle - 1;
            }
            answer += swaps;
        }
        return answer;
    }
}
```

---

### 2.2 Python 3  

```python
from collections import deque
from typing import Optional

# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val: int = 0, left: Optional['TreeNode'] = None,
                 right: Optional['TreeNode'] = None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def minimumOperations(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0

        answer = 0
        q = deque([root])

        while q:
            level_size = len(q)
            values = []

            for _ in range(level_size):
                node = q.popleft()
                values.append(node.val)
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)

            sorted_vals = sorted(values)
            pos = {v: i for i, v in enumerate(sorted_vals)}
            perm = [pos[v] for v in values]

            visited = [False] * len(perm)
            swaps = 0
            for i in range(len(perm)):
                if visited[i] or perm[i] == i:
                    continue
                cycle = 0
                j = i
                while not visited[j]:
                    visited[j] = True
                    j = perm[j]
                    cycle += 1
                swaps += cycle - 1

            answer += swaps

        return answer
```

---

### 2.3 C++ (g++‑17)  

```cpp
#include <bits/stdc++.h>
using namespace std;

// Definition for a binary tree node.
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    int minimumOperations(TreeNode* root) {
        if (!root) return 0;

        int answer = 0;
        queue<TreeNode*> q;
        q.push(root);

        while (!q.empty()) {
            int levelSize = q.size();
            vector<int> values(levelSize);
            int i = 0;

            // 1️⃣ gather values of the current level
            for (int j = 0; j < levelSize; ++j) {
                TreeNode* node = q.front(); q.pop();
                values[i++] = node->val;
                if (node->left)  q.push(node->left);
                if (node->right) q.push(node->right);
            }

            // 2️⃣ sorted copy + value → index map
            vector<int> sorted = values;
            sort(sorted.begin(), sorted.end());
            unordered_map<int,int> pos;
            for (int k = 0; k < (int)sorted.size(); ++k) pos[sorted[k]] = k;

            // 3️⃣ build the permutation
            vector<int> perm(values.size());
            for (int k = 0; k < (int)values.size(); ++k)
                perm[k] = pos[values[k]];

            // 4️⃣ count cycles
            vector<bool> vis(perm.size(), false);
            int swaps = 0;
            for (int k = 0; k < (int)perm.size(); ++k) {
                if (vis[k] || perm[k] == k) continue;
                int cycle = 0, j = k;
                while (!vis[j]) {
                    vis[j] = true;
                    j = perm[j];
                    ++cycle;
                }
                swaps += cycle - 1;
            }
            answer += swaps;
        }
        return answer;
    }
};
```

> All three implementations share the same logic:
> *BFS → collect level values → sort → cycle‑based swap count → accumulate.*

---

## 3. The “Good – Bad – Ugly” of This Problem  

| Aspect | What’s good | What’s problematic | Ugly / “gotchas” |
|--------|-------------|--------------------|------------------|
| **Good** | • BFS keeps the solution clean and intuitive.<br>• Cycle detection is `O(k)` per level – no quadratic brute force.<br>• The same logic works for any language (Java, Python, C++). | – | – |
| **Bad** | • Sorting every level costs `O(N log N)` – for a highly unbalanced tree the deepest level may contain almost `N` nodes, still okay but not constant‑time.<br>• Extra hash‑maps per level add overhead, but they’re tiny compared to `N`. | – |
| **Ugly** | The first naïve solution you might write uses a *bit‑hack* that packs value and original index into a single `long`. While it works, it’s error‑prone and unreadable for interviewees. | ✔️ Avoid the bit‑hack; use the permutation‑plus‑cycle method for clarity. | – |

---

## 4. Interview‑Ready Tips  

1. **Show the intuition first** – explain that each depth level is an independent “array” that needs to be sorted.  
2. **Demonstrate the cycle algorithm** – most interviewers will recognize the pattern.  
3. **Discuss complexity** – emphasise that you sort *only* the nodes at that depth, not the whole tree.  
4. **Mention edge cases** – single‑node tree → 0 swaps, deep unbalanced trees → still linear in practice.  
5. **Talk about memory** – BFS queue holds at most one level, so memory is O(2^h) ≤ O(N).

---

## 5. SEO‑Optimized Blog Article  

> **Title (≈ 55 chars)**  
> “LeetCode 2471 Explained – Java/Python/C++ Binary‑Tree Level‑Sort Interview Guide”

> **Meta‑Description (≈ 160 chars)**  
> “Master LeetCode 2471: Minimum Number of Operations to Sort a Binary Tree by Level. Learn the BFS + cycle‑counting solution in Java, Python and C++. Perfect for your next coding interview.”

> **Keywords**  
> `LeetCode 2471`, `minimum swaps to sort array`, `binary tree level sort`, `BFS interview question`, `job interview algorithms`, `Java Python C++ solutions`, `cycle detection algorithm`, `binary tree depth sorting`.

---

### Blog Body  

#### 5.1 Why LeetCode 2471 is a “Gold‑mine” for Interviewees  

* **Binary tree knowledge** – most candidates already know BFS/DFS.  
* **Array manipulation twist** – tests the ability to map a tree problem to a classic array problem.  
* **Multiple language support** – a great chance to show that you can translate the logic into Java, Python, or C++ on the fly.  

---

#### 5.2 The Good – What Works Like a Charm  

| Feature | Why it’s Good |
|---------|---------------|
| **Level‑by‑level BFS** | Keeps the algorithm intuitive; you only look at one depth at a time. |
| **Sorting + permutation** | Turns a tree swap problem into an array problem – reusable knowledge. |
| **Cycle counting** | Linear‑time, O(1) extra per node – no need to bubble‑sort or use `O(k²)` swapping loops. |
| **Clear separation of concerns** | Easy to unit‑test each part (collect values, compute swaps). |

---

#### 5.3 The Bad – Potential Pitfalls  

| Pitfall | Mitigation |
|---------|------------|
| **Sorting each level individually** | For a pathological chain tree the deepest level can contain almost `N` nodes. The `log N` factor is unavoidable, but remember it in your analysis. |
| **HashMap overhead** | The mapping from value to sorted index is necessary for clarity. If you’re extremely tight on memory, you can avoid the map by using an `unordered_map` or even a `vector` of pairs. |
| **Large input size** | The BFS queue may grow to `O(N)` in an unbalanced tree. Python’s `deque` handles this fine; Java’s `LinkedList` and C++’s `queue` do too. |

---

#### 5.4 The Ugly – Tricky or Obscure Parts  

* **Bit‑packing hack** – Some accepted solutions pack the value and its original index into a single `long` and sort those. This is clever but hard to read and maintain.  
* **Permutation vs. index mapping** – Forgetting that you need *indices* (not values) in the permutation leads to an off‑by‑one error.  
* **Cycle detection edge cases** – A cycle of length 1 (already in place) adds **zero** swaps. Be sure to skip those.  

---

#### 5.5 Final Thought – Why You Should Nail This Question  

* **Shows you can translate a tree problem into a linear‑time array trick.**  
* **Demonstrates familiarity with BFS, sorting, hash‑maps, and graph cycles – all staples of coding interviews.**  
* **Exhibits language‑agnostic thinking** – we’ve provided clear, idiomatic solutions in Java, Python, and C++.

Mastering LeetCode 2471 is a *significant confidence booster* before your next job interview. Happy coding!  

---

## 6. Quick‑Start: Running the Code  

| Language | How to compile/run |
|----------|--------------------|
| **Java** | `javac Solution.java` then run with your own test harness. |
| **Python** | `python3 - <<EOF\n…\nEOF` (paste the code and call `Solution().minimumOperations(root)`). |
| **C++** | `g++ -std=c++17 solution.cpp -O2 && ./a.out` (wrap in a `main()` that builds a test tree). |

---

### 6.1  Sample `main()` for each language (optional)  

```java
// Java
public static void main(String[] args) {
    TreeNode root = new TreeNode(1);
    root.left  = new TreeNode(4);
    root.right = new TreeNode(3);
    // … build the rest of the tree
    Solution sol = new Solution();
    System.out.println(sol.minimumOperations(root));
}
```

```python
# Python
def main():
    root = TreeNode(1)
    root.left = TreeNode(4)
    root.right = TreeNode(3)
    # … build the rest of the tree
    print(Solution().minimumOperations(root))

if __name__ == "__main__":
    main()
```

```cpp
// C++
int main() {
    TreeNode* root = new TreeNode(1);
    root->left = new TreeNode(4);
    root->right = new TreeNode(3);
    // … build the rest of the tree
    Solution sol;
    cout << sol.minimumOperations(root) << endl;
}
```

---

## 7. Final Checklist for Interview Day  

- [ ] Understand the problem statement in 30 seconds.  
- [ ] Write the BFS collector first.  
- [ ] Code the swap‑counting routine separately.  
- [ ] Run through a couple of edge cases mentally.  
- [ ] Explain your time and space complexity clearly.  
- [ ] Optionally, ask the interviewer if they’d like a quick demo in a language of their choice.  

Good luck, and may your binary‑tree nodes always fall into place!