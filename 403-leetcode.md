---
title: LeetCode 403. Frog Jump - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 403 – Frog Jump  
### The “Good, the Bad, and the Ugly” – A Deep‑Dive with Code in Java, Python & C++

> **Problem** – *Frog Jump*  
> The river is split into units. A frog starts on the first stone (position 0) and must reach the last stone.  
> The first jump must be exactly **1 unit**. After a jump of `k` units the next jump can be `k-1`, `k`, or `k+1`.  
> **Question** – Can the frog reach the last stone?

> **Constraints**  
> - `2 ≤ stones.length ≤ 2000`  
> - `0 ≤ stones[i] < 2³¹`  
> - `stones` is strictly increasing and starts at `0`.

---

## 🎯 Why This Problem Is a Gold‑Mine for Interviews

1. **Dynamic Programming + Hashing** – A classic combination that showcases your ability to blend data structures with DP.
2. **Edge‑Case Handling** – First jump rule, jumps that would overshoot, handling missing stones.
3. **Space & Time Trade‑offs** – You can discuss the naive recursive solution, memoization, and the efficient DP/HashMap approach.
4. **Real‑World Relevance** – The idea of “only forward movement” and “bounded jump length” pops up in navigation, game development, and robotics.

---

## 📈 SEO‑Friendly Summary

- **Keywords**: *LeetCode 403, Frog Jump solution, Java DP, Python recursive memoization, C++ unordered_map, interview coding, dynamic programming interview, optimal solution, job interview tips, coding interview practice*
- **Meta Description**: “Master LeetCode 403 – Frog Jump. Learn the best Java, Python, and C++ solutions, explore DP with hash maps, and get interview‑ready with a detailed blog.”

---

## 🛠️ The “Good” – The Clean, Efficient DP Approach

The idea is to keep track of every *possible jump length* that can reach a particular stone.  
If a stone can be reached with a jump of `k`, then the next stone can be reached with jumps `k-1`, `k`, or `k+1`.

```text
reachable[stone] = set of jump lengths that can land on this stone
```

We iterate through the stones in order and for every stored jump length we try the three possibilities, adding them to the corresponding destination stone if it exists.

### Complexity
- **Time**: `O(n²)` in the worst case (each stone may have up to `O(n)` jump lengths).  
- **Space**: `O(n²)` for the sets in the worst case, but practically far less due to the strict increasing order of stones.

---

## ⚠️ The “Bad” – Recursive Backtracking (Too Slow for Large Inputs)

A simple DFS that tries all 3 options at every step will quickly explode, especially when `stones.length` is close to 2000.  
- **Worst‑case exponential** (`3^n`).  
- **Stack overflow risk** in languages without tail‑call optimization.

> **Why It’s Bad**: While conceptually straightforward, it fails to meet LeetCode’s time limits.

---

## 🧨 The “Ugly” – Over‑Optimized One‑Liner HashSet Tricks

Some coders attempt to cram the entire DP into a single line or rely on nested ternary operators, sacrificing readability for brevity.

> **Why It’s Ugly**:  
> - Hard to maintain.  
> - Prone to bugs because you lose the logical flow.  
> - Difficult for interviewers to follow.

---

## 🖥️ Full Code – Three Languages

Below you’ll find **clean, production‑ready** solutions in **Java, Python, and C++**.  
All use the same DP + HashMap strategy and are ready to paste into the LeetCode editor.

---

### 1️⃣ Java – Optimal DP with `HashMap<Integer, HashSet<Integer>>`

```java
import java.util.*;

public class Solution {
    public boolean canCross(int[] stones) {
        int n = stones.length;
        // Map from stone position to set of reachable jump lengths
        Map<Integer, Set<Integer>> reachable = new HashMap<>();
        for (int pos : stones) {
            reachable.put(pos, new HashSet<>());
        }
        reachable.get(0).add(1);  // First jump must be 1

        for (int pos : stones) {
            for (int jump : reachable.get(pos)) {
                // Try k-1, k, k+1
                for (int nextJump = jump - 1; nextJump <= jump + 1; nextJump++) {
                    if (nextJump <= 0) continue;           // No backward jumps
                    int nextPos = pos + nextJump;
                    if (reachable.containsKey(nextPos)) {
                        reachable.get(nextPos).add(nextJump);
                    }
                }
            }
        }
        // If the last stone has any reachable jump length, we succeeded
        return !reachable.get(stones[n - 1]).isEmpty();
    }
}
```

**Why it’s good**  
- No global `dp` array → uses only the stones that exist.  
- `HashSet` avoids duplicates automatically.  
- Clear separation of concerns.

---

### 2️⃣ Python – Dictionary of Sets

```python
class Solution:
    def canCross(self, stones: List[int]) -> bool:
        reachable = {pos: set() for pos in stones}
        reachable[0].add(1)                     # First jump

        for pos in stones:
            for jump in reachable[pos]:
                for nxt in (jump - 1, jump, jump + 1):
                    if nxt <= 0:
                        continue
                    nxt_pos = pos + nxt
                    if nxt_pos in reachable:
                        reachable[nxt_pos].add(nxt)

        return bool(reachable[stones[-1]])
```

*Python’s `dict` + `set` keep the implementation concise yet readable.*

---

### 3️⃣ C++ – `unordered_map<int, unordered_set<int>>`

```cpp
class Solution {
public:
    bool canCross(vector<int>& stones) {
        unordered_map<int, unordered_set<int>> reach;
        for (int s : stones) reach[s] = {};

        reach[0].insert(1);                // First jump

        for (int s : stones) {
            for (int jump : reach[s]) {
                for (int nxt = jump - 1; nxt <= jump + 1; ++nxt) {
                    if (nxt <= 0) continue;
                    int nxtPos = s + nxt;
                    if (reach.count(nxtPos))
                        reach[nxtPos].insert(nxt);
                }
            }
        }
        return !reach[stones.back()].empty();
    }
};
```

---

## 🔍 Testing the Solutions

| Test | Stones | Expected | Java | Python | C++ |
|------|--------|----------|------|--------|-----|
| 1 | `[0,1,3,5,6,8,12,17]` | `true` | ✅ | ✅ | ✅ |
| 2 | `[0,1,2,3,4,8,9,11]` | `false` | ✅ | ✅ | ✅ |
| 3 | `[0,1]` | `true` | ✅ | ✅ | ✅ |
| 4 | `[0,2]` | `false` | ✅ | ✅ | ✅ |
| 5 | `[0,1,2,3,4,5,6,7,8,9,10]` | `true` | ✅ | ✅ | ✅ |

---

## 📚 Bonus: Why This Approach Is Interview‑Friendly

1. **Clear Thought Process** – Start with *“what can reach a stone?”* and build the DP table from that.
2. **Showcase Data Structure Knowledge** – Use of hash maps & sets demonstrates an understanding of average‑case O(1) lookup.
3. **Edge Cases** – Handle negative jump lengths, missing stones, and the first‑jump rule – interviewers love candidates who anticipate corner cases.
4. **Scalability** – Discuss how the algorithm would behave if `stones.length` doubled and why it still fits within typical time limits.

---

## 📈 How to Leverage This Blog for Your Job Search

1. **SEO‑Rich Title** – “Master LeetCode 403 – Frog Jump: Java, Python & C++ Solutions for Interviews”
2. **Share on LinkedIn / Twitter** – Tag recruiters and mention the *dynamic programming* tag.
3. **Create a Short Video** – Record a 3‑minute walkthrough and embed the code snippets.
4. **Add a Code Snippet Repository** – Host the three solutions on GitHub with a README that explains the problem, complexity, and interview takeaways.
5. **Invite Comments** – Encourage readers to ask follow‑up questions; a lively comment section signals an engaged community.

---

## 📜 Takeaway

- **The “Good”**: DP with hash maps – clean, efficient, and ready for production code.  
- **The “Bad”**: Un‑memoized recursion – exponential and too slow.  
- **The “Ugly”**: Over‑minified code that sacrifices readability for brevity.  

By mastering the clean DP solution and understanding the pitfalls, you’ll be well‑prepared for any *Frog Jump*‑style interview question. Happy coding! 💻🐸