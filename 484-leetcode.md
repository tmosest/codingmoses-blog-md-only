---
title: LeetCode 484. Find Permutation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Leetcode 484 – Find Permutation  
### “The Good, The Bad, and The Ugly” of Solving a Classic Interview Problem  
*(Java | Python | C++) –  O(n) time, O(1) auxiliary space)*  

---

### TL;DR  
* **Problem** – Build the lexicographically smallest permutation of `[1 … n]` that satisfies a pattern of “I” (increase) and “D” (decrease).  
* **Solution** – Scan the string once, and whenever a run of consecutive “D”s is found, reverse that segment of the natural order `[1, 2, …]`.  
* **Why it matters** – The greedy‑stack or sliding‑window technique is a frequent pattern in interviews (arrays, stacks, greedy). Mastering it shows you can translate a string of constraints into an optimal ordering in linear time.

---

## 1. Problem Recap

> **Input**: `s` – a string of length *n‑1*, each character is either `'I'` or `'D'`.  
> **Output**: the lexicographically smallest permutation `perm` of `[1 … n]` that satisfies  
> ```
> s[i] == 'I'  ⇔  perm[i] < perm[i+1]
> s[i] == 'D'  ⇔  perm[i] > perm[i+1]
> ```

> **Constraints**  
> * 1 ≤ s.length ≤ 10⁵  
> * s only contains `'I'` or `'D'`

> **Example**  
> `s = "DI"` → `[2, 1, 3]` (the smallest among `[2,1,3]` and `[3,1,2]`)

---

## 2. The Greedy Insight

*If we ignore the constraints for a moment, the natural ordering `[1,2,3,…]` is the lexicographically smallest permutation.*  

A run of `'D'` forces a **decreasing** subsequence.  
In a decreasing subsequence, the smallest lexicographic permutation is obtained by **reversing** that run.  

**Why reversing works**  
* Suppose we have a run `DI…I` of length `k+1` starting at index `i`.  
* The numbers that will occupy positions `i … i+k` are the next `k+1` natural numbers: `i+1, i+2, …, i+k+1`.  
* Reversing gives `i+k+1, …, i+2, i+1`, which is the smallest arrangement that is strictly decreasing.

Thus, the entire algorithm reduces to:

```
perm = [1, 2, …, n]
for each maximal consecutive 'D' block [l … r] in s:
    reverse perm[l … r+1]
return perm
```

The trick is to perform the reversal **in‑place** while scanning the string, avoiding an explicit stack or extra array.

---

## 3. The Three Idiomatic Implementations

Below are clean, production‑ready solutions in Java, Python, and C++.  
All run in `O(n)` time, use only the output array as auxiliary space (`O(1)` extra).

---

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public int[] findPermutation(String s) {
        int n = s.length() + 1;
        int[] perm = new int[n];

        // Fill with natural order 1..n
        for (int i = 0; i < n; ++i) perm[i] = i + 1;

        int i = 0;                     // current index in s
        while (i < s.length()) {
            if (s.charAt(i) == 'I') {  // nothing to do
                i++;
                continue;
            }

            // we hit a 'D' run. Find its end
            int start = i;
            while (i < s.length() && s.charAt(i) == 'D') i++;

            // reverse perm[start … i] (inclusive)
            int left = start, right = i;
            while (left < right) {
                int tmp = perm[left];
                perm[left] = perm[right];
                perm[right] = tmp;
                left++;
                right--;
            }
        }

        return perm;
    }

    // Simple test harness
    public static void main(String[] args) {
        System.out.println(Arrays.toString(
            new Solution().findPermutation("DI"))); // [2,1,3]
        System.out.println(Arrays.toString(
            new Solution().findPermutation("I")));  // [1,2]
    }
}
```

**Why this is the “Good” Java implementation**

| ✅ | Feature |
|---|---------|
| Single pass through `s` | `O(n)` time |
| No auxiliary stack or list | `O(1)` space |
| Clear intent: natural fill + selective reversal | Easy to read & maintain |

---

### 3.2 Python

```python
def find_permutation(s: str) -> list[int]:
    n = len(s) + 1
    perm = list(range(1, n + 1))          # natural order

    i = 0
    while i < len(s):
        if s[i] == 'I':
            i += 1
            continue

        start = i
        while i < len(s) and s[i] == 'D':
            i += 1

        # reverse perm[start : i+1] in place
        perm[start:i+1] = reversed(perm[start:i+1])

    return perm

# Demo
if __name__ == "__main__":
    print(find_permutation("DI"))  # [2, 1, 3]
    print(find_permutation("I"))   # [1, 2]
```

Python’s slice assignment with `reversed()` gives a one‑liner reversal – the *Pythonic* way.

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findPermutation(string s) {
        int n = s.size() + 1;
        vector<int> perm(n);
        iota(perm.begin(), perm.end(), 1);   // 1, 2, ..., n

        int i = 0;
        while (i < (int)s.size()) {
            if (s[i] == 'I') { ++i; continue; }

            int start = i;
            while (i < (int)s.size() && s[i] == 'D') ++i;

            // reverse perm[start .. i]
            reverse(perm.begin() + start, perm.begin() + i + 1);
        }
        return perm;
    }
};

int main() {
    Solution sol;
    auto res = sol.findPermutation("DI");
    for (int x : res) cout << x << ' ';   // 2 1 3
    cout << '\n';
}
```

C++ `iota` and `reverse` keep the code terse and efficient.

---

## 4. The Good, The Bad, and The Ugly

| Stage | What’s Happening | How to Avoid Pain |
|-------|------------------|-------------------|
| **The Good** | A single linear pass, only integer operations, no extra containers. | Use built‑in helpers (`iota`, `reverse`, slice assignment) to keep code readable. |
| **The Bad** | Trying to build a stack manually leads to **off‑by‑one** bugs and higher constant factors. | If you need a stack, use a `deque` or `vector<int>` and pop/push carefully. |
| **The Ugly** | Mixing the “reverse” logic inside an inner loop that also modifies the array can create confusing nested loops. | Separate the logic: 1) identify the `'D'` block, 2) perform a single reversal. 3) Continue scanning. |

> **Interview Tip** – If you’re ever asked to *explain* this solution, start by writing the natural order, then illustrate how a `D` run forces a reversal. Show a quick diagram of a sample string: `D D I I D` → `[1 2 3 4 5 6]` → reverse positions 0‑2 → `[3 2 1 4 5 6]` → continue.

---

## 5. Edge Cases & Complexity

| Edge Case | Result |
|-----------|--------|
| `s` = `"I"` | `[1, 2]` |
| `s` = `"D"` | `[2, 1]` |
| All `I`s | `[1, 2, …, n]` |
| All `D`s | `[n, n-1, …, 1]` |

**Complexity**

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | `O(n)` | `O(n)` | `O(n)` |
| Space  | `O(1)` (output array) | `O(1)` | `O(1)` |

The input size constraint (10⁵) is comfortably handled in all three languages.

---

## 6. Bonus – A Stack‑Based Alternative (for discussion)

A classic “stack” solution pushes indices onto a stack whenever an `'I'` or end of string is seen, then pops them to set values.  
While still `O(n)`, it uses an extra stack of size `O(n)` and can be slightly slower in practice due to memory overhead.  

```java
Stack<Integer> st = new Stack<>();
int val = 1;
for (int i = 0; i <= s.length(); i++) {
    st.push(i);
    if (i == s.length() || s.charAt(i) == 'I') {
        while (!st.isEmpty()) perm[st.pop()] = val++;
    }
}
```

Use this only if you’re explicitly asked to demonstrate stack usage; otherwise the sliding‑window reversal is cleaner.

---

## 7. SEO & Career‑Boosting Takeaway

1. **Keywords** – *Leetcode 484, Find Permutation, greedy stack, linear time, interview problem, Java Python C++ solution, O(n) algorithm, job interview coding*  
2. **Title** – *“Master Leetcode 484: Find Permutation – Greedy in Java, Python & C++”*  
3. **Meta description** – *Learn the fastest way to solve Leetcode 484, “Find Permutation”. See Java, Python and C++ implementations, complexity analysis, and interview‑ready explanations.*  

> **Why this matters for recruiters** – The pattern of translating a string constraint into an array ordering is a frequent interview question for software‑engineering roles. Demonstrating a clear, O(n) solution in multiple languages shows versatility and algorithmic fluency.

---

## 8. Final Thoughts

* Keep the code **simple**: natural order + targeted reversals.  
* Test with a few hand‑crafted examples, especially edge cases.  
* When discussing in an interview, walk through the example on a whiteboard, showing how each `'D'` run flips a segment.

Happy coding—and good luck landing that next job! 🚀