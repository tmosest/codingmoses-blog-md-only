---
title: LeetCode 691. Stickers to Spell Word - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 691 – **Stickers to Spell Word**

> **Problem statement (shortened)**  
> You have an unlimited supply of *n* different stickers.  
> Each sticker is a lowercase English word.  
> Using the letters on the stickers (you can cut any letter out of any sticker, rearrange them, and you can reuse a sticker any number of times) you must spell a given *target* string.  
> Return the *minimum* number of stickers required, or **‑1** if it’s impossible.  

|  |  |
|---|---|
| **n** | 1 ≤ n ≤ 50 |
| **stickers[i] length** | 1 ≤ ≤ 10 |
| **target length** | 1 ≤ ≤ 15 |
| **all characters** | lowercase English |

> **Why it matters**  
> The constraints look small, but the problem is NP‑hard.  
> Interviewers love this problem because it forces you to think about *state representation* (bitmask, string, freq array) and *search* (BFS, DFS + memoization, DP).

---

## 2.  Solution Overview

The standard “easiest to remember” approach is a **Breadth‑First Search (BFS)** over *remaining target strings*.

1. **Sort** every sticker and the target alphabetically.  
   This allows the `filter()` operation to run in *O(|target| + |sticker|)* by scanning both sorted strings.
2. Put the sorted target in a queue.  
   Each level of the BFS represents using one more sticker.
3. For every state, try every sticker:  
   *Use `filter()`* – remove letters that the sticker can cover.  
   *If nothing remains → we’re done* (current depth + 1 stickers).  
   *Otherwise enqueue the new state* if we haven’t seen it before.
4. If the queue empties → impossible → return **‑1**.

The BFS guarantees that the first time we reach an empty string we used the minimal number of stickers.

---

## 3.  Code

### 3.1  Java – BFS (the “Gangjig” solution)

```java
import java.util.*;

class Solution {
    public int minStickers(String[] stickers, String target) {
        int n = stickers.length;

        // Sort each sticker and the target
        target = sortChars(target);
        for (int i = 0; i < n; ++i) stickers[i] = sortChars(stickers[i]);

        Queue<String> q = new LinkedList<>();
        Set<String> visited = new HashSet<>();

        q.offer(target);
        int steps = 0;

        while (!q.isEmpty()) {
            int size = q.size();
            steps++;                     // we are using one more sticker
            while (size-- > 0) {
                String cur = q.poll();
                for (String st : stickers) {
                    String nxt = filter(cur, st);
                    if (nxt.isEmpty()) return steps;
                    if (!nxt.equals(cur) && visited.add(nxt)) {
                        q.offer(nxt);
                    }
                }
            }
        }
        return -1;                      // impossible
    }

    // Remove letters of st from a, both sorted
    private String filter(String a, String b) {
        StringBuilder sb = new StringBuilder();
        int j = 0; // pointer into b
        for (char c : a.toCharArray()) {
            boolean found = false;
            while (j < b.length() && b.charAt(j) <= c) {
                if (b.charAt(j++) == c) { // consume this letter
                    found = true;
                    break;
                }
            }
            if (!found) sb.append(c);
        }
        return sb.toString();
    }

    private String sortChars(String s) {
        char[] arr = s.toCharArray();
        Arrays.sort(arr);
        return new String(arr);
    }
}
```

**Complexities**

|  |  |
|---|---|
| **Time** |  `O( 2^m * n * m )`  (worst‑case, *m* = target length) – but practically fast for *m* ≤ 15. |
| **Space** |  `O( 2^m )`  for visited states (worst‑case). |

---

### 3.2  Python – Memoised DFS / DP (bit‑mask)

The Python implementation uses a *recursive* memoisation with a **bitmask** of the remaining target characters.  
This is the most concise yet highly efficient approach.

```python
class Solution:
    def minStickers(self, stickers: list[str], target: str) -> int:
        from functools import lru_cache

        # Pre‑process stickers into frequency counters
        m = len(target)
        target_letters = list(target)

        # Build a list of sticker masks (bitmask of letters it can supply)
        masks = []
        for st in stickers:
            mask = 0
            for ch in set(st):
                mask |= 1 << (ord(ch) - 97)
            masks.append(mask)

        @lru_cache(None)
        def dfs(state: int) -> int:
            if state == 0:
                return 0
            ans = float('inf')
            # Try each sticker
            for mask in masks:
                # If sticker does not cover any needed letter, skip
                if mask & state == 0:
                    continue
                # Build new state after using this sticker
                new_state = state
                for i, ch in enumerate(target_letters):
                    if state & (1 << i) and (mask & (1 << (ord(ch) - 97))):
                        new_state &= ~(1 << i)
                ans = min(ans, 1 + dfs(new_state))
            return ans

        res = dfs((1 << m) - 1)
        return -1 if res == float('inf') else res
```

**Complexities**

|  |  |
|---|---|
| **Time** |  `O( 2^m * n )`  (m ≤ 15 → 32 768 states) |
| **Space** |  `O( 2^m )` for memoisation + recursion stack |

---

### 3.3  C++ – DP with Bitmask (iterative)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minStickers(vector<string>& stickers, string target) {
        int m = target.size();
        int full = (1 << m) - 1;

        // Pre‑compute sticker masks
        vector<int> masks;
        for (auto& st : stickers) {
            int mask = 0;
            for (char c : st) mask |= 1 << (c - 'a');
            masks.push_back(mask);
        }

        const int INF = 1e9;
        vector<int> dp(1 << m, INF);
        dp[0] = 0;          // no letters needed → 0 stickers

        for (int state = 0; state <= full; ++state) {
            if (dp[state] == INF) continue;
            for (int smask : masks) {
                int new_state = state;
                // Try to cover each remaining letter with this sticker
                for (int i = 0; i < m; ++i) {
                    if (!(state & (1 << i))) continue;          // already covered
                    if (smask & (1 << (target[i] - 'a')))
                        new_state &= ~(1 << i);                // remove it
                }
                dp[new_state] = min(dp[new_state], dp[state] + 1);
            }
        }
        return dp[full] == INF ? -1 : dp[full];
    }
};
```

**Complexities**

|  |  |
|---|---|
| **Time** |  `O( 2^m * n * m )` – with *m* ≤ 15 still fast |
| **Space** |  `O( 2^m )` |

---

## 4.  Blog Article  
*(SEO‑optimized – “Stickers to Spell Word” + “LeetCode 691” + “Interview Prep”)*

---

### 🚀 **Stickers to Spell Word: The Good, The Bad, and The Ugly**

> **Keywords** – *LeetCode 691*, *Stickers to Spell Word*, *DP bitmask*, *BFS solution*, *coding interview*, *algorithm interview questions*, *job interview tips*, *Java BFS*, *Python DP*, *C++ bitmask*.

---

#### 1️⃣ What’s the problem about?

You’re given a handful of stickers, each a small word.  
Your goal: **cut out letters** from these stickers (you can cut *any* letter, reorder them, and use stickers any number of times) to spell a target word.

*Return the minimal number of stickers needed or -1 if it can’t be done.*

---

#### 2️⃣ Why is it *so* important in interviews?

- **Algorithmic depth**: It touches on *state compression*, *bitmask DP*, *BFS over strings* – all core concepts interviewers love.
- **Edge‑case thinking**: You have to consider *uncoverable letters*, *duplicate stickers*, *overlapping coverage*.
- **Time‑space trade‑off**: Solving it naïvely can explode; finding a clever, compact representation is the key.

If you nail this question, you’ll impress recruiters with both *coding* and *design* chops.

---

#### 3️⃣ The Good – Intuition & What Works

1. **Sort to simplify matching**  
   Sorting stickers and the target lets you run `filter()` in linear time.  
   No nested loops over characters – just two pointers.

2. **BFS guarantees optimality**  
   Every layer of BFS represents *using one more sticker*.  
   The first time we hit an empty target string is the answer.

3. **State compression with bitmasks**  
   Python’s and C++’s DP solutions compress the remaining letters into a single integer (`1 << m`).  
   Only 2^15 = 32,768 states – tiny and fast.

4. **Memoisation cuts recursion**  
   In Python, `@lru_cache` stores results for every sub‑problem, turning an exponential DFS into a manageable search.

---

#### 4️⃣ The Bad – Common Pitfalls

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Using a plain queue of strings** | Strings can be long (up to 15 chars) and many duplicates – memory blowup. | Use *sorted strings* + a `Set` to track visited. |
| **Re‑computing sticker masks each DFS call** | Adds *O(n)* overhead for every recursive step. | Pre‑compute a bitmask per sticker once. |
| **Ignoring duplicate stickers** | You might waste time exploring the same sticker again. | Deduplicate stickers or use a `Set` of masks. |
| **Wrong base case in DFS** | Returning 0 for non‑empty state leads to infinite loops. | Base case: `state == 0 → 0 stickers`. |
| **Not handling impossible cases** | Returning `float('inf')` directly can cause overflow. | After DP, check for `INF` and return -1. |

---

#### 5️⃣ The Ugly – When Complexity Goes Wild

If you naïvely try to generate **all combinations** of stickers, you’ll create *exponential* search trees:

- Every string state can branch into *n* stickers → `O(n^m)` paths.
- Without a smart mask or sorted string trick, you’ll time‑out on larger targets.

**Solution**:  
Keep your *state representation* tight (bitmask or sorted string) and avoid duplicate work.  
That’s the secret sauce to turning “ugly” into “efficient”.

---

#### 6️⃣ Quick‑Start: Which language do you prefer?

| Language | What you’ll learn | Code snippet |
|----------|-------------------|--------------|
| **Java (BFS)** | Two‑pointer string matching + queue traversal | `[Java code]` |
| **Python (DP)** | Recursion + bitmask + `lru_cache` | `[Python code]` |
| **C++ (iterative DP)** | Bitmask DP + arrays | `[C++ code]` |

Pick the one that matches your interview stack.

---

#### 6️⃣ Final Thought: Show *Design* Not just *Implementation*

When answering in a live interview:

1. **Explain the state representation** (why bitmask? why sorted strings?)  
2. **Walk through BFS layers** – emphasise optimality.  
3. **Mention pruning & memoisation** – show you know the time‑space trade‑off.  
4. **Handle edge cases** – test “impossible” and “single‑letter” scenarios.

You’ll turn this coding question into a **design‑driven narrative** that recruiters love.

---

#### 🎯 **Takeaway**

- LeetCode 691 is *one of the best* interview questions for *job seekers*.  
- Use **BFS** with sorted strings *or* **bitmask DP** with memoisation.  
- Keep the code clean, the states small, and the base cases correct.

---

> **Ready to practice?**  
> Clone the repo, run the Java, Python, and C++ solutions, and test against the official LeetCode test cases.  
> When you’re comfortable, add a *deduplication* step and try to reduce memory further – that’s how you go from *good* to *great*.

Happy coding! 🎉

--- 

*(End of article)*

--- 

## 5.  Conclusion

- We covered three idiomatic solutions: Java BFS, Python memoised DFS, C++ iterative DP.
- The Java implementation is a “real‑world” interview favourite – concise, fast, and easy to explain.
- The Python and C++ snippets showcase the power of *bitmask state compression* – the “ultimate” algorithmic trick for LeetCode 691.
- The blog article explains why the problem matters, how to avoid pitfalls, and how to frame your answer for recruiters.

Happy interviewing, and may your job offers come *in just the right number of stickers*! 🌟