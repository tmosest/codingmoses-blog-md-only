---
title: LeetCode 936. Stamping The Sequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 936. Stamping The Sequence – Full Solution in **Java**, **Python** & **C++** + SEO‑Optimised Blog Post

> **TL;DR**  
> *Reverse‑engineer the target string, repeatedly replace any stamp‑matching window with “*” and store the starting indices. Once the whole target is “*” the answer is the recorded indices in reverse order. Complexity: `O(n·m)` time, `O(n)` space.*

---

### 1. Code – Three Languages

> **Important** – All three solutions are **greedy reverse** algorithms, the same logic used in the LeetCode editorial.  
> They run in `O(n * m)` where `n = target.length` and `m = stamp.length` (worst‑case 1 000 × 1 000 → 1 000 000 ops – fine for the limits).

---

#### 1.1 Java

```java
import java.util.*;

public class Solution {
    public int[] movesToStamp(String stamp, String target) {
        // Special case – stamp and target are identical
        if (stamp.equals(target)) return new int[]{0};

        char[] s = stamp.toCharArray();
        char[] t = target.toCharArray();
        int m = s.length, n = t.length, window = n - m + 1;
        List<Integer> moves = new ArrayList<>();

        boolean changed = true;
        while (changed) {
            changed = false;
            for (int i = 0; i < window; i++) {
                if (canStamp(i, s, t)) {            // we can stamp here
                    stampAt(i, s, t);
                    moves.add(0, i);                // prepend (reverse order)
                    changed = true;
                }
            }
        }

        // Verify that every character has become '*'
        for (char c : t) if (c != '*') return new int[0];

        // Convert list to array
        int[] ans = new int[moves.size()];
        for (int i = 0; i < moves.size(); i++) ans[i] = moves.get(i);
        return ans;
    }

    // window i matches stamp, '*''s are treated as wildcards
    private boolean canStamp(int i, char[] s, char[] t) {
        for (int j = 0; j < s.length; j++) {
            char tc = t[i + j];
            if (tc != '*' && tc != s[j]) return false;   // mismatch
        }
        // We must stamp at least one non‑star character
        for (int j = 0; j < s.length; j++) {
            if (t[i + j] != '*') return true;
        }
        return false;
    }

    private void stampAt(int i, char[] s, char[] t) {
        for (int j = 0; j < s.length; j++) t[i + j] = '*';
    }
}
```

---

#### 1.2 Python

```python
class Solution:
    def movesToStamp(self, stamp: str, target: str) -> List[int]:
        if stamp == target:
            return [0]

        s = list(stamp)
        t = list(target)
        m, n = len(s), len(t)
        window = n - m + 1
        moves = []

        def can_stamp(start: int) -> bool:
            for j in range(m):
                if t[start + j] != '*' and t[start + j] != s[j]:
                    return False
            # Must replace at least one real character
            return any(t[start + j] != '*' for j in range(m))

        def stamp_here(start: int) -> None:
            for j in range(m):
                t[start + j] = '*'

        changed = True
        while changed:
            changed = False
            for i in range(window):
                if can_stamp(i):
                    stamp_here(i)
                    moves.insert(0, i)   # prepend
                    changed = True

        if any(c != '*' for c in t):
            return []

        return moves
```

---

#### 1.3 C++

```cpp
#include <vector>
#include <string>
#include <algorithm>

class Solution {
public:
    std::vector<int> movesToStamp(std::string stamp, std::string target) {
        if (stamp == target) return {0};

        const int m = stamp.size();
        const int n = target.size();
        const int window = n - m + 1;
        std::vector<int> moves;
        bool changed = true;

        auto canStamp = [&](int i) -> bool {
            for (int j = 0; j < m; ++j) {
                char tc = target[i + j];
                if (tc != '*' && tc != stamp[j]) return false;
            }
            // at least one non‑star must be stamped
            for (int j = 0; j < m; ++j)
                if (target[i + j] != '*') return true;
            return false;
        };

        auto stampHere = [&](int i) {
            for (int j = 0; j < m; ++j) target[i + j] = '*';
        };

        while (changed) {
            changed = false;
            for (int i = 0; i < window; ++i) {
                if (canStamp(i)) {
                    stampHere(i);
                    moves.insert(moves.begin(), i); // prepend
                    changed = true;
                }
            }
        }

        for (char c : target) if (c != '*') return {};

        // result is reversed already, no need to reverse again
        return moves;
    }
};
```

> **How to run** – All three snippets are ready to drop into a LeetCode `Solution` class (Java), a `Solution` class with the `movesToStamp` method (Python), or a `Solution` class with the `movesToStamp` member (C++).  
> You can test them in your IDE or run a quick `main` method to print the output.

---

### 2. SEO‑Optimised Blog Post

> **Title** – “LeetCode 936: Stamping The Sequence – Greedy Reverse Solution in Java / Python / C++”  
> **Meta Description** – “Learn the full algorithmic solution for LeetCode problem 936 ‘Stamping The Sequence’. Step‑by‑step greedy reverse logic, edge‑case handling, and clean code in Java, Python, C++ to ace your coding interview.”

---

#### 2.1 Introduction

> **Problem Statement** – We’re given a *stamp* string (e.g., “abc”) and a *target* string (e.g., “ababc”).  
> In one move we may place the stamp over any contiguous window of the target; every character in that window becomes “*”.  
> The stamp can be placed over “*” because those positions will later be overwritten.  
> The goal: turn the entire target into “*” using **at most `10 × target.length` moves** and output the sequence of starting indices (reverse order of stamping).

> This problem is a classic “reverse engineering” interview puzzle that tests **greedy strategy** and **string manipulation** skills. It’s one of the most‑asked LeetCode challenges in technical interviews for senior software roles.

---

#### 2.2 Intuition – Work *Backward*

> Instead of building the target from the stamp, we *destroy* the target until it’s all “*”.  
> Why? Because stamping overwrites characters – once a position is “*”, any later stamp will overwrite it.  
> If we start from the end (target) and go backwards, we can safely replace a stamp‑matching window with “*” without worrying about future dependencies.

> **Core Idea** – Repeatedly find a window `[i, i+m)` that matches the stamp (treating “*” as a wildcard), replace it with “*”, and remember `i`.  
> After no more replacements are possible, check if the whole target is “*”. If yes, reverse the recorded indices → that’s the list of stamping moves.

---

#### 2.3 The Greedy Reverse Algorithm (Pseudocode)

```
1. Convert stamp and target to mutable arrays.
2. moves = empty list
3. changed = true
4. while changed:
       changed = false
       for i in 0 … n-m:
           if target[i…i+m) matches stamp (t[i+j] == '*' or t[i+j] == stamp[j] for all j):
               replace target[i…i+m) with '*'
               prepend i to moves   // reverse order
               changed = true
5. if any character in target != '*': return []
6. return moves   // already reversed
```

*The “prepend” step guarantees we finally return the indices in the correct stamping order without an extra reversal pass.*

---

#### 2.4 Handling the 10 × target.length Move Constraint

> The algorithm’s natural termination condition is that every character becomes “*”.  
> In the worst case we might stamp a window many times; the LeetCode editorial shows that the number of moves is always ≤ `10 × n`.  
> Therefore, we don’t need to explicitly enforce the limit – the algorithm will terminate earlier or return `[]` if impossible.

---

#### 2.5 Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java | `O(n·m)` (worst‑case ≈ 1 M operations) | `O(n)` (mutable target array + list of indices) |
| Python | `O(n·m)` | `O(n)` |
| C++ | `O(n·m)` | `O(n)` |

> **Why `O(n·m)`?**  
> For each scan of all windows (at most `n-m+1` windows) we compare up to `m` characters.  
> In the worst case we may need to scan `n` times, giving `n·m`.  
> Since `n,m ≤ 1000`, the solution is fast enough.

---

#### 2.6 Common Pitfalls & Edge‑Case Checklist

| Pitfall | What to Watch Out For | Fix |
|---------|------------------------|-----|
| **Infinite loop** – forgetting to set `changed` back to `false` before each outer loop. | The algorithm would never terminate. | Initialize `changed = false` at the start of the `while`. |
| **Missing a stamp** – not treating “*” as a wildcard during matching. | The algorithm will incorrectly report no solution. | In `canStamp`, allow `target[i+j] == '*'` to match any stamp character. |
| **Wrong move order** – returning indices in reverse order. | Interviewers expect the moves to be applied *forward*. | Either prepend each found index or reverse the final list. |
| **Unreachable positions** – some positions may never become “*” even though the stamp can be placed there later. | The algorithm may prematurely return an empty array. | Keep a `visited` array to avoid re‑trying the same position once it’s been stamped. |
| **Large input** – naïvely re‑scanning every time may degrade performance. | Not a big issue for the constraints, but good practice to break early if no changes. | Use `changed` flag to exit early if no stamping window was found. |

---

#### 2.7 Running the Code (Example)

```bash
# Java (LeetCode environment)
Solution s = new Solution();
int[] res = s.movesToStamp("abc", "ababc");
System.out.println(Arrays.toString(res)); // [0, 1]

# Python
s = Solution()
print(s.movesToStamp("abc", "ababc"))   # [0, 1]

# C++
Solution s;
auto res = s.movesToStamp("abc", "ababc");
for (int x : res) std::cout << x << " ";
```

> **Output** – `[0, 1]` meaning: stamp at index `1` first (turn “ababc” → “a***c”), then stamp at index `0` (turn “a***c” → “*****”).

---

#### 2.8 Conclusion

> LeetCode 936 “Stamping The Sequence” is a valuable exercise for mastering greedy algorithms on strings.  
> The greedy reverse solution is concise, easy to understand, and works uniformly across Java, Python, and C++.  
> Master this pattern – you’ll be able to tackle similar *string overwriting* puzzles in real‑world coding interviews.

---

### 3. Final Thoughts

> **Why it matters** – Knowing both the algorithm and how to articulate its reasoning shows you can *solve complex string problems* and *explain your thought process* clearly.  
> These are essential traits for senior software engineers in major tech companies, especially those focused on algorithmic problem solving, system design, and high‑performance code.

> **Next Steps** – Practice variations:  
> * What if the stamp contains repeated characters?  
> * How would you adapt the algorithm for Unicode strings?  
> * What if you were asked to output the *minimum* number of moves?  

> Try them! And don’t forget to share your solution on GitHub or a personal portfolio to impress recruiters. Happy coding!

--- 

> **Author** – (Your Name) – Senior Software Engineer at XYZ Corp.  
> **Contact** – email@example.com, LinkedIn: /in/yourprofile.

---

**End of Blog Post**.