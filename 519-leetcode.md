---
title: LeetCode 519. Random Flip Matrix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 519. Random Flip Matrix – Full‑Stack Implementation (Java | Python | C++)  

Below you’ll find a **ready‑to‑copy** solution for every language that LeetCode supports, plus a **step‑by‑step blog post** that walks through the “good”, “bad”, and “ugly” parts of this classic interview problem.  
Feel free to drop the snippets into your IDE, run the provided tests, or paste them straight into the LeetCode editor.  

---

## 👨‍💻 Code – Fast, Memory‑Efficient, and Random‑Proof

> **Key Idea** – Map a *virtual* linear index to the real matrix index.  
> Each `flip` draws a random number `k` in `[0, remaining‑1]`.  
> If that index has already been flipped we store a *swap* in a hash map.  
> This turns the problem into a simple “reservoir sampling” trick with `O(1)` time and `O(#flips)` space.

| Language | Code |
|----------|------|
| **Java** | <details><summary>View Java</summary>```java
import java.util.*;

class Solution {
    private final int m, n;
    private int remaining;                     // cells that are still 0
    private final Map<Integer, Integer> map;   // virtual -> real mapping
    private final Random rand;

    public Solution(int m, int n) {
        this.m = m;
        this.n = n;
        this.remaining = m * n;
        this.map = new HashMap<>();
        this.rand = new Random();
    }

    /** @return [row, col] of a flipped cell */
    public int[] flip() {
        int r = rand.nextInt(remaining);      // pick a random free cell
        // real index may have been swapped earlier
        int real = map.getOrDefault(r, r);

        // choose the last free cell to fill the hole left by r
        int last = remaining - 1;
        int lastReal = map.getOrDefault(last, last);

        // map r to lastReal (so next time r will give lastReal)
        map.put(r, lastReal);
        // we no longer need last in the pool
        if (r == last) map.remove(r);

        remaining--;                         // one less free cell
        return new int[]{real / n, real % n};
    }

    public void reset() {
        map.clear();
        remaining = m * n;
    }
}
```</details> |
| **Python** | <details><summary>View Python</summary>```python
import random
from typing import List

class Solution:
    def __init__(self, m: int, n: int):
        self.m, self.n = m, n
        self.rem = m * n          # remaining zeros
        self.mapping = {}         # virtual -> real index

    def flip(self) -> List[int]:
        r = random.randint(0, self.rem - 1)
        real = self.mapping.get(r, r)

        last = self.rem - 1
        last_real = self.mapping.get(last, last)

        # map the chosen index to the last free cell
        self.mapping[r] = last_real
        if r == last:
            self.mapping.pop(r, None)

        self.rem -= 1
        return [real // self.n, real % self.n]

    def reset(self) -> None:
        self.mapping.clear()
        self.rem = self.m * self.n
```</details> |
| **C++** | <details><summary>View C++</summary>```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    int m, n, remaining;
    unordered_map<int,int> mp;   // virtual -> real mapping
    mt19937 rng;                 // modern random engine

public:
    Solution(int m, int n) : m(m), n(n), remaining(m*n) {
        rng.seed(random_device{}());
    }

    vector<int> flip() {
        uniform_int_distribution<int> dist(0, remaining - 1);
        int r = dist(rng);                  // pick a free virtual cell
        int real = mp.count(r) ? mp[r] : r; // translate to real index

        int last = remaining - 1;
        int lastReal = mp.count(last) ? mp[last] : last;

        // fill the hole left by r with the last free cell
        mp[r] = lastReal;
        if (r == last) mp.erase(r);

        --remaining;
        return { real / n, real % n };
    }

    void reset() {
        mp.clear();
        remaining = m * n;
    }
};
```</details> |

> **Why this works**  
> 1. Each call draws a random number from the *current* pool of free cells.  
> 2. We keep a bijection between the virtual indices `[0, remaining-1]` and the real indices `[0, m*n-1]`.  
> 3. After flipping, we replace the used index with the last free one, ensuring future draws never see the same index again.  
> 4. `reset()` simply clears the map and restores the original pool size.

---

## 📚 Blog Article – “The Good, The Bad, and The Ugly of LeetCode 519”

> **Target Audience:** Front‑end / Back‑end engineers, data‑structure enthusiasts, and anyone preparing for algorithm interviews.  
> **SEO Keywords:** *LeetCode 519*, *Random Flip Matrix*, *Java interview questions*, *Python interview problems*, *C++ interview algorithms*, *O(1) flip matrix*, *algorithm design*, *coding interview tips*.

---

### Title  
**The Good, The Bad, and The Ugly of LeetCode 519: Mastering Random Flip Matrix in Java, Python, & C++**

### TL;DR  
- **Good**: Linear time, O(#flips) memory, no extra grid, constant‑time random access.  
- **Bad**: Naive approaches are *O(m*n)* space and *O(m*n)* per flip – unscalable.  
- **Ugly**: Mishandling the random seed or swapping logic leads to subtle bugs and bias in randomness.  

---

#### 1. Problem Overview

> *You’re given an `m × n` binary matrix initially filled with zeros.  
> Each call to `flip()` must return a uniformly random index `(i, j)` where the cell is `0`, flip that cell to `1`, and guarantee that every remaining `0` has an equal chance of being selected.  
> The `reset()` operation restores the matrix to all zeros.*

Constraints are generous (`m, n ≤ 10⁴`), but only up to **1000** `flip`/`reset` operations. Thus we need an algorithm that works well even for massive matrices.

---

#### 2. The Naive (Bad) Approach

| Method | Time | Space | Comments |
|--------|------|-------|----------|
| 2‑D array + list of free cells | `O(m*n)` | `O(m*n)` | Stores the whole matrix – impossible for 10⁸ cells. |
| Random scan until a free cell is found | `O(m*n)` per flip | `O(m*n)` | Even with small `k`, each flip can loop thousands of times. |

The naive solution **fails** because it violates the interview constraints:
- You’d need to keep an array or list of *every* cell to guarantee uniformity, blowing up memory.
- Re‑scanning the matrix for each flip becomes a performance bottleneck.

> **Why it’s bad?**  
> The algorithm’s complexity is a function of the *entire* grid size, not the *actual work* you’re doing.  
> For a 10⁴ × 10⁴ matrix that would mean storing 10⁸ ints – simply impossible on most interview machines.

---

#### 3. The Elegant (Good) Map‑Swap Trick

1. **Flatten the Matrix**  
   Treat the 2‑D matrix as a 1‑D array of length `N = m * n`.  
   A linear index `x` maps to `(x / n, x % n)`.

2. **Maintain a “Remaining” Counter**  
   `remaining = N` initially.  
   Every flip reduces `remaining` by one.

3. **Use a Hash Map for Swapping**  
   ```text
   virtual_index  ↔  real_index
   ```
   *When a virtual index is used, we map it to the last free cell, then “remove” that cell from the pool.*

4. **Random Choice**  
   Pick `k` uniformly in `[0, remaining-1]`.  
   Translate `k` to a real index using the map (`k` may have been swapped earlier).  
   Swap `k` with `remaining-1` (the last free cell).  

5. **Return the 2‑D coordinates**  
   `row = real / n`, `col = real % n`.

**Why it’s Good**  
- **O(1)** per `flip`/`reset`.  
- **O(k)** memory where `k` is the number of flips (≤ 1000).  
- No need to store the entire matrix.  
- Randomness is *unbiased* because each draw is from a uniformly random subset of the current free cells.

> **Pseudocode (Python‑style)**  
> ```python
> r = random.randint(0, remaining-1)
> real = mapping.get(r, r)
> last = remaining-1
> lastReal = mapping.get(last, last)
> mapping[r] = lastReal
> remaining -= 1
> return [real // n, real % n]
> ```

---

#### 3.1. Subtle “Ugly” Pitfalls

| Bug | Symptom | Fix |
|-----|---------|-----|
| **Incorrect swap when `r == last`** | The map still contains a key that no longer exists in the pool, breaking future draws. | Remove the key (`mapping.pop(r, None)`) when `r == last`. |
| **Using `randint` with inclusive bounds** | Off‑by‑one error: `randint(0, remaining)` picks an index outside the pool. | Use `randint(0, remaining-1)` or `nextInt(remaining)`. |
| **Seed mis‑management** | Different runs produce the same sequence, making the solution appear deterministic. | Use a *fresh* seed (`mt19937` + `random_device` in C++, `Random()` in Java, default RNG in Python). |
| **Hash collisions in high‑level languages** | Rare, but can degrade performance. | Use `unordered_map` (C++), `HashMap` (Java), or Python’s dict – all are amortized O(1). |

---

#### 3. 3. Practical Implementation

The code snippets in the previous section already embody the **good** solution.  
Below are **complete, commented** snippets for each language – drop them straight into LeetCode.

> **Tip:** In Python, avoid `numpy` or any heavy libraries.  
> In C++ use `mt19937` instead of the deprecated `rand()` for better uniformity.  

---

#### 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| `flip()` | **O(1)** (expected) | `O(1)` map look‑ups/updates |
| `reset()` | **O(1)** | `O(1)` – clears the map |
| Total for `k` flips | **O(k)** | **O(k)** (k ≤ 1000) |

> **Memory Footprint**  
> At most 1000 integers in the hash map → < 10 KB even in Java (32‑bit int).  
> Far lighter than storing the entire matrix (up to 400 MB for 10⁸ cells).

---

#### 5. Why Randomness Matters in Interviews

- **Uniformity**: Interviewers expect you to provide a truly uniform random selection.  
- **Bias**: Even a single misplaced assignment can skew probabilities.  
- **Reproducibility**: Some interview platforms will run your solution on the same input multiple times. Ensure that your random generator does not use a fixed seed unless explicitly required.

---

#### 6. Practice Tips

1. **Run Your Own Tests**  
   ```python
   s = Solution(10000, 10000)
   for _ in range(1000):
       r = s.flip()
   s.reset()
   ```
   Verify that `reset()` restores the pool size.

2. **Check Uniformity**  
   Simulate 1 000 000 flips on a small matrix (`m=2, n=2`) and count the distribution of outputs. The histogram should be flat.

3. **Edge Cases**  
   - `flip()` called when the matrix is full (`remaining == 0`) – should throw an exception or handle gracefully.  
   - `reset()` called repeatedly – should always clear the map.

4. **Interview Pointers**  
   - Explain the “virtual → real” mapping idea.  
   - Mention **reservoir sampling** as a conceptual analogy.  
   - Highlight the trade‑offs between space/time vs. the naive method.

---

#### 7. Conclusion – You’re Ready to Ace the Interview

- **Good**: O(1) per operation, minimal memory, elegant use of a hash map.  
- **Bad**: Storing the entire grid is a *deadly mistake* on a large matrix.  
- **Ugly**: Skipping key edge cases (like swapping the same index) will produce biased or incorrect results, often unnoticed until a test case fails.  

Drop this code into your repository, add a brief description of the algorithm in your README, and you’ll have a **job‑ready** reference for future interviews.  

Happy coding! 🚀  

---  

### 📬 Want more interview‑ready code?  
Subscribe to our newsletter for weekly LeetCode problem walkthroughs, video explanations, and interview‑prep challenges.  

---  

> **Author:** *[Your Name]* – *Algorithm Engineer | Data‑Structure Evangelist*  
> **Contact:** `your.email@example.com` | `LinkedIn / Twitter` | `GitHub: yourgithub`  

---  

**End of Blog**  

---  

### 🔗 Quick Links

| Section | Anchor |
|---------|--------|
| Code | `#code` |
| Blog Article | `#blog` |
| TL;DR | `#tldr` |

--- 

Happy interviewing, and remember: **the right data‑structure trick is half the battle.**