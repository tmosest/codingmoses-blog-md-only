---
title: LeetCode 2573. Find the String with LCP - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 2573 – Find the String with LCP  
*Hard – LeetCode*  

> **Goal** – Reconstruct the lexicographically smallest word that satisfies a given  
> `lcp` matrix, or return an empty string if no such word exists.

Below you’ll find three production‑ready solutions (Java, Python, C++), a concise walkthrough of the algorithm, an analysis of edge‑cases, and a full‑blown blog article that explains the *good*, *bad*, and *ugly* parts of the problem.  
The blog is SEO‑optimised for titles like **“LeetCode 2573 – Find The String With LCP”** and **“Java/Python/C++ Solution”** so you can use it on your portfolio or LinkedIn posts.

---

## 🚀 Quick‑Start Code

### 1. Java (Java 17 +)

```java
import java.util.*;

public class Solution {
    public String findTheString(int[][] lcp) {
        int n = lcp.length;
        int[] group = new int[n];   // 1‑based group id
        int curGroup = 0;

        // 1️⃣ Build a *potential* string by grouping equal characters
        for (int i = 0; i < n; i++) {
            if (group[i] != 0) continue;
            if (++curGroup > 26) return "";          // > 26 distinct chars impossible
            group[i] = curGroup;
            for (int j = i + 1; j < n; j++) {
                if (lcp[i][j] > 0) group[j] = curGroup;
            }
        }

        // 2️⃣ Verify that the constructed groups really satisfy `lcp`
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                int expected = (group[i] == group[j]) ?
                               ((i + 1 < n && j + 1 < n) ? lcp[i + 1][j + 1] + 1 : 1) :
                               0;
                if (expected != lcp[i][j]) return "";
            }
        }

        // 3️⃣ Convert group ids to letters (a‑z)
        StringBuilder sb = new StringBuilder();
        for (int g : group) sb.append((char) ('a' + g - 1));
        return sb.toString();
    }
}
```

> **Complexity** – `O(n²)` time, `O(n)` auxiliary space.  
> `n ≤ 1000`, so the solution easily fits the limits.

---

### 2. Python (Python 3.10 +)

```python
class Solution:
    def findTheString(self, lcp: List[List[int]]) -> str:
        n = len(lcp)
        group = [0] * n      # 1‑based group id
        cur = 0

        # Build potential string
        for i in range(n):
            if group[i]:
                continue
            cur += 1
            if cur > 26:
                return ""
            group[i] = cur
            for j in range(i + 1, n):
                if lcp[i][j]:
                    group[j] = cur

        # Validate the lcp
        for i in range(n):
            for j in range(n):
                exp = 0
                if group[i] == group[j]:
                    exp = (lcp[i + 1][j + 1] + 1) if i + 1 < n and j + 1 < n else 1
                if exp != lcp[i][j]:
                    return ""

        # Convert to string
        return ''.join(chr(ord('a') + g - 1) for g in group)
```

---

### 3. C++ (C++17 +)

```cpp
class Solution {
public:
    string findTheString(vector<vector<int>>& lcp) {
        int n = lcp.size();
        vector<int> group(n, 0);   // 1‑based group id
        int cur = 0;

        // 1️⃣ Build a candidate string
        for (int i = 0; i < n; ++i) {
            if (group[i]) continue;
            if (++cur > 26) return "";
            group[i] = cur;
            for (int j = i + 1; j < n; ++j) {
                if (lcp[i][j] > 0) group[j] = cur;
            }
        }

        // 2️⃣ Verify the lcp matrix
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                int expected = 0;
                if (group[i] == group[j]) {
                    expected = (i + 1 < n && j + 1 < n) ? lcp[i + 1][j + 1] + 1 : 1;
                }
                if (expected != lcp[i][j]) return "";
            }
        }

        // 3️⃣ Convert to characters
        string res;
        res.reserve(n);
        for (int g : group) res.push_back('a' + g - 1);
        return res;
    }
};
```

---

## 📚 Algorithm Insight

| Step | What we do | Why it works |
|------|------------|--------------|
| **1. Group equal indices** | Any `lcp[i][j] > 0` means `word[i] == word[j]`. We assign them the same “group id”. | In an LCP matrix, a non‑zero entry forces equality at the current position. |
| **2. Use minimal letters** | The first index is always ‘a’. For each new index, we reuse the smallest letter that is allowed by any previous equality. | Lexicographic minimisation: we only introduce a new letter when absolutely necessary. |
| **3. Validate** | Compute the LCP of the constructed word (backwards DP: `dp[i][j] = 1 + dp[i+1][j+1]` if equal, else `0`) and compare it to the input. | A constructed word *must* reproduce the matrix exactly. The DP guarantees that we didn't make a mistake in step 1. |
| **4. Final string** | Map group ids to letters `a … z`. | The groups are a bijection to letters; if more than 26 groups were needed, the problem is impossible. |

The algorithm is essentially a **generative + verifier** strategy.  
It runs in `O(n²)` time – quadratic is unavoidable because the input itself is an `n × n` matrix – but the constant factors are tiny.

---

## ⚠️ Edge Cases & Common Pitfalls

| Edge case | What to watch out for | Fix |
|-----------|-----------------------|-----|
| **More than 26 distinct characters** | `curGroup > 26` → impossible. | Immediately return `""`. |
| **Zero‑based vs one‑based indexing** | The expected LCP for the last index in a group should be `1`. | Handle `i+1 < n` and `j+1 < n` carefully. |
| **Unequal dimensions** | Input guarantees `lcp` is `n×n`, but defensive coding helps when hacking the judge. | Add bounds checks before accessing `lcp[i+1][j+1]`. |
| **Large `n`** | `n = 1000` → 1 M entries. | Use `O(n)` auxiliary storage for the groups, no 2‑D DP array. |
| **Non‑symmetric matrix** | A malformed matrix will be caught during verification. | The verification step guarantees correctness. |

---

## 🧠 “Good”, “Bad”, and “Ugly” Parts

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Problem statement** | Clear input/output specification, constraints, and example matrix. | The matrix is dense: every entry must be checked. | The definition of `lcp` is *not* standard LCP; it's the *full* suffix‑matching table, which is confusing to newcomers. |
| **Algorithmic elegance** | One simple pass to build a candidate string, then a single pass to validate. | None – you only need to understand “`lcp[i][j] > 0` ⇒ same character” and “DP for LCP”. | People often try DP from scratch (O(n³)) or over‑engineer the construction; the greedy grouping is the sweet spot. |
| **Implementation difficulty** | Very small code base, no heavy data structures. | Requires careful handling of boundary conditions (last index). | When the `lcp` matrix is malformed (e.g. inconsistent zeros), you have to debug the mismatch between the constructed DP and the input. |
| **Testing** | Use the two provided examples, plus random matrices that satisfy the constraints. | Ensure you test `n = 1` and `n = 1000` corner cases. | Randomly corrupt one entry to verify that the code returns `""`. |

---

## 🧠 Why This Problem is a Great Interview Showcase

* **Graph‑like grouping** – the LCP matrix implicitly defines an undirected graph where edges exist if `lcp > 0`.  
* **Greedy + DP** – demonstrates a two‑phase approach that many candidates skip.  
* **Limits** – fits perfectly into the *O(n²)* paradigm, making it a clean benchmark for interviewers.  
* **Extensibility** – you can tweak the code to work with more than 26 letters, or to output all possible words, which is great for side‑projects.

---

## 📈 SEO‑Optimised Blog Post

Below is the full blog article you can paste to a personal blog, Medium, or LinkedIn.  
It includes the keyword‑rich title, a meta description, headings, and bullet points for readability.

---

# LeetCode 2573 – Find The String With LCP  
**Java / Python / C++ Solutions | O(n²) Algorithm | Interview Prep**

### 📌 Meta Description  
Solve LeetCode 2573 “Find The String With LCP” in Java, Python, and C++ with an optimal `O(n²)` algorithm. Learn the trick behind grouping indices, verifying LCP, and handling edge cases. Perfect for coding interview prep.

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Understanding the LCP Matrix](#understanding-the-lcp-matrix)  
3. [The Greedy‑Plus‑DP Strategy](#the-greedy-plus-dp-strategy)  
4. [Implementation in Java, Python, C++](#implementation-in-java-python-c)  
5. [Testing & Edge‑Case Checklist](#testing-edge-case-checklist)  
6. [Time & Space Complexity](#time-space-complexity)  
7. [Common Pitfalls (The Bad)](#common-pitfalls-the-bad)  
8. [Advanced Variations (The Ugly)](#advanced-variations-the-ugly)  
9. [Takeaway & Interview Tips](#takeaway-interview-tips)  
10. [Further Reading](#further-reading)

---

### 1. Problem Overview <a name="problem-overview"></a>

> **LeetCode 2573** – *Find The String With LCP*  
> You’re given an `n × n` matrix `lcp` where `lcp[i][j]` is the length of the longest common prefix of the suffixes starting at positions `i` and `j`.  
> Your task: **reconstruct the lexicographically smallest word** that satisfies this matrix, or return an empty string if no such word exists.

Why is this interesting?  
- It blends *string matching* with *matrix reasoning*.  
- The LCP matrix is a full 2‑D table, not a simple vector of values.  
- The lexicographic requirement forces a careful construction of the candidate word.

---

### 2. Understanding the LCP Matrix <a name="understanding-the-lcp-matrix"></a>

- **Zero entries** mean the two suffixes differ right at the first character: `word[i] != word[j]`.  
- **Positive entries** guarantee `word[i] == word[j]`.  
  In fact, the entire string is partitioned into **equivalence classes** of indices that share the same letter.

Example (for the string `"abbc"`):

|   | a | b | b | c |
|---|---|---|---|---|
| **a** | 4 | 0 | 0 | 0 |
| **b** | 0 | 3 | 2 | 0 |
| **b** | 0 | 2 | 2 | 0 |
| **c** | 0 | 0 | 0 | 1 |

You can see how every cell with a non‑zero value indicates the same letter at both positions.

---

### 3. The Greedy‑Plus‑DP Strategy <a name="the-greedy-plus-dp-strategy"></a>

1. **Build a *potential* string**  
   *Start with group id `1` for index 0 (letter `a`).  
   For each new index `i`:  
   - If there exists a `j < i` with `lcp[i][j] > 0`, reuse `word[j]`.  
   - Otherwise, pick the *next unused* letter (max(`word[0…i‑1]`) + 1).  
   - If we run out of letters (`> z`), immediately return `""`.*

2. **Validate**  
   Compute the LCP of the constructed word (reverse DP: `dp[i][j] = 1 + dp[i+1][j+1]` if chars equal, else `0`).  
   If the computed table differs from the input, the candidate is invalid → return `""`.

3. **Return** the built word.

Why does this work?  
- **Correctness**:  
  *Grouping by `lcp > 0` guarantees all equalities are satisfied.  
  The DP step ensures no hidden contradictions exist.*  
- **Lexicographic minimality**:  
  We always reuse the smallest letter possible. Only when necessary do we advance to the next letter. Thus the final string is the smallest possible.

---

### 4. Implementation in Java, Python, C++ <a name="implementation-in-java-python-c"></a>

*(See the code blocks above – one per language.)*  

All three implementations share the same logic:  
- A single pass to assign *group ids*.  
- A double loop to validate the entire matrix.  
- Final conversion to letters.  
The time complexity is `O(n²)` and the auxiliary space `O(n)`.

---

### 5. Testing & Edge‑Case Checklist <a name="testing-edge-case-checklist"></a>

| Test | Purpose |
|------|---------|
| **Minimum size** `n = 1` | Should return `"a"` regardless of the single entry. |
| **All zeros except diagonal** | Strings like `"abc"` → groups should be `a`, `b`, `c`. |
| **All positive** (full ones) | Should return `"aaaaaaaa…"` (all same letter). |
| **More than 26 letters needed** | Construct a matrix with 27 equivalence classes → verify immediate failure. |
| **Malformed matrix** (e.g., `lcp[0][1] = 1` but `lcp[1][0] = 0`) | Validation should catch mismatch → return `""`. |
| **Random valid matrices** | Use a generator that constructs a random string and computes its LCP; feed that matrix to the solver. |
| **Large `n`** `= 1000` | Stress test for runtime and memory usage. |
| **Boundary at last index** | Ensure that the DP uses `dp[n-1][n-1] = 1` correctly. |

---

### 6. Time & Space Complexity <a name="time-space-complexity"></a>

- **Time**: `O(n²)` – required because we have an `n × n` input.  
- **Space**: `O(n)` – only the group array; no extra 2‑D DP array.

These figures are ideal for interviewers: the solution fits within common interview time budgets and is easy to reason about.

---

### 6. Common Pitfalls (The Bad) <a name="common-pitfalls-the-bad"></a>

| Pitfall | How it shows up |
|---------|-----------------|
| **Confusing `lcp > 0` with DP** | Beginners might think “if `lcp[i][j]` is 1, the first two chars are same, but maybe later characters differ”. The key is that *any* non‑zero value forces equality at the *current* position. |
| **Boundary errors** | Accessing `lcp[i+1][j+1]` without checking bounds leads to `ArrayIndexOutOfBounds`. Always guard against `i+1 < n`. |
| **Skipping the verifier** | Some solutions stop after building the candidate string, but then fail on hidden contradictions. The DP step is essential. |

---

### 7. Advanced Variations (The Ugly) <a name="advanced-variations-the-ugly"></a>

> In a more “research‑style” problem, you might be asked to:
> - *Support alphabets larger than 26* (Unicode).  
> - *Output all possible words* that satisfy the matrix (exponential).  
> - *Recover the string when multiple valid solutions exist* and rank them lexicographically.

These variations require additional logic:  
- A disjoint‑set (Union‑Find) structure for grouping.  
- BFS/DFS to explore all letter assignments.  
- Careful pruning to avoid combinatorial explosion.

---

### 8. Takeaway & Interview Tips <a name="takeaway-interview-tips"></a>

- **Explain the equivalence‑class idea**: A quick mental model for reasoning about `lcp`.  
- **Show the two phases**: generation + verification.  
- **Talk about complexity**: Quadratic is the best you can hope for given the input size.  
- **Mention boundary handling**: last index special case → `1`.  

When explaining, emphasize that the greedy part ensures lexicographic minimality, while DP guarantees correctness.

---

### 9. Further Reading <a name="further-reading"></a>

- *Suffix Trees and LCP arrays* – Standard text on suffix‑array based LCP.  
- *Disjoint‑Set Union (Union‑Find)* – Useful for the grouping phase.  
- *InterviewBit LCP Problems* – Similar matrix‑matching challenges.  
- *Competitive Programming 4 – String Matching* – Deep dive into LCP computations.

---

## 🎓 Final Thoughts

LeetCode 2573 may appear daunting because it involves a dense `n × n` matrix, but the underlying principle is simple: **equality propagation + DP validation**.  

Implementing this in Java, Python, or C++ showcases:
- Ability to think graphically about strings.  
- Skill in greedy construction with correctness guarantees.  
- Careful handling of edge cases and constraints.

**Good luck on your next interview!** 🚀

---


---

Feel free to adapt the article or code snippets to your style, or add screenshots of your test harness.  

Happy coding!