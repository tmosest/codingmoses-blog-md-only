---
title: LeetCode 2029. Stone Game IX - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Stone Game IX – The “Math‑Only” Solution  
*(Java | Python | C++ – O(n) time, O(1) space)*  

---

### Problem Recap (LeetCode 2029)

> **Stone Game IX**  
> There is a row of `n` stones, each with a positive value `stones[i]`.  
> Alice and Bob take turns removing **one** stone.  
> If the **sum of all removed stones** is divisible by 3, the player who just removed a stone **loses**.  
> If the pile is empty after a move, Bob wins automatically.  
> Both play optimally. Does Alice win?

| Sample | Stones | Alice? |
|--------|--------|--------|
| 1 | `[2,1]` | ✅ |
| 2 | `[2]`   | ❌ |
| 3 | `[5,1,2,4,3]` | ❌ |

> **Constraints**  
> `1 ≤ stones.length ≤ 10⁵`  
> `1 ≤ stones[i] ≤ 10⁴`

---

## The Intuition – “Good, Bad, Ugly”

| Phase | What you **should** do | What you **shouldn’t** do | Why |
|-------|-----------------------|---------------------------|-----|
| **Good** | Count stones by remainder `value % 3`. | Try brute‑force search / DP over all subsets. | Exponential blow‑up – impossible for `n=10⁵`. |
| **Bad** | Use the parity of “divisible‑by‑3” stones (`cnt[0] % 2`). | Rely on the absolute count of `cnt[0]` (odd/even only matters). | An odd count can flip the outcome; counting too many is unnecessary. |
| **Ugly** | Remember that **no 1‑ or 2‑remainder stones → Bob wins**. | Ignore the corner case of `cnt[1]==cnt[2]==0`. | That small case is the only way Alice can lose without a move. |

The real “secret sauce” is that **only the parity of `cnt[0]` and the *relative* counts of `1`‑ and `2`‑remainder stones matter**.  
All other information can be discarded in O(1) space.

---

## The O(n) Solution Explained

```text
cnt[0] = number of stones with value % 3 == 0
cnt[1] = number of stones with value % 3 == 1
cnt[2] = number of stones with value % 3 == 2

If cnt[1]==0 && cnt[2]==0          -> Bob wins (false)

Let smaller = min(cnt[1], cnt[2])
    larger = max(cnt[1], cnt[2])

If cnt[0] is even:                // parity even
    Alice wins iff smaller > 0

Else (cnt[0] is odd):             // parity odd
    Alice wins iff larger > smaller + 2
```

Why does it work?

1. **Pairs of 1 & 2**  
   A `1` + `2` = `3` → removing both at the same time would instantly lose.  
   Therefore, Alice will try to **force Bob** to take a pair that leaves her a winning residue.

2. **Three of the same remainder (1 or 2)**  
   `1+1+1 = 3` (or `2+2+2 = 6`).  
   Removing any two leaves a single stone of that remainder that can be taken without causing loss.

3. **Parity of 0‑remainder stones**  
   Every time a `0`‑remainder stone is removed, the sum changes by `0` modulo 3.  
   Thus, the *parity* (odd/even) of those stones determines whether the overall “divisibility‑by‑3” status flips after Alice’s first move.

4. **When parity is even**  
   Alice can mirror Bob’s moves on the `0`‑remainder stones.  
   She wins if at least one non‑`0` stone exists (`smaller > 0`).  
   If all stones are `0`‑remainder, Bob will win because the final sum can never be divisible by 3.

5. **When parity is odd**  
   Alice can force a win if the **gap** between the counts of `1` and `2` stones is **≥ 3** (`larger > smaller + 2`).  
   Otherwise Bob can mirror Alice’s strategy and win.

---

## Implementation

### Java

```java
import java.util.*;

class Solution {
    public boolean stoneGameIX(int[] stones) {
        int[] cnt = new int[3];
        for (int s : stones) cnt[s % 3]++;

        if (cnt[1] == 0 && cnt[2] == 0) return false;

        int smaller = Math.min(cnt[1], cnt[2]);
        int larger  = Math.max(cnt[1], cnt[2]);

        if (cnt[0] % 2 == 0) {          // even number of 0‑remainder stones
            return smaller != 0;
        } else {                        // odd number of 0‑remainder stones
            return larger > smaller + 2;
        }
    }
}
```

---

### Python

```python
class Solution:
    def stoneGameIX(self, stones: List[int]) -> bool:
        cnt = [0, 0, 0]
        for s in stones:
            cnt[s % 3] += 1

        if cnt[1] == 0 and cnt[2] == 0:
            return False

        smaller = min(cnt[1], cnt[2])
        larger  = max(cnt[1], cnt[2])

        if cnt[0] % 2 == 0:          # even 0‑remainder stones
            return smaller != 0
        else:                        # odd 0‑remainder stones
            return larger > smaller + 2
```

---

### C++

```cpp
class Solution {
public:
    bool stoneGameIX(vector<int>& stones) {
        int cnt[3] = {0, 0, 0};
        for (int s : stones) cnt[s % 3]++;

        if (cnt[1] == 0 && cnt[2] == 0) return false;

        int smaller = min(cnt[1], cnt[2]);
        int larger  = max(cnt[1], cnt[2]);

        if (cnt[0] % 2 == 0) {          // even number of 0‑remainder stones
            return smaller != 0;
        } else {                        // odd number of 0‑remainder stones
            return larger > smaller + 2;
        }
    }
};
```

---

## Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Java / Python / C++ | **O(n)** (single pass to count) | **O(1)** (constant auxiliary array) |

`n` is up to 100 000, so this linear solution easily satisfies the constraints.

---

## Blog Article – “How to Nail Stone Game IX in Your Resume”

### Title  
**“Stone Game IX – The Math‑Only Approach: Code in Java, Python, C++, and a Job‑Winning Blog”**

#### Meta Description  
Learn the elegant O(n) solution to LeetCode 2029 – Stone Game IX. Get Java, Python, and C++ implementations, a clear explanation, and SEO‑optimized content that boosts your personal brand for software engineering roles.

---

### 1. Hook – Why This Matters

- **LeetCode’s “hard” problems are interview‑gold**.  
- Stone Game IX showcases *mathematical reasoning* + *algorithmic sharpness*.  
- Writing a concise article with code demonstrates **domain knowledge** and **communication skills**—exactly what hiring managers look for.

---

### 2. Problem Statement in Plain English

[Repeat problem recap]

---

### 3. The “Good, Bad, Ugly” Roadmap

[Insert table from earlier]

---

### 4. Walkthrough of the Optimal Strategy

Explain with a 5‑step example:  
1. Count residues.  
2. Handle the “no 1/2” corner case.  
3. Split counts.  
4. Check parity of 0‑remainder stones.  
5. Decide win/loss.

Add a diagram or 2‑column table for clarity.

---

### 5. Code Snippets

- Java block  
- Python block  
- C++ block

Add **syntax highlighting** and **short comment lines** for readability.

---

### 6. Why This Is “Job‑Ready”

- **Linear time, constant space** → meets production‑grade constraints.  
- **No extra libraries** → portable across all interview environments.  
- **Clear, maintainable comments** → shows you can write clean, production‑level code.  
- **Problem analysis** → demonstrates *algorithmic thinking* – the hallmark of a senior software engineer.

---

### 7. Quick‑Start Checklist

| ✔️ | Item |
|----|------|
| ✅ | Add the **LeetCode link** and **tags** (`#dynamic-programming`, `#math`) |
| ✅ | Include a **time‑complexity** table |
| ✅ | Provide **sample test cases** (above table) |
| ✅ | Add a **call‑to‑action**: “Feel free to drop me a message on LinkedIn – let’s discuss how I can bring this level of optimization to your team.” |

---

### 7. Final Thought – “Math is Powerful”

[Short paragraph encouraging readers to practice “count‑by‑remainder” tricks on other combinatorial games.]

---

### 8. Closing & Call to Action

- Invite comments and discussion.  
- Mention your personal LinkedIn/GitHub URL.  
- Offer to review other candidates’ solutions.

---

## SEO Checklist

| Item | Implementation |
|------|----------------|
| **Primary keyword** | “Stone Game IX solution” (used 3–5× in body) |
| **Secondary keywords** | “LeetCode 2029”, “Java LeetCode solutions”, “Python O(n) algorithm” |
| **Heading structure** | `<h1>`, `<h2>`, `<h3>` hierarchy |
| **Alt text** for diagrams | “Residue distribution for Stone Game IX” |
| **Internal links** | Link to your other LeetCode posts |
| **Outbound links** | [LeetCode 2029](https://leetcode.com/problems/stone-game-ix/) |
| **Image sitemap** | Add images to sitemap.xml for Google indexing |
| **Rich snippets** | Use JSON‑LD for “Code Example” structured data |

---

### 9. Takeaway

> *You just solved a 100‑000‑stone game in 50 lines of code and wrote a blog that shows you can turn that solution into a professional asset. Good luck on your next interview!*

---

## Conclusion

Stone Game IX may look like a game of chance, but the real trick is **parity + relative counts**.  
The provided Java/Python/C++ code is the shortest possible O(n) solution you can type into a coding interview.

---

> **Want more LeetCode mastery?**  
> Subscribe to my newsletter and get weekly “Interview‑Ready” articles, annotated solutions, and interview‑prep videos straight to your inbox.  
> 👉 [Subscribe now](https://yournewsletter.com)  

---