---
title: LeetCode 2573. Find the String with LCP - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2573 – Find the String with LCP  
> **Leetcode 2573 – Hard**  
> **Languages** : Java, Python, C++  
> **Keywords** : `lcp matrix`, `string reconstruction`, `lexicographically smallest`, `union‑find`, `interval graph`, `algorithm interview`

---

## 1. Problem restatement

You are given an `n × n` matrix `lcp` where

```
lcp[i][j] = longest common prefix length of word[i … n-1] and word[j … n-1]
```

`word` is a lowercase English string of length `n`.  
Your task is to recover **the lexicographically smallest** `word` that matches the given matrix, or return an empty string if no such word exists.

```
Constraints
1 ≤ n = lcp.length = lcp[i].length ≤ 1000
0 ≤ lcp[i][j] ≤ n
```

---

## 2. Intuition – “Same character” → “Different character”

* `lcp[i][j] > 0`  
  → The first characters of the suffixes at `i` and `j` are equal  
  → `word[i] == word[j]`.

* `lcp[i][j] == 0`  
  → The first characters differ  
  → `word[i] != word[j]`.

So the matrix tells us which indices must share the same character and which must differ.  
If we treat the indices as nodes of a graph and draw an edge between two nodes whenever `lcp[i][j] > 0`, each connected component must receive **one single letter**.

Once we know the components we only have to check that the given `lcp` values are *consistent* with the length of the common prefix between two equal‑letter positions:

```
if word[i] == word[j]   →   lcp[i][j] = lcp[i+1][j+1] + 1
if word[i] != word[j]   →   lcp[i][j] = 0
```

The above formula comes directly from the definition of the longest common prefix.



---

## 3. Algorithm

```
1. n ← lcp.length
2. A[0 … n-1] ← 0           // group id of each position
3. group ← 0

4. // ---------- 1. Build groups  ----------
   for i from 0 to n-1
       if A[i] ≠ 0: continue          // already assigned
       group ← group + 1
       if group > 26: return ""       // more than 26 letters → impossible
       for j from i to n-1
           if lcp[i][j] > 0
               A[j] ← group           // same character

5. // ---------- 2. Validate lcp ----------
   for i from 0 to n-1
       for j from 0 to n-1
           v ← (i+1 < n && j+1 < n) ? lcp[i+1][j+1] : 0
           v ← (A[i] == A[j]) ? v+1 : 0
           if lcp[i][j] ≠ v: return ""

6. // ---------- 3. Build answer ----------
   result ← empty string
   for each x in A
       result ← result + chr('a' + x - 1)
   return result
```

*The order of processing guarantees the **lexicographically smallest** answer.*  
When we encounter an unassigned index `i` we create a *new* group and assign the **next** unused letter (`'a'`, then `'b'`, …).  
Because the outer loop scans indices from left to right, earlier positions receive the smallest possible letters.



---

## 4. Correctness proof

We prove that the algorithm returns a string `word` that satisfies the matrix iff such a string exists.

### Lemma 1  
If `lcp[i][j] > 0` then `word[i] == word[j]`.  
If `lcp[i][j] == 0` then `word[i] != word[j]`.

*Proof.*  
By definition, `lcp[i][j]` is the length of the longest common prefix of the suffixes starting at `i` and `j`.  
A positive length means the first characters coincide; a zero length means they differ. ∎



### Lemma 2  
After step 4 the array `A` partitions the indices into groups such that
* inside a group all indices are pairwise equal (`word[i] == word[j]`);
* between different groups all indices are pairwise different.

*Proof.*  
Step 4 assigns the same group id to every pair with `lcp[i][j] > 0`.  
Because `lcp[i][j] > 0` implies equality (Lemma 1), all indices in the same group receive the same letter, so they are equal.  
If two indices are in different groups, there is no path of `>0` entries linking them, therefore at least one pair on that path has `lcp = 0` and thus different letters – again by Lemma 1. ∎



### Lemma 3  
For any indices `i, j` the value computed in step 5 (`v`) equals the expected LCP of the suffixes of the constructed word.

*Proof.*  
If `A[i] == A[j]`, the suffixes share the first character, so the LCP is 1 plus the LCP of the suffixes starting at `i+1` and `j+1`.  
If `A[i] != A[j]`, the LCP is 0.  
Step 5 implements exactly this rule, using the supplied `lcp[i+1][j+1]` as the sub‑problem value. ∎



### Theorem  
The algorithm outputs a string `word` iff `lcp` is realizable.  
If `lcp` is realizable, the produced word is the lexicographically smallest.

*Proof.*  

*Soundness* (output ⇒ realizable):  
If the algorithm returns a string, step 5 never failed.  
By Lemma 3 every pair `(i,j)` satisfies the definition of `lcp`.  
Thus the constructed word realizes the matrix.

*Completeness* (realizable ⇒ output):  
Assume there exists a word `w` that realizes `lcp`.  
Because `w` satisfies Lemma 1, its equal‑letter positions form a partition exactly as step 4 produces (maybe with a different numbering of groups).  
The algorithm assigns the **first** letter to the earliest group, then the second letter, …, which is always possible because at most 26 distinct letters are required.  
Step 5 will then confirm all `lcp` values (Lemma 3), so the algorithm will not return `""`.  
Hence it outputs a string.

*Lexicographic minimality*:  
The outer loop processes indices left‑to‑right.  
When an index is first seen it starts a new group and receives the smallest unused letter.  
Therefore any earlier position never receives a letter that could be replaced by a smaller one without violating the equal‑/different‑constraints.  
Thus the resulting word is the lexicographically smallest possible. ∎



---

## 5. Complexity analysis

```
Building groups:   O(n²)   (two nested loops, n ≤ 1000 → at most 1e6 iterations)
Validating lcp:    O(n²)
Building string:   O(n)
Total time:        O(n²)   ≈ 1 million operations for n=1000
Memory usage:      O(n)    (array A)
```

The solution easily fits into the limits.



---

## 6. Edge cases & pitfalls

| Case | Why it matters | How the algorithm handles it |
|------|----------------|------------------------------|
| `lcp[i][i]` is wrong (≠ n-i) | Self‑diagonal must equal the remaining suffix length | Validation step 5 will catch it |
| Too many distinct letters | We can only use 26 letters | Step 4 aborts if `group > 26` |
| `lcp[i][j] == 0` but `lcp[j][i] > 0` | The matrix is asymmetric → impossible | The algorithm treats `>0` symmetrically; any asymmetry will trigger a validation failure |
| `n` very small (`1`) | trivial matrix | Algorithm still runs; groups=1, validation passes |
| All zeros (except diagonal) | All positions must be different | Step 4 creates a new group for each index → 26 letters are enough for n≤26, otherwise `""` |

**Tip:**  
Never try to *guess* the characters first and then adjust – the matrix gives you a *hard constraint* that must be satisfied by the grouping.  
If you skip a pair while building groups you might miss a necessary equality and the validation will fail.



---

## 7. Full code

### 7.1 Java (Java 17+)

```java
import java.util.*;

public class Solution {
    public String findTheString(int[][] lcp) {
        int n = lcp.length;
        int[] group = new int[n];          // 0 means “unassigned”
        int g = 0;                         // current group id

        // --------- 1. Build groups ----------
        for (int i = 0; i < n; ++i) {
            if (group[i] != 0) continue;   // already part of a component
            ++g;
            if (g > 26) return "";        // too many distinct letters
            for (int j = i; j < n; ++j) {
                if (lcp[i][j] > 0) {
                    group[j] = g;
                }
            }
        }

        // --------- 2. Validate lcp ----------
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                int v = (i + 1 < n && j + 1 < n) ? lcp[i + 1][j + 1] : 0;
                v = (group[i] == group[j]) ? v + 1 : 0;
                if (lcp[i][j] != v) return "";
            }
        }

        // --------- 3. Build answer ----------
        StringBuilder sb = new StringBuilder();
        for (int x : group) {
            sb.append((char) ('a' + x - 1));
        }
        return sb.toString();
    }
}
```

---

### 7.2 Python (Python 3.8+)

```python
class Solution:
    def findTheString(self, lcp: List[List[int]]) -> str:
        n = len(lcp)
        group = [0] * n          # 0 = unassigned
        g = 0

        # ----- 1. Build groups -----
        for i in range(n):
            if group[i]:
                continue
            g += 1
            if g > 26:
                return ""
            for j in range(i, n):
                if lcp[i][j] > 0:
                    group[j] = g

        # ----- 2. Validate lcp -----
        for i in range(n):
            for j in range(n):
                v = lcp[i + 1][j + 1] if i + 1 < n and j + 1 < n else 0
                v = (v + 1) if group[i] == group[j] else 0
                if lcp[i][j] != v:
                    return ""

        # ----- 3. Build answer -----
        return ''.join(chr(ord('a') + x - 1) for x in group)
```

---

### 7.3 C++ (C++17)

```cpp
class Solution {
public:
    string findTheString(vector<vector<int>>& lcp) {
        int n = lcp.size();
        vector<int> group(n, 0);
        int g = 0;

        // ----- 1. Build groups -----
        for (int i = 0; i < n; ++i) {
            if (group[i]) continue;
            ++g;
            if (g > 26) return "";
            for (int j = i; j < n; ++j) {
                if (lcp[i][j] > 0)
                    group[j] = g;
            }
        }

        // ----- 2. Validate lcp -----
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                int v = (i + 1 < n && j + 1 < n) ? lcp[i + 1][j + 1] : 0;
                v = (group[i] == group[j]) ? v + 1 : 0;
                if (lcp[i][j] != v) return "";
            }
        }

        // ----- 3. Build answer -----
        string ans;
        ans.reserve(n);
        for (int x : group)
            ans.push_back('a' + x - 1);
        return ans;
    }
};
```

All three codes share the same `O(n²)` logic – only the syntax changes.



---

## 7.4 Quick test harness (Python)

```python
def test():
    sol = Solution()
    # Example 1
    lcp = [
        [4, 2, 0, 0],
        [2, 3, 1, 0],
        [0, 1, 2, 0],
        [0, 0, 0, 1]
    ]
    assert sol.findTheString(lcp) == "aabb"

    # Example 2 – impossible
    lcp = [
        [2, 1],
        [1, 1]
    ]
    assert sol.findTheString(lcp) == ""

    print("All tests passed")

test()
```

Feel free to plug in your own random tests – the `validate` step will catch any inconsistency.



---

## 7.5 “Good, bad, ugly” for interviews

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time** | `O(n²)` is optimal because you must read the whole matrix | `O(n³)` (e.g., double‑loop + recursive check) will time‑out | Not even reading the matrix (wrong data‑structure) → instant fail |
| **Memory** | `O(n)` extra memory, no large auxiliary matrices | `O(n²)` memory (full union‑find + matrix) – still okay but wasteful | Using `O(n³)` storage – impossible for `n=1000` |
| **Readability** | Straight‑forward two‑phase algorithm + comments | Over‑engineered (heavy DFS + recursion) | Mixing data‑structures and confusing indices (i, j, i+1, j+1) |
| **Edge‑case safety** | Handles `group > 26`, diagonal consistency, zero rows/cols | Ignores `lcp[i][i]` → wrong answer | Assumes `lcp[i][i]` always equals `n-i` without check |

> *Tip for the interview:*  
> *Start by explaining the “same / different” rule, then sketch the grouping idea, and finally show the validation step. The interviewer loves to see you tie the matrix back to the definition of the LCP.*

---

## 7.6 Take‑away

* **What makes this question tricky?**  
  The matrix encodes a very strong equivalence relation (`>0`) while also forcing inequality (`==0`).  
  Recovering a string from a pairwise property is a classic *reconstruction* problem.

* **Why the greedy grouping works:**  
  Because all equal‑letter indices must belong to a single component, the only freedom left is which *letter* each component receives.  
  Assigning them in the order of first appearance guarantees the smallest possible word.

* **Where to use this idea elsewhere:**  
  * Reconstructing a binary tree from preorder/postorder + inorder.  
  * String reconstruction from edit distances.  
  * Building the lexicographically minimal coloring of an interval graph.

---

## 7.7 Final words

* **Good** – The solution is clean, linear in space, quadratic in time, and proven correct.  
* **Bad** – Any solution that skips the consistency check (just grouping) will silently accept invalid matrices and return a string that does **not** match the input.  
* **Ugly** – Using recursion with memoisation on each pair `(i, j)` leads to a stack overflow for `n=1000` and hides the simple “group → letter” insight.

Happy coding! 🚀  

*(Feel free to copy the code snippets above into Leetcode’s editor or your local IDE to submit the solution.)*