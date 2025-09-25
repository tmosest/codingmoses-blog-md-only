---
title: LeetCode 45. Jump Game II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Jump Game II – The “Go‑Fast” Greedy Solution (Java | Python | C++)
*LeetCode #45 – Medium*  

> “I can’t finish the interview? Start with the greedy idea that guarantees the fewest jumps!”  

Below you’ll find three polished, production‑ready solutions – Java, Python, and C++ – all sharing the same clean‑cut greedy logic.  
I’ll also drop a short **blog post** (SEO‑friendly, job‑hunting‑oriented) that you can copy‑paste into Medium, Dev.to, or your own blog.  

---

## 1. Problem Recap  

You’re given an array `nums` where each element `nums[i]` is the maximum number of steps you can jump *forward* from index `i`.  
Start at index `0`. Find the *minimum* number of jumps required to reach the last index (`n‑1`).  
The input guarantees that it’s always possible.

```
nums = [2,3,1,1,4]   →  answer = 2
          ^ ^ ^ ^ ^
          0 1 2 3 4
```

---

## 2. Greedy Insight – “Near & Far”  

The key observation:  
**While you’re scanning indices that you can reach in *k* jumps, you only need to know the farthest index you can reach in *k+1* jumps.**  

You don’t have to explore every possible jump separately – just keep the *current reach* (`far`) and the *next reach* (`farthest`).  

> **Near** – the start of the current window of indices you’re considering.  
> **Far** – the end of that window.  
> **Farthest** – the farthest index you can get to from anywhere in `[near, far]`.  

Every time you “commit” a jump, you shift the window to `[far+1, farthest]`.

---

## 3. Code – 3 Languages

### 3.1 Java (Java 17+)

```java
// --------------- JumpGameII.java -----------------
import java.util.*;

public class JumpGameII {
    public static int jump(int[] nums) {
        int n = nums.length;
        if (n <= 1) return 0;            // already at the end

        int jumps  = 0;
        int near   = 0;                  // start of current window
        int far    = 0;                  // end of current window
        int farthest = 0;                 // farthest reachable index

        while (far < n - 1) {
            // Scan all indices we can reach with current number of jumps
            for (int i = near; i <= far; i++) {
                farthest = Math.max(farthest, i + nums[i]);
            }

            // Prepare for the next jump
            near = far + 1;
            far  = farthest;
            jumps++;
        }

        return jumps;
    }

    // -------------------------------------------------
    public static void main(String[] args) {
        int[] nums = {2, 3, 1, 1, 4};
        System.out.println("Minimum jumps: " + jump(nums)); // → 2
    }
}
```

> **Complexity** – O(n) time, O(1) extra space.

---

### 3.2 Python 3

```python
# --------------- jump_game_ii.py -----------------
def jump(nums):
    n = len(nums)
    if n <= 1:
        return 0

    jumps, near, far, farthest = 0, 0, 0, 0

    while far < n - 1:
        for i in range(near, far + 1):
            farthest = max(farthest, i + nums[i])

        near = far + 1
        far = farthest
        jumps += 1

    return jumps

# -------------------------------------------------
if __name__ == "__main__":
    nums = [2, 3, 1, 1, 4]
    print(f"Minimum jumps: {jump(nums)}")  # → 2
```

> **Complexity** – O(n) time, O(1) extra space.

---

### 3.3 C++ (C++17)

```cpp
// --------------- JumpGameII.cpp -----------------
#include <bits/stdc++.h>
using namespace std;

int jump(const vector<int>& nums) {
    int n = nums.size();
    if (n <= 1) return 0;

    int jumps = 0;
    int near = 0, far = 0, farthest = 0;

    while (far < n - 1) {
        for (int i = near; i <= far; ++i) {
            farthest = max(farthest, i + nums[i]);
        }
        near = far + 1;
        far = farthest;
        ++jumps;
    }
    return jumps;
}

// -------------------------------------------------
int main() {
    vector<int> nums = {2, 3, 1, 1, 4};
    cout << "Minimum jumps: " << jump(nums) << '\n';   // → 2
    return 0;
}
```

> **Complexity** – O(n) time, O(1) space.

---

## 4. Blog Post – “The Good, The Bad, and The Ugly of Jump Game II”

> **Target audience**: Front‑end / full‑stack developers who want to ace technical interviews.  
> **SEO keywords**: Jump Game II, LeetCode 45, interview coding problem, greedy algorithm, coding interview solutions, Java, Python, C++.

---

### Title  
**Jump Game II – The Good, The Bad, and The Ugly: A 5‑Minute Guide to Mastering LeetCode 45**

---

### Meta Description  
> Crack LeetCode 45 in minutes! Discover the greedy “Near & Far” strategy, compare Java/Python/C++ solutions, and learn why this problem is a must‑do for your next coding interview.

---

### 1. TL;DR  
Jump Game II is a classic interview puzzle that boils down to a *single pass* greedy algorithm.  
- **Good**: O(n) time, O(1) space – perfect for interviews.  
- **Bad**: Poor DP or brute‑force attempts waste time and stack depth.  
- **Ugly**: Over‑engineering or wrong boundary checks can lead to off‑by‑one errors.  

Grab the code snippets below, pick your language, and you’re interview‑ready.

---

### 2. Why Is This Problem So Popular?

- **Easy to state, hard to over‑think** – a clean problem statement with hidden pitfalls.  
- **Common interview theme** – many companies ask a variant in onsite interviews.  
- **Illustrates greedy vs. DP** – a great teaching moment for interviewers.

---

### 3. The “Good” – The Greedy Solution

The greedy “Near & Far” approach works because:
1. Any index you can reach in *k* jumps can only improve the farthest reach for *k+1* jumps.
2. You never need to revisit an earlier window – once you commit a jump, you’ve already considered all possibilities within that window.

**Step‑by‑step**  
| Variable | Meaning | Update Rule |
|----------|---------|-------------|
| `near` | Start of current window | `near = far + 1` |
| `far` | End of current window | `far = farthest` |
| `farthest` | Max reachable index from current window | `farthest = max(farthest, i + nums[i])` |
| `jumps` | Number of jumps made | `jumps++` |

> **Time**: O(n) – we inspect each index at most once.  
> **Space**: O(1) – only a handful of integer counters.

---

### 4. The “Bad” – What You Should Avoid

| Wrong Approach | Why It’s Bad |
|----------------|--------------|
| **Brute‑Force DFS** | Recurse on every possible jump → O(2ⁿ) time, stack overflow risk. |
| **Dynamic Programming** (bottom‑up) | Works, but O(n²) time and O(n) space – not a sweet spot for interviewers. |
| **Unbounded Integer Bounds** | Using `Integer.MAX_VALUE`/`INT_MAX` and then adding to indices can overflow. Use a sentinel like `farthest = 0` and compute only when needed. |

---

### 5. The “Ugly” – Edge Cases & Common Mistakes

| Pitfall | Fix |
|---------|-----|
| **`while (far < n-1)` vs. `while (farthest < n-1)`** | The loop must stop *after* you commit a jump that guarantees you’re at or beyond the last index. |
| **Off‑by‑one in the inner loop** (`for (int i = near; i <= far; ++i)`) | Make sure `far` is inclusive; the window is `[near, far]`. |
| **Large values of `nums[i]`** | No overflow in Java/C++ because `i + nums[i]` fits in `int` for LeetCode constraints. |
| **Array of length 1** | Return `0` immediately – you’re already at the target. |

---

### 6. The 3 Languages – Java, Python, C++  

*(Insert code snippets from Section 3.1‑3.3 here. Use code blocks with proper syntax highlighting.)*  

> *Tip*: Keep the same variable names (`near`, `far`, `farthest`, `jumps`).  
> *Tip*: Write a small `main()`/`__main__` to test on the sample input – that shows you can run the solution locally, a confidence booster for the interview.

---

### 7. How to Talk About This Problem in an Interview

1. **Clarify the question**: “Can we jump *exactly* `k` steps or at most `k`?”  
2. **Explain the greedy rationale** before writing code.  
3. **Mention boundary checks**: “What if the array has length 1?”  
4. **Walk through a simple example** on the whiteboard (hand‑draw the window shifts).  
5. **Finish with the code** – highlight that you’re using O(1) space and one pass.

---

### 8. Job‑Hunting Angle – Why This Matters

> *Coding interviews aren’t just about the answer; they’re about the narrative you present.*  
> - Show that you can think **greedy** and **efficiently**.  
> - Highlight your *code quality* – concise, well‑named variables, and comments.  
> - Share your solution on GitHub or a portfolio; recruiters love seeing clean, commented code.

**Resume Bullet**  
> • Implemented a linear‑time greedy algorithm to solve LeetCode 45 “Jump Game II,” achieving O(n) runtime and O(1) memory usage in Java, Python, and C++.

---

### 9. Final Takeaway

*The greedy Near‑Far solution is a perfect “show‑case” problem for your next technical interview. Master the logic, own the code in at least one language, and you’ll turn a 45‑second challenge into a confidence‑boosting interview win.*

Good luck, and may your next recruiter see you as the *fast‑moving* candidate who already *jumps* past the competition! 🚀

---  

> *Feel free to adapt the SEO keywords above into your blog’s tags. Add a short image of the sliding window diagram, and you’ll have a share‑worthy article that recruiters will love.*