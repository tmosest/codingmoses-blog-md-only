---
title: LeetCode 573. Squirrel Simulation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 573. Squirrel Simulation  
**Difficulty:** Medium  
**LeetCode URL:** <https://leetcode.com/problems/squirrel-simulation/>  

> **Goal** – The squirrel starts at a given cell, must pick up *all* nuts one‑by‑one, drop each nut under the tree and finally return to the tree.  
> **Movement** – Four directions only (up, down, left, right).  
> **Output** – The minimum number of moves required.

---

## 📌 Core Insight

When you pick up a nut, you always have to walk:

1. **From the tree to the nut** (or from the squirrel to the nut if it’s the first one).  
2. **From the nut back to the tree** to drop it.

If we naïvely sum *2 × distance(nut, tree)* for every nut we double‑count the leg that the squirrel takes *first* from its starting position to the first nut.  
The best we can do is pick the nut that gives the largest *saving*:

```
saving = distance(nut, tree) – distance(nut, squirrel)
```

The minimal distance is therefore:

```
total = 2 × Σ distance(nut, tree)
minimal = total – max(saving)
```

This formula runs in **O(n)** time and **O(1)** extra space.

---

## ✅ Implementations

Below you’ll find three clean, production‑ready solutions – one in **Java**, **Python**, and **C++**.  
All use the same Manhattan‑distance helper.

### Java

```java
public class Solution {
    public int minDistance(int height, int width,
                           int[] tree, int[] squirrel,
                           int[][] nuts) {
        int total = 0;
        int maxSaving = Integer.MIN_VALUE;

        for (int[] nut : nuts) {
            int distToTree   = manhattan(nut, tree);
            int distToSquirrel = manhattan(nut, squirrel);

            total += distToTree * 2;
            maxSaving = Math.max(maxSaving, distToTree - distToSquirrel);
        }

        return total - maxSaving;
    }

    private int manhattan(int[] a, int[] b) {
        return Math.abs(a[0] - b[0]) + Math.abs(a[1] - b[1]);
    }
}
```

### Python

```python
class Solution:
    def minDistance(self, height: int, width: int,
                    tree: List[int], squirrel: List[int],
                    nuts: List[List[int]]) -> int:
        total = 0
        max_saving = float('-inf')

        for nut in nuts:
            dist_tree = self._manhattan(nut, tree)
            dist_squirrel = self._manhattan(nut, squirrel)

            total += 2 * dist_tree
            max_saving = max(max_saving, dist_tree - dist_squirrel)

        return total - max_saving

    @staticmethod
    def _manhattan(a: List[int], b: List[int]) -> int:
        return abs(a[0] - b[0]) + abs(a[1] - b[1])
```

### C++

```cpp
class Solution {
public:
    int minDistance(int height, int width,
                    vector<int>& tree, vector<int>& squirrel,
                    vector<vector<int>>& nuts) {
        long long total = 0;          // use long long to avoid overflow
        long long maxSaving = LLONG_MIN;

        for (const auto& nut : nuts) {
            long long distTree   = manhattan(nut, tree);
            long long distSquirrel = manhattan(nut, squirrel);

            total += 2 * distTree;
            maxSaving = max(maxSaving, distTree - distSquirrel);
        }
        return static_cast<int>(total - maxSaving);
    }

private:
    long long manhattan(const vector<int>& a, const vector<int>& b) const {
        return llabs(a[0] - b[0]) + llabs(a[1] - b[1]);
    }
};
```

---

## 📚 Blog Article – “Squirrel Simulation: The Good, the Bad, and the Ugly”

> **SEO Keywords:** *Squirrel Simulation LeetCode*, *minDistance algorithm*, *Java Python C++ solution*, *interview coding problem*, *Manhattan distance*, *algorithm design*  

---

### 1. Introduction

The **Squirrel Simulation** problem is a staple on LeetCode for those preparing for coding interviews. It asks you to find the minimal number of moves a squirrel must make to collect all nuts and drop them under a tree, moving only up, down, left, or right. Although the statement feels whimsical, it actually touches on classic algorithmic concepts: **Manhattan distance** and **greedy optimization**.

### 2. Why This Problem Rocks

- **Clear Constraints** – No obstacles, only grid boundaries.  
- **No need for BFS** – Since there are no obstacles, Manhattan distance is exact.  
- **O(n) Solution** – A single pass through the nuts list gives you the answer instantly.

### 3. The Elegant Solution – “The Good”

| Step | What Happens | Why It Works |
|------|--------------|--------------|
| 1 | Compute **dist(nut, tree)** for every nut. | Gives the two legs that will always be taken: to tree and back. |
| 2 | Sum `2 * dist(nut, tree)`. | We double‑count because each nut needs a round trip. |
| 3 | Compute **saving** = `dist(nut, tree) – dist(nut, squirrel)`. | If we start with this nut, we replace the *tree → nut* leg with *squirrel → nut*. |
| 4 | Subtract the largest **saving** from the total. | That’s the best possible first nut; all other nuts stay on the round‑trip pattern. |

> **Result** – A minimal move count in linear time and constant extra space.

### 4. Edge Cases and Gotchas – “The Bad”

- **Large Inputs** – With up to 5,000 nuts, integer overflow can sneak in. Use `long long` in C++ or `long` in Java/Python `int` (which is unbounded) safely.
- **Unreachable Cells** – The problem guarantees that every nut and the tree are within bounds, so no path‑finding is needed. If the problem changed to include obstacles, the simple Manhattan shortcut would be invalid.
- **Negative Coordinates** – The constraints say coordinates are non‑negative, but an implementation should be robust enough to handle any integer values.

### 5. When Things Get Messy – “The Ugly”

If you were to tackle a variant where the squirrel cannot traverse certain cells (e.g., “holes” or “water”), the Manhattan shortcut breaks down. You would need:

1. **Shortest Path Computation** – BFS from each nut to the tree and from the squirrel to each nut, which is O(n · (h·w)).  
2. **Dynamic Programming or TSP** – The problem morphs into a variant of the Travelling Salesman, which is NP‑hard.  

Thus, while the “Squirrel Simulation” problem is a wonderful teaching tool, extending it without care quickly spirals into complexity territory.

### 6. Why You Should Love the Solution

- **Simplicity** – A single loop, a helper method, and a bit of math.  
- **Performance** – Runs in microseconds on typical interview stacks.  
- **Adaptability** – The pattern (double‑count + subtract maximum saving) appears in other “collect‑and‑return” problems.

### 7. Takeaway for Job Interviews

- **Highlight the Greedy Argument** – Explain why picking the nut with maximum saving gives the optimal first move.  
- **Talk About Complexity** – O(n) time, O(1) space is a great selling point.  
- **Show Language Versatility** – Provide solutions in Java, Python, and C++ to demonstrate breadth.

---

## 🚀 Conclusion

The Squirrel Simulation problem is a shining example of how a seemingly playful challenge can illustrate powerful algorithmic thinking. By leveraging Manhattan distances and a clever greedy adjustment, we get a solution that is both elegant and efficient.

Whether you’re polishing your interview prep, crafting a portfolio project, or simply enjoying the joy of coding, this problem and its solution offer a delightful blend of clarity, challenge, and mastery.

Happy coding—and may your squirrels always collect the nuts in the shortest possible way!